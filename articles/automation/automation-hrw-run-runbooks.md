---
title: Köra Azure Automation runbooks på en Hybrid Runbook Worker
description: Den här artikeln beskriver hur du kör Runbooks på datorer i ditt lokala data Center eller någon annan moln leverantör med Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2021
ms.topic: conceptual
ms.openlocfilehash: 8ea668ab2266a1deae108542687c89f3a221568e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220981"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Köra runbook-flöden på Hybrid Runbook Worker

Runbooks som körs på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md) hanterar vanligt vis resurser på den lokala datorn eller mot resurser i den lokala miljön där arbets tagaren distribueras. Runbooks i Azure Automation hanterar vanligt vis resurser i Azure-molnet. Även om de används på olika sätt är Runbooks som körs i Azure Automation och Runbooks som körs på en Hybrid Runbook Worker identiska i strukturen.

När du skapar en Runbook som ska köras på en Hybrid Runbook Worker bör du redigera och testa runbooken på den dator som är värd för arbetaren. Värddatorn har alla PowerShell-moduler och nätverks åtkomst som krävs för att hantera de lokala resurserna. När du har testat runbooken på den Hybrid Runbook Worker datorn kan du ladda upp den till Azure Automation-miljön där den kan köras på arbets platsen.

## <a name="plan-runbook-job-behavior"></a>Planera Runbook-jobbets beteende

