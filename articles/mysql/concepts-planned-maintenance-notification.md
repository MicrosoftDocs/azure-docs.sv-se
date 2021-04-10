---
title: Avisering om planerat underhåll – Azure Database for MySQL-enskild server
description: I den här artikeln beskrivs aviserings funktionen för planerat underhåll i Azure Database for MySQL-enskild server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c6d1bfbf1592da3a5e632eb875221225630aed3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108680"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Meddelande om planerat underhåll i Azure Database for MySQL-enskild server

Lär dig hur du förbereder för planerade underhålls händelser på din Azure Database for MySQL.

## <a name="what-is-a-planned-maintenance"></a>Vad är ett planerat underhåll?

Azure Database for MySQL tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller nya tjänst funktioner, säkerhets-och program uppdateringar. För MySQL-motorn är lägre versions uppgraderingar automatiska och ingår som en del av korrigerings cykeln. Det finns inga användar åtgärder eller konfigurations inställningar som krävs för korrigering. Korrigeringen testas omfattande och distribueras med hjälp av säkerhets metoder för säker distribution.

Ett planerat underhåll är ett underhålls fönster när de här tjänst uppdateringarna distribueras till servrar i en specifik Azure-region. Vid planerat underhåll skapas en meddelandehändelse som informerar kunderna om när tjänstuppdateringen distribueras i den Azure-region där värden för deras servrar finns. Minimi tiden mellan två planerade underhåll är 30 dagar. Du får ett meddelande om nästa underhållsperiod 72 timmar i förväg.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Planerat underhåll – varaktighet och kundpåverkan

Planerat underhåll för en Azure-region förväntas vanligtvis köras i 15 timmar. I fönstret ingår även buffertstorleken för att köra en återställnings plan vid behov. Under planerat underhåll kan det ske omstarter eller redundans för databas servrar, vilket kan leda till att databas servrarna inte är tillgängliga för slutanvändarna. Azure Database for MySQL servrar körs i behållare så att omstarter av databas servrar vanligt vis snabbt, förväntas slutföras normalt i 60-120 sekunder. Hela det planerade underhålls händelsen, inklusive varje omstart av servern, övervakas noggrant av teknik teamet. Serverns redundans tid är beroende av databasens återställnings tid, vilket kan göra att databasen blir online längre om du har tung transaktions aktivitet på servern vid redundansväxlingen. För att undvika längre omstart rekommenderar vi att du undviker tids krävande transaktioner (Mass inläsning) under planerade underhålls händelser.

I sammandrag, medan händelsen planerat underhåll körs i 15 timmar, varar den enskilda Server påverkan vanligt vis 60 sekunder beroende på transaktions aktiviteten på servern. Ett meddelande skickas 72 kalender timmar innan planerat underhåll påbörjas och ett annat under underhåll pågår för en specifik region.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Hur får jag meddelande om planerat underhåll?

Du kan använda funktionen för planerade underhålls meddelanden för att få aviseringar om kommande planerat underhålls händelser. Du får ett meddelande om kommande underhåll 72 kalender timmar före händelsen och en annan medan underhåll pågår för en specifik region.

### <a name="planned-maintenance-notification"></a>Meddelande om planerat underhåll

> [!IMPORTANT]
> Meddelanden om planerade underhåll är för närvarande tillgängliga i för hands versionen i alla regioner **utom** västra centrala USA

Med **planerade underhålls aviseringar** kan du få aviseringar om kommande planerat underhålls händelser till din Azure Database for MySQL. Dessa meddelanden är integrerade med [service Health](../service-health/overview.md) planerat underhåll och gör att du kan visa alla schemalagda underhåll för dina prenumerationer på ett och samma ställe. Den hjälper också till att skala meddelandet till rätt mål grupper för olika resurs grupper, eftersom du kan ha olika kontakter som är ansvariga för olika resurser. Du får ett meddelande om kommande underhåll 72 kalender timmar före händelsen.

