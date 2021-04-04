---
title: 'Snabb start: din första python-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs biblioteks biblioteket för python och kör din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 6fc7b8f6a6fa7cde8d10dec6a4b2b834d41325e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920133"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Snabb start: kör din första resurs diagram fråga med python

Det första steget för att använda Azure Resource Graph är att kontrol lera att de nödvändiga biblioteken för python är installerade. Den här snabb starten vägleder dig genom processen att lägga till biblioteken i python-installationen.

I slutet av den här processen har du lagt till biblioteken i python-installationen och kör din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Lägg till resurs diagram biblioteket

Om du vill aktivera python för att fråga Azure Resource Graph måste biblioteket läggas till. Det här biblioteket fungerar där python kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrol lera att den senaste python-versionen är installerad (minst **3,8**). Om den inte har installerats än kan du ladda ned den på [python.org](https://www.python.org/downloads/).

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om den inte har installerats än kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI krävs för att tillåta python att använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i [autentisera med hjälp av Azures hanterings bibliotek för python](/azure/developer/python/azure-sdk-authenticate).

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I din python-miljö väljer du de bibliotek som krävs för Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Om python installeras för alla användare måste dessa kommandon köras från en upphöjd konsol.

1. Verifiera att biblioteken har installerats. `azure-mgmt-resourcegraph` bör vara **2.0.0** eller högre, `azure-mgmt-resource` vara **9.0.0** eller högre, och `azure-cli-core` bör vara **2.5.0** eller högre.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Med python-biblioteken som har lagts till i din miljö väljer du tid för att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med de installerade biblioteken och `resources` metoden:

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

1. Uppdatera anropet till `getresources` och ändra frågan till `order by` egenskapen **namn** :

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Uppdatera anropet till `getresources` och ändra frågan till först `order by` egenskapen **namn** och sedan `limit` till de fem främsta resultaten:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade biblioteken från python-miljön kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs diagram biblioteken i python-miljön och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
