---
title: Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB
description: Få svar på vanliga frågor om Synapse Link för Azure Cosmos DB inom områden som fakturering, analysarkiv, säkerhet, time to live på analysarkiv.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483868"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link för Azure Cosmos DB skapar en nära integrering mellan Azure Cosmos DB och Azure Synapse Analytics. Det gör det möjligt för kunder att köra analys i nära realtid över sina driftdata med fullständig prestandaisolering från sina transaktionsarbetsbelastningar och utan en ETL-pipeline. Den här artikeln besvarar vanliga frågor om Synapse Link för Azure Cosmos DB.

## <a name="general-faq"></a>Allmänna vanliga frågor och svar

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Stöds Azure Synapse Link för alla Azure Cosmos DB-API:er?

Azure Synapse Link stöds för API:Azure Cosmos DB SQL (Core) och api:Azure Cosmos DB för MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Stöds Azure Synapse Link för konton med flera Azure Cosmos DB regioner?

Ja, för Azure Cosmos-konton i flera regioner distribueras även de data som lagras i analysarkivet globalt. Oavsett region för endast en skrivning eller regioner för flera skrivningar kan analysfrågor som utförs via Azure Synapse Analytics tjänas från den närmaste lokala regionen.

När du planerar att konfigurera ett Azure Cosmos DB ett konto med stöd för analysarkiv rekommenderar vi att alla nödvändiga regioner läggs till när kontot skapas.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Kan jag välja att aktivera Azure Synapse Link endast för en viss region och inte för alla regioner i ett konto för flera regioner?

När Azure Synapse Link har aktiverats för ett konto för flera regioner skapas analysarkivet i alla regioner. Underliggande data är optimerade för dataflöde och transaktionskonsekvens i transaktionslagret.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Stöds analysarkiv i alla Azure Cosmos DB regioner?

Ja.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Stöds säkerhetskopiering och återställning för Azure Synapse Link-aktiverade konton?

För containrar med analysarkiv aktiverat stöds inte automatisk säkerhetskopiering och återställning av data i analysarkivet för närvarande. 

När Synapse Link är aktiverat på ett databaskonto fortsätter Azure Cosmos DB [](./online-backup-and-restore.md) automatiskt att göra säkerhetskopior av dina data i transaktionslagret (endast) av containrar vid schemalagd säkerhetskopieringsintervall, som alltid. Det är viktigt att observera att när en container med analysarkiv aktiverat återställs till ett nytt konto, återställs containern med endast transaktionsarkiv och inget analysarkiv aktiverat. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Kan jag inaktivera funktionen Azure Synapse Link för mitt Azure Cosmos DB konto?

När Synapse Link-funktionen har aktiverats på kontonivå kan du för närvarande inte inaktivera den. Du kommer inte att debiteras om Synapse Link-funktionen aktiveras på kontonivå och det inte finns några analysarkivaktiverade containrar.

Om du behöver inaktivera funktionen har du två alternativ. Det första är att ta bort och Azure Cosmos DB-kontot, skapa ett nytt och sedan migrera data om det behövs. Det andra alternativet är att öppna en supportbegäran för att få hjälp med att migrera data till ett annat konto.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Har analysarkivet någon inverkan på Cosmos DB serviceavtal för transaktioner?

Nej, det finns ingen påverkan.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analysarkiv

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Kan jag aktivera analysarkiv på befintliga containrar?

För närvarande kan analysarkivet endast aktiveras för nya containrar (både i nya och befintliga konton).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Kan jag inaktivera analysarkiv på mina Azure Cosmos DB när jag har aktivera det när containern skapas?

För närvarande kan inte analysarkivet inaktiveras på en Azure Cosmos DB-container när den har aktiverats medan containern skapas.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Stöds analysarkiv för Azure Cosmos DB containrar med etablerat dataflöde för autoskalning?

Ja, analysarkivet kan aktiveras på containrar med etablerat dataflöde för autoskalning.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Har det någon effekt på Azure Cosmos DB etablerade RU:er i transaktionslagret?

