---
title: Felsöka Azure VNet gateway och anslutningar – Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan förklaras hur du använder Azure Network Watcher felsöka PowerShell-cmdlet
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
ms.openlocfilehash: 99a30065e11a55f4c21b9e6ffc69b0a1693ecbdc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019745"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Felsöka Virtual Network gateway och anslutningar med Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner som är relaterade till förståelse av dina nätverks resurser i Azure. En av dessa funktioner är resurs fel sökning. Resurs fel sökning kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas kontrollerar Network Watcher hälsan för en Virtual Network gateway eller en anslutning och returnerar resultatet.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher.

En lista över Gateway-typer som stöds finns i [Gateway-typer som stöds](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Resurs fel sökning ger möjlighet att felsöka problem som uppstår med Virtual Network gatewayer och anslutningar. När en begäran om resurs fel sökning görs efter frågas och inspekteras loggar. När inspektionen är klar returneras resultatet. Resurs fel söknings förfrågningar är tids krävande begär Anden, vilket kan ta flera minuter att returnera ett resultat. Loggarna från fel sökning lagras i en behållare på ett lagrings konto som har angetts.

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Det första steget är att hämta Network Watcher-instansen. `$networkWatcher`Variabeln skickas till cmdlet: en `Start-AzNetworkWatcherResourceTroubleshooting` i steg 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Virtual Network Gateway-anslutning

I det här exemplet körs resurs fel sökning på en anslutning. Du kan också skicka det till en Virtual Network Gateway.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Resurs fel sökning returnerar data om resursens hälso tillstånd, den sparar också loggar till ett lagrings konto som ska granskas. I det här steget skapar vi ett lagrings konto om det finns ett befintligt lagrings konto som du kan använda.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Köra Network Watcher Resource Troubleshooting

Du felsöker resurser med `Start-AzNetworkWatcherResourceTroubleshooting` cmdleten. Vi skickar cmdleten Network Watcher-objektet, ID: t för anslutningen eller Virtual Network Gateway, lagrings kontots ID och sökvägen för att lagra resultaten.

> [!NOTE]
> `Start-AzNetworkWatcherResourceTroubleshooting`Cmdleten körs länge och det kan ta några minuter att slutföra den.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

När du har kört cmdleten granskar Network Watcher resursen för att verifiera hälso tillståndet. Det returnerar resultatet till gränssnittet och lagrar loggar av resultaten i det angivna lagrings kontot.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärds texten ger allmän vägledning om hur du löser problemet. Om en åtgärd kan vidtas för problemet, finns en länk med ytterligare vägledning. I de fall där det inte finns någon ytterligare vägledning ger svaret URL: en för att öppna ett support ärende.  Mer information om svarets egenskaper och vad som ingår finns i [Network Watcher Felsök översikt](network-watcher-troubleshoot-overview.md)

Anvisningar om hur du laddar ned filer från Azure Storage-konton finns i [komma igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer hittar du här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som förhindrar VPN-anslutning, se [Hantera nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md) för att spåra de nätverks säkerhets grupper och säkerhets regler som kan vara i fråga.