Vi kommer att göra varje försök att tillhandahålla **planerat underhåll meddelande** 72 timmar meddelande om alla händelser. I fall av kritiska uppdateringar eller säkerhets korrigeringar kan du dock skicka meddelanden närmare händelsen eller utelämnas.

Du kan antingen kontrol lera den planerade underhålls aviseringen på Azure Portal eller konfigurera aviseringar för att få aviseringar. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Kontrol lera planerat underhålls meddelande från Azure Portal

1. I [Azure Portal](https://portal.azure.com)väljer du **service Health**.
2. Fliken Välj **planerat underhåll**
3. Välj den **prenumeration**, **region** och **tjänst** för vilken du vill kontrol lera aviseringen om planerat underhåll. 
   
### <a name="to-receive-planned-maintenance-notification"></a>För att få ett meddelande om planerat underhåll

1. I [portalen](https://portal.azure.com)väljer du **service Health**.
2. I avsnittet **aviseringar** väljer du **hälso aviseringar**.
3. Välj **+ Lägg till tjänstens hälso tillstånds avisering** och fyll i fälten.
4. Fyll i de obligatoriska fälten. 
5. Välj **händelse typ**, Välj **planerat underhåll** eller **Välj alla**
6. I **Åtgärds grupper** definierar du hur du vill få aviseringen (få ett e-postmeddelande, utlösa en Logic app osv.)  
7. Se till att aktivera regel vid skapande har angetts till Ja.
8. Välj **skapa aviserings regel** för att slutföra aviseringen

Detaljerade anvisningar om hur du skapar **tjänstens hälso aviseringar** finns i [skapa aktivitets logg aviseringar för tjänst meddelanden](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Kan jag avbryta eller senarelägga planerat underhåll?

Underhåll krävs för att hålla din server säker, stabil och uppdaterad. Det går inte att annullera eller skjuta upp händelsen planerat underhåll. När meddelandet har skickats till en viss Azure-region kan inte korrigerings schema ändringarna göras för någon enskild server i den regionen. Korrigeringen distribueras för hela regionen på samma gång. Azure Database for MySQL-enskild server-tjänst är utformad för internt program i molnet som inte kräver detaljerad kontroll eller anpassning av tjänsten. Om du vill ha möjlighet att schemalägga underhåll för dina servrar rekommenderar vi att du betraktar [flexibla servrar](./flexible-server/overview.md).

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Korrigeras alla Azure-regioner samtidigt?

Nej, alla Azure-regioner korrigeras under tids perioden för distributionen. Fönstret för distributions fönstret sträcker sig normalt ut från 5 PM – 8, lokal tid nästa dag, i en specifik Azure-region. Geo-kopplade Azure-regioner korrigeras på olika dagar. För hög tillgänglighet och affärs kontinuitet för databas servrar rekommenderas att du använder [flera Läs repliker för flera regioner](./concepts-read-replicas.md#cross-region-replication) .

## <a name="retry-logic"></a>Logik för omprövning

Ett tillfälligt fel, även kallat ett tillfälligt fel, är ett fel som kommer att lösa sig självt. [Tillfälliga fel](./concepts-connectivity.md#transient-errors) kan uppstå under underhållet. De flesta av dessa händelser begränsas automatiskt av systemet på mindre än 60 sekunder. Tillfälliga fel ska hanteras med [logiken för omprövning](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Nästa steg

- Om du har frågor eller förslag kan du behöva arbeta med Azure Database for MySQL, skicka ett e-postmeddelande till Azure Database for MySQL-teamet på AskAzureDBforMySQL@service.microsoft.com
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- [Felsöka anslutnings problem till Azure Database for MySQL-enskild server](howto-troubleshoot-common-connection-issues.md)
- [Hantera tillfälliga fel och Anslut effektivt till Azure Database for MySQL-enskild server](concepts-connectivity.md)