Azure Cosmos DB garanterar prestandaisolering mellan transaktions- och analytiska arbetsbelastningar. Aktivering av analysarkivet på en container påverkar inte RU/s som etablerats på Azure Cosmos DB transaktionslager. Transaktionerna (läs- & skrivningar) och lagringskostnaderna för analysarkivet debiteras separat. Se [prissättningen för Azure Cosmos DB analysarkiv för](analytical-store-introduction.md#analytical-store-pricing) mer information.

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Kan jag begränsa nätverksåtkomsten till Azure Cosmos DB analysarkiv?

Ja, du kan konfigurera en [hanterad privat slutpunkt](analytical-store-private-endpoints.md) och begränsa nätverksåtkomsten för analysarkiv till Azure Synapse hanterat virtuellt nätverk. Hanterade privata slutpunkter upprättar en privat länk till analysarkivet. 

Du kan lägga till privata slutpunkter för både transaktionslager och analysarkiv till samma Azure Cosmos DB konto i en Azure Synapse Analytics arbetsyta. Om du bara vill köra analysfrågor kanske du bara vill aktivera den privata slutpunkten för analys Synapse Analytics arbetsytan.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Kan jag använda kund hanterade nycklar med Azure Cosmos DB analysarkiv?

Du kan sömlöst kryptera data i transaktions- och analysarkiv med hjälp av samma kund hanterade nycklar på ett automatiskt och transparent sätt. Om du vill använda kund hanterade nycklar med analysarkivet måste du använda Azure Cosmos DB-kontots systemtilldeade hanterade identitet i din Azure Key Vault åtkomstprincip. Detta beskrivs [här.](how-to-setup-cmk.md#using-managed-identity) Du bör sedan kunna aktivera analysarkivet på ditt konto.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Återspeglas borttagnings- och uppdateringsåtgärder i transaktionsarkivet i analysarkivet?

Ja, borttagningar och uppdateringar av data i transaktionslagret återspeglas i analysarkivet. Du kan konfigurera Time to Live (TTL) i containern så att historiska data inkluderas så att analysarkivet behåller alla versioner av objekt som uppfyller TTL-kriterierna för analys. Mer information [finns i översikten över TTL](analytical-store-introduction.md#analytical-ttl) för analys.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Kan jag ansluta till analysarkiv från andra analysmotorer än Azure Synapse Analytics?

Du kan bara komma åt och köra frågor mot analysarkivet med hjälp av de olika körningar som tillhandahålls av Azure Synapse Analytics. Du kan bara köra frågor mot och analysera analysarkivet med:

* Synapse Spark med fullständigt stöd för Scala, Python, SparkSQL och C#. Synapse Spark är centralt för datateknik och vetenskapsscenarier
* Serverlös SQL-pool med T-SQL-språk och stöd för välbekanta BI-verktyg (till Power BI Premium osv.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Kan jag ansluta till analysarkiv från Synapse SQL etablerat?

För stunden går det inte att komma åt analysarkivet från Synapse SQL etableras.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Kan jag skriva tillbaka frågeaggregeringsresultaten från Synapse tillbaka till analysarkivet?

Analysarkiv är ett skrivskyddade arkiv i en Azure Cosmos DB container. Därför kan du inte direkt skriva tillbaka sammansättningsresultatet till analysarkivet, utan kan skriva dem till Azure Cosmos DB-transaktionslagret i en annan container, som senare kan användas som betjänande lager.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Är den autosynkrona replikeringen från transaktionslager till analysarkivet asynkron eller synkron och vilka är svarstiderna?

Svarstiden för automatisk synkronisering är vanligtvis inom 2 minuter. I fall med en databas med delat dataflöde med ett stort antal containrar kan svarstiden för automatisk synkronisering av enskilda containrar vara högre och ta upp till 5 minuter. Vi vill veta mer om hur den här svarstiden passar dina scenarier. För att göra det kan du kontakta [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Finns det några scenarier där objekten från transaktionslagret inte sprids automatiskt till analysarkivet?

Om specifika objekt i containern bryter [mot det väldefinierade schemat för analys](analytical-store-introduction.md#analytical-schema)tas de inte med i analysarkivet. Om du har scenarier som blockerats av ett väldefinierat schema för analys skickar du ett [e-postmeddelande Azure Cosmos DB om](mailto:cosmosdbsynapselink@microsoft.com) hjälp.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Kan jag partitionera data i analysarkiv på ett annat sätt än i transaktionsarkivet?

Data i analysarkiv partitioneras baserat på den vågräta partitioneringen i shards i transaktionslagret. För närvarande kan du inte välja en annan partitioneringsstrategi för analysarkivet.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Kan jag anpassa eller åsidosätta hur transaktionsdata omvandlas till kolumnformat i analysarkivet?

För närvarande kan du inte transformera dataobjekten när de sprids automatiskt från transaktionslagret till analysarkivet. Om du har scenarier som blockerats av den här begränsningen skickar du ett [e-postmeddelande Azure Cosmos DB teamet](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>Har Terraform stöd för analytisk lagring?

Terraform har för närvarande inte stöd för analytiska lagringscontainrar. Läs mer i [GitHub-problem för Terraform](https://github.com/hashicorp/terraform/issues).

## <a name="analytical-time-to-live-ttl"></a>TTL (Analytical Time to Live)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Stöds TTL för analysdata på både container- och objektnivå?

För närvarande kan TTL för analysdata bara konfigureras på containernivå och det finns inget stöd för att ange TTL för analys på objektnivå.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Kan jag ändra till ett annat värde senare när jag har Azure Cosmos DB TTL på containernivå?

Ja, TTL för analys kan uppdateras till ett giltigt värde. Mer information [om TTL för](analytical-store-introduction.md#analytical-ttl) analys finns i artikeln TTL för analys.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Kan jag uppdatera eller ta bort ett objekt från analysarkivet när det har tagits bort från transaktionslagret?

Alla transaktionsuppdateringar och borttagningar kopieras till analysarkivet, men om objektet har rensats från transaktionslagret kan det inte uppdateras i analysarkivet. Mer information finns i artikeln [TTL för analys.](analytical-store-introduction.md#analytical-ttl)

## <a name="billing"></a>Fakturering

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Vad är faktureringsmodellen för Azure Synapse Link för Azure Cosmos DB?

Faktureringsmodellen för Azure Synapse Link innehåller kostnader som uppstår vid användning av Azure Cosmos DB-analysarkivet och Synapse-körningen. Mer information finns i artikeln [om Azure Cosmos DB priser för analysarkiv](analytical-store-introduction.md#analytical-store-pricing) och Azure Synapse Analytics [prisartiklar.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Hur påverkas faktureringen om jag aktiverar Synapse Link i mitt Azure Cosmos DB databaskonto?

Inga. Du debiteras bara när du skapar en analysarkivaktiverad container och börjar läsa in data.


## <a name="security"></a>Säkerhet

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>På vilka sätt kan du autentisera med analysarkivet?

Autentisering med analysarkivet är samma som ett transaktionslager. För en viss databas kan du autentisera med den primära eller skrivskyddade nyckeln. Du kan använda länkad tjänst i Azure Synapse Studio för att förhindra att Azure Cosmos DB i Spark-anteckningsböckerna. Åtkomst till den här länkade tjänsten är tillgänglig för alla som har åtkomst till arbetsytan.

När du använder Synapse-serverlösa SQL-pooler kan du fråga Azure Cosmos DB-analysarkivet genom att skapa SQL-autentiseringsuppgifter som lagrar kontonycklarna i förväg och referera till dem i OPENROWSET-funktionen. Mer information finns i artikeln Query with a serverless SQL pool in Azure Synapse Link (Fråga med en [serverlös SQL-pool Azure Synapse Link).](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

## <a name="synapse-run-times"></a>Synapse-körningar

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Vilka Synapse-körningar stöds för närvarande för åtkomst Azure Cosmos DB analysarkiv?

|Azure Synapse runtime |Aktuellt stöd |
|---------|---------|
|Azure Synapse Spark-pooler | Läsa, skriva (via transaktionslager), tabell, tillfällig vy |
|Azure Synapse serverlös SQL-pool    | Läsa, visa |
|Azure Synapse SQL-etablering   |  Inte tillgängligt |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Synkroniserar Azure Synapse Spark-tabeller med mina Azure Synapse SQL-pooltabeller på samma sätt som med Azure Data Lake?

Den här funktionen är inte tillgänglig för närvarande.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Kan jag göra Spark-strukturerad strömning från analysarkiv?

Stöd för strukturerad strömning i Spark Azure Cosmos DB för närvarande implementeras med ändringsflödesfunktionen i transaktionslagret och stöds inte ännu från analysarkivet.

### <a name="is-streaming-supported"></a>Stöds strömning?

Vi stöder inte strömning av data från analysarkivet.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Hur kan jag Azure Synapse i Azure Synapse Studio om jag är ansluten till en Azure Cosmos DB container med analysarkivet aktiverat?

En Azure Cosmos DB container som är aktiverad med analysarkiv har följande ikon:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB container aktiverad med analysarkivikon":::

En container för transaktionslager representeras med följande ikon:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB container aktiverad med transaktionsarkivikonen":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Hur skickar du Azure Cosmos DB autentiseringsuppgifter från Azure Synapse Studio?

För Azure Cosmos DB autentiseringsuppgifter skickas när den länkade tjänsten skapas av den användare som har åtkomst till de Azure Cosmos DB databaserna. Åtkomst till butiken är tillgänglig för andra användare som har åtkomst till arbetsytan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer [om fördelarna med Azure Synapse Link](synapse-link.md#synapse-link-benefits)

* Lär dig mer [om integreringen mellan Azure Synapse Link och Azure Cosmos DB](synapse-link.md#synapse-link-integration).
