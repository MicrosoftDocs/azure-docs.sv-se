---
title: Hantera anslutningar i Azure Automation
description: Den här artikeln beskriver hur du hanterar Azure Automation till externa tjänster eller program och hur du arbetar med dem i runbooks.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834902"
---
# <a name="manage-connections-in-azure-automation"></a>Hantera anslutningar i Azure Automation

En Azure Automation-anslutningstillgång innehåller informationen nedan. Den här informationen krävs för anslutning till en extern tjänst eller ett program från en runbook eller DSC-konfiguration. 

* Information som behövs för autentisering, till exempel användarnamn och lösenord
* Anslutningsinformation, till exempel URL eller port

Anslutningstillgången håller ihop alla egenskaper för att ansluta till ett visst program, vilket gör det onödigt att skapa flera variabler. Du kan redigera värdena för en anslutning på ett ställe och du kan skicka namnet på en anslutning till en runbook eller DSC-konfiguration i en enda parameter. Runbooken eller konfigurationen kommer åt egenskaperna för en anslutning med hjälp av den interna `Get-AutomationConnection` cmdleten.

När du skapar en anslutning måste du ange en anslutningstyp. Anslutningstypen är en mall som definierar en uppsättning egenskaper. Du kan lägga till en anslutningstyp för Azure Automation med hjälp av en integreringsmodul med en metadatafil. Du kan också skapa en anslutningstyp med [hjälp av Azure Automation-API:et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integreringsmodulen innehåller en anslutningstyp och importeras till ditt Automation-konto. 

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje Automation-konto. Azure Automation nyckeln i den system-hanterade Key Vault. Innan du lagrar en säker tillgång läser Automation in nyckeln från Key Vault och använder den sedan för att kryptera tillgången. 

## <a name="connection-types"></a>Anslutningstyper

Azure Automation gör följande inbyggda anslutningstyper tillgängliga:

* `Azure` – Representerar en anslutning som används för att hantera klassiska resurser.
* `AzureServicePrincipal` – Representerar en anslutning som används av Kör som-kontot i Azure.
* `AzureClassicCertificate` – Representerar en anslutning som används av det klassiska Kör som-kontot i Azure.

I de flesta fall behöver du inte skapa en anslutningsresurs eftersom den skapas när du skapar ett [Kör som-konto.](automation-security-overview.md)

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-cmdlets för att komma åt anslutningar

Cmdletarna i följande tabell skapar och hanterar Automation-anslutningar med PowerShell. De levereras som en del av [Az-modulerna](shared-resources/modules.md#az-modules).

|Cmdlet|Beskrivning|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Hämtar information om en anslutning.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Skapar en ny anslutning.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Tar bort en befintlig anslutning.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Ställer in värdet för ett visst fält för en befintlig anslutning.|

## <a name="internal-cmdlets-to-access-connections"></a>Interna cmdlets för att få åtkomst till anslutningar

Den interna cmdleten i följande tabell används för att komma åt anslutningar i dina runbooks och DSC-konfigurationer. Den här cmdleten levereras med den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [Interna cmdlets](shared-resources/modules.md#internal-cmdlets).

|Intern cmdlet|Description|
|---|---|
|`Get-AutomationConnection` | Hämtar värdena för de olika fälten i anslutningen och returnerar dem som en [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Du kan sedan använda den här hash-tabellen med lämpliga kommandon i runbook- eller DSC-konfigurationen.|

>[!NOTE]
>Undvik att använda variabler med `Name` parametern . `Get-AutomationConnection` Användning av variabler i det här fallet kan göra det komplicerat att identifiera beroenden mellan runbooks eller DSC-konfigurationer och anslutningstillgångar vid designtiden.

## <a name="python-functions-to-access-connections"></a>Python-funktioner för att få åtkomst till anslutningar

Funktionen i följande tabell används för att komma åt anslutningar i en Python 2- och 3-runbook. Python 3-runbooks är för närvarande i förhandsversion.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_connection` | Hämtar en anslutning. Returnerar en ordlista med egenskaperna för anslutningen. |

> [!NOTE]
> Du måste importera modulen `automationassets` överst i din Python-runbook för att få åtkomst till tillgångsfunktionerna.

## <a name="create-a-new-connection"></a>Skapa en ny anslutning

### <a name="create-a-new-connection-with-the-azure-portal"></a>Skapa en ny anslutning med Azure Portal

Så här skapar du en ny anslutning i Azure Portal:

1. Från ditt Automation-konto klickar du **på Anslutningar** under **Delade resurser.**
2. Klicka **på + Lägg till en** anslutning på sidan Anslutningar.
4. I fältet **Typ** i fönstret Ny anslutning väljer du vilken typ av anslutning du vill skapa. Dina val är `Azure` , `AzureServicePrincipal` och `AzureClassicCertificate` . 
5. Formuläret visar egenskaper för den anslutningstyp som du har valt. Fyll i formuläret och klicka på **Skapa** för att spara den nya anslutningen.

### <a name="create-a-new-connection-with-windows-powershell"></a>Skapa en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med `New-AzAutomationConnection` hjälp av cmdleten . Denna cmdlet har en parameter som förväntar sig en hash-tabell som definierar värden för var och en `ConnectionFieldValues` av de egenskaper som definieras av anslutningstypen.

Du kan använda följande exempelkommandon som ett alternativ till att skapa Kör som-kontot från portalen för att skapa en ny anslutningstillgång.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

När du skapar ditt Automation-konto innehåller det flera globala moduler som standard, tillsammans med anslutningstypen `AzureServicePrincipal` för att skapa `AzureRunAsConnection` anslutningstillgången. Om du försöker skapa en ny anslutningstillgång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod misslyckas åtgärden eftersom anslutningstypen inte redan har definierats i ditt Automation-konto. Mer information om hur du skapar en egen anslutningstyp för en anpassad modul finns i [Lägga till en anslutningstyp.](#add-a-connection-type)

## <a name="add-a-connection-type"></a>Lägga till en anslutningstyp

Om din runbook eller DSC-konfiguration ansluter till en extern tjänst måste du definiera en anslutningstyp i en anpassad modul [som](shared-resources/modules.md#custom-modules) kallas integreringsmodul. Den här modulen innehåller en metadatafil som anger egenskaperna för anslutningstypen och heter **&lt; ModuleName &gt;-Automation.jspå**, som finns i modulmappen i den komprimerade **ZIP-filen.** Den här filen innehåller fälten för en anslutning som krävs för att ansluta till systemet eller tjänsten som modulen representerar. Med den här filen kan du ange fältnamn, datatyper, krypteringsstatus och valfri status för anslutningstypen. 

Följande exempel är en mall i **.json-filformatet** som definierar egenskaper för användarnamn och lösenord för en anpassad anslutningstyp med namnet `MyModuleConnection` :

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Hämta en anslutning i en runbook eller DSC-konfiguration

Hämta en anslutning i en runbook eller DSC-konfiguration med den interna `Get-AutomationConnection` cmdleten. Den här cmdleten rekommenderas framför `Get-AzAutomationConnection` cmdleten eftersom den hämtar anslutningsvärdena i stället för information om anslutningen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du använder Kör som-kontot för att autentisera med Azure Resource Manager i din runbook. Den använder en anslutningstillgång som representerar Kör som-kontot, som refererar till det certifikatbaserade tjänsthuvudkontot.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

I följande exempel visas hur du autentiserar med hjälp av Kör som-anslutningen i en Python 2- och 3-runbook.

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

### <a name="graphical-runbook-examples"></a>Grafiska runbook-exempel

Du kan lägga till en aktivitet för den interna `Get-AutomationConnection` cmdleten i en grafisk runbook. Högerklicka på anslutningen i fönstret Bibliotek i den grafiska redigeraren och välj Lägg **till på arbetsytan.**

![lägga till på arbetsytan](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder ett anslutningsobjekt i en grafisk runbook. I det här exemplet `Constant value` används datauppsättningen `Get RunAs Connection` för aktiviteten, som använder ett anslutningsobjekt för autentisering. En [pipelinelänk](automation-graphical-authoring-intro.md#use-links-for-workflow) används här eftersom `ServicePrincipalCertificate` parameteruppsättningen förväntar sig ett enda objekt.

![hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdlets som används för att komma åt anslutningar finns [i Hantera moduler i Azure Automation](shared-resources/modules.md).
* Allmän information om runbooks finns i [Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns i [State Configuration översikt.](automation-dsc-overview.md)
