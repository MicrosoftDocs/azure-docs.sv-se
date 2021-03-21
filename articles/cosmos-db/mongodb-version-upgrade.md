---
title: Uppgradera Mongo-versionen av din Azure Cosmos DBs API för MongoDB-konto
description: Så här uppgraderar du MongoDB Wire-Protocol-versionen för din befintliga Azure Cosmos DBs API för MongoDB-konton sömlöst
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661179"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Uppgradera API-versionen av din Azure Cosmos DB API för MongoDB-kontot
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I den här artikeln beskrivs hur du uppgraderar API-versionen av din Azure Cosmos DBs API för MongoDB-konto. När du har uppgraderat kan du använda de senaste funktionerna i Azure Cosmos DBs API för MongoDB. Uppgraderings processen avbryter inte tillgängligheten för ditt konto och den förbrukar inte RU/s eller minskar kapaciteten för databasen när som helst. Inga befintliga data eller index påverkas av den här processen. 

När du uppgraderar till en ny API-version börjar du med utvecklings-och test arbets belastningar innan du uppgraderar produktions arbets belastningar. Det är viktigt att uppgradera klienterna till en version som är kompatibel med den API-version som du uppgraderar till innan du uppgraderar ditt Azure Cosmos DB-API för MongoDB-konto.

>[!Note]
> För närvarande kan endast kvalificerade konton som använder Server version 3,2 uppgraderas till version 3,6 eller 4,0. Om ditt konto inte visar uppgraderings alternativet ska du skicka in [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Uppgradering till 4.0 eller 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Fördelar med att uppgradera till version 4.0

Följande är de nya funktionerna som ingår i version 4,0:
- Stöd för transaktioner med flera dokument i unsharded-samlingar.
- Nya sammansättningsoperatorer
- Förbättrad genomsökningsprestanda
- Snabbare, effektivare lagring

### <a name="benefits-of-upgrading-to-version-36"></a>Fördelar med att uppgradera till version 3.6

Följande är de nya funktionerna som ingår i version 3,6:
- Bättre prestanda och stabilitet
- Stöd för nya databaskommandon
- Stöd för sammansättningspipeline som standard samt nya aggregeringsfaser
- Stöd för ändringsströmmar
- Stöd för sammansatta index
- Stöd för följande åtgärder mellan olika partitioner: update, delete, count och sort
- Bättre prestanda för följande aggregeringsåtgärder: $count, $skip, $limit och $group
- Nu finns stöd för indexering med jokertecken

### <a name="changes-from-version-32"></a>Ändringar från version 3.2

- Som standard är funktionen för [återförsök på serversidan (SSR)](prevent-rate-limiting-errors.md) aktiverad, så att begäranden från klientprogrammet inte returnerar 16500-fel. I stället återupptas begäranden tills de har slutförts eller nått tidsgränsen på 60 sekunder.
- Tidsgränsen för begäranden är inställd på 60 sekunder.
- Endast `_id`-egenskapen indexeras som standard för MongoDB-samlingar som skapats på den nya trådprotokollversionen.

### <a name="action-required-when-upgrading-from-32"></a>Åtgärd krävs vid uppgradering från 3.2

När du uppgraderar från 3.2 uppdateras databaskontots slutpunktssuffix till följande format:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Om du uppgraderar från version 3,2 måste du ersätta den befintliga slut punkten i dina program och driv rutiner som är anslutna till det här databas kontot. **Endast anslutningar som använder den nya slutpunkten får tillgång till funktionerna i den nya API-versionen**. Den tidigare 3.2-slutpunkten ska ha suffixet `.documents.azure.com`.

>[!Note]
> Den här slut punkten kan ha små skillnader om ditt konto har skapats i en suverän, myndighets eller begränsad Azure-moln.

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Gå till Azure Portal och navigera till bladet för ditt Azure Cosmos DB-API för MongoDB-konto. Kontrol lera att den aktuella Server versionen är det du förväntar dig.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Översikt över Azure Portal med MongoDB-konto" border="false":::

2. Välj bladet från alternativen till vänster `Features` . Då visas de kontonivåfunktioner som är tillgängliga för ditt databaskonto.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal med MongoDB konto översikt med bladet funktioner markerat" border="false":::

3. Klicka på `Upgrade Mongo server version` raden. Om du inte ser det här alternativet kanske kontot inte är kvalificerat för den här uppgraderingen. Ange [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om så är fallet.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Funktions blad med alternativ." border="false":::

4. Granska informationen som visas om uppgraderingen. Klicka på `Enable` så snart du är redo att starta processen.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Utökad uppgraderings vägledning." border="false":::

5. När processen har startats visas `Features` uppgraderingens status i menyn. Statusen går från `Pending` (Väntande) till `In Progress` (Pågår) till `Upgraded` (Uppgraderad). Den här processen påverkar inte befintliga funktioner eller åtgärder för databaskontot.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Uppgraderings status efter initiering." border="false":::

6. När uppgraderingen har slutförts visas statusen som `Upgraded` . Klicka på den för att lära dig mer om nästa steg och åtgärder som du behöver utföra för att slutföra processen. [Kontakta supporten](https://azure.microsoft.com/en-us/support/create-ticket/) om du har problem med att bearbeta din begäran.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Status för uppgraderat konto." border="false":::

7. 
    1. Om du har uppgraderat från 3,2 går du tillbaka till `Overview` bladet och kopierar den nya anslutnings strängen som ska användas i ditt program. Den gamla anslutningssträngen som kör 3.2 avbryts inte. För att säkerställa en konsekvent upplevelse måste alla program använda den nya slutpunkten.
    2. Om du har uppgraderat från 3.6 uppgraderas den befintliga anslutningssträngen till den angivna versionen och ska fortsätta att användas.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Bladet ny översikt." border="false":::


## <a name="how-to-downgrade"></a>Så här nedgraderar du
Du kan också nedgradera ditt konto från 4,0 till 3,6 via samma steg i avsnittet "så här uppgraderar". 

Om du har uppgraderat från 3,2 till (4,0 eller 3,6) och vill nedgradera tillbaka till 3,2, kan du helt enkelt växla tillbaka till att använda din tidigare (3,2) anslutnings sträng med värden `accountname.documents.azure.com` som fortfarande är aktiv efter uppgradering som kör version 3,2.


## <a name="next-steps"></a>Nästa steg

- Läs mer om funktioner som stöds och inte stöds [i MongoDB version 4.0](mongodb-feature-support-40.md).
- Läs mer om funktioner som stöds och inte stöds i [MongoDB version 3.6](mongodb-feature-support-36.md).
- Mer information finns i [funktioner i Mongo version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
