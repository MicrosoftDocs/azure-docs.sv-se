---
title: Tillfälliga operativsystemdiskar
description: Läs mer om tillfälliga OS-diskar för virtuella Azure-datorer.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762631"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Tillfälliga OS-diskar för virtuella Azure-datorer

Tillfälliga OS-diskar skapas på den lokala vm-lagringen och sparas inte till fjärrlagringsservern Azure Storage. Tillfälliga OS-diskar fungerar bra för tillståndslösa arbetsbelastningar, där program är toleranta mot enskilda VM-fel, men som påverkas mer av vm-distributionstiden eller avbildning av enskilda VM-instanser. Med tillfälliga OS-diskar får du lägre läs-/skrivfördröjning till OS-disken och snabbare VM-avbildning. 
 
De viktigaste funktionerna i tillfälliga diskar är: 
- Perfekt för tillståndslösa program.
- De kan användas med både Marketplace-avbildningar och anpassade avbildningar.
- Möjlighet att snabbt återställa eller återställa avbildningar av virtuella datorer och skalningsuppsättningsinstanser till det ursprungliga starttillståndet.  
- Kortare svarstider, liknar en tillfällig disk. 
- Tillfälliga OS-diskar är kostnadsfria. Du har ingen lagringskostnad för OS-disken.
- De är tillgängliga i alla Azure-regioner. 
- Tillfälliga OS-diskar stöds av [Shared Image Gallery](./shared-image-galleries.md). 
 

 
Viktiga skillnader mellan beständiga och tillfälliga OS-diskar:

