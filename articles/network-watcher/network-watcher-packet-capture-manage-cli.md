---
title: Hantera paket fångster med Azure Network Watcher – Azure CLI | Microsoft Docs
description: På den här sidan förklaras hur du hanterar funktionen för att skapa paket i Network Watcher med hjälp av Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698645"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Hantera paket fångster med Azure Network Watcher med Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Med Network Watcher paket insamling kan du skapa avbildnings sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingsbufferten för att se till att du bara fångar den trafik som du vill använda. Med paket fångst kan du diagnostisera nätverks avvikelser både återaktivt och proaktivt. Andra användnings områden innefattar insamling av nätverks statistik, få information om nätverks intrång, för att felsöka klient-server-kommunikation och mycket mer. Genom att kunna fjärrutlös paket fångster kan den här funktionen under lätta belastningen på att köra en paket registrering manuellt och på önskad dator, vilket sparar värdefull tid.

För att utföra stegen i den här artikeln måste du [Installera Azure Command-Line-gränssnittet för Mac, Linux och Windows (Azure CLI)](/cli/azure/install-azure-cli).

Den här artikeln tar dig igenom de olika hanterings uppgifter som för närvarande är tillgängliga för paket fångst.

- [**Starta en paket fångst**](#start-a-packet-capture)
- [**Stoppa en paket fångst**](#stop-a-packet-capture)
- [**Ta bort en paket avbildning**](#delete-a-packet-capture)
- [**Ladda ned en paket avbildning**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Network Watcher i den region som du vill skapa en paket fångst
- En virtuell dator med paket insamlings tillägget aktiverat.

> [!IMPORTANT]
> Paket fångst kräver att en agent körs på den virtuella datorn. Agenten installeras som ett tillägg. Instruktioner för VM-tillägg finns i [tillägg och funktioner för virtuella datorer](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

Kör `az vm extension set` kommandot för att installera paket insamlings agenten på den virtuella gäst datorn.

För virtuella Windows-datorer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

För virtuella Linux-datorer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Steg 2

Kontrol lera att agenten är installerad genom att köra `vm extension show` kommandot och skicka det till resurs gruppen och namnet på den virtuella datorn. Kontrol lera den resulterande listan för att se till att agenten är installerad.

För virtuella Windows-datorer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

För virtuella Linux-datorer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Följande exempel är ett exempel på svaret från att köras `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Starta en paket fångst

När föregående steg har slutförts installeras paket insamlings agenten på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Hämta ett lagrings konto. Det här lagrings kontot används för att lagra paket insamlings filen.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Steg 2

Nu är du redo att skapa en paket fångst.  Först ska vi undersöka de parametrar som du kanske vill konfigurera. Filter är en sådan parameter som kan användas för att begränsa de data som lagras av paket fångsten. I följande exempel ställer du in en paket avbildning med flera filter.  De första tre filtren samlar endast utgående TCP-trafik från lokala IP-10.0.0.3 till mål portarna 20, 80 och 443.  Det sista filtret samlar endast in UDP-trafik.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

I följande exempel visas förväntade utdata från att köra `az network watcher packet-capture create` kommandot.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Hämta en paket fångst

Kör `az network watcher packet-capture show-status` kommandot, hämtar status för en pågående eller slutförd paket fångst.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

I följande exempel visas utdata från `az network watcher packet-capture show-status` kommandot. I följande exempel visas när avbildningen stoppas, med en StopReason på TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Stoppa en paket fångst

Genom att köra `az network watcher packet-capture stop` kommandot, om en redigeringssession pågår, stoppas den.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Kommandot returnerar inget svar när det kördes på en pågående redigeringssession eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort en paket avbildning

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Om du tar bort en paket avbildning tas inte filen bort i lagrings kontot.

## <a name="download-a-packet-capture"></a>Ladda ned en paket avbildning

När din paket insamlings session har slutförts kan infångstfilen överföras till Blob Storage eller till en lokal fil på den virtuella datorn. Lagrings platsen för paket fångsten definieras vid skapandet av sessionen. Ett användbart verktyg för att komma åt dessa insamlingsfiler som sparas till ett lagrings konto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagrings konto anges sparas paket insamlings filer till ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar paket fångster med aviseringar för virtuella datorer genom att visa [skapa en varning utlöst paket fångst](network-watcher-alert-triggered-packet-capture.md)

Ta reda på om en viss trafik tillåts i eller från den virtuella datorn genom [att gå igenom kontrol lera IP-flöde verifiera](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->