---
title: Azure Disk Encryption exempel skript för virtuella Windows-datorer
description: Den här artikeln är bilagan till Microsoft Azure disk kryptering för virtuella Windows-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 413553165fcf74fa4590cb4661212b885a277579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550645"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Exempelskript för Azure Disk Encryption 

Den här artikeln innehåller exempel skript för att förbereda förkrypterade virtuella hård diskar och andra uppgifter.

> [!NOTE]
> Alla skript avser den senaste, icke-AAD-versionen av ADE, förutom där det anges.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exempel på PowerShell-skript för Azure Disk Encryption 


- **Lista alla krypterade virtuella datorer i din prenumeration**

  Du kan hitta alla ADE-krypterade virtuella datorer och tilläggs versionen i alla resurs grupper som finns i en prenumeration med hjälp av [det här PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Dessa cmdletar visar också alla ADE-krypterade virtuella datorer (men inte tilläggs versionen):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Visa en lista över alla krypterade VMSS-instanser i din prenumeration**
    
    Du kan hitta alla ADE-krypterade VMSS-instanser och tilläggs versionen i alla resurs grupper som finns i en prenumeration med hjälp av [det här PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).
 
- **Lista alla disk krypterings hemligheter som används för att kryptera virtuella datorer i ett nyckel valv**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Använda PowerShell-skriptet Azure Disk Encryption krav

Om du redan är bekant med kraven för Azure Disk Encryption kan du använda [PowerShell-skriptet Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder PowerShell-skriptet finns i [kryptera en snabb start för virtuell dator](disk-encryption-powershell-quickstart.md). Du kan ta bort kommentarerna från ett avsnitt i skriptet, med början på rad 211, för att kryptera alla diskar för befintliga virtuella datorer i en befintlig resurs grupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 

|Parameter|Beskrivning|Erforderlig?|
|------|------|------|
|$resourceGroupName| Namnet på den resurs grupp som nyckel valvet tillhör.  En ny resurs grupp med det här namnet kommer att skapas om det inte finns någon.| Sant|
|$keyVaultName|Namnet på det nyckel valv där krypterings nycklar ska placeras. Ett nytt valv med det här namnet kommer att skapas om det inte finns något.| Sant|
|$location|Plats för nyckel valvet. Se till att det nyckel valv och de virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|Sant|
|$subscriptionId|Identifierare för den Azure-prenumeration som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|Sant|
|$aadAppName|Namnet på det Azure AD-program som ska användas för att skriva hemligheter till nyckel valv. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen redan finns skickar du aadClientSecret-parametern till skriptet.|Falskt|
|$aadClientSecret|Klient hemlighet för Azure AD-programmet som skapades tidigare.|Falskt|
|$keyEncryptionKeyName|Namn på valfri nyckel krypterings nyckel i nyckel valvet. En ny nyckel med det här namnet kommer att skapas om det inte finns någon.|Falskt|

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-App

- [Aktivera disk kryptering på en befintlig eller Windows-VM som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Inaktivera kryptering på en virtuell Windows-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-App (tidigare version) 
 
- [Aktivera disk kryptering på en befintlig eller Windows-VM som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Inaktivera kryptering på en virtuell Windows-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Skapa en ny krypterad hanterad disk från en förkrypterad VHD/Storage-BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterad hanterad disk som tillhandahöll en förkrypterad virtuell hård disk och dess motsvarande krypterings inställningar

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Förbereda en förkrypterad Windows-VHD
Avsnitten som följer är nödvändiga för att förbereda en förkrypterad Windows-VHD för distribution som en krypterad virtuell hård disk i Azure IaaS. Använd informationen för att förbereda och starta en ny virtuell Windows-dator (VHD) på Azure Site Recovery eller Azure. Mer information om hur du förbereder och laddar upp en virtuell hård disk finns i [överföra en generaliserad virtuell hård disk och använda den för att skapa nya virtuella datorer i Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grup princip för att tillåta icke-TPM för OS-skydd
Konfigurera BitLocker-grupprincip inställningen **BitLocker-diskkryptering**, som du hittar under dator konfiguration för **lokal dator princip**  >    >  **administrativa mallar**  >  **Windows-komponenter**. Om du ändrar den här inställningen till **operativ system enheter**  >  **krävs ytterligare autentisering vid start**  >  **Tillåt BitLocker utan en kompatibel TPM**, som visas i följande bild:

![Microsoft Antimalware i Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installera funktions komponenter i BitLocker
För Windows Server 2012 och senare använder du följande kommando:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

För Windows Server 2008 R2 använder du följande kommando:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbered operativ system volymen för BitLocker med hjälp av `bdehdcfg`
Om du vill komprimera OS-partitionen och förbereda datorn för BitLocker kör du [BdeHdCfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) vid behov:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda operativ system volymen med hjälp av BitLocker
Använd [`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) kommandot för att aktivera kryptering på Start volymen med hjälp av ett externt nyckel skydd. Placera även den externa nyckeln (. Bek-filen) på den externa enheten eller volymen. Kryptering är aktiverat på system-/start volymen efter nästa omstart.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> Förbered den virtuella datorn med en separat data-/resurs-VHD för att hämta den externa nyckeln med hjälp av BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterad virtuell hård disk till ett Azure Storage-konto
När DM-Crypt kryptering har Aktiver ATS måste den lokala krypterade virtuella hård disken överföras till ditt lagrings konto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Överför hemligheten för den förkrypterade virtuella datorn till ditt nyckel valv
Den disk krypterings hemlighet som du fick tidigare måste laddas upp som en hemlighet i ditt nyckel valv.  Detta kräver att du beviljar behörigheten Ange hemlighet och wrapkey till det konto som ska överföra hemligheterna.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk krypterings hemligheten är inte krypterad med en KEK
Använd [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)för att ställa in hemligheten i nyckel valvet. Lösen frasen kodas som en Base64-sträng och överförs sedan till nyckel valvet. Se dessutom till att följande Taggar anges när du skapar hemligheten i nyckel valvet.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd `$secretUrl` i nästa steg för att [koppla OS-disken utan att använda KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk krypterings hemlighet krypterad med en KEK
Innan du överför hemligheten till nyckel valvet kan du också kryptera den med hjälp av en nyckel krypterings nyckel. Använd wrap- [API: et](/rest/api/keyvault/wrapkey) för att först kryptera hemligheten med nyckel krypterings nyckeln. Utdata från den här figur sättningen är en Base64-kodad sträng, som du sedan kan ladda upp som en hemlighet med hjälp av [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdleten.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för att [koppla OS-disken med hjälp av KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du ansluter en OS-disk

###  <a name="without-using-a-kek"></a>Utan att använda en KEK
När du ansluter OS-disken måste du skicka `$secretUrl` . URL: en skapades i avsnittet "disk krypterings hemlighet som inte är krypterad med ett KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Använda en KEK
När du ansluter OS-disken skickar du `$KeyEncryptionKey` och `$secretUrl` . URL: en skapades i avsnittet "disk krypterings hemlighet krypterad med ett KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
