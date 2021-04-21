---
title: Köra Azure Automation runbooks på en Hybrid Runbook Worker
description: Den här artikeln beskriver hur du kör runbooks på datorer i ditt lokala datacenter eller en annan molnleverantör med Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6501fd26a5c7966c4cde596df40346b106c57cce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834794"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Köra runbook-flöden på Hybrid Runbook Worker

Runbooks som körs [på en Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) hanterar vanligtvis resurser på den lokala datorn eller mot resurser i den lokala miljön där arbetaren distribueras. Runbooks i Azure Automation vanligtvis hantera resurser i Azure-molnet. Även om de används på olika sätt är runbooks som körs i Azure Automation och runbooks som körs på en Hybrid Runbook Worker identiska i strukturen.

När du skapar en runbook som ska köras på en Hybrid Runbook Worker bör du redigera och testa runbooken på den dator som är värd för arbetsrollen. Värddatorn har alla PowerShell-moduler och nätverksåtkomst som krävs för att hantera de lokala resurserna. När du testar runbooken på Hybrid Runbook Worker-datorn kan du ladda upp den till Azure Automation-miljön, där den kan köras på arbetsdatorn.

## <a name="plan-runbook-job-behavior"></a>Planera runbook-jobbbeteende

Azure Automation hanterar jobb på Hybrid Runbook Workers på ett annat sätt än jobb som körs i Azure-sandbox-miljöerna. Om du har en långvarig runbook kontrollerar du att den är motståndskraftig mot eventuell omstart. Mer information om jobbbeteendet finns i [Hybrid Runbook Worker jobb](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Jobb för Hybrid Runbook Workers körs under det lokala **systemkontot** i Windows eller **nxautomation-kontot** i Linux. För Linux kontrollerar du att **nxautomation-kontot** har åtkomst till den plats där Runbook-modulerna lagras. När du använder cmdleten [Install-Module](/powershell/module/powershellget/install-module) måste du ange AllUsers för parametern för att säkerställa att `Scope` **nxautomation-kontot** har åtkomst. Mer information om PowerShell på Linux finns i [Kända problem för PowerShell på icke-Windows-plattformar.](/powershell/scripting/whats-new/what-s-new-in-powershell-70)

## <a name="configure-runbook-permissions"></a>Konfigurera runbook-behörigheter

Definiera behörigheter för din runbook att köras på Hybrid Runbook Worker på följande sätt:

* Be runbooken att tillhandahålla sin egen autentisering till lokala resurser.
* Konfigurera autentisering med [hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
* Ange ett Kör som-konto för att tillhandahålla en användarkontext för alla runbooks.

### <a name="use-runbook-authentication-to-local-resources"></a>Använda runbook-autentisering till lokala resurser

Om du förbereder en runbook som tillhandahåller en egen autentisering för resurser använder [du autentiseringsuppgifter](./shared-resources/credentials.md) [och certifikattillgångar](./shared-resources/certificates.md) i din runbook. Det finns flera cmdlets som gör att du kan ange autentiseringsuppgifter så att runbooken kan autentisera till olika resurser. I följande exempel visas en del av en runbook som startar om en dator. Den hämtar autentiseringsuppgifter från en autentiseringstillgång och namnet på datorn från en variabeltillgång och använder sedan dessa värden med `Restart-Computer` cmdleten .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda en [InlineScript-aktivitet.](automation-powershell-workflow.md#use-inlinescript) `InlineScript` gör att du kan köra kodblock på en annan dator med autentiseringsuppgifter.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Använda runbook-autentisering med hanterade identiteter

Hybrid Runbook Workers på virtuella Azure-datorer kan använda hanterade identiteter för att autentisera till Azure-resurser. Användning av hanterade identiteter för Azure-resurser i stället för Kör som-konton ger fördelar eftersom du inte behöver:

* Exportera Kör som-certifikatet och importera det sedan till Hybrid Runbook Worker.
* Förnya certifikatet som används av Kör som-kontot.
* Hantera Kör som-anslutningsobjektet i din Runbook-kod.

Följ nästa steg för att använda en hanterad identitet för Azure-resurser på en Hybrid Runbook Worker:

1. Skapa en virtuell Azure-dator.
1. Konfigurera hanterade identiteter för Azure-resurser på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Ge den virtuella datorn åtkomst till en resursgrupp i Resource Manager. Se Använda en [system tilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Installera Hybrid Runbook Worker den virtuella datorn. Se [Distribuera en Windows-Hybrid Runbook Worker](automation-windows-hrw-install.md) eller [Distribuera en Linux-Hybrid Runbook Worker](automation-linux-hrw-install.md).
1. Uppdatera runbooken så att den använder [cmdleten Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) med `Identity` parametern för att autentisera till Azure-resurser. Den här konfigurationen minskar behovet av att använda ett Kör som-konto och utföra den associerade kontohanteringen.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` fungerar för en Hybrid Runbook Worker med hjälp av en system-tilldelad identitet och en enskild användar-tilldelad identitet. Om du använder flera användar tilldelade identiteter på Hybrid Runbook Worker måste din runbook ange parametern för för att välja `AccountId` `Connect-AzAccount` en specifik användar-tilldelad identitet.

### <a name="use-runbook-authentication-with-run-as-account"></a>Använda Runbook-autentisering med Kör som-konto

I stället för att din runbook tillhandahåller sin egen autentisering till lokala resurser kan du ange ett Kör som-konto för en Hybrid Runbook Worker grupp. Om du vill ange ett Kör som-konto måste du definiera [en autentiseringstillgång](./shared-resources/credentials.md) som har åtkomst till lokala resurser. Dessa resurser omfattar certifikatarkiv och alla runbooks som körs under dessa autentiseringsuppgifter på Hybrid Runbook Worker i gruppen.

- Användarnamnet för autentiseringssuppgifter måste ha något av följande format:

   * Domän\användarnamn
   * username@domain
   * användarnamn (för konton som är lokala för den lokala datorn)

- Om du vill använda PowerShell Runbook **Export-RunAsCertificateToHybridWorker** måste du installera Az-modulerna för Azure Automation på den lokala datorn.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Använda en autentiseringstillgång för att ange ett Kör som-konto

Använd följande procedur för att ange ett Kör som-konto för en Hybrid Runbook Worker grupp:

1. Skapa en [autentiseringstillgång med](./shared-resources/credentials.md) åtkomst till lokala resurser.
1. Öppna Automation-kontot i Azure Portal.
1. Välj **Hybrid Worker Grupper** och välj sedan den specifika gruppen.
1. Välj **Alla inställningar** följt av Hybrid **Worker-gruppinställningar.**
1. Ändra värdet för **Kör som från** Standard **till** **Anpassad.**
1. Välj autentiseringssuppgifter och klicka på **Spara.**

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installera Kör som-kontocertifikat

Som en del av den automatiserade byggprocessen för att distribuera resurser i Azure kan du behöva åtkomst till lokala system för att stödja en uppgift eller en uppsättning steg i distributionssekvensen. Om du vill tillhandahålla autentisering mot Azure med hjälp av Kör som-kontot måste du installera Kör som-kontocertifikatet.

>[!NOTE]
>Den här PowerShell-runbooken körs för närvarande inte på Linux-datorer. Den körs bara på Windows-datorer.
>

Följande PowerShell-runbook, som kallas **Export-RunAsCertificateToHybridWorker,** exporterar Kör som-certifikatet från ditt Azure Automation konto. Runbooken laddar ned och importerar certifikatet till den lokala datorns certifikatarkiv på en Hybrid Runbook Worker som är ansluten till samma konto. När den har slutfört det steget verifierar runbooken att arbetsrollen kan autentisera till Azure med hjälp av Kör som-kontot.

>[!NOTE]
>Den här PowerShell-runbooken är inte utformad eller avsedd att köras utanför ditt Automation-konto som ett skript på måldatorn.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Om du inte ser när du söker i biblioteksobjekten kan du använda `Connect-AzAccount` `Add-AzAccount` eller uppdatera modulerna i ditt Automation-konto.

Slutför förberedelsen av Kör som-kontot:

1. Spara **runbooken Export-RunAsCertificateToHybridWorker** på datorn med filnamnstillägget **.ps1.**
1. Importera den till ditt Automation-konto.
1. Redigera runbooken och ändra värdet för `Password` variabeln till ditt eget lösenord.
1. Publicera runbooken.
1. Kör runbooken med den Hybrid Runbook Worker som kör och autentiserar runbooks med hjälp av Kör som-kontot. 
1. Granska jobbströmmen för att se att den rapporterar försöket att importera certifikatet till det lokala datorarkivet, följt av flera rader. Det här beteendet beror på hur många Automation-konton du definierar i din prenumeration och hur framgångsrik autentiseringen är.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Arbeta med signerade runbooks på en Windows-Hybrid Runbook Worker

Du kan konfigurera en Windows-Hybrid Runbook Worker att endast köra signerade runbooks.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker att endast köra signerade runbooks kan osignerade runbooks inte köras på arbetsrollen.

### <a name="create-signing-certificate"></a>Skapa signeringscertifikat

I följande exempel skapas ett självsignerat certifikat som kan användas för signering av runbooks. Den här koden skapar certifikatet och exporterar det så att Hybrid Runbook Worker kan importera det senare. Tumavtrycket returneras också för senare användning vid referens av certifikatet.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importera certifikat och konfigurera arbetare för signaturvalidering

Kopiera certifikatet som du har skapat till varje Hybrid Runbook Worker i en grupp. Kör följande skript för att importera certifikatet och konfigurera arbetarna så att de använder signaturvalidering på runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signera dina runbooks med hjälp av certifikatet

När Hybrid Runbook Workers har konfigurerats för att endast använda signerade runbooks måste du signera runbooks som ska användas på Hybrid Runbook Worker. Använd följande PowerShell-exempelkod för att signera dessa runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

När en runbook har signerats måste du importera den till ditt Automation-konto och publicera den med signaturblocket. Information om hur du importerar runbooks finns i [Importera en runbook.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Arbeta med signerade runbooks på en Linux-Hybrid Runbook Worker

För att kunna arbeta med signerade runbooks måste en Linux-Hybrid Runbook Worker ha [GPG-körbar](https://gnupg.org/index.html) fil på den lokala datorn.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker att endast köra signerade runbooks kan osignerade runbooks inte köras på arbetsrollen.

Du utför följande steg för att slutföra den här konfigurationen:

* Skapa en GPG-nyckelring och ett nyckelpar
* Gör nyckelringen tillgänglig för Hybrid Runbook Worker
* Kontrollera att signaturverifiering är på
* Signera en runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Skapa en GPG-nyckelring och ett nyckelpar

Om du vill skapa GPG-nyckelringen och nyckelparet använder du Hybrid Runbook Worker [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Använd sudo-programmet för att logga in som **nxautomation-konto.**

    ```bash
    sudo su - nxautomation
    ```

1. När du använder **nxautomation** genererar du GPG-nyckelparet. GPG vägleder dig genom stegen. Du måste ange namn, e-postadress, förfallotid och lösenfras. Sedan väntar du tills det finns tillräckligt med entropi på datorn för att nyckeln ska genereras.

    ```bash
    sudo gpg --generate-key
    ```

1. Eftersom GPG-katalogen genererades med sudo måste du ändra dess ägare till **nxautomation** med hjälp av följande kommando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Gör nyckelringen tillgänglig för Hybrid Runbook Worker

När nyckelringen har skapats gör du den tillgänglig för Hybrid Runbook Worker. Ändra inställningsfilen **home/nxautomation/state/worker.conf** så att den innehåller följande exempelkod under filavsnittet `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Kontrollera att signaturverifiering är på

Om signaturverifiering har inaktiverats på datorn måste du aktivera den genom att köra följande sudo-kommando. Ersätt `<LogAnalyticsworkspaceId>` med ditt arbetsyte-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signera en runbook

När du har konfigurerat signaturverifieringen använder du följande GPG-kommando för att signera runbooken.

```bash
gpg --clear-sign <runbook name>
```

Den signerade runbooken heter **<runbook name> .asc**.

Du kan nu ladda upp den signerade runbooken till Azure Automation köra den som en vanlig runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Starta en runbook på en Hybrid Runbook Worker

[Starta en runbook i Azure Automation](start-runbooks.md) beskriver olika metoder för att starta en runbook. När du startar en runbook på Hybrid Runbook Worker använder du alternativet **Kör** på som gör att du kan ange namnet på en Hybrid Runbook Worker grupp. När en grupp anges hämtar och kör en av arbetarna i gruppen runbooken. Om din runbook inte anger det här alternativet Azure Automation kör runbooken som vanligt.

När du startar en runbook i Azure Portal visas alternativet **Kör på** som du kan välja **Azure** eller **Hybrid Worker**. Om du **Hybrid Worker** väljer kan du välja Hybrid Runbook Worker grupp i en listrutan.

När du startar en runbook med PowerShell använder du `RunOn` parametern med cmdleten [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) I följande exempel används Windows PowerShell att starta en runbook med namnet **Test-Runbook** på en Hybrid Runbook Worker med namnet MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Loggning

För att felsöka problem med runbooks som körs på en Hybrid Runbook Worker lagras loggarna lokalt på följande plats:

* I Windows på för `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` detaljerad jobbkörningsprocessloggning. Statushändelser för runbook-jobb på hög nivå skrivs till program- **och tjänstloggarna\Microsoft-Automation\Operations-händelseloggen.**

* I Linux finns användarens Hybrid Worker-loggar på `/home/nxautomation/run/worker.log` , och system-runbook worker-loggar finns på `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Nästa steg

* Om dina runbooks inte slutförs korrekt kan du läsa felsökningsguiden för [runbook-körningsfel.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Mer information om PowerShell, inklusive språkreferenser och inlärningsmoduler, finns i [PowerShell Docs](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
