---
title: Felsöka Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller fel söknings tips för Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563378"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Azure Disk Encryption för fel söknings guide för Linux-VM: ar

Den här guiden är för IT-proffs, informations säkerhets analytiker och moln administratörer vars organisationer använder Azure Disk Encryption. Den här artikeln är att hjälpa till med fel sökning av problem med disk kryptering.

Innan du vidtar stegen nedan måste du först se till att de virtuella datorer som du försöker kryptera finns bland de [VM-storlekar och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems)och att du uppfyller alla krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverkskrav](disk-encryption-overview.md#networking-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Felsöka Linux OS disk Encryption

Disk kryptering för Linux-operativsystem (OS) måste demontera operativ system enheten innan den kan köras genom hela disk krypterings processen. Om det inte går att demontera enheten visas fel meddelandet "Det gick inte att demontera efter..." sannolikt kommer att inträffa.

Det här felet kan inträffa när disk kryptering för operativ systemet görs på en virtuell dator med en miljö som har ändrats från den bild som stöds av lager galleriet. Avvikelser från den avbildning som stöds kan störa tilläggets möjlighet att demontera OS-enheten. Exempel på avvikelser kan innehålla följande objekt:
- Anpassade avbildningar matchar inte längre ett fil system eller partitionerings schema som stöds.
- Stora program som SAP, MongoDB, Apache Cassandra och Docker stöds inte när de är installerade och körs i operativ systemet före kryptering. Azure Disk Encryption kan inte stänga av dessa processer på ett säkert sätt som krävs för att förbereda operativ system enheten för disk kryptering. Om det fortfarande finns aktiva processer som arbetar med öppna fil referenser till operativ system enheten, kan inte operativ system enheten demonteras, vilket leder till att operativ system enheten inte krypteras. 
- Anpassade skript som körs i nära tiden närmar sig den kryptering som aktive ras, eller om andra ändringar görs på den virtuella datorn under krypterings processen. Den här konflikten kan inträffa när en Azure Resource Manager mall definierar flera tillägg som ska köras samtidigt, eller när ett anpassat skript tillägg eller annan åtgärd körs samtidigt för disk kryptering. Att serialisera och isolera sådana steg kan lösa problemet.
- Security Enhanced Linux (SELinux) har inte inaktiverats innan kryptering aktive ras, så det går inte att demontera steget. SELinux kan aktive ras igen när krypteringen är klar.
- OS-disken använder ett LVM-schema (Logical Volume Manager). Även om det begränsade stödet för LVM-datadisk är tillgängligt är det inte en LVM OS-disk.
- Minimi kraven på minne är inte uppfyllda (7 GB rekommenderas för disk kryptering i operativ system).
- Data enheter monteras rekursivt under/mnt/-katalogen, eller med varandra (till exempel/mnt/data1,/mnt/data2,/DATA3 +/DATA3/DATA4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Uppdatera standard kärnan för Ubuntu 14,04-LTS

Ubuntu 14,04 LTS-avbildningen levereras med en standard-kernel-version på 4,4. Den här kernel-versionen har ett känt problem där slut på minne-Killer felaktigt avslutar kommandot DD under processen för operativ system kryptering. Den här buggen har åtgärd ATS i den senaste Azure-justerade Linux-kärnan. För att undvika det här felet, innan du aktiverar kryptering på avbildningen, uppdaterar du till [Azure-justerade kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) eller senare med hjälp av följande kommandon:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

När den virtuella datorn har startats om i den nya kerneln kan den nya kernel-versionen bekräftas med:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Uppdatera Azure Virtual Machine agent och tilläggs versioner

Azure Disk Encryption åtgärder kan Miss lyckas på avbildningar av virtuella datorer med hjälp av versioner av Azure Virtual Machine agent som inte stöds. Linux-avbildningar med agent versioner tidigare än 2.2.38 bör uppdateras innan kryptering aktive ras. Mer information finns i [så här uppdaterar du Azure Linux-agenten på en VM](../extensions/update-linux-agent.md) och [lägsta versions stöd för virtuella dator agenter i Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Rätt version av gäst agent tillägget Microsoft. Azure. Security. AzureDiskEncryption eller Microsoft. Azure. Security. AzureDiskEncryptionForLinux krävs också. Tilläggs versioner underhålls och uppdateras automatiskt av plattformen när krav för Azure Virtual Machine agent är uppfyllda och en version av den virtuella dator agenten som stöds används.

Tillägget Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux är föråldrat och stöds inte längre.  

## <a name="unable-to-encrypt-linux-disks"></a>Det gick inte att kryptera Linux-diskar

I vissa fall verkar Linux-diskkryptering vara fastnat vid "OS disk Encryption startade" och SSH är inaktiverat. Krypterings processen kan ta mellan 3-16 timmar att avsluta på en akties galleri bild. Om data diskar med flera terabyte-storlek läggs till kan processen ta flera dagar.

Disk krypterings ordningen i Linux OS demonterar tillfälligt operativ system enheten. Den utför sedan block-för-block-kryptering av hela OS-disken innan den monteras på nytt i krypterat tillstånd. Linux Disk Encryption tillåter inte samtidig användning av den virtuella datorn medan krypteringen pågår. Prestanda egenskaperna för den virtuella datorn kan göra en betydande skillnad i den tid som krävs för att slutföra krypteringen. Dessa egenskaper inkluderar disk storleken och om lagrings kontot är standard-eller Premium-lagring (SSD).

Medan operativ system enheten krypteras, övergår den virtuella datorn till ett underhålls tillstånd och inaktiverar SSH för att förhindra eventuella avbrott i den pågående processen.  Kontrol lera krypterings statusen med kommandot Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) och kontrol lera fältet **ProgressMessage** . **ProgressMessage** kommer att rapportera en serie med status som data och OS-diskar är krypterade:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage** kommer fortfarande att finnas kvar i **operativ systemets disk kryptering som har startats** för de flesta krypterings processen.  När krypteringen är klar och slutförd kommer **ProgressMessage** att returnera:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

När det här meddelandet är tillgängligt förväntas den krypterade OS-enheten vara klar för användning och den virtuella datorn är redo att användas igen.

Om start informationen, förlopps meddelandet eller ett fel rapporterar att operativ system krypteringen har misslyckats i mitten av den här processen återställer du den virtuella datorn till ögonblicks bilden eller säkerhets kopian som gjorts omedelbart före krypteringen. Ett exempel på ett meddelande är fel meddelandet "Det gick inte att demontera" som beskrivs i den här hand boken.

Innan du försöker utföra kryptering igen ska du utvärdera egenskaperna för den virtuella datorn och kontrol lera att alla krav är uppfyllda.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk Encryption bakom en brand vägg

Se [disk kryptering i ett isolerat nätverk](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Felsöka krypterings status 

Portalen kan visa en disk som krypterad även efter att den har krypterats på den virtuella datorn.  Detta kan inträffa när lågnivå kommandon används för att direkt dekryptera disken inifrån den virtuella datorn, i stället för att använda Azure Disk Encryption hanterings kommandon på högre nivå.  Med kommandona på högre nivå kan du inte bara dekryptera disken inifrån den virtuella datorn, men utanför den virtuella datorn uppdaterar de också viktiga krypterings inställningar och tilläggs inställningar som är associerade med den virtuella datorn.  Om dessa inte behålls i justeringen kommer plattformen inte att kunna rapportera krypterings status eller upprätta den virtuella datorn korrekt.

Om du vill inaktivera Azure Disk Encryption med PowerShell använder du [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) följt av [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Det går inte att köra Remove-AzVMDiskEncryptionExtension innan krypteringen har inaktiverats.

Om du vill inaktivera Azure Disk Encryption med CLI använder du [inaktivera AZ VM-kryptering](/cli/azure/vm/encryption).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker problemen. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Använd disk kryptering i Azure Security Center](../../security-center/asset-inventory.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)