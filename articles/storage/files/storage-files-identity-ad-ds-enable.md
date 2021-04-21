---
title: Aktivera AD DS-autentisering till Azure-filresurser
description: Lär dig hur du aktiverar Active Directory Domain Services-autentisering via SMB för Azure-filresurser. Dina domän-ansluten virtuella Windows-datorer kan sedan komma åt Azure-filresurser med hjälp av AD DS-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: f38911b1fffb083902ba67e262141b6780a43ada
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817854"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Del ett: Aktivera AD DS-autentisering för dina Azure-filresurser 

Innan du aktiverar Active Directory Domain Services (AD DS)-autentisering bör du [](storage-files-identity-auth-active-directory-enable.md) läsa översiktsartikeln för att förstå de scenarier och krav som stöds.

I den här artikeln beskrivs den process som krävs för att Active Directory Domain Services autentisering (AD DS) på ditt lagringskonto. När du har aktivera funktionen måste du konfigurera ditt lagringskonto och din AD DS för att använda AD DS-autentiseringsuppgifter för autentisering till din Azure-filresurs. Om du vill aktivera AD DS-autentisering via SMB för Azure-filresurser måste du registrera ditt lagringskonto med AD DS och sedan ange de domänegenskaper som krävs för lagringskontot.

Om du vill registrera ditt lagringskonto med AD DS skapar du ett konto som representerar det i din AD DS. Du kan tänka på den här processen som om det vore som att skapa ett konto som representerar en lokal Windows-filserver i AD DS. När funktionen är aktiverad på lagringskontot gäller den för alla nya och befintliga filresurser i kontot.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Alternativ ett (rekommenderas): Använd AzFilesHybrid PowerShell-modulen

Cmdletarna i PowerShell-modulen AzFilesHybrid gör nödvändiga ändringar och aktiverar funktionen åt dig. Eftersom vissa delar av cmdletarna interagerar med din lokala AD DS förklarar vi vad cmdletarna gör, så att du kan avgöra om ändringarna överensstämmer med dina efterlevnads- och säkerhetsprinciper och se till att du har rätt behörigheter för att köra cmdletarna. Vi rekommenderar att du använder Modulen AzFilesHybrid, men om du inte kan göra det, tillhandahåller vi stegen så att du kan utföra dem manuellt.

### <a name="download-azfileshybrid-module"></a>Ladda ned AzFilesHybrid-modulen

