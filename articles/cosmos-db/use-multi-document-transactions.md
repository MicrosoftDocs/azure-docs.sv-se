---
title: Använd transaktioner med flera dokument i Azure Cosmos DB API för MongoDB
description: Lär dig hur du skapar en Mongo Shell-app som kan köra en transaktion med flera dokument (all-eller-Nothing-semantik) i en fast samling i Azure Cosmos DB API för MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692478"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Använd transaktioner med flera dokument i Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I den här artikeln skapar du en Mongo-app som kör en transaktion för flera dokument i en fast samling i Azure Cosmos DB API för MongoDB med Server version 4,0.

## <a name="what-are-multi-document-transactions"></a>Vad är transaktioner med flera dokument?

I Azure Cosmos DB API för MongoDB är åtgärder i ett enda dokument atomiska. Transaktioner med flera dokument gör det möjligt för program att köra atomiska åtgärder i flera dokument. Den erbjuder "allt-eller-Nothing"-semantik till åtgärderna. Vid incheckning behålls de ändringar som gjorts i transaktionerna och om transaktionen Miss lyckas ignoreras alla ändringar i transaktionen.

Transaktioner i flera dokument följer **sur** semantik:

* Atomicitet: alla åtgärder som behandlas som ett
* Konsekvens: data som har allokerats är giltiga
* Isolering: isolerad från andra åtgärder
* Hållbarhet: transaktions data bevaras när klienten uppmanas att göra det

## <a name="requirements"></a>Krav

Transaktioner med flera dokument stöds i en unsharded-samling i API-version 4,0. Transaktioner med flera dokument stöds inte i samlingar eller i shardade-samlingar i 4,0. Tids gränsen för transaktioner är en fast 5 sekunder.

Alla driv rutiner som stöder Wire Protocol version 4,0 eller senare stöder Azure Cosmos DB-API för MongoDB transaktioner i flera dokument.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Köra transaktioner i flera dokument i MongoDB-gränssnittet
> [!Note]
> Det här exemplet fungerar inte i MongoSH beta (Shell) Embedded i MongoDB-kompassen.

1. Öppna en kommando tolk, gå till katalogen där Mongo Shell version 4,0 och högre är installerad:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Skapa ett Mongo Shell-skript *connect_friends.js* och Lägg till följande innehåll

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Kör följande kommando för att köra transaktioner med flera dokument. Du hittar värden, porten, användaren och nyckeln i Azure Portal.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Nästa steg

Utforska vad som är nytt i [Azure Cosmos DB API för MongoDB 4,0](mongodb-feature-support-40.md)