Azure Automation hanterar jobb i hybrid Runbook Worker på olika sätt från jobb som körs i Azure-sandbox. Om du har en långvarig Runbook ser du till att den är elastisk och kan startas om. Mer information om jobb beteendet finns i [hybrid Runbook Worker jobb](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Jobb för Hybrid Runbook Worker körs under det lokala **system** kontot på Windows eller **nxautomation** -kontot på Linux. För Linux kontrollerar du att **nxautomation** -kontot har åtkomst till den plats där Runbook-modulerna lagras. När du använder cmdleten [install-module](/powershell/module/powershellget/install-module) måste du ange allusers för `Scope` parametern för att kontrol lera att **nxautomation** -kontot har åtkomst. Mer information om PowerShell på Linux finns i [kända problem för PowerShell på andra plattformar än Windows-plattformar](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Konfigurera Runbook-behörigheter

Definiera behörigheter för din Runbook så att de körs på Hybrid Runbook Worker på följande sätt:

* Låt runbooken tillhandahålla egen autentisering för lokala resurser.
* Konfigurera autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Ange ett Kör som-konto för att tillhandahålla en användar kontext för alla Runbooks.

### <a name="use-runbook-authentication-to-local-resources"></a>Använd Runbook-autentisering för lokala resurser

Om du förbereder en Runbook som tillhandahåller egen autentisering till resurser använder du [autentiseringsuppgifter](./shared-resources/credentials.md) och [certifikat](./shared-resources/certificates.md) till gångar i din Runbook. Det finns flera cmdletar som gör att du kan ange autentiseringsuppgifter så att runbooken kan autentiseras för olika resurser. I följande exempel visas en del av en Runbook som startar om en dator. Den hämtar autentiseringsuppgifter från en inloggnings till gång och namnet på datorn från en variabel till gång och använder sedan dessa värden med `Restart-Computer` cmdleten.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda en [InlineScript](automation-powershell-workflow.md#use-inlinescript) -aktivitet. `InlineScript` gör att du kan köra kodblock på en annan dator med autentiseringsuppgifter.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Använd Runbook-autentisering med hanterade identiteter

Hybrid Runbook Worker på virtuella Azure-datorer kan använda hanterade identiteter för att autentisera till Azure-resurser. Användning av hanterade identiteter för Azure-resurser i stället för kör som-konton ger fördelar eftersom du inte behöver:

* Exportera kör som-certifikatet och importera det sedan till Hybrid Runbook Worker.
* Förnya certifikatet som används av kör som-kontot.
* Hanterar Kör som-anslutningsobjektet i Runbook-koden.

Följ nästa steg för att använda en hanterad identitet för Azure-resurser på en Hybrid Runbook Worker:

1. Skapa en virtuell Azure-dator.
2. Konfigurera hanterade identiteter för Azure-resurser på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Ge VM-åtkomst till en resurs grupp i Resource Manager. Se [använda en Windows VM-systemtilldelad hanterad identitet för åtkomst till Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installera Hybrid Runbook Worker på den virtuella datorn. Se [distribuera en Windows hybrid Runbook Worker](automation-windows-hrw-install.md) eller [distribuera en Linux-hybrid Runbook Worker](automation-linux-hrw-install.md).
5. Uppdatera runbooken så att den använder cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) med `Identity` parametern för att autentisera till Azure-resurser. Den här konfigurationen minskar behovet av att använda ett Kör som-konto och utföra den associerade konto hanteringen.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` fungerar för en Hybrid Runbook Worker som använder en tilldelad identitet och en enda användardefinierad identitet. Om du använder flera användare tilldelade identiteter på Hybrid Runbook Worker, måste din Runbook ange `AccountId` parametern för `Connect-AzAccount` att välja en användardefinierad identitet.

### <a name="use-runbook-authentication-with-run-as-account"></a>Använd Runbook-autentisering med kör som-konto

I stället för att låta din Runbook tillhandahålla egen autentisering för lokala resurser kan du ange ett Kör som-konto för en Hybrid Runbook Worker grupp. Om du vill ange ett Kör som-konto måste du definiera en [referens till gång](./shared-resources/credentials.md) som har åtkomst till lokala resurser. Dessa resurser omfattar certifikat Arkiv och alla Runbooks som körs under dessa autentiseringsuppgifter på en Hybrid Runbook Worker i gruppen.

Användar namnet för autentiseringsuppgiften måste vara i något av följande format:

* namn
* username@domain
* användar namn (för lokala konton på den lokala datorn)

Använd följande procedur för att ange ett Kör som-konto för en Hybrid Runbook Worker grupp:

1. Skapa en [inloggnings till gång](./shared-resources/credentials.md) med till gång till lokala resurser.
2. Öppna Automation-kontot i Azure Portal.
3. Välj **hybrid Worker grupper** och välj sedan den aktuella gruppen.
4. Välj **alla inställningar**, följt av **inställningarna för Hybrid Worker-grupper**.
5. Ändra värdet för **Kör som** **från standardvärdet** till **Custom**.
6. Välj autentiseringsuppgiften och klicka på **Spara**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installera certifikat för kör som-konto

Som en del av den automatiserade Bygg processen för att distribuera resurser i Azure kan du behöva åtkomst till lokala system för att stödja en uppgift eller en uppsättning steg i din distributions ordning. Om du vill tillhandahålla autentisering mot Azure med hjälp av kör som-kontot måste du installera certifikatet för kör som-kontot.

>[!NOTE]
>Denna PowerShell-runbook körs för närvarande inte på LInux-datorer. Den körs bara på Windows-datorer.
>

Följande PowerShell-Runbook, som kallas **export-RunAsCertificateToHybridWorker**, exporterar kör som-certifikatet från ditt Azure Automation-konto. Runbooken laddar ned och importerar certifikatet till den lokala datorns certifikat Arkiv på en Hybrid Runbook Worker som är ansluten till samma konto. När det här steget har slutförts verifierar Runbook att arbets tagaren kan autentisera till Azure med hjälp av kör som-kontot.

>[!NOTE]
>PowerShell-runbooken är inte utformad eller avsedd att köras utanför ditt Automation-konto som ett skript på mål datorn.
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
>För PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för `Connect-AzAccount` . Om du inte ser `Connect-AzAccount` kan du använda `Add-AzAccount` , eller så kan du uppdatera dina moduler i ditt Automation-konto när du söker efter biblioteks objekt.

För att slutföra förberedelse av kör som-kontot:

1. Spara **export-RunAsCertificateToHybridWorker-** runbooken till datorn med tillägget **. ps1** .
2. Importera den till ditt Automation-konto.
3. Redigera runbooken och ändra värdet för `Password` variabeln till ditt eget lösen ord.
4. Publicera runbooken.
5. Kör runbooken och fokusera på Hybrid Runbook Worker grupp som kör och autentiserar Runbooks med hjälp av kör som-kontot. 
6. Granska jobb strömmen för att se att den rapporterar försöket att importera certifikatet till den lokala datorns Arkiv, följt av flera rader. Detta beteende beror på hur många Automation-konton du definierar i din prenumeration och graden av lyckade autentisering.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Arbeta med signerade Runbooks på en Windows-Hybrid Runbook Worker

Du kan konfigurera en Windows-Hybrid Runbook Worker att bara köra signerade Runbooks.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker att bara köra signerade Runbooks, kan osignerade Runbooks inte köras i arbetaren.

### <a name="create-signing-certificate"></a>Skapa signerings certifikat

I följande exempel skapas ett självsignerat certifikat som kan användas för signering av Runbooks. Den här koden skapar certifikatet och exporterar det så att Hybrid Runbook Worker kan importera det senare. Tumavtrycket returneras också för senare användning vid referenser till certifikatet.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importera certifikat och konfigurera arbetare för signaturverifiering

Kopiera certifikatet som du har skapat till varje Hybrid Runbook Worker i en grupp. Kör följande skript för att importera certifikatet och konfigurera arbets tagarna så att de använder signaturverifiering på Runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signera dina Runbooks med hjälp av certifikatet

Med hybrid Runbook Worker som kon figurer ATS att bara använda signerade Runbooks måste du signera Runbooks som ska användas på Hybrid Runbook Worker. Använd följande PowerShell-kod för att signera dessa Runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

När en Runbook har signerats måste du importera den till ditt Automation-konto och publicera den med signaturen block. Information om hur du importerar Runbooks finns i [Importera en Runbook](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Arbeta med signerade Runbooks på en Linux-Hybrid Runbook Worker

För att kunna arbeta med signerade Runbooks måste en Linux-Hybrid Runbook Worker ha den körbara filen [GPG](https://gnupg.org/index.html) på den lokala datorn.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker att bara köra signerade Runbooks, kan osignerade Runbooks inte köras i arbetaren.

Du utför följande steg för att slutföra den här konfigurationen:

* Skapa en GPG-nyckelring och nyckel par
* Gör nyckel ringen tillgänglig för Hybrid Runbook Worker
* Verifiera att signaturverifiering är aktiverat
* Signera en Runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Skapa en GPG-nyckelring och nyckel par

Om du vill skapa GPG-nyckelring och nyckel par använder du Hybrid Runbook Worker [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Använd sudo-programmet för att logga in som **nxautomation** -konto.

    ```bash
    sudo su – nxautomation
    ```

2. När du använder **nxautomation** genererar du GPG-nyckelpar. GPG vägleder dig genom stegen. Du måste ange namn, e-postadress, förfallo tid och lösen fras. Sedan väntar du tills det finns tillräckligt med entropi på datorn för att nyckeln ska genereras.

    ```bash
    sudo gpg --generate-key
    ```

3. Eftersom GPG-katalogen genererades med sudo måste du ändra dess ägare till **nxautomation** med hjälp av följande kommando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Gör nyckel ringen tillgänglig för Hybrid Runbook Worker

När nyckel ringen har skapats, gör den tillgänglig för Hybrid Runbook Worker. Ändra inställnings filen **Home/nxautomation/State/Worker. conf** för att inkludera följande exempel kod under avsnittet fil `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifiera att signaturverifiering är aktiverat

Om verifiering av signatur har inaktiverats på datorn måste du aktivera det genom att köra följande sudo-kommando. Ersätt `<LogAnalyticsworkspaceId>` med ditt arbetsyte-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signera en Runbook

När du har konfigurerat verifieringen av signaturen använder du följande GPG-kommando för att signera runbooken.

```bash
gpg –-clear-sign <runbook name>
```

Den signerade runbooken kallas **<runbook name> . asc**.

Nu kan du ladda upp den signerade runbooken till Azure Automation och köra den som en vanlig Runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Starta en Runbook på en Hybrid Runbook Worker

[Starta en Runbook i Azure Automation](start-runbooks.md) beskriver olika metoder för att starta en Runbook. Om du startar en Runbook på en Hybrid Runbook Worker används ett **Kör** som-alternativ som gör att du kan ange namnet på en hybrid Runbook Worker grupp. När en grupp anges hämtar och kör en av arbets tagarna i gruppen och kör runbooken. Om din Runbook inte anger det här alternativet Kör Azure Automation Runbook som vanligt.

När du startar en Runbook i Azure Portal visas alternativet **kör på** där du kan välja **Azure** eller **hybrid Worker**. Om du väljer **hybrid Worker** kan du välja hybrid Runbook Workers gruppen i en listruta.

När du startar en Runbook med PowerShell använder du `RunOn` parametern med cmdleten [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . I följande exempel används Windows PowerShell för att starta en Runbook med namnet **test-Runbook** på en hybrid Runbook Worker grupp med namnet MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Loggning

För att hjälpa till att felsöka problem med dina runbooks som körs på en hybrid Runbook Worker lagras loggar lokalt på följande plats:

* I Windows i `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` för detaljerad loggning av jobb körnings processer. Status händelser för Runbook-jobb på hög nivå skrivs till händelse loggen **program-och tjänst Logs\Microsoft-Automation\Operations** .

* I Linux finns användarnas hybrid Worker-loggar på `/home/nxautomation/run/worker.log` och system Runbook Worker-loggar finns på `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Nästa steg

* Om Runbooks inte slutförs kan du läsa fel söknings guiden för [körning av Runbook-fel](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokument](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
