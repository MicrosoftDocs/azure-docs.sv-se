---
title: Felsöka VNET gateway och anslutningar – Azure REST API
titleSuffix: Azure Network Watcher
description: Den här sidan förklarar hur du felsöker Virtual Network gatewayer och anslutningar med Azure Network Watcher med REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011041"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Felsöka Virtual Network gateway och anslutningar med Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner som är relaterade till förståelse av dina nätverks resurser i Azure. En av dessa funktioner är resurs fel sökning. Resurs fel sökning kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas kontrollerar Network Watcher hälsan för en Virtual Network gateway eller en anslutning och returnerar resultatet.

Den här artikeln tar dig igenom de olika hanterings aktiviteter som för närvarande är tillgängliga för resurs fel sökning.

- [**Felsöka en Virtual Network Gateway**](#troubleshoot-a-virtual-network-gateway)
- [**Felsöka en anslutning**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST API med hjälp av PowerShell. ARMClient finns på choklad på [ARMClient](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher.

En lista över Gateway-typer som stöds finns i [Gateway-typer som stöds](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Network Watcher fel sökning ger möjlighet att felsöka problem som uppstår med Virtual Network gatewayer och anslutningar. När en begäran görs till resurs fel sökningen, frågar och inspekteras loggarna. När inspektionen är klar returneras resultatet. Felsök API-begäranden är tids krävande begär Anden, vilket kan ta flera minuter att returnera ett resultat. Loggar lagras i en behållare på ett lagrings konto.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Felsöka en Virtual Network Gateway


### <a name="post-the-troubleshoot-request"></a>PUBLICERA fel söknings förfrågan

I följande exempel frågas statusen för en Virtual Network Gateway.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Eftersom den här åtgärden körs för länge returneras URI: n för att fråga åtgärden och URI: n för resultatet i svars huvudet som visas i följande svar:

**Viktiga värden**

* **Azure-AsyncOperation** – den här egenskapen innehåller URI: n för att skicka frågor till den asynkrona fel söknings åtgärden
* **Plats** – den här egenskapen innehåller URI: n där resultatet är när åtgärden har slutförts

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Fråga den asynkrona åtgärden för slut för ande

Använd Operations-URI: n för att fråga efter åtgärdens förlopp som visas i följande exempel:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

När åtgärden pågår, visar svaret **det som visas** i följande exempel:

```json
{
  "status": "InProgress"
}
```

När åtgärden har slutförts ändras statusen till **slutförd**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Hämta resultaten

När statusen som returnerades är **klar** anropar du en get-metod på OperationResult under pågående-URI: n för att hämta resultaten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Följande svar är exempel på ett typiskt försämrat svar som returneras när du frågar efter resultatet av fel sökning av en gateway. Se [förstå resultaten](#understanding-the-results) för att få information om vad egenskaperna i svaret innebär.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Felsöka anslutningar

I följande exempel frågas statusen för en anslutning.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Fel söknings åtgärden kan inte köras parallellt på en anslutning och dess motsvarande gatewayer. Åtgärden måste slutföras innan den körs på den tidigare resursen.

Eftersom det här är en tids krävande transaktion, i svars huvudet, returneras URI: n för att fråga åtgärden och URI: n för resultatet som visas i följande svar:

**Viktiga värden**

* **Azure-AsyncOperation** – den här egenskapen innehåller URI: n för att skicka frågor till den asynkrona fel söknings åtgärden
* **Plats** – den här egenskapen innehåller URI: n där resultatet är när åtgärden har slutförts

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Fråga den asynkrona åtgärden för slut för ande

Använd Operations-URI: n för att fråga efter åtgärdens förlopp som visas i följande exempel:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

När åtgärden pågår, visar svaret **det som visas** i följande exempel:

```json
{
  "status": "InProgress"
}
```

När åtgärden har slutförts ändras statusen till **slutförd**.

```json
{
  "status": "Succeeded"
}
```

Följande svar är exempel på ett typiskt svar som returneras när du frågar efter resultatet av fel sökning av en anslutning.

### <a name="retrieve-the-results"></a>Hämta resultaten

När statusen som returnerades är **klar** anropar du en get-metod på OperationResult under pågående-URI: n för att hämta resultaten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Följande svar är exempel på ett typiskt svar som returneras när du frågar efter resultatet av fel sökning av en anslutning.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärds texten ger allmän vägledning om hur du löser problemet. Om en åtgärd kan vidtas för problemet, finns en länk med ytterligare vägledning. I de fall där det inte finns någon ytterligare vägledning ger svaret URL: en för att öppna ett support ärende.  Mer information om svarets egenskaper och vad som ingår finns i [Network Watcher Felsök översikt](network-watcher-troubleshoot-overview.md)

Anvisningar om hur du laddar ned filer från Azure Storage-konton finns i [komma igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer hittar du här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som förhindrar VPN-anslutning, se [Hantera nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md) för att spåra de nätverks säkerhets grupper och säkerhets regler som kan vara i fråga.