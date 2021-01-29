---
title: Hantera anslutningar i Azure Automation
description: Den här artikeln beskriver hur du hanterar Azure Automation anslutningar till externa tjänster eller program och hur du arbetar med dem i Runbooks.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054271"
---
# <a name="manage-connections-in-azure-automation"></a>Hantera anslutningar i Azure Automation

En Azure Automation anslutnings till gång innehåller den information som anges nedan. Den här informationen krävs för anslutning till en extern tjänst eller ett program från en Runbook eller DSC-konfiguration. 

* Information som krävs för autentisering, t. ex. användar namn och lösen ord
* Anslutnings information, till exempel URL eller port

Anslutnings till gången hålls tillsammans alla egenskaper för att ansluta till ett visst program, vilket gör det onödigt att skapa flera variabler. Du kan redigera värdena för en anslutning på en plats och du kan skicka namnet på en anslutning till en Runbook-eller DSC-konfiguration i en enda parameter. Runbooken eller konfigurationen får åtkomst till egenskaperna för en anslutning med hjälp av den interna `Get-AutomationConnection` cmdleten.

När du skapar en anslutning måste du ange en Anslutnings typ. Anslutnings typen är en mall som definierar en uppsättning egenskaper. Du kan lägga till en Anslutnings typ till Azure Automation att använda en integrerings-modul med en metadatafil. Det är också möjligt att skapa en Anslutnings typ med hjälp av [Azure Automation-API: et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integrerings modulen innehåller en Anslutnings typ och har importer ATS till ditt Automation-konto. 

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Azure Automation lagrar nyckeln i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Automation in nyckeln från Key Vault och använder den för att kryptera till gången. 

## <a name="connection-types"></a>Anslutningstyper

Azure Automation gör följande inbyggda anslutnings typer tillgängliga:

* `Azure` – Representerar en anslutning som används för att hantera klassiska resurser.
* `AzureServicePrincipal` – Representerar en anslutning som används av kör som-kontot i Azure.
* `AzureClassicCertificate` – Representerar en anslutning som används av det klassiska kör som-kontot i Azure.

I de flesta fall behöver du inte skapa en anslutnings resurs eftersom den skapas när du skapar ett [Kör som-konto](automation-security-overview.md).

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-cmdletar för åtkomst till anslutningar

Cmdletarna i följande tabell skapar och hanterar Automation-anslutningar med PowerShell. De levereras som en del av [AZ-modulerna](shared-resources/modules.md#az-modules).

|Cmdlet|Beskrivning|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Hämtar information om en anslutning.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Skapar en ny anslutning.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Tar bort en befintlig anslutning.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Ställer in värdet för ett visst fält för en befintlig anslutning.|

## <a name="internal-cmdlets-to-access-connections"></a>Interna cmdlets för att få åtkomst till anslutningar

Den interna cmdleten i följande tabell används för att få åtkomst till anslutningar i dina runbooks och DSC-konfigurationer. Denna cmdlet ingår i den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [interna cmdletar](shared-resources/modules.md#internal-cmdlets).

|Intern cmdlet|Description|
|---|---|
|`Get-AutomationConnection` | Hämtar värdena för de olika fälten i anslutningen och returnerar dem som en [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables)-tabellen. Du kan sedan använda den här hash-tabellen med lämpliga kommandon i Runbook-eller DSC-konfigurationen.|

>[!NOTE]
>Undvik att använda variabler med- `Name` parametern `Get-AutomationConnection` . Användningen av variabler i det här fallet kan komplicera identifiering av beroenden mellan Runbooks och DSC-konfigurationer och anslutnings till gångar i design läge.

## <a name="python-functions-to-access-connections"></a>Python-funktioner för att få åtkomst till anslutningar

Funktionen i följande tabell används för att få åtkomst till anslutningar i en python 2-eller 3-Runbook. Python 3-Runbooks är för närvarande en för hands version.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_connection` | Hämtar en anslutning. Returnerar en ord lista med egenskaperna för anslutningen. |

> [!NOTE]
> Du måste importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="create-a-new-connection"></a>Skapa en ny anslutning

### <a name="create-a-new-connection-with-the-azure-portal"></a>Skapa en ny anslutning med Azure Portal

Så här skapar du en ny anslutning i Azure Portal:

1. Från ditt Automation-konto klickar du på **anslutningar** under **delade resurser**.
2. Klicka på **+ Lägg till en anslutning** på sidan anslutningar.
4. I fältet **typ** i fönstret ny anslutning väljer du den typ av anslutning som ska skapas. Dina val är `Azure` , `AzureServicePrincipal` och `AzureClassicCertificate` . 
5. Formuläret visar egenskaperna för den Anslutnings typ som du har valt. Fyll i formuläret och klicka på **skapa** för att spara den nya anslutningen.

### <a name="create-a-new-connection-with-windows-powershell"></a>Skapa en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med hjälp av `New-AzAutomationConnection` cmdleten. Denna cmdlet har en `ConnectionFieldValues` parameter som förväntar sig en hash-definition som definierar värden för var och en av de egenskaper som definieras av anslutnings typen.

Du kan använda följande exempel kommandon som ett alternativ till att skapa ett Kör som-konto från portalen för att skapa en ny anslutning till gång.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

När du skapar ett Automation-konto innehåller det flera globala moduler som standard, tillsammans med anslutnings typen `AzureServicePrincipal` för att skapa `AzureRunAsConnection` anslutnings till gången. Om du försöker skapa en ny anslutning till gång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod, Miss lyckas åtgärden eftersom anslutnings typen inte redan har definierats i ditt Automation-konto. Mer information om hur du skapar en egen Anslutnings typ för en anpassad modul finns i [lägga till en Anslutnings typ](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Lägg till en Anslutnings typ

Om din Runbook-eller DSC-konfiguration ansluter till en extern tjänst måste du definiera en Anslutnings typ i en [anpassad modul](shared-resources/modules.md#custom-modules) som kallas en integrerings modul. Den här modulen innehåller en metadatafil som anger egenskaper för Anslutnings typ och heter **&lt; Modulnamn &gt;-Automation.jspå**, som finns i mappen module i den komprimerade **zip** -filen. Den här filen innehåller fälten i en anslutning som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Med hjälp av den här filen kan du ange fält namn, data typer, krypterings status och valfri status för anslutnings typen. 

Följande exempel är en mall i **JSON** -filformatet som definierar användar namn och lösen ords egenskaper för en anpassad Anslutnings typ som heter `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Få en anslutning i en Runbook-eller DSC-konfiguration

Hämta en anslutning i en Runbook-eller DSC-konfiguration med den interna `Get-AutomationConnection` cmdleten. Denna cmdlet föredras över `Get-AzAutomationConnection` cmdleten, eftersom den hämtar anslutnings värden i stället för information om anslutningen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du använder kör som-kontot för att autentisera med Azure Resource Manager resurser i din Runbook. Den använder en anslutnings till gång som representerar kör som-kontot, som refererar till det certifikatbaserade tjänstens huvud namn.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

I följande exempel visas hur du autentiserar med hjälp av kör som-anslutningen i en python 2-eller 3-Runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Grafiska Runbook-exempel

Du kan lägga till en aktivitet för den interna `Get-AutomationConnection` cmdleten i en grafisk Runbook. Högerklicka på anslutningen i fönstret Bibliotek i den grafiska redigeraren och välj **Lägg till i arbets ytan**.

![Lägg till på arbets ytan](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder ett anslutnings objekt i en grafisk Runbook. I det här exemplet används `Constant value` data uppsättningen för `Get RunAs Connection` aktiviteten, som använder ett anslutnings objekt för autentisering. En [pipeline-länk](automation-graphical-authoring-intro.md#use-links-for-workflow) används här eftersom `ServicePrincipalCertificate` parameter uppsättningen förväntar sig ett enskilt objekt.

![Hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdletar som används för att få åtkomst till anslutningar finns [i hantera moduler i Azure Automation](shared-resources/modules.md).
* Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns i [Översikt över tillstånds konfiguration](automation-dsc-overview.md).