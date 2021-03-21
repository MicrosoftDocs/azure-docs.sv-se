---
title: Använd Läs inställningar med Azure Cosmos DBs API för MongoDB
description: Lär dig hur du använder MongoDB-Läs inställningar med Azure Cosmos DBs API för MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349052"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Distribuera läsningar globalt med Azure Cosmos DB s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här artikeln visar hur du distribuerar Läs åtgärder globalt med [MongoDB](https://docs.mongodb.com/manual/core/read-preference/) med hjälp av Azure Cosmos DB s API för MongoDB.

## <a name="prerequisites"></a>Krav 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

I den här [snabb starts](tutorial-global-distribution-mongodb.md) artikeln hittar du instruktioner om hur du använder Azure Portal för att skapa ett Cosmos-konto med global distribution och sedan ansluta till det.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna ett git-terminalfönster, till exempel git bash och `cd` till en arbetskatalog.  

Kör följande kommandon för att klona exempellagringsplatsen. Använd någon av följande exempel databaser baserat på din plattform av intresse:

1. [.NET-exempel program](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Exempel program för NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Exempel program för Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Exempel program för Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Exempel program för SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Kör programmet

Beroende på vilken plattform som används installerar du de nödvändiga paketen och startar programmet. Om du vill installera beroenden följer du README-filen som ingår i exempel programmets lagrings plats. I exempel programmet NodeJS använder du exempelvis följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
node index.js
```
Programmet försöker ansluta till en MongoDB-källa och Miss lyckas eftersom anslutnings strängen är ogiltig. Följ stegen i README för att uppdatera anslutnings strängen `url` . Uppdatera även `readFromRegion` till en Läs region i Cosmos-kontot. Följande instruktioner är från NodeJS-exemplet:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Efter följande steg körs exempel programmet och genererar följande utdata:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Läsa med Läs inställnings läge

MongoDB-protokollet tillhandahåller följande Läs inställnings lägen för klienter som ska användas:

1. HUVUD
2. PRIMARY_PREFERRED
3. ALTERNATIV
4. SECONDARY_PREFERRED
5. NÄRA

Mer information om beteendet för var och en av dessa inställningar finns i dokumentationen om [MongoDB Read Preference Behavior](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) . I Cosmos DB, primära mappar till Skriv region och sekundära Maps för att läsa region.

Baserat på vanliga scenarier rekommenderar vi att du använder följande inställningar:

1. Om **låg latens läsningar** krävs använder du **närmaste** Läs inställnings läge. Den här inställningen styr Läs åtgärder till närmaste tillgängliga region. Observera att om den närmaste regionen är Skriv region dirigeras dessa åtgärder till den regionen.
2. Om **hög tillgänglighet och geo-distribution** krävs (svars tid är inte en begränsning) använder du alternativet **primärt** **prioriterat eller skrivskyddat** preferens läge. Den här inställningen dirigerar Läs åtgärder till en tillgänglig Skriv-eller Läs region. Om regionen inte är tillgänglig dirigeras förfrågningar till nästa tillgängliga region enligt Läs inställningen.

Följande kodfragment från exempel programmet visar hur du konfigurerar närmaste Läs inställning i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

På samma sätt visar kodfragmentet nedan hur du konfigurerar SECONDARY_PREFERRED Läs preferens i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Läs inställningen kan också anges genom `readPreference` att skicka som en parameter i URI-alternativen för anslutnings strängen:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Se motsvarande exempel program databaser för andra plattformar, till exempel [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Läsa med Taggar

Förutom läsa inställnings läget tillåter MongoDB-protokollet att Taggar används för att dirigera Läs åtgärder. I Cosmos DB s API för MongoDB `region` inkluderas taggen som standard som en del av `isMaster` svaret:

```json
"tags": {
         "region": "West US"
      }
```

Därför kan MongoClient använda `region` taggen tillsammans med region namnet för att dirigera Läs åtgärder till vissa regioner. För Cosmos-konton finns regions namn i Azure Portal till vänster under **Inställningar – >replik data globalt**. Den här inställningen är användbar för att uppnå **Läs isolering** – fall där klient programmet bara vill dirigera Läs åtgärder till en specifik region. Den här inställningen är idealisk för scenarier som inte är produktion/analys, som körs i bakgrunden och som inte är produktions kritiska tjänster.

Följande kodfragment från exempel programmet visar hur du konfigurerar inställningen läsa med taggar i NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Se motsvarande exempel program databaser för andra plattformar, till exempel [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

I den här artikeln har du lärt dig hur du globalt distribuerar Läs åtgärder med hjälp av Läs inställningar med Azure Cosmos DB s API för MongoDB.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda den här appen tar du bort alla resurser som skapats i den här artikeln i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Konfigurera en globalt distribuerad databas med Azure Cosmos DB s API för MongoDB](tutorial-global-distribution-mongodb.md)
* [Utveckla lokalt med Azure Cosmos DB-emulatorn](local-emulator.md)