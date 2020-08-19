---
title: 'Snabb start: bygga en python-app med Azure Cosmos DB SQL API-konto'
description: Presenterar ett Python-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API:t
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: 16f1a7d3608b13dd5423205dc75500d76f62d69a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588168"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snabb start: bygga ett python-program med ett Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och från Visual Studio Code med en python-app klonad från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Cosmos DB konto. Du kan välja mellan följande alternativ:
    * I en Azure Active-prenumeration:
        * [Skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free) eller Använd din befintliga prenumeration 
        * [Månads krediter för Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB kostnads fri nivå](https://docs.microsoft.com/azure/cosmos-db/optimize-dev-test#azure-cosmos-db-free-tier)
    * Utan en Azure-aktiv prenumeration:
        * [Prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/), en test miljö som varar i 30 dagar.
        * [Azure Cosmos DB-emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 eller 3.5.3 +](https://www.python.org/downloads/), med den `python` körbara filen i din `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Python-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

Du kan nu använda Datautforskaren-verktyget i Azure Portal för att skapa en databas och behållare. 

1. Välj **datautforskaren**  >  **ny behållare**. 
    
    Avsnittet **Lägg till behållare** visas längst till höger. du kan behöva rulla åt höger för att se det.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::

2. På sidan **Lägg till behållare** anger du inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Aktiviteter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Poster|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckel**| /category| Exemplet som beskrivs i den här artikeln använder */Category* som partitionsnyckel.|
    
    Förutom de föregående inställningarna kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckel princip när du skapar en behållare, säkerställer du att ett eller flera värden är unika per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från GitHub, ange anslutningssträngen och köra appen. I den här snabb starten används version 4 av [python SDK](https://pypi.org/project/azure-cosmos/#history).

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```cmd
    md "git-samples"
    ```
   Om du använder en bash-prompt bör du i stället använda följande kommando:

   ```bash
   mkdir "git-samples"
   ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I ditt Azure Cosmos DB konto i [Azure Portal](https://portal.azure.com/)väljer du **nycklar** i det vänstra navigerings fältet. Använd kopierings knapparna till höger på skärmen för att kopiera **URI: n** och **primär nyckeln** till filen *cosmos_get_started. py* i nästa steg.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Hämta en åtkomst nyckel och en URI i inställningarna för nycklar i Azure Portal":::

2. Öppna filen *cosmos_get_started. py* i *\Git-samples\azure-Cosmos-DB-python-Getting-Started*i Visual Studio Code.

3. Kopiera ditt **URI** -värde från portalen (med kopierings knappen) och gör det till värdet för **slut punkts** variabeln i *cosmos_get_started. py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopiera sedan värdet för **primär nyckeln** från portalen och gör det till värdet för **nyckeln** i *cosmos_get_started. py*. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

    `key = 'FILLME'`

5. Spara filen *cosmos_get_started. py* .

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Lär dig mer om databas resurserna som skapats i kod eller gå vidare till [Uppdatera anslutnings strängen](#update-your-connection-string).

Följande kodfragment är alla hämtade från filen *cosmos_get_started. py* .

* CosmosClient initieras. Se till att uppdatera värdena "slut punkt" och "nyckel" enligt beskrivningen i avsnittet [Uppdatera anslutnings strängen](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* En ny databas skapas.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* En ny behållare skapas med 400 RU/s av [tillhandahållet data flöde](request-units.md). Vi väljer `lastName` som [partitionsnyckel](partitioning-overview.md#choose-partitionkey), vilket gör att vi kan göra effektiva frågor som filtrerar på den här egenskapen. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Vissa objekt läggs till i containern. Behållare är en samling objekt (JSON-dokument) som kan ha varierande schema. Hjälp metoder ```get_[name]_family_item``` returnerar representationer av en familj som lagras i Azure Cosmos DB som JSON-dokument.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Punkt läsningar (nyckel värdes uppslag) utförs med hjälp av `read_item` metoden. Vi skriver ut [avgiften](request-units.md) för varje åtgärd.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* En fråga utförs med SQL-frågesyntax. Eftersom vi använder nyckel värden i ```lastName``` WHERE-satsen kommer Azure Cosmos DB att effektivt dirigera den här frågan till relevanta partitioner, vilket förbättrar prestandan.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Kör appen

1. I Visual Studio Code väljer du **Visa**  >  **kommando palett**. 

2. I kommandotolken anger du **Python: Välj tolk** och välj sedan den version av Python som ska användas.

    Sidfoten i Visual Studio Code uppdateras för att ange vilken tolk som valts. 

3. Välj **Visa**  >  **integrerad Terminal** för att öppna Visual Studio Code Integrated Terminal.

4. Se till att du är i mappen *Azure-Cosmos-DB-python-Startbok* i det integrerade terminalfönstret. Om du inte är det kör du följande kommando för att växla till exempelmappen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Kör följande kommando för att installera paketet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Om du får ett felmeddelande om nekad åtkomst när du försöker installera azure-cosmos måste du [köra VS Code som administratör](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Kör följande kommando för att köra exemplet och skapa och lagra nya dokument i Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Om du vill bekräfta att de nya objekten har skapats och sparats väljer du **datautforskaren**  >  **AzureSampleFamilyDatabase**  >  **objekt**i rutan Azure Portal. Visa de objekt som har skapats. Här är ett exempel på JSON-dokument för Andersen-serien:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en behållare med hjälp av Datautforskaren och kör en python-app i Visual Studio Code. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med SQL API:t](import-data.md)


