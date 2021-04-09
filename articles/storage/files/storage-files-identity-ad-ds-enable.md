---
title: Aktivera AD DS-autentisering till Azure-filresurser
description: Lär dig hur du aktiverar Active Directory Domain Services autentisering över SMB för Azure-filresurser. Dina domänanslutna virtuella Windows-datorer kan sedan komma åt Azure-filresurser med hjälp av AD DS-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 5ee4481b3151e28d5d37760e486a43adbc194994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553229"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Del ett: Aktivera AD DS-autentisering för dina Azure-filresurser 

Innan du aktiverar autentisering med Active Directory Domain Services (AD DS) ser du till att du har läst [översikts artikeln](storage-files-identity-auth-active-directory-enable.md) för att förstå de scenarier och krav som stöds.

Den här artikeln beskriver den process som krävs för att aktivera autentisering med Active Directory Domain Services (AD DS) på ditt lagrings konto. När du har aktiverat funktionen måste du konfigurera ditt lagrings konto och AD DS för att använda AD DS-autentiseringsuppgifter för autentisering till Azure-filresursen. Om du vill aktivera AD DS-autentisering över SMB för Azure-filresurser måste du registrera ditt lagrings konto med AD DS och sedan ange de domän egenskaper som krävs för lagrings kontot.

Registrera ditt lagrings konto med AD DS genom att skapa ett konto som representerar det i AD DS. Du kan tänka på den här processen som om den var som att skapa ett konto som representerar en lokal Windows-filserver i AD DS. När funktionen är aktive rad på lagrings kontot gäller den för alla nya och befintliga fil resurser i kontot.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Alternativ ett (rekommenderas): Använd AzFilesHybrid PowerShell-modulen

Cmdletarna i AzFilesHybrid PowerShell-modulen gör nödvändiga ändringar och aktiverar funktionen åt dig. Eftersom vissa delar av cmdletarna interagerar med din lokala AD DS förklarar vi vad cmdletarna gör, så att du kan avgöra om ändringarna överensstämmer med dina efterlevnads-och säkerhets principer och se till att du har rätt behörighet för att köra cmdletarna. Vi rekommenderar att du använder AzFilesHybrid-modulen, om du inte kan göra det, så att du kan utföra dem manuellt.

### <a name="download-azfileshybrid-module"></a>Hämta AzFilesHybrid-modul

