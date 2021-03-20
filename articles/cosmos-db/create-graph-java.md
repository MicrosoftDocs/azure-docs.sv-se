---
title: Bygg en graf-databas med java i Azure Cosmos DB
description: Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor mot Azure Cosmos DB via Gremlin.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360980"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Snabb start: bygga en graf-databas med Java SDK och Azure Cosmos DB Gremlin-API: et
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB Gremlin (Graph) API-konto från Azure Portal och lägger till data med hjälp av en Java-app som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` miljö variabeln till den mapp där JDK är installerad.
- Ett [binärt maven-Arkiv](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en grafdatabas måste du skapa ett Gremlin-databaskonto (Graph) med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Nu ska vi klona en Gremlin-API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.  

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till en mapp där du vill installera exempelappen.  

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-information).

Följande kodfragment är alla hämtade från filen *C:\git-samples\azure-Cosmos-DB-Graph-Java-Getting-started\src\GetStarted\Program.java* .

Den här Java-konsolen använder en [Gremlin-API](graph-introduction.md) -databas med oss [Apache TinkerPop](https://tinkerpop.apache.org/) -drivrutinen. 

- Gremlin `Client` initieras från konfigurationen i *C:\git-samples\azure-Cosmos-DB-Graph-Java-Getting-started\src\remote.yaml* -filen.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Serier med Gremlin-steg körs med `client.submit`-metoden.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Uppdatera din anslutningsinformation

Gå nu tillbaka till Azure Portal för att hämta anslutningsinformation och kopiera den till appen. De här inställningarna gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. I ditt Azure Cosmos DB konto i [Azure Portal](https://portal.azure.com/)väljer du **nycklar**. 

    Kopiera den första delen av URI-värdet.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar":::

2. Öppna filen *src/Remote. yaml* och klistra in det unika ID-värdet över `$name$` i `hosts: [$name$.graphs.azure.com]` .

    Rad 1 i *Remote. yaml* bör nu se ut ungefär så här 

    `hosts: [test-graph.graphs.azure.com]`

3. Ändra `graphs` till `gremlin.cosmosdb` i värdet `endpoint`. (Om du skapade grafdatabaskontot innan 20 december, 2017 så gör du inga ändringar i slutpunktsvärdet och går vidare till nästa steg.)

    Slutpunktsvärdet bör nu se ut så här:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. I Azure Portal använder du kopieringsknappen för att kopiera PRIMÄR NYCKEL och klistra in den över `$masterKey$` i `password: $masterKey$`.

    Rad 4 i *Remote. yaml* bör nu se ut ungefär så här 

    `password: 2Ggkr662ifxz2Mg==`

5. Ändra rad 3 i *Remote. yaml* från

    `username: /dbs/$database$/colls/$collection$`

    på 

    `username: /dbs/sample-database/colls/sample-graph`

    Om du använde ett unikt namn för din exempeldatabas eller exempeldiagram, så uppdatera värden efter behov.

6. Spara filen *Remote. yaml* .

## <a name="run-the-console-app"></a>Kör konsolappen

1. I git-terminalfönstret `cd` till mappen azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Java-paket som krävs.

   ```git
   mvn package
   ```

3. I git-terminalfönstret använder du följande kommando för att starta Java-programmet.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    I terminalfönstret visas de hörn som läggs till i tabellen. 
    
    Om du får timeout-fel kontrollerar du att du har uppdaterat anslutningsinformationen på rätt sätt i [Uppdatera din anslutningsinformation](#update-your-connection-information). Prova även att köra det senaste kommandot igen. 
    
    När programmet har stoppats väljer du retur och växlar sedan tillbaka till Azure Portal i webbläsaren. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Granska och lägg till exempeldata

Nu kan du gå tillbaka till datautforskaren och se de hörn som lagts till i grafen, och lägga till ytterligare datapunkter.

1. I ditt Azure Cosmos DB konto i Azure Portal väljer du **datautforskaren**, expanderar **exempel diagram**, väljer **diagram** och väljer sedan **Använd filter**. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Skärm bild som visar diagram som valts från en P I med alternativet att tillämpa filter.":::

2. I listan **Resultat** kan du se nya användare som har lagts till i grafen. Välj **ben** och lägg märke till att användaren är kopplad till robin. Du kan flytta hörnen genom att dra och släppa, zooma in och ut genom att bläddra med mushjulet, och utöka diagrammets storlek med hjälp av dubbelpilen. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Nya hörn i grafen i datautforskaren på Azure Portal":::

3. Vi ska nu lägga till några nya användare. Välj **ny nod** för att lägga till data i diagrammet.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Skärm bild som visar fönstret nytt hörn där du kan ange värden.":::

4. Ange *person* i etikettrutan.

5. Välj **Lägg till egenskap** för att lägga till var och en av följande egenskaper. Tänk på att du kan skapa unika egenskaper för varje person i grafen. Endast id-nyckeln krävs.

    key|värde|Kommentarer
    ----|----|----
    id|ashley|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|kvinna| 
    teknik | Java | 

    > [!NOTE]
    > I den här snabbstartsguiden skapar du en icke-partitionerad samling. Men om du skapar en partitionerad samling genom att ange en partitionsnyckel när samlingen skapas, måste du lägga till partitionsnyckeln som nyckel i varje nytt hörn. 

6. Välj **OK**. Du kan behöva expandera skärmen för att se **OK** längst ned på skärmen.

7. Välj **nytt hörn** igen och Lägg till ytterligare en ny användare. 

8. Ange en etikett för *person*.

9. Välj **Lägg till egenskap** för att lägga till var och en av följande egenskaper:

    key|värde|Kommentarer
    ----|----|----
    id|rakesh|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|man| 
    skola|MIT| 

10. Välj **OK**. 

11. Välj knappen **Använd filter** med standard `g.V()` filtret för att visa alla värden i diagrammet. Nu visas alla användare i listan **Resultat**. 

    Allteftersom du lägger till data kan du använda filter för att begränsa resultaten. Som standard använder Datautforskaren `g.V()` för att hämta alla hörnen i ett diagram. Du kan ändra till en annan [diagramfråga](tutorial-query-graph.md), t.ex. `g.V().count()`, för att returnera en uppräkning av alla hörn i diagrammet i JSON-format. Om du har ändrat filtret ändrar du tillbaka filtret till `g.V()` och väljer **Använd filter** för att visa alla resultat igen.

12. Nu kan du koppla ihop rakesh och ashley. Se till att **Ashley** är markerat i **resultat** listan och välj sedan :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="ändra målet för ett formhörn i ett diagram":::  bredvid **mål** på nedre högra sidan. Du kan behöva bredda fönstret för att se knappen.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ändra målet för ett formhörn i ett diagram – Azure-CosmosDB":::

13. I rutan **mål** anger du *Rakesh* och i rutan **kant etikett** anger du *känner* till och markerar sedan kryss rutan.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Lägg till en anslutning i Datautforskaren – Azure-CosmosDB":::

14. Markera nu **rakesh** i resultatlistan och se att Ashley och Rakesh är anslutna. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Två hörn anslutna i Datautforskaren – Azure-CosmosDB":::

Då är delen om att skapa resurser slutförd i den här självstudien. Du kan fortsätta att lägga till hörn i diagrammet, ändra befintliga hörn eller ändra frågorna. Vi ska nu titta på de mått som Azure Cosmos DB tillhandahåller, och sedan ska vi rensa resurserna. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en graf med hjälp av Datautforskaren och kör en Java-app som lägger till data i grafen. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

