---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 5c17c1b7fed50c311d356aadcb7ca2837cc20abd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92741751"
---
1. Kontrol lera att du har installerat den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och loggat till ett Azure-konto i med [AZ-inloggning](/cli/azure/reference-index).

1. Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.

    > [!IMPORTANT]
    > Kamel notation inte, om du gör det kan det uppstå problem när du tilldelar ytterligare diskar till resursen i Azure Portal.

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

1.    Skapa en instans av en DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet. 

        > [!NOTE]
        > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett fel meddelande som "det går inte att hitta Active Directory-objektet" när du kör följande kommando, väntar du några minuter och försöker igen.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
