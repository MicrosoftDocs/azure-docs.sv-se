---
title: 'Snabbstart: Cassandra API med Node.js – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Cassandra API i Azure Cosmos DB för att skapa ett profilprogram med Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: 126ece1327fa92c9b92c587922f1b8d9335d1a01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559288"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Snabb start: Bygg en Cassandra-app med Node.js SDK och Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

I den här snabb starten skapar du ett Azure Cosmos DB API för Cassandra konto och använder en Cassandra Node.js app som klonas från GitHub för att skapa en Cassandra-databas och-behållare. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Du kan även [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

Dessutom behöver du:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) version v0.10.29 eller senare
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk. Skapa en ny mapp med namnet `git-samples`. Stäng kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, till exempel git bash. Använd kommandot `cd` för att ändra till den nya mappen för att installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. Installera Node.js beroenden med NPM.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kodavsnitt kommer från filen `uprofile.js` i mappen `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

* Användarnamnet och lösenordet angavs med hjälp av anslutningssträngsidan i Azure-portalen. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* `client` har initierats med contactPoint-information. contactPoint hämtas från Azure-portalen.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* `client` ansluter till Azure Cosmos DB Cassandra-API.

    ```javascript
    client.connect();
    ```

* Ett nytt keyspace skapas.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* En ny tabell skapas.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* Nyckel/värde-entiteter infogas.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Fråga för att hämta alla nyckelvärden.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Fråga för att hämta ett nyckelvärde.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Stäng anslutning. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. Välj **anslutnings sträng** i Azure Cosmos DB-kontot i [Azure Portal](https://portal.azure.com/). 

1. Använd :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: knappen på höger sida av skärmen för att kopiera det översta värdet, kontakt punkten.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Visa och kopiera KONTAKTPUNKT, ANVÄNDARNAMN och LÖSENORD från Azure Portal, sidan med anslutningssträng":::

1. Öppna filen `config.js`. 

1. Klistra in värdet för kontakt punkt från portalen `'CONTACT-POINT` på rad 9.

    Rad 9 bör nu se ut ungefär så här 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Kopiera ANVÄNDARNAMN-värdet från portalen och klistra in det ovanpå `<FillMEIN>` på rad 2.

    Rad 2 bör nu se ut ungefär så här 

    `username: 'cosmos-db-quickstart',`

1. Kopiera LÖSENORD från portalen och klistra in det över `USERNAME` på rad 8.

    Rad 8 bör nu se ut ungefär som

    `password: '2Ggkr662ifxz2Mg==',`

1. Ersätt REGION med Azure-regionen som du skapade resursen i.

1. Spara filen `config.js`.


## <a name="run-the-nodejs-app"></a>Köra Node.js-appen

1. I terminalfönstret kontrollerar du att du är i den exempel katalog som du har klonat tidigare:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Kör Node-programmet:

    ```bash
    npm start
    ```

4. Kontrollera att resultatet blir det man kan förvänta sig från kommandoraden.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Visa och verifiera utdata":::

    Tryck på CTRL + C för att stoppa körningen av programmet och stänga konsol fönstret. 

5. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Visa data i Datautforskaren"::: 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB konto med API för Cassandra och kör en Cassandra Node.js-app som skapar en Cassandra-databas och-behållare. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)