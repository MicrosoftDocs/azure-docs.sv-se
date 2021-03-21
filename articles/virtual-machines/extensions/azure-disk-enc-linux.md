---
title: Azure Disk Encryption för Linux
description: Distribuerar Azure Disk Encryption för Linux till en virtuell dator med ett tillägg för virtuell dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 7c79391e3459804a4b5ce72c2230d17af3269641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566268"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption för Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar del systemet dm-crypt i Linux för att tillhandahålla fullständig disk kryptering på [utvalda Azure Linux-distributioner](../linux/disk-encryption-overview.md).  Den här lösningen är integrerad med Azure Key Vault för att hantera disk krypterings nycklar och hemligheter.

## <a name="prerequisites"></a>Förutsättningar

En fullständig lista över krav finns i [Azure Disk Encryption för virtuella Linux-datorer](../linux/disk-encryption-overview.md), särskilt i följande avsnitt:

- [Virtuella datorer och operativ system som stöds](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare krav för virtuell dator](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Nätverkskrav](../linux/disk-encryption-overview.md#networking-requirements)
- [Lagrings krav för krypterings nyckel](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Tilläggs schema

Det finns två versioner av tilläggs schema för Azure Disk Encryption (ADE):
- v 1.1 – ett senare Rekommenderat schema som inte använder Azure Active Directory egenskaper (AAD).
- v 0,1 – ett äldre schema som kräver Azure Active Directory egenskaper (AAD). 

Om du vill välja ett mål schema `typeHandlerVersion` måste egenskapen anges till den version av schemat som du vill använda.

### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: ingen AAD (rekommenderas)

Schemat v 1.1 rekommenderas och kräver inte Azure Active Directory-egenskaper (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0,1: med AAD 

Schemat 0,1 kräver `AADClientID` och, antingen `AADClientSecret` eller `AADClientCertificate` .

Använda `AADClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Använda `AADClientCertificate` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| utgivare | Microsoft. Azure. Security | sträng |
| typ | AzureDiskEncryptionForLinux | sträng |
| typeHandlerVersion | 1,1, 0,1 | int |
| (0,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1-schema) AADClientSecret | password | sträng |
| (0,1-schema) AADClientCertificate | begäran | sträng |
| valfritt (0,1-schema) Fraser | password | sträng |
| DiskFormatQuery | {"dev_path": "", "namn": "", "file_system": ""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| (valfritt-standard-RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sträng |
| KeyVaultURL | url | sträng |
| KeyVaultResourceId | url | sträng |
| valfritt KeyEncryptionKeyURL | url | sträng |
| valfritt KekVaultResourceId | url | sträng |
| valfritt SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS, data, alla | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på mallar som distribueras baserat på schema v 1.1 finns i Azure snabb starts mal len [201-Encrypted-Linux-VM-utan-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Ett exempel på en mall distribution baserad på schema v 0,1 finns i mallen för Azure snabb start [201-kryptera-kör-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar eventuella öppna filer som behöver nås under krypterings processen. Om du vill kontrol lera förloppet använder du PowerShell-cmdleten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [VM-kryptering Visa](/cli/azure/vm/encryption#az-vm-encryption-show) CLI-kommando. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av data volymer. Krypterings tiden för data volymer är proportionell mot storlek och kvantitet för data volymerna om inte alternativet Kryptera format alla används. 
> - Det går bara att inaktivera kryptering på virtuella Linux-datorer för data volymer. Det stöds inte på data-eller OS-volymer om operativ system volymen har krypterats. 

>[!NOTE]
> Även om `VolumeType` parametern har angetts till alla, krypteras data diskar endast om de är korrekt monterade.

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Information om fel sökning finns i [fel söknings guiden för Azure Disk Encryption](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). 

Du kan också skriva en support incident för Azure. Gå till [supporten för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

* Mer information om VM-tillägg finns i [tillägg och funktioner för virtuella datorer för Linux](features-linux.md).
* Mer information om Azure Disk Encryption för Linux finns i [virtuella Linux-datorer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).