---
title: 'Snabbstart: Skapa en Python-app med hjälp Azure Cosmos DB SQL API-konto'
description: Presenterar ett Python-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API:t
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 04/06/2021
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: de05fdc110e653dd63cf098a8b31ffb81b13b1c5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365696"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snabbstart: Skapa ett Python-program med ett Azure Cosmos DB SQL API-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Spark v3-anslutningsapp](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och från Visual Studio Code med en Python-app som klonas från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärdes- och grafdatabaser med global distribution och horisontella skalningsfunktioner.

## <a name="prerequisites"></a>Förutsättningar

- Ett Cosmos DB konto. Du har följande alternativ:
    * Inom en aktiv Azure-prenumeration:
        * [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free) eller använd din befintliga prenumeration 
        * [Visual Studio månadskrediter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB kostnadsfri nivå](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Utan en aktiv Azure-prenumeration:
        * [Prova Azure Cosmos DB utan kostnad](https://azure.microsoft.com/try/cosmosdb/), en testmiljö som varar i 30 dagar.
        * [Azure Cosmos DB-emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 eller 3.6+](https://www.python.org/downloads/), med den `python` körbara filen i `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Python-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

Nu kan du använda Datautforskaren i Azure Portal för att skapa en databas och container. 

1. Välj **Datautforskaren**  >  **Ny container**. 
    
    Området **Lägg till** container visas längst till höger. Du kan behöva rulla åt höger för att se det.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::

2. På sidan **Lägg till container** anger du inställningarna för den nya containern.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Uppgifter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Markera alternativet **Etablera databasens dataflöde.** Med det kan du dela dataflödet som etablerats till databasen över alla containrar i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
    |**Dataflöde**|400|Lämna dataflödet på 400 enheter för begäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Poster|Ange *Objekt* som namn på den nya containern. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckel**| /category| Exemplet som beskrivs i den här artikeln använder */category* som partitionsnyckel.|
    
    Förutom de föregående inställningarna kan du även lägga till **Unika nycklar** för containern. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckelprincip när du skapar en container ser du till att ett eller flera värden per partitionsnyckel är unika. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från GitHub, ange anslutningssträngen och köra appen. I den här snabbstarten används version 4 av [Python SDK.](https://pypi.org/project/azure-cosmos/#history)

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```cmd
    md "git-samples"
    ```
   Om du använder en bash-kommandotolk bör du i stället använda följande kommando:

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

1. I ditt Azure Cosmos DB-konto [i Azure Portal](https://portal.azure.com/)väljer du **Nycklar i** det vänstra navigeringsfönstret. Använd kopieringsknapparna till höger på skärmen för  att kopiera **URI:en** och primärnyckeln till *filen cosmos_get_started.py* i nästa steg.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Hämta en åtkomstnyckel och URI i inställningarna för Nycklar i Azure Portal":::

2. I Visual Studio Code öppnar du *filen cosmos_get_started.py* i *\git-samples\azure-cosmos-db-python-getting-started*.

3. Kopiera ditt **URI-värde** från portalen (med kopieringsknappen)  och gör det till värdet för slutpunktsvariabeln *i cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopiera sedan **värdet för PRIMÄRNYCKEL** från portalen och gör det till värdet för **nyckeln i** *cosmos_get_started.py*. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

    `key = 'FILLME'`

5. Spara filen *cosmos_get_started.py.*

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Lär dig mer om databasresurserna som skapats i koden eller gå vidare till [Uppdatera anslutningssträngen](#update-your-connection-string).

Följande kodfragment är alla tagna från *filen cosmos_get_started.py.*

* CosmosClient initieras. Se till att uppdatera värdena "slutpunkt" och "nyckel" enligt beskrivningen i [avsnittet Uppdatera anslutningssträngen.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* En ny databas skapas.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* En ny container skapas med 400 RU/s etablerat [dataflöde](request-units.md). Vi väljer `lastName` som [partitionsnyckel,](partitioning-overview.md#choose-partitionkey)vilket gör att vi kan köra effektiva frågor som filtrerar på den här egenskapen. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Vissa objekt läggs till i containern. Containrar är en samling objekt (JSON-dokument) som kan ha olika scheman. Hjälpmetoderna ```get_[name]_family_item``` returnerar representationer av en familj som lagras i Azure Cosmos DB JSON-dokument.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Punktläsningar (nyckelvärdesuppslag) utförs med hjälp av `read_item` metoden . Vi skriver ut [RU-avgiften](request-units.md) för varje åtgärd.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* En fråga utförs med SQL-frågesyntax. Eftersom vi använder partitionsnyckelvärden i i WHERE-satsen dirigerar Azure Cosmos DB den här frågan effektivt till relevanta ```lastName``` partitioner, vilket förbättrar prestandan.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Kör appen

1. I Visual Studio Code väljer du **Visa**  >  **kommandopalett.** 

2. I kommandotolken anger du **Python: Välj tolk** och välj sedan den version av Python som ska användas.

    Sidfoten i Visual Studio Code uppdateras för att ange vilken tolk som valts. 

3. Välj **Visa**  >  **integrerad terminal för** att öppna den Visual Studio Code-integrerade terminalen.

4. I det integrerade terminalfönstret ser du till att du befinner dig i mappen *azure-cosmos-db-python-getting-started.* Om du inte är det kör du följande kommando för att växla till exempelmappen. 

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

7. Bekräfta att de nya objekten har skapats och sparats genom att Azure Portal på **Datautforskaren**  >  **AzureSampleFamilyDatabase**  >  **Items**. Visa de objekt som har skapats. Här är till exempel ett JSON-exempeldokument för familjen Andersen:
   
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

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en container med Datautforskaren och kör en Python-app i Visual Studio Code. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med SQL API:t](import-data.md)
