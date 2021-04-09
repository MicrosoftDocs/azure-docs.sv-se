---
title: 'Snabbstart: Gremlin-API med Python – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Gremlin-API i Azure Cosmos DB för att skapa ett konsolprogram med Azure Portal och Python
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 03/29/2021
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: cef397789d5ebcfa95c01e42dac9a80b9e1564e0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106950"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Snabb start: skapa en diagram databas i Azure Cosmos DB med python och Azure Portal
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB Gremlin (Graph) API-konto från Azure Portal och lägger till data med hjälp av en python-app som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
- Installations program för [python 3.6 +](https://www.python.org/downloads/) inklusive [pip](https://pip.pypa.io/en/stable/installing/) -paketet.
- [Python-drivrutin för Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Den här snabbstarten kräver ett databaskonto för diagram som skapats efter den 20 december 2017. Befintliga konton stöder Python när de är migrerade till allmän tillgänglighet.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en grafdatabas måste du skapa ett Gremlin-databaskonto (Graph) med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Nu ska vi klona en Gremlin-API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.  

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    mkdir "./git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till en mapp där du vill installera exempelappen.  

    ```bash
    cd "./git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kod avsnitt hämtas från filen *Connect.py* i mappen *C:\git-samples\azure-Cosmos-DB-Graph-python-Getting-Started \\* . Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-information).

* Gremlin `client` initieras på rad 155 i *Connect.py*. Se till att ersätta `<YOUR_DATABASE>` och `<YOUR_CONTAINER_OR_GRAPH>` med värdena för kontots databas namn och diagram namn:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_CONTAINER_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* En serie med Gremlin-steg deklareras i början av *Connect.py* -filen. De körs sedan med metoden `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Uppdatera din anslutningsinformation

Gå nu tillbaka till Azure Portal för att hämta anslutningsinformation och kopiera den till appen. De här inställningarna gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. I ditt Azure Cosmos DB konto i [Azure Portal](https://portal.azure.com/)väljer du **nycklar**. 

    Kopiera den första delen av URI-värdet.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar":::

2. Öppna filen *Connect.py* och i rad 155 klistrar du in URI-värdet `<YOUR_ENDPOINT>` i här:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    URI-delen av klientobjektet bör nu likna den här koden:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Ändra den andra parametern för objektet `client` för att ersätta strängarna `<YOUR_DATABASE>` och `<YOUR_COLLECTION_OR_GRAPH>`. Om du använde de föreslagna värdena bör parametern se ut som den här koden:

    `username="/dbs/sample-database/colls/sample-graph"`

    Hela objektet `client` bör nu se ut som den här koden:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. På sidan **nycklar** använder du kopierings knappen för att kopiera primär nyckeln och klistra in den över `<YOUR_PASSWORD>` i `password=<YOUR_PASSWORD>` parametern.

    Hela objektdefinitionen `client` bör nu se ut som den här koden:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Spara filen *Connect.py* .

## <a name="run-the-console-app"></a>Kör konsolappen

1. I git-terminalfönstret `cd` till mappen azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "./git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Python-paket som krävs.

   ```
   pip install -r requirements.txt
   ```

3. I git-terminalfönstret använder du följande kommando för att starta Python-programmet.
    
    ```
    python connect.py
    ```

    I terminalfönstret visas de hörn och gränser som läggs till i tabellen. 
    
    Om du får timeout-fel kontrollerar du att du har uppdaterat anslutningsinformationen på rätt sätt i [Uppdatera din anslutningsinformation](#update-your-connection-information). Prova även att köra det senaste kommandot igen. 
    
    När programmet har slutat arbeta trycker du på Retur och växlar sedan tillbaka till Azure Portal i webbläsaren.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Granska och lägg till exempeldata

När hörnen och kanterna har infogats kan du nu gå tillbaka till Datautforskaren och se de hörn som har lagts till i grafen och lägga till ytterligare data punkter.

1. I ditt Azure Cosmos DB konto i Azure Portal väljer du **datautforskaren**, expanderar **exempel diagram**, väljer **diagram** och väljer sedan **Använd filter**. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="Skärm bild som visar diagram som valts från en P I med alternativet att tillämpa filter.":::

2. Lägg märke till att tre nya användare läggs till i diagrammet i **resultat** listan. Du kan flytta hörnen genom att dra och släppa, zooma in och ut genom att bläddra med mushjulet, och utöka diagrammets storlek med hjälp av dubbelpilen. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Nya hörn i grafen i datautforskaren på Azure Portal":::

3. Vi ska nu lägga till några nya användare. Välj knappen **nytt hörn** för att lägga till data i diagrammet.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Skärm bild som visar fönstret nytt hörn där du kan ange värden.":::

4. Ange en etikett för *person*.

5. Välj **Lägg till egenskap** för att lägga till var och en av följande egenskaper. Tänk på att du kan skapa unika egenskaper för varje person i grafen. Endast id-nyckeln krävs.

    key|värde|Kommentarer
    ----|----|----
    villkor|/pk| 
    id|ashley|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|kvinna| 
    teknik | Java | 

    > [!NOTE]
    > I den här snabbstartsguiden skapar vi en icke-partitionerad samling. Men om du skapar en partitionerad samling genom att ange en partitionsnyckel när samlingen skapas, måste du lägga till partitionsnyckeln som nyckel i varje nytt hörn. 

6. Välj **OK**. Du kan behöva expandera skärmen för att se **OK** längst ned på skärmen.

7. Välj **nytt hörn** igen och Lägg till ytterligare en ny användare. 

8. Ange en etikett för *person*.

9. Välj **Lägg till egenskap** för att lägga till var och en av följande egenskaper:

    key|värde|Kommentarer
    ----|----|----
    villkor|/pk| 
    id|rakesh|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|man| 
    skola|MIT| 

10. Välj **OK**. 

11. Välj knappen **Använd filter** med standard `g.V()` filtret för att visa alla värden i diagrammet. Nu visas alla användare i listan **Resultat**. 

    Allteftersom du lägger till data kan du använda filter för att begränsa resultaten. Som standard använder Datautforskaren `g.V()` för att hämta alla hörnen i ett diagram. Du kan ändra till en annan [diagramfråga](tutorial-query-graph.md), t.ex. `g.V().count()`, för att returnera en uppräkning av alla hörn i diagrammet i JSON-format. Om du har ändrat filtret ändrar du tillbaka filtret till `g.V()` och väljer **Använd filter** för att visa alla resultat igen.

12. Nu kan vi koppla ihop Rakesh och Ashley. Se till att **Ashley** är markerat i **resultat** listan och välj sedan knappen Redigera bredvid **mål** nere till höger. Du kan behöva bredda fönstret för att se området **Egenskaper**.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ändra mål för ett hörn i en graf":::

13. I rutan **mål** skriver du *Rakesh* och i rutan **kant etikett** skriver du *vet* och markerar sedan kryss rutan.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="Lägg till en anslutning mellan Ashley och Rakesh i datautforskaren":::

14. Markera nu **rakesh** i resultatlistan och se att Ashley och Rakesh är anslutna. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Två hörn anslutna i datautforskaren":::

Då är delen om att skapa resurser slutförd i den här självstudien. Du kan fortsätta att lägga till hörn i diagrammet, ändra befintliga hörn eller ändra frågorna. Vi ska nu titta på de mått som Azure Cosmos DB tillhandahåller, och sedan ska vi rensa resurserna. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en graf med hjälp av Datautforskaren och kör en python-app för att lägga till data i grafen. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

