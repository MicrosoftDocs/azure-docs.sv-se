---
title: Aktivera dubbel kryptering i rest – Azure CLI-hanterade diskar
description: Aktivera dubbel kryptering i vila för dina hanterade disk data med hjälp av Azure CLI.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 266ba4a6fbe0607fd09f86f5cd01addfa60252aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558329"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Använd Azure CLI för att aktivera dubbel kryptering i vila för hanterade diskar

Azure-disklagring stöder dubbel kryptering i vila för hanterade diskar. Grundläggande information om dubbel kryptering i vila och andra typer av hanterade disk krypterings typer finns i avsnittet [dubbel kryptering i vilo](../disk-encryption.md#double-encryption-at-rest) läge i vår disk krypterings artikel.

## <a name="prerequisites"></a>Förutsättningar

Installera den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

## <a name="getting-started"></a>Komma igång

1. Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Skapa en DiskEncryptionSet med encryptionType angivet som EncryptionAtRestWithPlatformAndCustomerKeys. Använd API-version **2020-05-01** i Azure Resource Manager-mallen (arm). 
    
        ```azurecli
        az deployment group create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet. 

        > [!NOTE]
        > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett fel meddelande som "det går inte att hitta Active Directory-objektet" när du kör följande kommando, väntar du några minuter och försöker igen.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande länkar innehåller exempel skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

- [Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Aktivera Kundhanterade nycklar med kryptering på Server sidan – exempel](disks-enable-customer-managed-keys-cli.md#examples)
