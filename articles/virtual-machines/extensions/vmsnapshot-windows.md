---
title: Windows-tillägg för VM-ögonblicksbild för Azure Backup
description: Ta tillämpnings programmets konsekventa säkerhets kopiering av den virtuella datorn från Azure Backup med tillägget för ögonblicks bild
services: backup, virtual-machines
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: 834c886c15e50870ec008dbb5ceab38132cc4649
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561081"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Windows-tillägg för VM-ögonblicksbild för Azure Backup

Azure Backup ger stöd för säkerhets kopiering av arbets belastningar från lokalt till molnet och säkerhets kopiering av moln resurser till Recovery Services Vault. Azure Backup använder tillägget för ögonblicks bilder av virtuella datorer för att utföra en konsekvent säkerhets kopiering av den virtuella Azure-datorn utan att behöva stänga av den virtuella datorn. VM Snapshot-tillägget publiceras och stöds av Microsoft som en del av Azure Backup-tjänsten. Azure Backup installerar tillägget som en del av den första schemalagda säkerhets kopieringen som utlöses efter aktivering av säkerhets kopiering. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för VM-tillägget för ögonblicks bilder.

VMSnapshot-tillägget visas endast i Azure Portal för icke-hanterade virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem
En lista över operativ system som stöds finns i [operativ system som stöds av Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för ögonblicks bilds tillägget för den virtuella datorn. Tillägget kräver uppgifts-ID – detta identifierar det säkerhets kopierings jobb som utlöste ögonblicks bilden på den virtuella datorn, status-uri-URI: n, där status för ögonblicks bild åtgärden skrivs, den schemalagda start tiden för ögonblicks bilden, loggar BLOB-URI: Where loggar som motsvarar ögonblicks bilds uppgiften skrivs, objstr-representation av VM-diskar och metadata.  Eftersom de här inställningarna ska behandlas som känsliga data bör de lagras i en skyddad inställnings konfiguration. Skyddade inställnings data för Azure VM-tillägg krypteras och endast dekrypteras på den virtuella mål datorn. Observera att de här inställningarna rekommenderas att endast skickas från Azure Backup-tjänsten som en del av säkerhets kopierings jobbet.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | sträng |
| commandStartTimeUTCTicks | 6.36458 e + 17 | sträng |
| locale | sv-se | sträng |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | sträng |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |



## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Det rekommenderade sättet att lägga till ett ögonblicks bilds tillägg för virtuella datorer på en virtuell dator är dock att aktivera säkerhets kopiering på den virtuella datorn. Detta kan uppnås via en Resource Manager-mall.  En exempel på en Resource Manager-mall som aktiverar säkerhets kopiering på en virtuell dator finns i [Azure Snabbstart-galleriet](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att aktivera säkerhets kopiering på en virtuell dator. Efter att aktivera säkerhets kopiering måste det första schemalagda säkerhets kopierings jobbet installera VM Snapshot-tillägget på den virtuella datorn.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utökning av utdata loggas i följande fil:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

Felsöknings information finns i [fel söknings guiden för Azure VM-säkerhetskopiering](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
