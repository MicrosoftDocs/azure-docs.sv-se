---
title: Ansluta ett Node.js Mongoose-program till Azure Cosmos DB
description: Lär dig hur du använder Mongoose-ramverket för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 8958699ae279d2613f8dbadca802ee2137407e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442420"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Ansluta ett Node.js Mongoose-program till Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här självstudien visar hur du använder [Mongoose-ramverket](https://mongoosejs.com/) när du lagrar data i Cosmos dB. Vi använder Azure Cosmos DBs API för MongoDB för den här genom gången. För dem som inte vet är Mongoose ett ramverk för modellering av objekt för MongoDB i Node.js och tillhandahåller en enkel och schemabaserad lösning för att modellera dina programdata.

Cosmos DB är Microsofts globalt distribuerade databas tjänst för flera modeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version v 0.10.29 eller senare.

## <a name="create-a-cosmos-account"></a>Skapa ett Cosmos-konto

Nu ska vi skapa ett Cosmos-konto. Om du redan har ett konto som du vill använda kan du gå vidare till Konfigurera Node.js-programmet. Om du använder Azure Cosmos DB emulatorn följer du stegen i [Azure Cosmos DB emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till konfigurera ditt Node.js program.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Skapa en databas 
I det här programmet kommer vi att gå igenom två sätt att skapa samlingar i Azure Cosmos DB: 
- **Lagring av varje objekt modell i en separat samling**: Vi rekommenderar att du [skapar en databas med dedikerat data flöde](set-throughput.md#set-throughput-on-a-database). Med den här kapacitets modellen får du bättre kostnads effektivitet.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js självstudie – skärm bild av Azure Portal, som visar hur du skapar en databas i Datautforskaren för ett Azure Cosmos DB konto, för användning med Mongoose Node-modulen":::

- **Lagra alla objekt modeller i en enda Cosmos DB samling**: om du vill lagra alla modeller i en enda samling kan du bara skapa en ny databas utan att välja alternativet Tillhandahåll data flöde. Genom att använda den här kapacitets modellen skapas varje samling med sin egen data flödes kapacitet för varje objekt modell.

När du har skapat databasen använder du namnet i `COSMOSDB_DBNAME` miljö variabeln nedan.

## <a name="set-up-your-nodejs-application"></a>Konfigurera Node.js-programmet

>[!Note]
> Om du bara vill gå igenom exempelkoden istället för konfigurationen av själva programmet kan du klona [exemplet](https://github.com/Azure-Samples/Mongoose_CosmosDB) som används för den här självstudien och skapa ett Node.js Mongoose-program på Azure Cosmos DB.

1. Om du vill skapa ett Node.js-program i en valfri mapp kör du följande kommando i en nods kommandotolk.

    ```npm init```

    När du svarar på frågorna är projektet klart.

2. Lägg till en ny fil och ge den namnet ```index.js```.
3. Installera de paket som behövs med något av ```npm install```-alternativen:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Mongoose exempel anslutningen nedan baseras på Mongoose 5 +, som har ändrats sedan tidigare versioner.
    
   * Dotenv (om du vill läsa in dina hemligheter från en .env-fil): ```npm install dotenv --save```

     >[!Note]
     > Flaggan ```--save``` lägger till beroendet till package.json-filen.

4. Importera beroendena i din index.js-fil.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Lägg till Cosmos DB-anslutningssträngen och Cosmos DB-namnet till filen ```.env```. Ersätt plats hållarna {Cosmos-Account-name} och {dbname} med ditt eget Cosmos-kontonamn och databas namn, utan klammerparenteser.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMOSDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Anslut till Cosmos DB med Mongoose-ramverket genom att lägga till följande kod i slutet av index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMOSDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Här läses miljövariablerna in med process.env.{variableName} med hjälp av ”dotenv”-npm-paketet.

    När du är ansluten till Azure Cosmos DB kan du börja konfigurera objektmodeller i Mongoose.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Metod tips för att använda Mongoose med Cosmos DB

Mongoose skapar en ny samling för varje modell som du skapar. Detta är det bästa sättet att använda [data flödes alternativet på databas nivå](set-throughput.md#set-throughput-on-a-database)som tidigare diskuterats. Om du vill använda en enda samling måste du använda Mongoose [diskriminatorer](https://mongoosejs.com/docs/discriminators.html). Diskriminatorer är en mekanism för schemaarv. De gör att du kan ha flera modeller med överlappande scheman ovanpå samma underliggande MongoDB-samling.

Du kan lagra olika datamodeller i samma samling och sedan använda en filtersats vid frågetiden för att endast hämta de data som krävs. Låt oss gå igenom alla modeller.

### <a name="one-collection-per-object-model"></a>En samling per objektmodell

I det här avsnittet beskrivs hur du uppnår detta med Azure Cosmos DBs API för MongoDB. Den här metoden är vår rekommenderade metod eftersom du kan kontrol lera kostnaderna och kapaciteten. Det innebär att mängden enheter för programbegäran i databasen inte är beroende av antalet objekt modeller. Det här är standard operativ modellen för Mongoose, så du kanske är bekant med detta.

1. Öppna ```index.js``` igen.

1. Skapa schemadefinitionen för ”Familj”.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Skapa ett objekt för ”Familj”.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Slutligen ska vi spara objektet till Cosmos DB. Det skapar en samling på ett och samma ställe.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Nu ska vi skapa ytterligare ett schema och ett objekt. Den här gången skapar vi ett för de ”semesterdestinationer” familjerna kan ha intresse av.
   1. Precis som förra gången skapar vi schemat
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Skapa ett exempelobjekt (Du kan lägga till flera objekt till det här schemat) och spara det.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Nu ska du gå till den Azure Portal. du märker två samlingar som skapats i Cosmos DB.

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text="Node.js självstudie – skärm bild av Azure Portal som visar ett Azure Cosmos DB-konto med flera samlings namn markerade – nod databas":::

1. Slutligen ska vi läsa data från Cosmos DB. Eftersom vi använder standarddriftmodellen i Mongoose är läsningarna desamma som alla andra läsningar med Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Använda Mongoose-diskriminatorer för att lagra data i en enda samling

I den här metoden använder vi [Mongoose-diskriminatorer](https://mongoosejs.com/docs/discriminators.html) för att hjälpa till att optimera kostnaderna för varje samling. Diskriminatorer gör att du kan definiera en särskiljande ”nyckel”, som gör att du kan lagra, särskilja och filtrera på olika objektmodeller.

Här skapar vi en basobjektmodell med en särskiljande nyckel och lägger till ”Familj” och ”VacationDestinations” som tillägg till basmodellen.

1. Vi konfigurerar baskonfigurationen och definierar diskriminatornyckeln.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Nu ska vi definiera den gemensamma objektmodellen

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Nu definierar vi modellen ”Familj”. Lägg märke till att vi här använder ```commonModel.discriminator``` istället för ```mongoose.model```. Vi lägger dessutom till grundkonfigurationen till mongoose-schemat. Så här är discriminatorKey ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. På samma sätt lägger vi till ytterligare ett schema, denna gång för ”VacationDestinations”. Här är DiscriminatorKey ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Slutligen skapar vi objekt för modellen och sparar den.
   1. Nu ska vi lägga till objekt till modellen ”Familj”.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Buddy" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Därefter lägger vi till objekt till modellen ”VacationDestinations” och sparar den.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Om du nu går tillbaka till Azure-portalen lägger du märke till att du enda samling som heter ```alldata``` med både ”Familj”- och ”VacationDestinations”-data.

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text="Node.js självstudie – skärm bild av Azure Portal som visar ett Azure Cosmos DB-konto med samlings namnet markerat – Node Database":::

1. Lägg också märke till att varje objekt har ytterligare ett attribut som heter ```__type```, som hjälper till att skilja mellan de två olika objektmodellerna.

1. Läs till sist data som är lagrade i Azure Cosmos DB. Mongoose tar hand om filtrering av data baserat på modell. Så du gör inget annorlunda när du läser data. Ange bara din modell (i det här fallet ```Family_common```) så hanterar Mongoose filtreringen av ”DiscriminatorKey”.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Som du ser är det enkelt att arbeta med diskriminatorer i Mongoose. Så om du har en app som använder Mongoose-ramverket, är den här självstudien ett sätt för dig att komma igång med ditt program med Azure Cosmos API för MongoDB utan att det krävs för många ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
