---
title: 'Snabbstart: Din första Python-fråga'
description: I den här snabbstarten följer du stegen för att aktivera Resource Graph för Python och köra din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0bae0639e3f9913bc47b18fbc0b1d3d9ef1ad324
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533022"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Snabbstart: Kör din första Resource Graph fråga med Hjälp av Python

Det första steget för att Azure Resource Graph är att kontrollera att de bibliotek som krävs för Python är installerade. Den här snabbstarten går igenom processen för att lägga till biblioteken i Python-installationen.

I slutet av den här processen har du lagt till biblioteken i Python-installationen och kört din första Resource Graph fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Lägga till Resource Graph bibliotek

Om du vill aktivera Python för Azure Resource Graph måste biblioteket läggas till. Det här biblioteket fungerar överallt där Python kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrollera att den senaste Python-versionen är installerad (minst **3.8**). Om det inte har installerats än laddar du ned det på [Python.org](https://www.python.org/downloads/).

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om det inte har installerats än kan du [gå till Installera Azure CLI.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Azure CLI krävs för att Python ska kunna använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i Autentisera [med hjälp av Azure-hanteringsbiblioteken för Python.](/azure/developer/python/azure-sdk-authenticate)

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I valfri Python-miljö installerar du de bibliotek som krävs för Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Om Python är installerat för alla användare måste dessa kommandon köras från en upphöjd konsol.

1. Kontrollera att biblioteken har installerats. `azure-mgmt-resourcegraph` bör vara **2.0.0** eller högre, bör vara `azure-mgmt-resource` **9.0.0** eller högre och bör vara `azure-cli-core` **2.5.0** eller högre.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

När Python-biblioteken har lagts till i din miljö är det dags att prova en enkel Resource Graph fråga. Frågan returnerar de första fem Azure-resurserna **med namn** **och resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph fråga med hjälp av de installerade biblioteken och `resources` metoden :

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera anropet `getresources` till och ändra frågan till `order by` **egenskapen** Namn:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommandoordningen begränsar först frågeresultatet och beställer dem sedan.

1. Uppdatera anropet `getresources` till och ändra frågan till först egenskapen `order by` **Name** och sedan till de fem `limit` främsta resultaten:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

När den sista frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekventa och sorterade efter egenskapen **Name,** men fortfarande begränsade till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade biblioteken från Python-miljön kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lagt till Resource Graph i Python-miljön och kört din första fråga. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
