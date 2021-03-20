---
title: Hantera paket fångster – Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan förklaras hur du hanterar funktionen för att hämta paket i Network Watcher med hjälp av PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8779381425d4180cc7320f251de9c2cfdd1f0510
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99223431"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Hantera paket fångster med Azure Network Watcher med hjälp av PowerShell

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Med Network Watcher paket insamling kan du skapa avbildnings sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingsbufferten för att se till att du bara fångar den trafik som du vill använda. Med paket fångst kan du diagnostisera nätverks avvikelser både återaktivt och proaktivt. Andra användnings områden innefattar insamling av nätverks statistik, få information om nätverks intrång, för att felsöka klient-server-kommunikation och mycket mer. Genom att kunna fjärrutlös paket fångster kan den här funktionen under lätta belastningen på att köra en paket registrering manuellt och på önskad dator, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika hanterings uppgifter som för närvarande är tillgängliga för paket fångst.

- [**Starta en paket fångst**](#start-a-packet-capture)
- [**Stoppa en paket fångst**](#stop-a-packet-capture)
- [**Ta bort en paket avbildning**](#delete-a-packet-capture)
- [**Ladda ned en paket avbildning**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i den region som du vill skapa en paket fångst

* En virtuell dator med paket insamlings tillägget aktiverat.

> [!IMPORTANT]
> Paket fångst kräver ett tillägg för virtuell dator `AzureNetworkWatcherExtension` . För att installera tillägget på en virtuell Windows-dator går du till [azure Network Watcher agent-tillägget virtuell dator för Windows](../virtual-machines/extensions/network-watcher-windows.md) och för virtuella Linux-datorer gå till [Azure Network Watcher virtuell dator tillägg för Linux](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Steg 2

I följande exempel hämtas den tilläggs information som krävs för att köra `Set-AzVMExtension` cmdleten. Denna cmdlet installerar paket insamlings agenten på den virtuella gäst datorn.

> [!NOTE]
> Det `Set-AzVMExtension` kan ta flera minuter att slutföra cmdleten.

För virtuella Windows-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

För virtuella Linux-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Följande exempel är ett lyckat svar när du har kört `Set-AzVMExtension` cmdleten.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Steg 3

Kontrol lera att agenten är installerad genom att köra `Get-AzVMExtension` cmdleten och skicka den till den virtuella datorns namn och tilläggets namn.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Följande exempel är ett exempel på svaret från att köras `Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Starta en paket fångst

När föregående steg har slutförts installeras paket insamlings agenten på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Nästa steg är att hämta Network Watcher-instansen. Den här variabeln skickas till `New-AzNetworkWatcherPacketCapture` cmdleten i steg 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher  | Where {$_.Location -eq "westcentralus" }
```

### <a name="step-2"></a>Steg 2

Hämta ett lagrings konto. Det här lagrings kontot används för att lagra paket insamlings filen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Steg 3

Filter kan användas för att begränsa de data som lagras av paket fångsten. I följande exempel konfigureras två filter.  Ett filter samlar endast utgående TCP-trafik från lokala IP-10.0.0.3 till mål portarna 20, 80 och 443.  Det andra filtret samlar endast in UDP-trafik.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Flera filter kan definieras för en paket fångst.

### <a name="step-4"></a>Steg 4

Kör `New-AzNetworkWatcherPacketCapture` cmdleten för att starta paket fångst processen och överför de nödvändiga värdena som hämtades i föregående steg.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

I följande exempel visas förväntade utdata från att köra `New-AzNetworkWatcherPacketCapture` cmdleten.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Hämta en paket fångst

Kör `Get-AzNetworkWatcherPacketCapture` cmdleten, hämtar status för en pågående eller slutförd paket fångst.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

I följande exempel visas utdata från `Get-AzNetworkWatcherPacketCapture` cmdleten. Följande exempel är efter att avbildningen har slutförts. PacketCaptureStatus-värdet har stoppats, med en StopReason på TimeExceeded. Det här värdet visar att paket fångsten lyckades och körde tiden.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Stoppa en paket fångst

Genom att köra `Stop-AzNetworkWatcherPacketCapture` cmdleten stoppas den om en insamlings session pågår.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Cmdleten returnerar inget svar när den kördes på en pågående infångstutlösare eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort en paket avbildning

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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
