---
title: Hantera python 3-paket i Azure Automation
description: Den här artikeln beskriver hur du hanterar python 3-paket (för hands version) i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734309"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Hantera python 3-paket (för hands version) i Azure Automation

Med Azure Automation kan du köra python 3-Runbooks (för hands version) på Azure och på Linux hybrid Runbook Worker. För att under lätta för Runbooks kan du använda python-paket för att importera de moduler som du behöver. Den här artikeln beskriver hur du hanterar och använder python 3-paket (för hands version) i Azure Automation.

## <a name="import-packages"></a>Importera paket

I ditt Automation-konto väljer du **python-paket** under **delade resurser**. Välj **+ Lägg till ett python-paket**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Skärm bild av sidan python 3-paket visar python 3-paket på den vänstra menyn och lägger till ett python 2-paket markerat.":::

På sidan **Lägg till python-paket** väljer du python 3 för **versionen** och väljer ett lokalt paket som ska laddas upp. Paketet kan vara en **. WHL** -eller **. tjär. gz** -fil. När paketet är markerat väljer du **OK** för att ladda upp det.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Skärm bild som visar sidan Lägg till python 3-paket med en uppladdad tar. gz-fil vald.":::

När ett paket har importer ATS visas det på sidan python-paket i ditt Automation-konto under fliken **python 3-paket (för hands version)** . Om du behöver ta bort ett paket markerar du paketet och klickar på **ta bort**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Skärm bild som visar sidan python 3-paket när ett paket har importer ATS.":::

## <a name="import-packages-with-dependencies"></a>Importera paket med beroenden

Azure Automation matchar inte beroenden för python-paket under import processen. Det finns dock ett sätt att importera ett paket med alla dess beroenden.

### <a name="manually-download"></a>Hämta manuellt

På en dator med Windows 64-bitars med [Python 3,8](https://www.python.org/downloads/release/python-380/) och [pip](https://pip.pypa.io/en/stable/) installerat kör du följande kommando för att ladda ned ett paket och alla dess beroenden:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

När paketen har hämtats kan du importera dem till ditt Automation-konto.

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
