---
title: 'Snabbstart: Gremlin-API med PHP – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Gremlin-API i Azure Cosmos DB för att skapa ett konsolprogram med Azure Portal och PHP
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: php
ms.topic: quickstart
ms.date: 01/05/2019
ms.author: chrande
ms.openlocfilehash: dfb7b530ce7d907407eef987b5df8af245c90950
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360844"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-php-and-the-azure-portal"></a>Snabb start: skapa en diagram databas i Azure Cosmos DB med PHP och Azure Portal
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Den här snabbstarten visar hur du använder [Gremlin API](graph-introduction.md) i PHP och Azure Cosmos DB för att skapa en konsolapp genom att klona ett exempel från GitHub. Denna snabbstart visar dig också hur man skapar ett Azure Cosmos DB-konto med hjälp av den webbaserade Azure-portalen.   

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, tabeller, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.  

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Du kan även [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

Dessutom gäller följande:
* [PHP](https://php.net/) 5.6 eller senare
* [Composer](https://getcomposer.org/download/)

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-php-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kodavsnitt hämtas från filen connect.php i mappen C:\git-samples\azure-cosmos-db-graph-php-getting-started\. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-information). 

* Gremlin `connection` initieras i början av filen `connect.php` med objektet `$db`.

    ```php
    $db = new Connection([
        'host' => '<your_server_address>.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

* En mängd Gremlin-steg utförs med metoden `$db->send($query);`.

    ```php
    $query = "g.V().drop()";
    ...
    $result = $db->send($query);
    $errors = array_filter($result);
    }
    ```

## <a name="update-your-connection-information"></a>Uppdatera din anslutningsinformation

Gå nu tillbaka till Azure Portal för att hämta anslutningsinformation och kopiera den till appen. De här inställningarna gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. I [Azure Portal](https://portal.azure.com/) klickar du på **Nycklar**. 

    Kopiera den första delen av URI-värdet.

    :::image type="content" source="./media/create-graph-php/keys.png" alt-text="Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar":::

2. Öppna filen `connect.php` och i rad 8 klistrar du in URI-värdet över `your_server_address`.

    Anslutningsobjektets initiering bör se ut ungefär som följande kod:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.gremlin.cosmosdb.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

3. Ändra parametern `username` i parameter i anslutningsobjektet med din databas och ditt grafnamn. Om du använde de rekommenderade värdena `sample-database` och `sample-graph` ska det se ut som följande kod:

    `'username' => '/dbs/sample-database/colls/sample-graph'`

    Hela anslutningsobjektet ska nu se ut som följande kodavsnitt:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.gremlin.cosmosdb.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => 'your_primary_key',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

4. I Azure Portal använder du kopieringsknappen för att kopiera PRIMÄR NYCKEL och klistra in den över `your_primary_key` i lösenordsparametern.

    Anslutningsobjektets initiering bör nu se ut som följande kod:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => '2Ggkr662ifxz2Mg==',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

5. Spara filen `connect.php`.

## <a name="run-the-console-app"></a>Kör konsolappen

1. I git-terminalfönstret `cd` till mappen azure-cosmos-db-graph-php-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-php-getting-started"
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de PHP-beroenden som krävs.

   ```
   composer install
   ```

3. I git-terminalfönstret använder du följande kommando för att starta PHP-programmet.
    
    ```
    php connect.php
    ```

    I terminalfönstret visas de hörn som läggs till i tabellen. 
    
    Om du får timeout-fel kontrollerar du att du har uppdaterat anslutningsinformationen på rätt sätt i [Uppdatera din anslutningsinformation](#update-your-connection-information). Prova även att köra det senaste kommandot igen. 
    
    När programmet har slutat arbeta trycker du på Retur och växlar sedan tillbaka till Azure Portal i webbläsaren. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Granska och lägg till exempeldata

Nu kan du gå tillbaka till datautforskaren och se de hörn som lagts till i grafen, och lägga till ytterligare datapunkter.

1. Klicka på **Datautforskaren**, expandera **sample-graph**, klicka på **Diagram** och klicka sedan på **Tillämpa filter**. 

   :::image type="content" source="./media/create-graph-php/azure-cosmosdb-data-explorer-expanded.png" alt-text="Skärm bild som visar diagram som valts från en P I med alternativet att tillämpa filter.":::

2. I listan **Resultat** kan du se nya användare som har lagts till i grafen. Välj **ben** och Lägg märke till att de är anslutna till tax. Du kan flytta hörnen genom att dra och släppa, zooma in och ut genom att bläddra med mushjulet, och utöka diagrammets storlek med hjälp av dubbelpilen. 

   :::image type="content" source="./media/create-graph-php/azure-cosmosdb-graph-explorer-new.png" alt-text="Nya hörn i grafen i datautforskaren på Azure Portal":::

3. Vi ska nu lägga till några nya användare. Klicka på **Nytt hörn** om du vill lägga till data i grafen.

   :::image type="content" source="./media/create-graph-php/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Skärm bild som visar fönstret nytt hörn där du kan ange värden.":::

4. Ange en etikett för *person*.

5. Klicka på **Lägg till egenskap** för att lägga till var och en av följande egenskaper. Tänk på att du kan skapa unika egenskaper för varje person i grafen. Endast **ID-** nyckeln krävs.

    Tangent | Värde | Obs!
    ----|----|----
    **id** | ashley | Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    **kön** | kvinna | 
    **teknik** | Java | 

    > [!NOTE]
    > I den här snabbstartsguiden skapar du en icke-partitionerad samling. Men om du skapar en partitionerad samling genom att ange en partitionsnyckel när samlingen skapas, måste du lägga till partitionsnyckeln som nyckel i varje nytt hörn. 

6. Klicka på **OK**. Du kan behöva expandera skärmen för att se **OK** längst ned på skärmen.

7. Klicka på **Nytt hörn** igen och lägg till ytterligare en ny användare. 

8. Ange en etikett för *person*.

9. Klicka på **Lägg till egenskap** för att lägga till var och en av följande egenskaper:
    
    Tangent | Värde | Obs!
    ----|----|----
    **id** | rakesh | Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    **kön** | man | 
    **skola** | MIT | 

10. Klicka på **OK**. 

11. Klicka på knappen **Tillämpa filter** med standardfiltret `g.V()` för att visa alla värden i diagrammet. Nu visas alla användare i listan **Resultat**. 

    Allteftersom du lägger till data kan du använda filter för att begränsa resultaten. Som standard använder Datautforskaren `g.V()` för att hämta alla hörnen i ett diagram. Du kan ändra till en annan [diagramfråga](tutorial-query-graph.md), t.ex. `g.V().count()`, för att returnera en uppräkning av alla hörn i diagrammet i JSON-format. Om du har ändrat filtret ändrar du tillbaka det till `g.V()` och klickar på **Tillämpa filter** om du vill visa alla resultaten igen.

12. Nu kan du koppla ihop Rakesh och Ashley. Se till att **ashley** är markerat i listan **Resultat** och klicka sedan på redigeringsknappen bredvid **Mål** nere till höger. Du kan behöva bredda fönstret för att se området **Egenskaper**.

    :::image type="content" source="./media/create-graph-php/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ändra mål för ett hörn i en graf":::

13. I rutan **Mål** skriver du *rakesh* och i rutan **Edge label** (Kantetikett) skriver du *känner* och klickar sedan på kryssmarkeringen.

    :::image type="content" source="./media/create-graph-php/azure-cosmosdb-data-explorer-set-target.png" alt-text="Lägg till en anslutning mellan Ashley och Rakesh i datautforskaren":::

14. Markera nu **rakesh** i resultatlistan och se att Ashley och Rakesh är anslutna. 

    :::image type="content" source="./media/create-graph-php/azure-cosmosdb-graph-explorer.png" alt-text="Två hörn anslutna i datautforskaren":::

    Då är delen om att skapa resurser slutförd i den här snabbstarten. Du kan fortsätta att lägga till hörn i diagrammet, ändra befintliga hörn eller ändra frågorna. Vi ska nu titta på de mått som Azure Cosmos DB tillhandahåller, och sedan ska vi rensa resurserna. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en graf med datautforskaren och att köra en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

