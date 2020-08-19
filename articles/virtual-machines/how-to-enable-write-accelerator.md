---
title: Azure-Skrivningsaccelerator
description: Dokumentation om hur du aktiverar och använder Skrivningsaccelerator
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: 0b5e6134de2260998e599bad0d1bf6b381898ffd
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513248"
---
# <a name="enable-write-accelerator"></a>Aktivera Skrivningsaccelerator

Skrivningsaccelerator är en disk kapacitet för Virtual Machines-serien (VM) på Premium Storage med Azure Managed Disks exklusivt. Som namn tillstånd är syftet med funktionen att förbättra I/O-svars tiden för skrivningar mot Azure Premium Storage. Skrivningsaccelerator passar utmärkt var logg fils uppdateringar krävs för att vara kvar på disk i ett mycket bra sätt för moderna databaser.

Skrivningsaccelerator är allmänt tillgängligt för virtuella datorer i M-serien i det offentliga molnet.

## <a name="planning-for-using-write-accelerator"></a>Planera för att använda Skrivningsaccelerator

Skrivningsaccelerator ska användas för volymer som innehåller transaktions loggen eller för att göra om loggar för ett DBMS. Vi rekommenderar inte att du använder Skrivningsaccelerator för data volymer i ett DBMS eftersom funktionen har optimerats för användning mot logg diskar.

