---
title: Konflikt lösnings typer och lösnings principer i Azure Cosmos DB
description: I den här artikeln beskrivs de olika kategorierna och konflikt lösnings principerna i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ba55d88de3a5a4087db30613b22a7d2441de9be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334386"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Konflikt typer och lösnings principer när flera Skriv regioner används
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Konflikter och principer för konflikt lösning gäller om ditt Azure Cosmos DB-konto har kon figurer ATS med flera Skriv regioner.

För Azure Cosmos-konton som kon figurer ATS med flera Skriv regioner kan uppdaterings konflikter uppstå när skribenter uppdaterar samma objekt i flera regioner samtidigt. Uppdaterings konflikter kan vara av följande tre typer:

* **Infognings konflikter**: dessa konflikter kan uppstå när ett program infogar två eller flera objekt samtidigt med samma unika index i två eller flera regioner. Den här konflikten kan till exempel inträffa med en ID-egenskap.

* **Ersätt konflikter**: dessa konflikter kan uppstå när ett program uppdaterar samma objekt samtidigt i två eller flera regioner.

* **Ta bort konflikter**: dessa konflikter kan uppstå när ett program samtidigt tar bort ett objekt i en region och uppdaterar det i en annan region.

## <a name="conflict-resolution-policies"></a>Konfliktlösningsprinciper

Azure Cosmos DB erbjuder en flexibel princip driven mekanism för att lösa Skriv konflikter. Du kan välja mellan två principer för konflikt lösning på en Azure Cosmos-behållare:

* **Senaste Skriv WINS (LWW)**: den här lösnings principen använder som standard en systemdefinierad timestamp-egenskap. Den baseras på Time-Synchronize Clock-protokollet. Om du använder SQL-API: et kan du ange andra anpassade numeriska egenskaper (t. ex. det egna begreppet tidstämpel) som ska användas för konflikt lösning. En anpassad numerisk egenskap kallas även för *konflikt lösnings Sök vägen*. 

  Om två eller flera objekt står i konflikt med åtgärderna Infoga eller Ersätt, blir objektet med det högsta värdet för matchnings Sök vägen för konflikten den vinnare. Systemet fastställer vinnare om flera objekt har samma numeriska värde för matchnings Sök vägen för konflikten. Alla regioner konvergerar till en enda vinnare och får samma version av det allokerade objektet. När borttagnings konflikter är inblandade är den borttagna versionen alltid WINS över antingen infoga eller Ersätt konflikter. Detta inträffar oavsett vad värdet för konflikt lösnings Sök vägen är.

  > [!NOTE]
  > Senaste Skriv-WINS är standard lösnings principen för konflikt lösning och använder timestamp `_ts` för följande API: er: SQL, MongoDB, Cassandra, Gremlin och Table. Anpassad numerisk egenskap är endast tillgänglig för SQL API.

  Läs mer i [exempel som använder LWW konflikt lösnings principer](how-to-manage-conflicts.md).

* **Anpassad**: den här lösnings principen är utformad för programdefinierade semantik för avstämning av konflikter. När du ställer in den här principen på din Azure Cosmos-behållare måste du också registrera en *lagrad lagrad procedur*. Den här proceduren anropas automatiskt när konflikter identifieras under en databas transaktion på servern. Systemet ger exakt en garanti för körning av en sammanfognings procedur som en del av åtagande protokollet.  

  Om du konfigurerar din behållare med alternativet för anpassad upplösning och du inte kan registrera en sammanfognings procedur på behållaren eller om sammanfognings proceduren genererar ett undantag vid körningen, skrivs konflikterna till den *konflikter* som uppstår. Ditt program måste sedan manuellt lösa konflikterna i den motstridiga feeden. Mer information finns i [exempel på hur du använder den anpassade lösnings principen och hur du använder den här feeden](how-to-manage-conflicts.md).

  > [!NOTE]
  > Anpassad lösning för konflikt lösning är endast tillgängligt för SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar principer för konflikt lösning:

* [Så här konfigurerar du flera Skriv regioner för dina program](how-to-multi-master.md)
* [Hantera principer för konflikt lösning](how-to-manage-conflicts.md)
* [Läsa från feeden för konflikter](how-to-manage-conflicts.md#read-from-conflict-feed)
