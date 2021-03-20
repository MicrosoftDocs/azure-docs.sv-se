---
title: Hantera certifikat i Azure Automation
description: Den här artikeln beskriver hur du arbetar med certifikat för åtkomst av Runbooks och DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734818"
---
# <a name="manage-certificates-in-azure-automation"></a>Hantera certifikat i Azure Automation

Azure Automation lagrar certifikat säkert för åtkomst av Runbooks och DSC-konfigurationer med hjälp av cmdleten [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) för Azure Resource Manager-resurser. Med säker certifikat lagring kan du skapa Runbooks och DSC-konfigurationer som använder certifikat för autentisering, eller lägga till dem i Azure eller från resurser från tredje part.

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Automation med hjälp av en unik nyckel som genereras för varje Automation-konto. Automation lagrar nyckeln i den systemhanterade Key Vaults tjänsten. Innan du lagrar en säker till gång läser Automation in nyckeln från Key Vault och använder den för att kryptera till gången.

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell-cmdletar för att komma åt certifikat

Cmdletarna i följande tabell skapar och hanterar Automation-certifikat med PowerShell. De levereras som en del av [AZ-modulerna](modules.md#az-modules).

|Cmdlet |Beskrivning|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Hämtar information om ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Du kan bara hämta själva certifikatet med hjälp av den interna `Get-AutomationCertificate` cmdleten.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Skapar ett nytt certifikat i Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Tar bort ett certifikat från Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikat filen och inställning av lösen ordet för en **PFX** -fil.|

Cmdleten [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) kan också användas för att ladda upp ett tjänst certifikat för den angivna moln tjänsten.

## <a name="internal-cmdlets-to-access-certificates"></a>Interna cmdlets för att komma åt certifikat

Den interna cmdleten i följande tabell används för att komma åt certifikat i dina runbooks. Denna cmdlet ingår i den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [interna cmdletar](modules.md#internal-cmdlets).

| Intern cmdlet | Beskrivning |
|:---|:---|
|`Get-AutomationCertificate`|Hämtar ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Returnerar ett [system. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -objekt.|

> [!NOTE]
> Du bör undvika att använda variabler i- `Name` parametern i `Get-AutomationCertificate` en Runbook-eller DSC-konfiguration. Sådana variabler kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och automation-variabler i design läge.

## <a name="python-functions-to-access-certificates"></a>Python-funktioner för att komma åt certifikat

Använd funktionen i följande tabell för att komma åt certifikat i en python 2-eller 3-Runbook. Python 3-Runbooks är för närvarande en för hands version.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_certificate` | Hämtar information om en certifikat till gång. |

> [!NOTE]
> Du måste importera `automationassets` modulen i början av din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en CER-eller PFX-fil som ska automatiseras. Om du markerar att certifikatet kan exporteras kan du överföra det från automations certifikat arkiv. Om den inte kan exporteras kan du bara använda den för signering i Runbook-eller DSC-konfigurationen. Automation kräver att certifikatet har leverantören **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure Portal

1. Från ditt Automation-konto väljer du **certifikat** under **delad resurs** i den vänstra rutan.
1. På sidan **certifikat** väljer du **Lägg till ett certifikat**.
1. I fältet **namn** anger du ett namn för certifikatet.
1. Om du vill bläddra efter en **CER** -eller **PFX** -fil går du till **överför en certifikat fil** och väljer **Välj en fil**. Om du väljer en **. pfx** -fil anger du ett lösen ord och anger om det kan exporteras.
1. Välj **skapa** för att spara den nya certifikat till gången.

### <a name="create-a-new-certificate-with-powershell"></a>Skapa ett nytt certifikat med PowerShell

Följande exempel visar hur du skapar ett nytt Automation-certifikat och markerar det som kan exporteras. I det här exemplet importeras en befintlig **. pfx** -fil.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Skapa ett nytt certifikat med en Resource Manager-mall

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

Använd den interna cmdleten för att hämta ett certifikat `Get-AutomationCertificate` . Du kan inte använda cmdleten [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) eftersom den returnerar information om certifikat till gången, men inte själva certifikatet.

### <a name="textual-runbook-examples"></a>Text Runbook-exempel

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du lägger till ett certifikat i en moln tjänst i en Runbook. I det här exemplet hämtas lösen ordet från en krypterad Automation-variabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

I följande exempel visas hur du får åtkomst till certifikat i python 2-Runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

I följande exempel visas hur du får åtkomst till certifikat i python 3-Runbooks (för hands version).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Exempel på grafisk Runbook

Lägg till en aktivitet för den interna `Get-AutomationCertificate` cmdleten i en grafisk Runbook genom att högerklicka på certifikatet i fönstret Bibliotek och välja **Lägg till på arbets ytan**.

![Skärm bild som visar hur du lägger till ett certifikat på arbets ytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk Runbook.

![Skärm bild av ett exempel på grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdletar som används för att komma åt certifikat finns [i hantera moduler i Azure Automation](modules.md).
* Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns i [Översikt över Azure Automation tillstånds konfiguration](../automation-dsc-overview.md).