- [Ladda ned och zippa upp AzFilesHybrid-modulen (ga-modulen: v 0.2.0 +)](https://github.com/Azure-Samples/azure-files-samples/releases) Observera att AES 256 Kerberos-kryptering stöds på v-0.2.2 eller senare. Om du har aktiverat funktionen med en AzFilesHybrid-version under v 0.2.2 och vill uppdatera för att stödja AES 256 Kerberos-kryptering, se [den här artikeln](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Installera och kör modulen i en enhet som är domänansluten till lokala AD DS med AD DS-autentiseringsuppgifter som har behörighet att skapa ett tjänst inloggnings konto eller ett dator konto i mål AD.
-  Kör skriptet med en lokal AD DS-autentiseringsuppgift som synkroniseras med din Azure AD. Den lokala AD DS-autentiseringsuppgiften måste ha antingen lagrings kontots ägare eller rollen som deltagare i Azure.

### <a name="run-join-azstorageaccountforauth"></a>Kör Join-AzStorageAccountForAuth

`Join-AzStorageAccountForAuth`Cmdleten utför motsvarigheten till en frånkopplad domän anslutning åt det angivna lagrings kontot. Skriptet använder cmdleten för att skapa ett [dator konto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) i AD-domänen. Om du av någon anledning inte kan använda ett dator konto kan du ändra skriptet för att skapa ett [tjänst inloggnings konto](/windows/win32/ad/about-service-logon-accounts) i stället. Om du väljer att köra kommandot manuellt bör du välja det konto som passar bäst för din miljö.

AD DS-kontot som skapas av cmdleten representerar lagrings kontot. Om AD DS-kontot skapas under en organisationsenhet (OU) som tillämpar lösen ordets giltighets tid måste du uppdatera lösen ordet innan du får högsta ålder för lösen ord. Det gick inte att uppdatera konto lösen ordet innan det datumet resulterar i autentiseringsfel vid åtkomst till Azure-filresurser. Information om hur du uppdaterar lösen ordet finns i [Uppdatera AD DS-kontots lösen ord](storage-files-identity-ad-ds-update-password.md).

Ersätt plats hållarnas värden med dina egna i parametrarna nedan innan du kör det i PowerShell.
> [!IMPORTANT]
> Domän kopplings-cmdleten skapar ett AD-konto som representerar lagrings kontot (fil resursen) i AD. Du kan välja att registrera ett dator konto eller tjänst inloggnings konto, se [vanliga frågor och svar](./storage-files-faq.md#security-authentication-and-access-control) för mer information. För dator konton finns det ett standard ålder för lösen ord som anges i AD med 30 dagar. På samma sätt kan tjänst inloggnings kontot ha ett standard-ålder för lösen ord som angetts i AD-domänen eller organisationsenheten (OU).
> För båda konto typerna rekommenderar vi att du kontrollerar lösen ordets förfallo ålder som kon figurer ATS i din AD-miljö och planerar att [Uppdatera lösen ordet för ditt lagrings kontos identitet](storage-files-identity-ad-ds-update-password.md) för AD-kontot innan du anger den högsta tillåtna åldern för lösen ord. Du kan överväga att [skapa en ny AD-organisationsenhet (OU) i AD](/powershell/module/addsadministration/new-adorganizationalunit) och inaktivera princip för lösen ords förfallo datum på [dator konton](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) eller tjänst inloggnings konton i enlighet med detta. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Alternativ 2: utför aktiverings åtgärderna manuellt

Om du redan har kört `Join-AzStorageAccountForAuth` skriptet ovan går du till avsnittet bekräfta att [funktionen är aktive rad](#confirm-the-feature-is-enabled) . Du behöver inte utföra följande manuella steg.

### <a name="checking-environment"></a>Kontrollerar miljö

Först måste du kontrol lera status för din miljö. Mer specifikt måste du kontrol lera om [Active Directory PowerShell](/powershell/module/addsadministration/) är installerat och om gränssnittet körs med administratörs behörighet. Kontrollera sedan för att se om [modulen Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) är installerad, i annat fall installerar du den. När du har slutfört kontrollerna kontrollerar du AD DS för att se om det finns något [dator konto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller [tjänst inloggnings konto](/windows/win32/ad/about-service-logon-accounts) som redan har skapats med SPN/UPN som "CIFS/ditt-Storage-Account-name-här. File. Core. Windows. net". Om kontot inte finns skapar du ett enligt beskrivningen i följande avsnitt.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Skapa en identitet som representerar lagrings kontot i din AD manuellt

Skapa en ny Kerberos-nyckel för ditt lagrings konto för att skapa det här kontot manuellt. Använd sedan Kerberos-nyckeln som lösen ord för ditt konto med PowerShell-cmdletarna nedan. Den här nyckeln används bara under installationen och kan inte användas för alla kontroll-eller data Plans åtgärder mot lagrings kontot. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

När du har den nyckeln skapar du antingen en tjänst eller ett dator konto under din ORGANISATIONSENHET. Använd följande specifikation (kom ihåg att ersätta exempel texten med ditt lagrings konto namn):

SPN: "CIFS/ditt-Storage-Account-name-här. File. Core. Windows. net" Password: Kerberos Key för ditt lagrings konto.

Om din OU kräver att lösen ordet upphör att gälla måste du uppdatera lösen ordet innan du får den högsta åldern för lösen ord för att förhindra autentiseringsfel vid åtkomst till Azure-filresurser. Mer information finns i [Uppdatera lösen ordet för din lagrings konto identitet i AD](storage-files-identity-ad-ds-update-password.md) .

Behåll SID för den nya identiteten, du behöver den för nästa steg. Den identitet som du har skapat som representerar lagrings kontot behöver inte synkroniseras med Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Aktivera funktionen på ditt lagrings konto

Nu kan du aktivera funktionen på ditt lagrings konto. Ange en del konfigurations information för domän egenskaperna i följande kommando och kör den sedan. Det lagrings konto-SID som krävs i följande kommando är SID för identiteten som du skapade i AD DS i [föregående avsnitt](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Felsökning

Du kan köra cmdleten Debug-AzStorageAccountAuth för att utföra en uppsättning grundläggande kontroller på din AD-konfiguration med den inloggade AD-användaren. Den här cmdleten stöds i versionen AzFilesHybrid v0.1.2+. Mer information om de kontroller som utförs i denna cmdlet finns i [det går inte att montera Azure Files med AD-autentiseringsuppgifter](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) i fel söknings guiden för Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Bekräfta att funktionen är aktive rad

Du kan kontrol lera om funktionen är aktive rad på ditt lagrings konto med följande skript:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Om det lyckas bör utdata se ut så här:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Nästa steg

Du har nu aktiverat funktionen på ditt lagrings konto. Om du vill använda funktionen måste du tilldela behörigheter på resurs nivå. Fortsätt till nästa avsnitt.

[Del två: tilldela behörigheter på resurs nivå till en identitet](storage-files-identity-ad-ds-assign-permissions.md)