|                             | Beständig OS-disk                          | Differentierande OS-disk                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Storleksgräns för OS-disk**      | 2 TiB                                                                                        | Cachestorlek för VM-storleken eller 2TiB, beroende på vilket som är mindre. Information om **cachestorleken i GiB** finns [i DS,](sizes-general.md) [ES](sizes-memory.md), [M,](sizes-memory.md) [FS](sizes-compute.md)och [GS](sizes-previous-gen.md#gs-series)              |
| **VM-storlekar som stöds**          | Alla                                                                                          | VM-storlekar som stöder Premium-lagring, till exempel DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Stöd för disktyper**           | Hanterad och ohanterad OS-disk                                                                | Endast hanterad OS-disk                                                               |
| **Stöd för regioner**              | Alla regioner                                                                                  | Alla regioner                              |
| **Datapersistence**            | OS-diskdata som skrivs till OS-disken lagras i Azure Storage                                  | Data som skrivs till OS-disken lagras på den lokala vm-lagringen och sparas inte Azure Storage. |
| **Statusen Stoppa frisallokerad**      | Virtuella datorer och skalningsuppsättningsinstanser kan stoppas och startas om från stopptillståndet | Virtuella datorer och skalningsuppsättningsinstanser kan inte stoppas                                  |
| **Specialiserad os-disksupport** | Ja                                                                                          | Inga                                                                                 |
| **Ändra storlek på os-disk**              | Stöds när den virtuella datorn skapas och efter att den virtuella datorn har stoppats                                | Stöds endast när den virtuella datorn skapas                                                  |
| **Ändra storlek till en ny vm-storlek**   | OS-diskdata bevaras                                                                    | Data på OS-disken tas bort, operativsystemet etableras igen       
| **Sidfilsplacering**   | För Windows lagras sidfilen på resursdisken                                              | För Windows lagras sidfilen på OS-disken   |

## <a name="size-requirements"></a>Storlekskrav

Du kan distribuera virtuella datorer och instansavbildningar upp till storleken på VM-cacheminnet. Till exempel är Standard Windows Server-avbildningar från marknadsplatsen cirka 127 GiB, vilket innebär att du behöver en VM-storlek som har ett cacheminne som är större än 127 GiB. I det här fallet [har Standard_DS2_v2](dv2-dsv2-series.md) en cachestorlek på 86 GiB, vilket inte är tillräckligt stort. Den Standard_DS3_v2 har en cachestorlek på 172 GiB, vilket är tillräckligt stort. I det här fallet är Standard_DS3_v2 den minsta storleken i DSv2-serien som du kan använda med den här avbildningen. Grundläggande Linux-avbildningar på Marketplace och Windows Server-avbildningar som betecknas av tenderar att vara cirka 30 GiB och kan använda de flesta av de tillgängliga `[smallsize]` VM-storlekarna.

Tillfälliga diskar kräver också att VM-storleken stöder Premium-lagring. Storlekarna har vanligtvis (men inte alltid) en i namnet, till `s` exempel DSv2 och EsV3. Mer information finns i Storlekar [på virtuella Azure-datorer för](sizes.md) information om vilka storlekar som stöder Premium-lagring.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Förhandsversion – Tillfälliga OS-diskar kan nu lagras på temporära diskar
Tillfälliga OS-diskar kan nu lagras på temporär/resursdisk för virtuella datorer utöver VM-cachen. Så nu kan du använda tillfälliga OS-diskar med virtuella datorer som inte har ett cacheminne eller inte har tillräckligt med cache men som har en temporär/resursdisk för att lagra den tillfälliga OS-disken som Dav3, Dav4, Eav4 och Eav3. Om en virtuell dator har tillräckligt med cacheminne och temporärt utrymme kan du nu också ange var du vill lagra den tillfälliga OS-disken med hjälp av en ny egenskap som heter [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Med den här funktionen konfigurerar vi växlingsfilen så att den finns på OS-disken när en virtuell Windows-dator etableras. Den här funktionen finns för närvarande som en förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Kom igång genom att [begära åtkomst](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Om du vill använda en ohemlig disk för en distribution av en virtuell PowerShell-dator använder du [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) i din VM-konfiguration. Ange `-DiffDiskSetting` till `Local` och till `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

För distributioner av skalningsuppsättning använder du cmdleten [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) i konfigurationen. Ange `-DiffDiskSetting` till `Local` och till `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Om du vill använda en ohemlig disk för en CLI VM-distribution anger du `--ephemeral-os-disk` parametern i [az vm create](/cli/azure/vm#az_vm_create) till och `true` `--os-disk-caching` parametern till `ReadOnly` .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

För skalningsuppsättningar använder du samma `--ephemeral-os-disk true` parameter för [az-vmss-create och](/cli/azure/vmss#az_vmss_create) anger `--os-disk-caching` parametern till `ReadOnly` .

## <a name="portal"></a>Portalen

I Azure Portal du välja att använda tillfälliga diskar när du distribuerar en virtuell  dator genom att öppna avsnittet **Avancerat på fliken** Diskar. För **Använd ohemlig OS-disk väljer** du **Ja.**

![Skärmbild som visar alternativknappen för att välja en ohemlig OS-disk](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Om alternativet för att använda en ohemlig disk är nedtonat kan du ha valt en VM-storlek som inte har en cachestorlek som är större än OS-avbildningen eller som inte stöder Premium Storage. Gå tillbaka till sidan **Grundläggande inställningar och** prova att välja en annan vm-storlek.

Du kan också skapa skalningsuppsättningar med tillfälliga OS-diskar med hjälp av portalen. Se bara till att du väljer en VM-storlek med tillräckligt stor cachestorlek och i **Använd ohemlig OS-disk väljer** du **Ja.**

![Skärmbild som visar alternativknappen för att välja en ohemlig OS-disk för din skalningsuppsättning](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Distribution av skalningsuppsättningsmall  
Processen för att skapa en skalningsuppsättning som använder en ohemlig OS-disk är att lägga till egenskapen `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` till resurstypen i mallen. Dessutom måste cachelagringsprincipen anges till för `ReadOnly` den tillfälliga OS-disken. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Distribution av VM-mall 
Du kan distribuera en virtuell dator med en ohemlig OS-disk med hjälp av en mall. Processen för att skapa en virtuell dator som använder tillfälliga OS-diskar är att lägga till egenskapen till `diffDiskSettings` resurstypen Microsoft.Compute/virtualMachines i mallen. Dessutom måste cachelagringsprincipen anges till för `ReadOnly` den tillfälliga OS-disken. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Avbildning av en virtuell dator med hjälp av REST
Du kan avbildning av en instans av en virtuell dator med en ohemlig OS-disk med REST API enligt beskrivningen nedan och via Azure Portal genom att gå till fönstret Översikt för den virtuella datorn. För skalningsuppsättningar är avbildning redan tillgängligt via PowerShell, CLI och portalen.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Hur stor är de lokala OS-diskarna?**

S: Vi stöder plattformsbaserade och anpassade avbildningar, upp till VM-cachestorleken, där alla läsningar/skrivningar till OS-disken kommer att vara lokala på samma nod som den virtuella datorn. 

**F: Går det att ändra storlek på den tillfälliga OS-disken?**

S: Nej, när den tillfälliga OS-disken har etablerats går det inte att ändra storlek på OS-disken. 

**F: Kan jag koppla en Managed Disks till en ohemlig virtuell dator?**

S: Ja, du kan ansluta en hanterad datadisk till en virtuell dator som använder en ohemlig OS-disk. 

**F: Kommer alla VM-storlekar att stödjas för tillfälliga OS-diskar?**

S: Nej, de Premium Storage virtuella datorstorlekarna stöds (DS, ES, FS, GS, M osv.). Om du vill veta om en viss VM-storlek stöder tillfälliga OS-diskar kan du:

Anropa `Get-AzComputeResourceSku` PowerShell-cmdleten
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**F: Kan den tillfälliga OS-disken tillämpas på befintliga virtuella datorer och skalningsuppsättningar?**

S: Nej, tillfälliga OS-diskar kan bara användas när den virtuella datorn och skalningsuppsättningen skapas. 

**F: Kan du blanda tillfälliga och normala OS-diskar i en skalningsuppsättning?**

S: Nej, du kan inte ha en blandning av tillfälliga och beständiga OS-diskinstanser inom samma skalningsuppsättning. 

**F: Kan den tillfälliga OS-disken skapas med powershell eller CLI?**

S: Ja, du kan skapa virtuella datorer med tillfälliga OS-diskar med hjälp av REST, mallar, PowerShell och CLI.

**F: Vilka funktioner stöds inte med tillfälliga OS-diskar?**

S: Tillfälliga diskar stöder inte:
- Samla in VM-avbildningar
- Ögonblicksbilder 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Diskväxling för operativsystem 

> [!NOTE]
> 
> Tillfälliga diskar kan inte nås via portalen. Du får felmeddelandet "Resursen hittades inte" eller "404" när du öppnar den tillfälliga disken som förväntas.
> 
 
## <a name="next-steps"></a>Nästa steg
Du kan skapa en virtuell dator med en ohemlig OS-disk med hjälp av [Azure CLI.](/cli/azure/vm#az_vm_create)
