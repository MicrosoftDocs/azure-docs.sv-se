---
title: Azure Disk Encryption för Linux
description: Distribuerar Azure Disk Encryption Linux till en virtuell dator med hjälp av ett tillägg för virtuell dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792389"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption för Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Översikt

Azure Disk Encryption använder undersystemet dm-crypt i Linux för att tillhandahålla fullständig diskkryptering på utvalda [Azure Linux-distributioner.](../linux/disk-encryption-overview.md)  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklar och hemligheter.

## <a name="prerequisites"></a>Förutsättningar

En fullständig lista över förhandskrav finns i [Azure Disk Encryption virtuella Linux-datorer,](../linux/disk-encryption-overview.md)särskilt följande avsnitt:

- [Virtuella datorer och operativsystem som stöds](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare krav för virtuella datorer](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Nätverkskrav](../linux/disk-encryption-overview.md#networking-requirements)
- [Lagringskrav för krypteringsnyckel](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Tilläggsschema

Det finns två versioner av tilläggsschemat för Azure Disk Encryption (ADE):
- v1.1 – Ett nyare rekommenderat schema som inte använder Azure Active Directory (AAD).
- v0.1 – Ett äldre schema som kräver Azure Active Directory (AAD). 

Om du vill välja ett `typeHandlerVersion` målschema måste egenskapen anges som den version av schemat som du vill använda.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Ingen AAD (rekommenderas)

Schemat v1.1 rekommenderas och kräver inte Azure Active Directory (AAD).

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


### <a name="schema-v01-with-aad"></a>Schema v0.1: med AAD 

0.1-schemat kräver `AADClientID` och antingen `AADClientSecret` eller `AADClientCertificate` .

Med hjälp av `AADClientSecret` :

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

Med hjälp av `AADClientCertificate` :

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


### <a name="property-values"></a>Egenskapsvärden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| utgivare | Microsoft.Azure.Security | sträng |
| typ | AzureDiskEncryptionForLinux | sträng |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1-schema) AADClientSecret | password | sträng |
| (0.1-schema) AADClientCertificate | Stämpel | sträng |
| (valfritt) (0.1-schema) Lösenfras | password | sträng |
| DiskFormatQuery | {"dev_path":","name":"","file_system":""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| (valfritt – RSA-OAEP som standard) KeyEncryptionAlgorithm | RSA-OAEP, RSA-OAEP-256, RSA1_5 | sträng |
| KeyVaultURL | url | sträng |
| KeyVaultResourceId | url | sträng |
| (valfritt) KeyEncryptionKeyURL | url | sträng |
| (valfritt) KekVaultResourceId | url | sträng |
| (valfritt) SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS, Data, Alla | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på malldistribution baserat på schema v1.1 finns i Azure-snabbstartsmallen [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Ett exempel på malldistribution baserat på schema v0.1 finns i Azure-snabbstartsmallen [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar alla öppna filer som behöver nås under krypteringsprocessen. Om du vill kontrollera förloppet använder du [PowerShell-cmdleten Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [kommandot vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI. Den här processen kan förväntas ta några timmar för en os-volym på 30 GB, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolymerna är proportionell mot storleken och kvantiteten för datavolymerna, såvida inte alternativet kryptera formatera alla används. 
> - Inaktivering av kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

>[!NOTE]
> Om `VolumeType` parametern dessutom är inställd på Alla krypteras datadiskar endast om de är korrekt monterade.

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Information om felsökning finns i [Azure Disk Encryption felsökningsguiden](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure](https://azure.microsoft.com/support/community/)och Stack Overflow forumen . 

Du kan också skapa en Azure-supportincident. Gå till [Azure-support](https://azure.microsoft.com/support/options/) och välj Få support. Information om hur du använder Azure Support finns i vanliga [Microsoft Azure support .](https://azure.microsoft.com/support/faq/)

## <a name="next-steps"></a>Nästa steg

* Mer information om VM-tillägg finns i [Tillägg och funktioner för virtuella datorer för Linux.](features-linux.md)
* Mer information om hur Azure Disk Encryption Linux finns i [Virtuella Linux-datorer.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)