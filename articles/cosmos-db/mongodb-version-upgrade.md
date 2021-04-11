---
title: Uppgradera Mongo-versionen av din Azure Cosmos DBs API för MongoDB-konto
description: Så här uppgraderar du MongoDB Wire-Protocol-versionen för din befintliga Azure Cosmos DBs API för MongoDB-konton sömlöst
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771841"
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

1. Logga in på [Azure Portal.](https://portal.azure.com/)

1. Navigera till ditt Azure Cosmos DB-API för MongoDB-konto. Öppna **översikts** fönstret och kontrol lera att den aktuella **Server versionen** är antingen 3,2 eller 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Kontrol lera den aktuella versionen av ditt MongoDB-konto från Azure Portal." border="true":::

1. Öppna fönstret på den vänstra menyn `Features` . I den här rutan visas de funktioner på konto nivå som är tillgängliga för ditt databas konto.

1. Välj raden `Upgrade MongoDB server version`. Om du inte ser det här alternativet kanske kontot inte är kvalificerat för den här uppgraderingen. Ange [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om så är fallet.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Öppna bladet funktioner och uppgradera ditt konto." border="true":::

1. Granska informationen som visas om uppgraderingen. Välj `Set server version to 4.0` (eller 3,6 beroende på din aktuella version).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Granska uppgraderings vägledningen och välj uppgradera." border="true":::

1. När du har startat uppgraderingen är **funktions** menyn nedtonad och statusen är inställd på *väntar*. Uppgraderingen tar cirka 15 minuter att slutföra. Den här processen påverkar inte befintliga funktioner eller åtgärder för ditt databas konto. När den är klar visas den uppgraderade versionen av status för **uppdateringen av MongoDB-serverns version** . [Kontakta supporten](https://azure.microsoft.com/en-us/support/create-ticket/) om du har problem med att bearbeta din begäran.

1. Följande är några saker du behöver tänka på när du har uppgraderat ditt konto:

    1. Om du har uppgraderat från 3,2 går du tillbaka till **översikts** fönstret och kopierar den nya anslutnings strängen som ska användas i ditt program. Den gamla anslutningssträngen som kör 3.2 avbryts inte. För att säkerställa en konsekvent upplevelse måste alla program använda den nya slutpunkten.

    1. Om du har uppgraderat från 3.6 uppgraderas den befintliga anslutningssträngen till den angivna versionen och ska fortsätta att användas.

## <a name="how-to-downgrade"></a>Så här nedgraderar du

Du kan också nedgradera ditt konto från 4,0 till 3,6 via samma steg i avsnittet "så här uppgraderar".

Om du har uppgraderat från 3,2 till (4,0 eller 3,6) och vill nedgradera tillbaka till 3,2, kan du helt enkelt växla tillbaka till att använda din tidigare (3,2) anslutnings sträng med värden `accountname.documents.azure.com` som fortfarande är aktiv efter uppgradering som kör version 3,2.

## <a name="next-steps"></a>Nästa steg

- Läs mer om funktioner som stöds och inte stöds [i MongoDB version 4.0](mongodb-feature-support-40.md).
- Läs mer om funktioner som stöds och inte stöds i [MongoDB version 3.6](mongodb-feature-support-36.md).
- Mer information finns i [funktioner i Mongo version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
