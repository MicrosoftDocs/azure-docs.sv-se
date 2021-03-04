---
title: Hantera python 3-paket i Azure Automation
description: Den här artikeln beskriver hur du hanterar python 3-paket (för hands version) i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122042"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Hantera python 3-paket (för hands version) i Azure Automation

Med Azure Automation kan du köra python 3-Runbooks (för hands version) på Azure och på Linux hybrid Runbook Worker. För att under lätta för Runbooks kan du använda python-paket för att importera de moduler som du behöver. Information om hur du importerar ett enda paket finns i [Importera ett paket](#import-a-package). Information om hur du importerar ett paket med flera paket finns i [Importera ett paket med beroenden](#import-a-package-with-dependencies). Den här artikeln beskriver hur du hanterar och använder python 3-paket (för hands version) i Azure Automation.

## <a name="import-a-package"></a>Importera ett paket

I ditt Automation-konto väljer du **python-paket** under **delade resurser**. Välj **+ Lägg till ett python-paket**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Skärm bild av sidan python 3-paket visar python 3-paket på den vänstra menyn och lägger till ett python 2-paket markerat.":::

På sidan **Lägg till python-paket** väljer du **python 3** för **versionen** och väljer ett lokalt paket som ska laddas upp. Paketet kan vara en **. WHL** -eller **. tjär. gz** -fil. När paketet är markerat väljer du **OK** för att ladda upp det.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Skärm bild som visar sidan Lägg till python 3-paket med en uppladdad tar. gz-fil vald.":::

När ett paket har importer ATS visas det på sidan python-paket i ditt Automation-konto under fliken **python 3-paket (för hands version)** . Om du behöver ta bort ett paket markerar du paketet och klickar på **ta bort**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Skärm bild som visar sidan python 3-paket när ett paket har importer ATS.":::

### <a name="import-a-package-with-dependencies"></a>Importera ett paket med beroenden

Du kan importera ett python 3-paket och dess beroenden genom att importera följande Python-skript till en python 3-Runbook och sedan köra det.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importera skriptet till en Runbook
Information om hur du importerar en Runbook finns i [Importera en Runbook från Azure Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Kopiera filen från GitHub till lagrings utrymmet som portalen har åtkomst till innan du kör importen.

Sidan **Importera en Runbook** är som standard Runbook-namnet som matchar namnet på skriptet. Om du har åtkomst till fältet kan du ändra namnet. **Runbook-typen** får som standard **python 2**. Om det gör det, se till att ändra det till **python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Skärm bild som visar webbplatsen för python 3-Runbook-import.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Kör runbooken för att importera paketet och beroenden

När du har skapat och publicerat runbooken kör du den för att importera paketet. Information om hur du kör en Runbook finns [i starta en Runbook i Azure Automation](start-runbooks.md) .

Skriptet ( `import_py3package_from_pypi.py` ) kräver följande parametrar.

| Parameter | Beskrivning |
|---------------|-----------------|
|subscription_id | Prenumerations-ID för Automation-kontot |
| resource_group | Namnet på den resurs grupp som Automation-kontot definieras i |
| automation_account | Namn på Automation-konto |
| module_name | Namn på den modul som importen ska importeras från `pypi.org` |

Mer information om hur du använder parametrar med Runbooks finns i [arbeta med Runbook-parametrar](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Använda ett paket i en Runbook

När paketet har importer ATS kan du använda det i en Runbook. Lägg till följande kod för att lista alla resurs grupper i en Azure-prenumeration.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Nästa steg

Information om hur du förbereder en python-Runbook finns i [skapa en python-Runbook](learn/automation-tutorial-runbook-textual-python-3.md).
