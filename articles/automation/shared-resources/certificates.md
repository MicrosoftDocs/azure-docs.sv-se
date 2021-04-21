---
title: Hantera certifikat i Azure Automation
description: Den här artikeln beskriver hur du arbetar med certifikat för åtkomst av runbooks och DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788f15cd1edad228e695e6e87f5b630b8e4fdf55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834452"
---
# <a name="manage-certificates-in-azure-automation"></a>Hantera certifikat i Azure Automation

Azure Automation lagrar certifikat på ett säkert sätt för åtkomst via runbooks och DSC-konfigurationer, med hjälp av cmdleten [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) för Azure Resource Manager resurser. Med säker certifikatlagring kan du skapa runbooks och DSC-konfigurationer som använder certifikat för autentisering, eller lägga till dem i Azure eller resurser från tredje part.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Automation med hjälp av en unik nyckel som genereras för varje Automation-konto. Automation lagrar nyckeln i den system-hanterade Key Vault tjänsten. Innan du lagrar en säker tillgång läser Automation in nyckeln från Key Vault och använder den sedan för att kryptera tillgången.

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell-cmdlets för att komma åt certifikat

Cmdletarna i följande tabell skapar och hanterar Automation-certifikat med PowerShell. De levereras som en del av [Az-modulerna](modules.md#az-modules).

|Cmdlet |Beskrivning|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Hämtar information om ett certifikat som ska användas i en runbook eller DSC-konfiguration. Du kan bara hämta själva certifikatet med hjälp av den interna `Get-AutomationCertificate` cmdleten.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Skapar ett nytt certifikat i Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Tar bort ett certifikat från Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive uppladdning av certifikatfilen och inställning av lösenordet för en **PFX-fil.**|

Cmdleten [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) kan också användas för att ladda upp ett tjänstcertifikat för den angivna molntjänsten.

## <a name="internal-cmdlets-to-access-certificates"></a>Interna cmdlets för att komma åt certifikat

Den interna cmdleten i följande tabell används för att komma åt certifikat i dina runbooks. Den här cmdleten levereras med den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [Interna cmdlets](modules.md#internal-cmdlets).

| Intern cmdlet | Description |
|:---|:---|
|`Get-AutomationCertificate`|Hämtar ett certifikat som ska användas i en runbook- eller DSC-konfiguration. Returnerar [ett System.Security.Cryptography.X509Certificates.X509Certificate2-objekt.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE]
> Du bör undvika att använda variabler i `Name` parametern för `Get-AutomationCertificate` i en runbook- eller DSC-konfiguration. Sådana variabler kan komplicera identifieringen av beroenden mellan runbooks eller DSC-konfigurationer och Automation-variabler vid designtiden.

## <a name="python-functions-to-access-certificates"></a>Python-funktioner för att komma åt certifikat

Använd funktionen i följande tabell för att komma åt certifikat i en Python 2- och 3-runbook. Python 3-runbooks är för närvarande i förhandsversion.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_certificate` | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste importera modulen `automationassets` i början av din Python-runbook för att få åtkomst till tillgångsfunktionerna.

## <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en .cer- eller .pfx-fil till Automation. Om du markerar certifikatet som exporterbart kan du överföra det från Automation-certifikatarkivet. Om den inte kan exporteras kan den bara användas för signering i runbook- eller DSC-konfigurationen. Automation kräver att certifikatet har providern **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure Portal

1. Välj Certifikat under Delad resurs i det vänstra fönstret **från ditt** **Automation-konto.**
1. På sidan **Certifikat** väljer du Lägg **till ett certifikat.**
1. I fältet **Namn** skriver du ett namn för certifikatet.
1. Om du vill bläddra efter **en .cer-** **eller .pfx-fil** går **du till Ladda upp en certifikatfil** och **väljer Välj en fil**. Om du väljer en **PFX-fil** anger du ett lösenord och anger om det kan exporteras.
1. Välj **Skapa för** att spara den nya certifikattillgången.

### <a name="create-a-new-certificate-with-powershell"></a>Skapa ett nytt certifikat med PowerShell

I följande exempel visas hur du skapar ett nytt Automation-certifikat och markerar det exporterbart. I det här exemplet importeras en **befintlig PFX-fil.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Skapa ett nytt certifikat med en Resource Manager mall

Följande exempel visar hur du distribuerar ett certifikat till ditt Automation-konto med hjälp av en Resource Manager-mall via PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Hämta ett certifikat

Använd den interna cmdleten för att `Get-AutomationCertificate` hämta ett certifikat. Du kan inte använda cmdleten [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) eftersom den returnerar information om certifikattillgången, men inte själva certifikatet.

### <a name="textual-runbook-examples"></a>Exempel på textbaserade runbooks

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du lägger till ett certifikat till en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automationsvariabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

I följande exempel visas hur du kommer åt certifikat i Python 2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

I följande exempel visas hur du kommer åt certifikat i Python 3-runbooks (förhandsversion).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Exempel på grafisk runbook

Lägg till en aktivitet för den interna cmdleten i en grafisk runbook genom att högerklicka på certifikatet i fönstret Bibliotek och välja `Get-AutomationCertificate` **Lägg till på arbetsytan.**

![Skärmbild av att lägga till ett certifikat på arbetsytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook.

![Skärmbild av ett exempel på grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdlets som används för att komma åt certifikat finns [i Hantera moduler i Azure Automation](modules.md).
* Allmän information om runbooks finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns [i Azure Automation State Configuration översikt](../automation-dsc-overview.md).