Skrivningsaccelerator fungerar bara tillsammans med [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Om du aktiverar Skrivningsaccelerator för operativ system disken för den virtuella datorn startas den virtuella datorn om.
>
> Om du vill aktivera Skrivningsaccelerator till en befintlig Azure-disk som inte ingår i en volym version av flera diskar med Windows disk-eller volym hanterare, Windows lagrings utrymmen, Windows-skalbar fil server (SOFS), Linux-LVM eller MDADM, måste arbets belastningen som använder Azure-disken stängas av. Databas program som använder Azure-disken måste stängas av.
>
> Om du vill aktivera eller inaktivera Skrivningsaccelerator för en befintlig volym som är inbyggd från flera Azure Premium Storage-diskar och stripas med hjälp av Windows disk eller volym hanterare, Windows lagrings utrymmen, Windows-SOFS (Scale-Out File Server), Linux LVM eller MDADM, måste alla diskar som skapar volymen vara aktiverade eller inaktiverade för Skrivningsaccelerator i separata steg. **Innan du aktiverar eller inaktiverar Skrivningsaccelerator i en sådan konfiguration stänger du den virtuella Azure-datorn**.

Det bör inte vara nödvändigt att aktivera Skrivningsaccelerator för OS-diskar för SAP-relaterade VM-konfigurationer.

### <a name="restrictions-when-using-write-accelerator"></a>Begränsningar när skrivningsacceleratorer används

När du använder Skrivningsaccelerator för en Azure-disk/VHD gäller följande begränsningar:

- Tjänsten för Premium-disk-cachelagring måste vara inställd på none eller Read Only. Alla andra lägen för cachelagring stöds inte.
- Ögonblicks bilder stöds för närvarande inte för Skrivningsaccelerator-aktiverade diskar. Under säkerhets kopieringen utesluter Azure Backup tjänsten automatiskt Skrivningsaccelerator-aktiverade diskar som är anslutna till den virtuella datorn.
- Endast mindre I/O-storlekar (<= 512 KiB) tar den accelererade sökvägen. I arbets belastnings situationer där data hämtas Mass inläsning eller där transaktions logg buffertarna för olika DBMS är fyllda till en större grad innan de sparas i lagringen, är sannolikheten att i/O-skrivning till disk inte tar den accelererade sökvägen.

Det finns gränser för Azure Premium Storage virtuella hård diskar per virtuell dator som kan användas av Skrivningsaccelerator. De aktuella gränserna är:

| VM-SKU | Antal Skrivningsaccelerator diskar | Skrivningsaccelerator disk-IOPS per virtuell dator |
| --- | --- | --- |
| M416ms_v2 M416s_v2| 16 | 20000 |
| M208ms_v2 M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

IOPS-gränserna är per virtuell dator och *inte* per disk. Alla Skrivningsaccelerator diskar delar samma IOPS-gräns per virtuell dator.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Aktivera skrivningsacceleratorer på en specifik disk

I följande avsnitt beskrivs hur Skrivningsaccelerator kan aktive ras på Azure Premium Storage-VHD: er.

### <a name="prerequisites"></a>Krav

Följande krav gäller för användning av Skrivningsaccelerator vid den här tidpunkten:

- De diskar som du vill använda Azure-Skrivningsaccelerator mot måste vara [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/) på Premium Storage.
- Du måste använda en virtuell dator i M-serien

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Aktivera Azure Write Accelerator med Azure PowerShell

Azure Power Shell-modulen från version 5.5.0 inkluderar ändringarna i relevanta cmdletar för att aktivera eller inaktivera Skrivningsaccelerator för vissa Azure Premium Storage-diskar.
Följande Power Shell-kommandon har ändrats och utökas för att acceptera en parameter för Skrivningsaccelerator för att aktivera eller distribuera diskar som stöds av Skrivningsaccelerator.

En ny växel parameter, **-WriteAccelerator** har lagts till i följande cmdletar:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Om du inte anger egenskapen till false och distribuerar diskar som inte har stöd för Skrivningsaccelerator.

En ny växel parameter, **-OsDiskWriteAccelerator** , har lagts till i följande cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Om du inte anger parametern anger egenskapen till false som standard, vilket returnerar diskar som inte utnyttjar Skrivningsaccelerator.

En ny valfri boolesk parameter (icke-nullbar), **-OsDiskWriteAccelerator** har lagts till i följande cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Ange antingen $true eller $false för att kontrol lera stöd för Azure-Skrivningsaccelerator med diskarna.

Exempel på kommandon kan se ut så här:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Två huvud scenarier kan följas av skript som de visas i följande avsnitt.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Lägga till en ny disk som stöds av Skrivningsaccelerator med PowerShell

Du kan använda det här skriptet för att lägga till en ny disk till den virtuella datorn. Disken som skapas med det här skriptet använder Skrivningsaccelerator.

Ersätt `myVM` , `myWAVMs` , `log001` , storlek på disken och LunID på disken med värden som är lämpliga för din speciella distribution.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Aktivera Skrivningsaccelerator på en befintlig Azure-disk med PowerShell

Du kan använda det här skriptet för att aktivera Skrivningsaccelerator på en befintlig disk. Ersätt `myVM` , `myWAVMs` och `test-log001` med lämpliga värden för din speciella distribution. Skriptet lägger till Skrivningsaccelerator till en befintlig disk där värdet för **$newstatus** är inställt på "$true". Om du använder värdet $false inaktive ras Skrivningsaccelerator på en specifik disk.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Att köra skriptet ovan kopplar bort den angivna disken, aktiverar Skrivningsaccelerator mot disken och ansluter sedan disken igen

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Aktivera Azure Write Accelerator med Azure-portalen

Du kan aktivera Skrivningsaccelerator via portalen där du anger inställningarna för diskcachelagring:

![Skrivningsaccelerator på Azure Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Aktivera Azure Write Accelerator med Azure CLI

Du kan använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att aktivera Skrivningsaccelerator.

Om du vill aktivera Skrivningsaccelerator på en befintlig disk använder du [AZ VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update). du kan använda följande exempel om du ersätter DiskName, VMName och ResourceGroup med dina egna värden: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Om du vill ansluta en disk med Skrivningsaccelerator aktiverat Använd [AZ VM disk Attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)kan du använda följande exempel om du ersätter dina egna värden: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Om du vill inaktivera Skrivningsaccelerator använder du [AZ VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)och anger egenskaperna till falskt: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Aktivera Skrivningsaccelerator med hjälp av REST API: er

Om du vill distribuera via Azure REST API måste du installera Azure-armclient.

### <a name="install-armclient"></a>Installera armclient

Om du vill köra armclient måste du installera den genom choklad. Du kan installera det via cmd.exe eller PowerShell. Använd utökade rättigheter för dessa kommandon ("kör som administratör").

Kör följande kommando med cmd.exe: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Använd Power Shell och kör följande kommando: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nu kan du installera armclient genom att använda följande kommando i antingen cmd.exe eller PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Hämta din aktuella VM-konfiguration

Om du vill ändra disk konfigurationens attribut måste du först hämta den aktuella konfigurationen i en JSON-fil. Du kan hämta den aktuella konfigurationen genom att köra följande kommando: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Ersätt villkoren i <<   >> med dina data, inklusive fil namnet som JSON-filen ska ha.

Utdata kan se ut så här:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Uppdatera sedan JSON-filen och aktivera Skrivningsaccelerator på disken med namnet "log1". Detta kan åstadkommas genom att lägga till det här attributet i JSON-filen efter cache-posten på disken.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Uppdatera sedan den befintliga distributionen med det här kommandot: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Utdata bör se ut som den som visas nedan. Du kan se att Skrivningsaccelerator aktive rad för en disk.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

När du har gjort den här ändringen bör enheten stödjas av Skrivningsaccelerator.
