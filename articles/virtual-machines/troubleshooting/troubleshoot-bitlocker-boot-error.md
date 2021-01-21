---
title: Felsöka start fel i BitLocker på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker start fel i BitLocker i en virtuell Azure-dator
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 87bf311b5199ec187c24c28a42314d9dc6787998
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633035"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker-startfel på en virtuell Azure-dator

 I den här artikeln beskrivs BitLocker-fel som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure.

## <a name="symptom"></a>Symptom

 En virtuell Windows-dator startar inte. När du kontrollerar skärm bilderna i fönstret [Boot Diagnostics](./boot-diagnostics.md) visas något av följande fel meddelanden:

- Anslut USB-drivrutinen som har BitLocker-nyckeln

- Du är låst! Ange återställnings nyckeln för att komma igång igen (tangentbordslayout: US) fel inloggnings information har angetts för många gånger, så att datorn var låst för att skydda din integritet. Hämta återställnings nyckeln genom att gå till https://windows.microsoft.com/recoverykeyfaq från en annan dator eller mobil enhet. Om du behöver det är nyckel-ID: t XXXXXXX. Du kan också återställa datorn.

- Ange lösen ordet för att låsa upp enheten [] Tryck på INSERT-tangenten för att se lösen ordet när du skriver.
- Ange återställnings nyckeln Läs in din återställnings nyckel från en USB-enhet.

## <a name="cause"></a>Orsak

Det här problemet kan inträffa om den virtuella datorn inte kan hitta filen med återställnings nyckeln för BitLocker (BEK) för att dekryptera den krypterade disken.

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Lös problemet genom att stoppa och frigöra den virtuella datorn och sedan starta den. Den här åtgärden tvingar den virtuella datorn att hämta BEK-filen från Azure Key Vault och placerar den på den krypterade disken. 

Om den här metoden inte löser problemet följer du dessa steg för att återställa BEK-filen manuellt:

1. Ta en ögonblicks bild av system disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Anslut system disken till en virtuell återställnings dator](troubleshoot-recovery-disks-portal-windows.md). Om du vill köra kommandot [manage-bde](/windows-server/administration/windows-commands/manage-bde) i steg 7 måste **BitLocker-diskkryptering** funktionen vara aktive rad i den virtuella återställnings datorn.

    När du ansluter en hanterad disk kan du få ett fel meddelande om att "innehåller krypterings inställningar och inte kan användas som en data disk". I den här situationen kör du följande skript för att försöka ansluta disken:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Det går inte att ansluta en hanterad disk till en virtuell dator som har återställts från en BLOB-avbildning.

3. När disken är ansluten gör du en fjärr skrivbords anslutning till den virtuella återställnings datorn så att du kan köra vissa Azure PowerShell skript. Kontrol lera att du har den [senaste versionen av Azure PowerShell](/powershell/azure/) installerad på den virtuella återställnings datorn.

4. Öppna en förhöjd Azure PowerShell-session (kör som administratör). Kör följande kommandon för att logga in på Azure-prenumerationen:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Kör följande skript för att kontrol lera namnet på filen BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Följande är exempel på utdata. I det här fallet antar vi att fil namnet är EF7B2F5A-50C6-4637-0001-7F599C12F85C. Bek.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Om du ser två duplicerade volymer är volymen som har den nya tidsstämpeln den aktuella BEK-filen som används av den virtuella återställnings datorn.

    Om värdet för **innehålls typen** är **figursatt Bek** går du till [KEK-scenarierna (Key Encryption Key)](#key-encryption-key-scenario).

    Nu när du har namnet på BEK-filen för enheten måste du skapa hemlighet-File-name. BEK-fil för att låsa upp enheten.

6.  Hämta filen BEK till återställnings disken. I följande exempel sparas filen BEK i mappen C:\BEK. Kontrol lera att `C:\BEK\` sökvägen finns innan du kör skripten.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Om du vill låsa upp den anslutna disken med hjälp av BEK-filen kör du följande kommando.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. kontrol lera att du använder rätt enhets beteckning. 

8. När disken har låsts upp med BEK-nyckeln kopplar du bort disken från den virtuella återställnings datorn och återskapar sedan den virtuella datorn med hjälp av den nya OS-disken.

    > [!NOTE]
    > Växling av operativ system disk stöds inte för virtuella datorer som använder disk kryptering.

9. Om den nya virtuella datorn fortfarande inte kan starta normalt kan du prova något av följande när du har låst upp enheten:

    - Pausa skyddet för att tillfälligt stänga av BitLocker genom att köra följande:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Dekryptera enheten helt. Gör detta genom att köra följande kommando:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Nyckel krypterings nyckel scenario

Följ dessa steg för ett scenario med nyckel krypterings nyckel:

1. Se till att det inloggade användar kontot kräver behörigheten "unwrap" i Key Vault åtkomst principer i **användaren | Nyckel behörigheter | Kryptografiska åtgärder | Unwrap-nyckel**.
2. Spara följande skript till en. PS1-fil:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Ange parametrarna. Skriptet bearbetar KEK-hemligheten för att skapa BEK-nyckeln och sparar den sedan i en lokal mapp på den virtuella återställnings datorn. Om du får fel meddelanden när du kör skriptet, se avsnittet [skript fel sökning](#script-troubleshooting) .

4. Följande utdata visas när skriptet börjar:

    Versions plats för GAC                                                                              
    ---    -------        --------                                                                              
    Falsk v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  Falsk v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    När skriptet har slutförts visas följande utdata:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Om du vill låsa upp den anslutna disken med hjälp av BEK-filen kör du följande kommando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    I det här exemplet är den anslutna OS-disken enhet F. kontrol lera att du använder rätt enhets beteckning. 

6. När disken har låsts upp med BEK-nyckeln kopplar du bort disken från den virtuella återställnings datorn och återskapar sedan den virtuella datorn med hjälp av den nya OS-disken. 

    > [!NOTE]
    > Växling av operativ system disk stöds inte för virtuella datorer som använder disk kryptering.

7. Om den nya virtuella datorn fortfarande inte kan starta normalt kan du prova något av följande när du har låst upp enheten:

    - Pausa skyddet för att tillfälligt stänga av BitLocker genom att köra följande kommando:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Dekryptera enheten helt. Gör detta genom att köra följande kommando:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Skript fel sökning

**Fel: det gick inte att läsa in filen eller sammansättningen**

Felet beror på att Sök vägarna för ADAL-sammansättningarna är felaktiga. Du kan söka efter `Az.Accounts` en mapp för att hitta rätt sökväg.

**Fel: Get-AzKeyVaultSecret eller Get-AzKeyVaultSecret känns inte igen som namnet på en cmdlet**

Om du använder den gamla AZ PowerShell-modulen måste du ändra de två kommandona till `Get-AzureKeyVaultSecret` och `Get-AzureKeyVaultSecret` .

**Parameter exempel**

| Parametrar  | Värde exempel  |Kommentarer   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Namnet på nyckel valvet som lagrar nyckeln |
|$kekName   |mykey   | Namnet på den nyckel som används för att kryptera den virtuella datorn|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Namnet på hemligheten för den virtuella dator nyckeln|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |Sökvägen för att skriva BEK-filen.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN eller GUID för Azure Active Directory som är värd för nyckel valvet |
