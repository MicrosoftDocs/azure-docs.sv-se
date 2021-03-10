---
title: Azure PowerShell – aktivera dubbel kryptering på rest-hanterade diskar
description: Aktivera dubbel kryptering i vila för dina hanterade disk data med hjälp av Azure PowerShell.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cfef3925152c0b94a59c662443cf202724dadc0a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549557"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>Använd Azure PowerShell-modulen för att aktivera dubbel kryptering i vila för hanterade diskar

Azure-disklagring stöder dubbel kryptering i vila för hanterade diskar. Grundläggande information om dubbel kryptering i vila och andra typer av hanterade disk krypterings typer finns i avsnittet [dubbel kryptering i vilo](../disk-encryption.md#double-encryption-at-rest) läge i vår disk krypterings artikel.

## <a name="prerequisites"></a>Förutsättningar

Installera den senaste [Azure PowerShell versionen](/powershell/azure/install-az-ps)och logga in på ett Azure-konto med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

## <a name="getting-started"></a>Komma igång

1. Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Skapa en DiskEncryptionSet med encryptionType angivet som EncryptionAtRestWithPlatformAndCustomerKeys. Använd API-version **2020-05-01** i Azure Resource Manager-mallen (arm). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet.

    > [!NOTE]
    > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett fel meddelande som "det går inte att hitta Active Directory-objektet" när du kör följande kommando, väntar du några minuter och försöker igen.

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande länkar innehåller exempel skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

- [Azure PowerShell – aktivera Kundhanterade nycklar med krypterings hanterade diskar på Server Sidan](disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
