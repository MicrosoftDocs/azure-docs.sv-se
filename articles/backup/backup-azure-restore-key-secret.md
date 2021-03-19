---
title: Återställa Key Vault nyckel & hemlighet för krypterad virtuell dator
description: Lär dig hur du återställer Key Vault nyckel och hemlighet i Azure Backup med PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 456ce18f253ffa02cd6b13826a7839f18beecba7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88827094"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Återställa nyckel och hemlighet för Key Vault för krypterade virtuella datorer med Azure Backup

Den här artikeln pratar om hur du använder Azure VM backup för att utföra återställning av krypterade virtuella Azure-datorer, om nyckeln och hemligheten inte finns i nyckel valvet. De här stegen kan också användas om du vill ha en separat kopia av nyckeln (nyckel krypterings nyckel) och hemlighet (BitLocker-krypteringsnyckel) för den återställda virtuella datorn.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

* **Säkerhetskopiera krypterade virtuella datorer** – krypterade virtuella Azure-datorer har säkerhetskopierats med Azure Backup. Läs artikeln [hantera säkerhets kopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md) för information om hur du säkerhetskopierar krypterade virtuella Azure-datorer.
* **Konfigurera Azure Key Vault** – kontrol lera att nyckel valvet som nycklar och hemligheter måste återställas till redan finns. Läs artikeln [Kom igång med Azure Key Vault](../key-vault/general/overview.md) för information om hantering av nyckel valv.
* **Återställ disk** – kontrol lera att du har utlöst återställnings jobbet för att återställa diskar för krypterad virtuell dator med hjälp av [PowerShell-steg](backup-azure-vms-automation.md#restore-an-azure-vm). Detta beror på att det här jobbet genererar en JSON-fil i ditt lagrings konto som innehåller nycklar och hemligheter för att den krypterade virtuella datorn ska återställas.

## <a name="get-key-and-secret-from-azure-backup"></a>Hämta nyckel och hemlighet från Azure Backup

> [!NOTE]
> När disken har återställts för den krypterade virtuella datorn kontrollerar du att:
>
> * $details har fyllts i med Återställ disk jobb information, enligt [beskrivningen i PowerShell-steg i avsnittet återställa diskar](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Den virtuella datorn ska bara skapas från återställda diskar **när nyckeln och hemligheten har återställts till nyckel valvet**.

Fråga om de återställda disk egenskaperna för jobb informationen.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ange Azure Storage-kontexten och Återställ JSON-konfigurationsfilen som innehåller information om nyckel och hemlighet för den krypterade virtuella datorn.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Återställ nyckel

När JSON-filen har genererats i den mål Sök väg som anges ovan genererar du nyckel-BLOB-filen från JSON-filen för att återställa nyckel-cmdleten för att flytta nyckeln (KEK) tillbaka i nyckel valvet.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Återställ hemlighet

Använd den JSON-fil som genererades ovan för att hämta hemligt namn och värde och ange en hemlig cmdlet för att placera hemligheten (BEK) igen i nyckel valvet. Använd de här cmdletarna om den **virtuella datorn är krypterad med Bek och KEK**.

**Använd dessa cmdlets om din virtuella Windows-dator krypteras med hjälp av BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Använd dessa cmdlets om din virtuella Linux-dator krypteras med hjälp av BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Använd den JSON-fil som genererades ovan för att hämta hemligt namn och värde och ange en hemlig cmdlet för att placera hemligheten (BEK) igen i nyckel valvet. Använd de här cmdletarna om den **virtuella datorn är krypterad med Bek** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Värdet för $secretname kan hämtas genom att referera till utdata från $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl och använda text efter hemligheter/t. ex. URL: en för den hemliga utdata `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` och det hemliga namnet är B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Värdet för taggen DiskEncryptionKeyFileName är detsamma som det hemliga namnet.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Skapa virtuell dator från återställd disk

Om du har säkerhetskopierat en krypterad virtuell dator med hjälp av Azure VM Backup kan du använda PowerShell-cmdletarna ovan för att återställa nyckel och hemligt tillbaka till nyckel valvet. När du har återställt dem läser du artikeln [hantera säkerhets kopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) för att skapa krypterade virtuella datorer från återställd disk, nyckel och hemlighet.

## <a name="legacy-approach"></a>Äldre metod

Den metod som anges ovan fungerar för alla återställnings punkter. Den äldre metoden för att hämta nyckel-och hemlig information från återställnings punkten är dock giltig för återställnings punkter äldre än 11 juli 2017 för virtuella datorer som har krypterats med BEK och KEK. När återställningen av disk jobbet har slutförts för en krypterad virtuell dator med hjälp av [PowerShell-steg](backup-azure-vms-automation.md#restore-an-azure-vm)kontrollerar du att $RP har ett giltigt värde.

### <a name="restore-key-legacy-approach"></a>Restore Key (äldre metod)

Använd följande cmdlets för att hämta nyckel (KEK) information från återställnings punkt och Använd den för att återställa nyckel-cmdleten för att placera den i nyckel valvet igen.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret-legacy-approach"></a>Återställ hemlighet (äldre metod)

Använd följande cmdlets för att hämta information om hemlighet (BEK) från återställnings punkt och Lägg till en hemlig cmdlet för att lägga tillbaka den i nyckel valvet.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Värdet för $secretname kan hämtas genom att referera till utdata från $rp 1. KeyAndSecretDetails. SecretUrl och använder text efter hemligheter/till exempel är URL: en för den hemliga utdata `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` och det hemliga namnet B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Värdet för taggen DiskEncryptionKeyFileName är detsamma som hemligt namn.
> * Värdet för DiskEncryptionKeyEncryptionKeyURL kan hämtas från Key Vault efter att nycklarna återställts och med [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) -cmdlet
>
>

## <a name="next-steps"></a>Nästa steg

När du har återställt nyckel och hemlighet till nyckel valvet kan du läsa artikeln [hantera säkerhets kopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) för att skapa krypterade virtuella datorer från återställd disk, nyckel och hemlighet.
