---
title: Azure Disk Encryption för Windows
description: Distribuerar Azure Disk Encryption till en virtuell Windows-dator med ett tillägg för virtuell dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 10268f8041f21f74e8ebcfaee41d207a53618260
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566251"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption för Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar BitLocker för att tillhandahålla fullständig disk kryptering på virtuella Azure-datorer som kör Windows.  Den här lösningen är integrerad med Azure Key Vault för att hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. 

## <a name="prerequisites"></a>Förutsättningar

En fullständig lista över krav finns i [Azure Disk Encryption för virtuella Windows-datorer](../windows/disk-encryption-overview.md), särskilt i följande avsnitt:

- [Virtuella datorer och operativ system som stöds](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverkskrav](../windows/disk-encryption-overview.md#networking-requirements)
- [grupprincip krav](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Tilläggs schema

Det finns två versioner av tilläggs schema för Azure Disk Encryption (ADE):
- v 2.2 – ett senare Rekommenderat schema som inte använder Azure Active Directory egenskaper (AAD).
- v 1.1 – ett äldre schema som kräver Azure Active Directory egenskaper (AAD). 

Om du vill välja ett mål schema `typeHandlerVersion` måste egenskapen anges till den version av schemat som du vill använda.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: ingen AAD (rekommenderas)

V 2.2-schemat rekommenderas för alla nya virtuella datorer och kräver inte Azure Active Directory egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v 1.1: med AAD 

Schemat för 1,1 måste vara `aadClientID` `aadClientSecret` eller `AADClientCertificate` och rekommenderas inte för nya virtuella datorer.

Använda `aadClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
| typ | AzureDiskEncryption | sträng |
| typeHandlerVersion | 2,2, 1,1 | sträng |
| (1,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1,1-schema) AADClientSecret | password | sträng |
| (1,1-schema) AADClientCertificate | begäran | sträng |
| EncryptionOperation | EnableEncryption | sträng | 
| (valfritt-standard-RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sträng |
| KeyVaultURL | url | sträng |
| KeyVaultResourceId | url | sträng |
| valfritt KeyEncryptionKeyURL | url | sträng |
| valfritt KekVaultResourceId | url | sträng |
| valfritt SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS, data, alla | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på en mall distribution baserad på schema v 2.2 finns i Azure snabb starts mal len [201-Encryption-Running-Windows-VM-utan-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Ett exempel på en mall distribution baserad på schema v 1.1 finns i Azure snabb starts mal len [201-Encryption-Running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Även om `VolumeType` parametern har angetts till alla, krypteras data diskar endast om de är korrekt formaterade. 

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Information om fel sökning finns i [fel söknings guiden för Azure Disk Encryption](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). 

Du kan också skriva en support incident för Azure. Gå till [supporten för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

* Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Windows](features-windows.md).
* Mer information om Azure Disk Encryption för Windows finns i [virtuella Windows-datorer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