- [Ladda ned och packa upp Modulen AzFilesHybrid (GA-modul: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) Observera att AES 256 Kerberos-kryptering stöds på v0.2.2 eller högre. Om du har aktiverat funktionen med en AzFilesHybrid-version som är lägre än v0.2.2 och vill uppdatera för att stödja AES 256 Kerberos-kryptering kan du läsa den här [artikeln.](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption) 
- Installera och kör modulen på en enhet som är domän-ansluten till lokal AD DS med AD DS-autentiseringsuppgifter som har behörighet att skapa ett konto för tjänstinloggning eller ett datorkonto i mål-AD.
-  Kör skriptet med hjälp av en lokal AD DS-autentiseringsidentifiering som synkroniseras till din Azure AD. Lokala AD DS-autentiseringsuppgifter måste ha antingen lagringskontots ägare eller Azure-rollen deltagare.

### <a name="run-join-azstorageaccountforauth"></a>Kör Join-AzStorageAccountForAuth

`Join-AzStorageAccountForAuth`Cmdleten utför motsvarigheten till en domänkoppling offline för det angivna lagringskontots räkning. Skriptet använder cmdleten för att skapa ett [datorkonto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) i AD-domänen. Om du av någon anledning inte kan använda ett datorkonto kan du ändra skriptet för att skapa ett [inloggningskonto för tjänsten i](/windows/win32/ad/about-service-logon-accounts) stället. Om du väljer att köra kommandot manuellt bör du välja det konto som passar bäst för din miljö.

AD DS-kontot som skapas av cmdleten representerar lagringskontot. Om AD DS-kontot skapas under en organisationsenhet (OU) som framtvingar att lösenordet upphör att gälla måste du uppdatera lösenordet före den högsta lösenordsåldern. Om kontolösenordet inte uppdateras före det datumet resulterar det i autentiseringsfel vid åtkomst till Azure-filresurser. Information om hur du uppdaterar lösenordet finns i Uppdatera [AD DS-kontolösenordet](storage-files-identity-ad-ds-update-password.md).

Ersätt platshållarvärdena med dina egna i parametrarna nedan innan du kör dem i PowerShell.
> [!IMPORTANT]
> Cmdleten för domänkoppling skapar ett AD-konto som representerar lagringskontot (filresursen) i AD. Du kan välja att registrera dig som ett datorkonto eller ett tjänstinloggningskonto. Mer information [finns i Vanliga](./storage-files-faq.md#security-authentication-and-access-control) frågor och svar. För datorkonton finns en förfalloålder för lösenord som är inställd på 30 dagar i AD. På samma sätt kan tjänstinloggningskontot ha en förfalloålder för standardlösenord som angetts för AD-domänen eller organisationsenheten (OU).
> För båda kontotyperna rekommenderar vi att du kontrollerar den förfalloålder för lösenord som konfigurerats i AD-miljön och planerar att uppdatera lösenordet för din lagringskontoidentitet för AD-kontot före den högsta lösenordsåldern. [](storage-files-identity-ad-ds-update-password.md) Du kan överväga att skapa en ny AD-organisationsenhet [(OU)](/powershell/module/addsadministration/new-adorganizationalunit) i AD och inaktivera förfalloprincip för lösenord på datorkonton eller tjänstinloggningskonton på motsvarande sätt. [](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

# Select the target subscription for the current session
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

## <a name="option-2-manually-perform-the-enablement-actions"></a>Alternativ 2: Utför aktivera-åtgärderna manuellt

Om du redan har kört `Join-AzStorageAccountForAuth` skriptet ovan går du till avsnittet Bekräfta att funktionen [är](#confirm-the-feature-is-enabled) aktiverad. Du behöver inte utföra följande manuella steg.

### <a name="checking-environment"></a>Kontrollera miljön

Först måste du kontrollera tillståndet för din miljö. Mer specifikt måste du kontrollera [om Active Directory PowerShell](/powershell/module/addsadministration/) är installerat och om gränssnittet körs med administratörsbehörighet. Kontrollera sedan för att se om [modulen Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) är installerad, i annat fall installerar du den. När du har slutfört kontrollerna kontrollerar du din AD [](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) DS för att se om det finns ett datorkonto (standard) eller ett tjänstinloggningskonto som redan har skapats med SPN/UPN som "cifs/your-storage-account-name-here.file.core.windows.net". [](/windows/win32/ad/about-service-logon-accounts) Om kontot inte finns skapar du ett enligt beskrivningen i följande avsnitt.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Skapa en identitet som representerar lagringskontot i ad manuellt

Om du vill skapa det här kontot manuellt skapar du en ny Kerberos-nyckel för ditt lagringskonto. Använd sedan Kerberos-nyckeln som lösenord för ditt konto med PowerShell-cmdletarna nedan. Den här nyckeln används endast under installationen och kan inte användas för kontroll- eller dataplansåtgärder mot lagringskontot. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

När du har nyckeln skapar du antingen ett tjänst- eller datorkonto under din organisationsenhet. Använd följande specifikation (kom ihåg att ersätta exempeltexten med namnet på ditt lagringskonto):

SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Lösenord: Kerberos-nyckel för ditt lagringskonto.

Om din organisationsenhet kräver att lösenordet upphör att gälla måste du uppdatera lösenordet före den högsta lösenordsåldern för att förhindra autentiseringsfel vid åtkomst till Azure-filresurser. Mer [information finns i Uppdatera lösenordet för din lagringskontoidentitet](storage-files-identity-ad-ds-update-password.md) i AD.

Behåll SID för den nyligen skapade identiteten. Du behöver det för nästa steg. Den identitet som du har skapat som representerar lagringskontot behöver inte synkroniseras med Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Aktivera funktionen på ditt lagringskonto

Nu kan du aktivera funktionen på ditt lagringskonto. Ange viss konfigurationsinformation för domänegenskaperna i följande kommando och kör den sedan. Lagringskontots SID som krävs i följande kommando är SID för den identitet som du skapade i AD DS [i föregående avsnitt.](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)

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

Du kan köra Debug-AzStorageAccountAuth cmdlet för att utföra en uppsättning grundläggande kontroller i AD-konfigurationen med den inloggade AD-användaren. Den här cmdleten stöds i versionen AzFilesHybrid v0.1.2+. Mer information om de kontroller som utförs i den här cmdleten finns i [Unable to mount Azure Files with AD credentials](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) (Det går inte att montera med AD-autentiseringsuppgifter) i felsökningsguiden för Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Bekräfta att funktionen är aktiverad

Du kan kontrollera om funktionen är aktiverad på ditt lagringskonto med följande skript:

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

Nu har du aktiverat funktionen på ditt lagringskonto. Om du vill använda funktionen måste du tilldela behörigheter på resursnivå. Fortsätt till nästa avsnitt.

[Del två: Tilldela behörigheter på resursnivå till en identitet](storage-files-identity-ad-ds-assign-permissions.md)