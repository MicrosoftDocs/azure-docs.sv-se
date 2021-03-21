---
title: Migrera din dedikerade SQL-pool (tidigare SQL DW) till Gen2
description: Instruktioner för att migrera en befintlig dedikerad SQL-pool (tidigare SQL DW) till Gen2 och schemat för migrering per region.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120129"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Uppgradera din dedikerade SQL-pool (tidigare SQL DW) till Gen2

Microsoft hjälper till att minska kostnaden på ingångs nivå för att köra en dedikerad SQL-pool (tidigare SQL DW).  Lägre beräknings nivåer som kan hantera krävande frågor är nu tillgängliga för dedikerad SQL-pool (tidigare SQL DW). Läs hela meddelandet [lägre beräknings nivå stöd för Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Det nya erbjudandet är tillgängligt i de regioner som anges i tabellen nedan. För regioner som stöds kan befintliga gen1-dedikerade SQL-poolen (tidigare SQL DW) uppgraderas till Gen2 via antingen:

- **Den automatiska uppgraderings processen:** Automatiska uppgraderingar startar inte så fort tjänsten är tillgänglig i en region.  När automatiska uppgraderingar startar i en viss region sker enskilda data lager uppgraderingar under det valda underhålls schemat.
- [**Själv uppgradering till Gen2:**](#self-upgrade-to-gen2) Du kan styra när du ska uppgradera genom att göra en själv uppgradering till Gen2. Om din region ännu inte stöds, kan du återställa från en återställnings punkt direkt till en Gen2-instans i en region som stöds.

## <a name="automated-schedule-and-region-availability-table"></a>Automatiserad schema-och region tillgänglighets tabell

I följande tabell sammanfattas efter region när den nedre Gen2 beräknings nivån är tillgänglig och när automatiska uppgraderingar startar. Datumen kan komma att ändras. Kom tillbaka till se när din region blir tillgänglig.

\* anger att ett angivet schema för regionen inte är tillgängligt för tillfället.

| **Region** | **Lägre tillgängliga Gen2** | **Automatiska uppgraderingar börjar** |
|:--- |:--- |:--- |
| Kanada, östra |Den 1 juni 2020 |Den 1 juli 2020 |
| Kina, östra |\* |\* |
| Kina, norra |\* |\* |
| Tyskland, centrala |\* |\* |
| Tyskland, västra centrala |Tillgänglig |Den 1 maj 2020 |
| Indien, västra |Tillgänglig |Den 1 maj 2020  |

## <a name="automatic-upgrade-process"></a>Automatisk uppgraderings process

Baserat på tillgänglighets diagrammet ovan kommer vi att schemalägga automatiska uppgraderingar för dina gen1-instanser. För att undvika oväntade avbrott i tillgängligheten för den dedikerade SQL-poolen (tidigare SQL DW) schemaläggs de automatiska uppgraderingarna under ditt underhålls schema. Möjligheten att skapa en ny gen1-instans kommer att inaktive ras i regioner som genomgår automatisk uppgradering till Gen2. Gen1 kommer att bli inaktuell när de automatiska uppgraderingarna har slutförts. Mer information om scheman finns i [Visa ett underhålls schema](maintenance-scheduling.md#view-a-maintenance-schedule)

Uppgraderings processen innebär en kort nedrullningsbar anslutning (cirka 5 min) när vi startar om din dedikerade SQL-pool (tidigare SQL DW).  När din dedikerade SQL-pool (tidigare SQL DW) har startats om kommer den att vara helt tillgänglig för användning. Det kan dock uppstå sämre prestanda när uppgraderings processen fortsätter att uppgradera datafilerna i bakgrunden. Hur länge prestandaförsämringen pågår beror på storleken på dina filer.

Du kan också påskynda uppgraderings processen för data filen genom att köra [Alter index Rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller med ett större service nivå mål och en resurs klass efter omstarten.

> [!NOTE]
> Alter index Rebuild är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän återställningen har slutförts.

## <a name="self-upgrade-to-gen2"></a>Själv uppgradering till Gen2

Du kan välja att själv uppgradera genom att följa de här stegen i en befintlig gen1-dedikerad SQL-pool (tidigare SQL DW). Om du väljer att själv uppgradera måste du slutföra den innan den automatiska uppgraderings processen börjar i din region. På så sätt ser du till att du undviker en risk för automatiska uppgraderingar som orsakar en konflikt.

Det finns två alternativ när du utför en själv uppgradering.  Du kan antingen uppgradera den aktuella dedikerade SQL-poolen (tidigare SQL DW) på plats eller så kan du återställa en gen1 dedikerad SQL-pool (tidigare SQL DW) till en Gen2-instans.

- [Uppgradera på plats – med](upgrade-to-latest-generation.md) det här alternativet uppgraderas den befintliga gen1-dedikerade SQL-poolen (tidigare SQL DW) till Gen2. Uppgraderings processen innebär en kort nedrullningsbar anslutning (cirka 5 min) när vi startar om din dedikerade SQL-pool (tidigare SQL DW).  När den har startats om är den helt tillgänglig för användning. Om det uppstår problem under uppgraderingen öppnar du en [support förfrågan](sql-data-warehouse-get-started-create-support-ticket.md) och en referens "Gen2 uppgradering" som möjlig orsak.
- [Uppgradera från återställnings punkt](sql-data-warehouse-restore-points.md) – skapa en användardefinierad återställnings punkt för den aktuella gen1 dedikerade SQL-poolen (tidigare SQL DW) och återställ sedan direkt till en Gen2-instans. Den befintliga gen1-dedikerade SQL-poolen (tidigare SQL DW) kommer att vara på plats. När återställningen har slutförts kommer din Gen2 dedikerade SQL-pool (tidigare SQL DW) att vara helt tillgänglig för användning.  När du har kört alla testnings-och validerings processer på den återställda Gen2-instansen kan den ursprungliga gen1-instansen tas bort.

  - Steg 1: [skapa en användardefinierad återställnings punkt](sql-data-warehouse-restore-active-paused-dw.md)från Azure Portal.
  - Steg 2: när du återställer från en användardefinierad återställnings punkt ställer du in "prestanda nivå" på din önskade Gen2-nivå.

Det kan uppstå en prestandaförsämring när datafilerna uppgraderas i bakgrunden. Hur länge prestandaförsämringen pågår beror på storleken på dina filer.

Om du vill påskynda migreringen av bakgrunds data kan du omedelbart framtvinga data förflyttning genom att köra [Alter index Rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller som du frågar efter en större service nivå mål och resurs klass.

> [!NOTE]
> Alter index Rebuild är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän återställningen har slutförts.

Om du stöter på problem med din dedikerade SQL-pool (tidigare SQL DW) skapar du en [support förfrågan](sql-data-warehouse-get-started-create-support-ticket.md) och referens "Gen2 Upgrade" som möjlig orsak.

Mer information finns i [Uppgradera till Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Vanliga frågor och svar om migrering

**F: kostar Gen2 samma som gen1?**

- S: Ja.

**F: Hur påverkar uppgraderingar mina Automation-skript?**

- A: alla Automation-skript som refererar till ett service nivå mål ska ändras så att de motsvarar Gen2 motsvarande.  Se information [här](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**F: hur lång tid tar en själv uppgradering ut normalt?**

- S: du kan uppgradera på plats eller uppgradera från en återställnings punkt.

  - Om du uppgraderar på plats kommer din dedikerade SQL-pool (tidigare SQL DW) att tillfälligt pausa och återupptas.  En bakgrunds process kommer att fortsätta när den dedikerade SQL-poolen (tidigare SQL DW) är online.  
  - Det tar längre tid om du uppgraderar via en återställnings punkt, eftersom uppgraderingen går igenom hela återställnings processen.

**F: hur lång tid kommer den automatiska uppgraderingen att ta?**

- S: den faktiska stillestånds tiden för uppgraderingen är bara den tid det tar att pausa och återuppta tjänsten, vilket är mellan 5 och 10 minuter. Efter det korta driftstoppet körs en lagringsmigrering i en bakgrundsprocess. Tiden för bakgrunds processen beror på storleken på den dedikerade SQL-poolen (tidigare SQL DW).

**F: När kommer den här automatiska uppgraderingen att ske?**

- A: under ditt underhålls schema. Genom att dra nytta av det valda underhålls schemat minimeras störningar i verksamheten.

**F: Vad gör jag om min bakgrunds uppgraderings process verkar vara fastnad?**

- A: starta om en Omindexering av dina columnstore-tabeller. Observera att Omindexering av tabellen kommer att vara offline under den här åtgärden.

**F: Vad händer om Gen2 inte har service nivå målet på gen1?**

- A: om du kör en DW600 eller DW1200 på gen1, rekommenderar vi att du använder DW500c eller DW1000c eftersom Gen2 ger mer minne, resurser och högre prestanda än gen1.

**F: kan jag inaktivera geo-säkerhetskopiering?**

- S: Nej. Geo-Backup är en företags funktion för att bevara din dedikerade SQL-pool (tidigare SQL DW)-tillgänglighet i händelse av att en region blir otillgänglig. Öppna en [support förfrågan](sql-data-warehouse-get-started-create-support-ticket.md) om du har ytterligare frågor.

**F: finns det en skillnad i T-SQL-syntaxen mellan gen1 och Gen2?**

- S: det finns ingen ändring i språksyntaxen T-SQL från gen1 till Gen2.

**F: stöder Gen2 underhålls fönster?**

- S: Ja.

**F: kan jag skapa en ny gen1-instans när min region har uppgraderats?**

- S: Nej. När en region har uppgraderats kommer skapandet av nya gen1-instanser att inaktive ras.

## <a name="next-steps"></a>Nästa steg

- [Uppgraderings steg](upgrade-to-latest-generation.md)
- [Underhållsfönster](maintenance-scheduling.md)
- [Övervakaren resurs hälsa](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Granska innan du påbörjar en migrering](upgrade-to-latest-generation.md#before-you-begin)
- [Uppgradera på plats och uppgradera från en återställnings punkt](upgrade-to-latest-generation.md)
- [Skapa en användardefinierad återställnings punkt](sql-data-warehouse-restore-points.md)
- [Lär dig hur du återställer till Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Öppna en support förfrågan för Azure Synapse Analytics](./sql-data-warehouse-get-started-create-support-ticket.md)