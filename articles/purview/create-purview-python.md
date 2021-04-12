---
title: 'Snabb start: skapa ett avdelningens kontroll-konto med python'
description: Skapa ett Azure avdelningens kontroll-konto med python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387573"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Snabb start: skapa ett avdelningens kontroll-konto med python

I den här snabb starten skapar du ett avdelningens kontroll-konto med python. 

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Ditt konto måste ha behörighet att skapa resurser i prenumerationen

* Om du **Azure policy** blockera alla program från att skapa **lagrings konto** och **EventHub-namnområde** måste du göra princip undantag med hjälp av taggen, som kan anges under processen för att skapa ett avdelningens kontroll-konto. Huvud orsaken är att för varje avdelningens kontroll-konto måste det skapas en hanterad resurs grupp och inom den här resurs gruppen, ett lagrings konto och ett EventHub-namnområde. Mer information hittar du i [Skapa katalog Portal](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Installera Python-paketet

1. Öppna en terminal eller kommandotolk med administratörsbehörighet. 
2. Installera först Python-paketet för hanteringsresurser i Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Kör följande kommando för att installera python-paketet för avdelningens kontroll:

    ```python
    pip install azure-mgmt-purview
    ```

    [Python SDK för avdelningens kontroll](https://github.com/Azure/azure-sdk-for-python) har stöd för python 2,7, 3,3, 3,4, 3,5, 3,6 och 3,7.

4. Kör följande kommando för att installera python-paketet för Azure Identity Authentication:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Paketet "Azure-Identity" kan ha konflikter med "Azure-CLI" på vissa vanliga beroenden. Om du uppfyller eventuella autentiseringsproblem tar du bort "Azure-CLI" och dess beroenden eller använder en ren dator utan att installera "Azure-CLI"-paketet så att det fungerar.
    
## <a name="create-a-purview-client"></a>Skapa en avdelningens kontroll-klient

1. Skapa en fil med namnet **purview.py**. Lägg till följande instruktioner för att lägga till referenser till namnområden.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Lägg till följande kod i **main** -metoden som skapar en instans av klassen PurviewManagementClient. Du använder det här objektet för att skapa ett avdelningens kontroll-konto, ta bort avdelningens kontroll-konto, kontrol lera namn tillgänglighet och andra resurs leverantörs åtgärder.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Skapa ett avdelningens kontroll-konto

Lägg till följande kod i **main** -metoden som skapar ett **avdelningens kontroll-konto**. Om din resursgrupp redan finns kommenterar du ut det första `create_or_update`-instruktionen.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Lägg nu till följande instruktion för att anropa **Main**-metoden när programmet körs:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Fullständigt skript

Här är den fullständiga Python-koden:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera de blob(ar) som kopierats till outputBlobPath från inputBlobPath som du angav i variablerna.

Här är exempel på utdata:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Verifiera utdata

Gå till sidan med **avdelningens kontroll-konton** i Azure Portal och kontrol lera kontot som skapats med ovanstående kod. 

## <a name="delete-purview-account"></a>Ta bort avdelningens kontroll-konto

Om du vill ta bort avdelningens kontroll-kontot lägger du till följande kod i programmet:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Nästa steg

Koden i den här självstudien skapar ett avdelningens kontroll-konto och tar bort ett avdelningens kontroll-konto. Nu kan du Hämta python SDK och lära dig mer om andra resurs leverantörs åtgärder som du kan utföra för ett avdelningens kontroll-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure avdelningens kontroll-konto. 

> [!div class="nextstepaction"]
> [Lägg till användare till ditt Azure avdelningens kontroll-konto](catalog-permissions.md)
