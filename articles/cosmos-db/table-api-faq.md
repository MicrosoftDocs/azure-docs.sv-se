---
title: Vanliga frågor och svar om Tabell-API i Azure Cosmos DB
description: Få svar på vanliga frågor om Tabell-API i Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341248"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Vanliga frågor och svar om Tabell-API i Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB Tabell-API finns i [Azure Portal](https://portal.azure.com) först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB Tabell-API-konto till din Azure-prenumeration och sedan lägga till tabeller i ditt konto. Du hittar de språk som stöds och de kopplade snabb starterna i [introduktionen till Azure Cosmos DB tabell-API](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Tabell-API i Azure Cosmos DB vs Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>På vilka punkter är inte tabell-API:et identiskt med Azure Table Storage-beteendet?

Det finns vissa beteende skillnader som användare kommer från Azure Table Storage som vill skapa tabeller med Azure Cosmos DB Tabell-API bör vara medvetna om:

* Azure Cosmos DB Tabell-API använder en reserverad kapacitets modell för att säkerställa garanterad prestanda men det innebär att en betalar för kapaciteten så fort tabellen skapas, även om kapaciteten inte används. Med Azure Table Storage betalar det bara för den kapacitet som används. Detta hjälper till att förklara varför Tabell-API kan erbjuda ett 10 MS Read-och 15 MS-Skriv-SLA på 99 percentilen medan Azure Table Storage erbjuder ett service avtal på 10 sekunder. Men till följd, med Tabell-API tabeller, även tomma tabeller utan begär Anden, kostar pengar för att se till att kapaciteten är tillgänglig för att hantera begär anden till dem enligt SLA som erbjuds av Azure Cosmos DB.

* Frågeresultat som returneras av Tabell-API sorteras inte i partitionsnyckel eller rad nyckel ordning som de är i Azure Table Storage.

* Rad nycklar kan bara vara upp till 255 byte.

* Batchar kan bara ha upp till 2 MB.

* CORS stöds inte för närvarande.

* Tabell namn i Azure Table Storage är inte Skift läges känsliga, men de är i Azure Cosmos DB Tabell-API.

* Några av Azure Cosmos DBs interna format för kodnings information, till exempel binära fält, är för närvarande inte lika effektiva som en sådan. Detta kan därför orsaka oväntade begränsningar av data storleken. För närvarande kan till exempel en inte använda en fullständig MB av en tabell enhet för att lagra binära data eftersom kodningen ökar data storleken.

* Enhetens egenskaps namn "ID" stöds inte för närvarande.

* TableQuery-TakeCount är inte begränsat till 1000.

* I termer av REST API finns det ett antal slut punkter/frågealternativ som inte stöds av Azure Cosmos DB Tabell-API:

  | Rest-metod (er) | REST-slutpunkt/frågealternativ | Dokument-URL: er | Förklaring |
  | ------------| ------------- | ---------- | ----------- |
  | HÄMTA, PLACERA | `/?restype=service@comp=properties`| [Ange tabell tjänst egenskaper](/rest/api/storageservices/set-table-service-properties) och [Hämta Table service-egenskaper](/rest/api/storageservices/get-table-service-properties) | Den här slut punkten används för att ange CORS-regler, Storage Analytics-konfiguration och loggnings inställningar. CORS stöds för närvarande inte och analys och loggning hanteras på ett annat sätt i Azure Cosmos DB än Azure Storage tabeller |
  | SÄTT | `/<table-resource-name>` | [CORS-begäran för CORS-tabellen](/rest/api/storageservices/preflight-table-request) | Detta är en del av CORS som Azure Cosmos DB för närvarande inte stöder. |
  | GET | `/?restype=service@comp=stats` | [Hämta Table service stats](/rest/api/storageservices/get-table-service-stats) | Innehåller information om hur snabbt data replikeras mellan primär och sekundär. Detta behövs inte i Cosmos DB eftersom replikeringen är en del av skrivningar. |
  | HÄMTA, PLACERA | `/mytable?comp=acl` | [Hämta tabell-ACL](/rest/api/storageservices/get-table-acl) och [Ange tabell-ACL](/rest/api/storageservices/set-table-acl) | Detta hämtar och anger de lagrade åtkomst principerna som används för att hantera signaturer för delad åtkomst (SAS). Även om SAS stöds, är de inställda och hanterade på olika sätt. |

* Azure Cosmos DB Tabell-API stöder bara JSON-formatet, inte ATOM.

* Medan Azure Cosmos DB stöder signaturer för delad åtkomst (SAS) finns det vissa principer som inte stöds, särskilt de som är relaterade till hanterings åtgärder, till exempel rätten att skapa nya tabeller.

* För .NET SDK är det särskilt vissa klasser och metoder som Azure Cosmos DB för närvarande inte stöder.

  | Klass | Metoden stöds inte |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (den här klassen är föråldrad) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Andra vanliga frågor och svar

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Behöver jag ett nytt SDK för att använda Tabell-API?

Nej, befintliga SDK: er för lagring bör fortfarande fungera. Vi rekommenderar dock att man alltid får de senaste SDK: erna för bästa möjliga support och i många fall överlägsna prestanda. Se listan över tillgängliga språk i [introduktionen till Azure Cosmos DB tabell-API](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Vilken anslutnings sträng jag behöver använda för att ansluta till Tabell-API?

Anslutnings strängen är:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Du kan hämta anslutnings strängen från sidan anslutnings sträng i Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hur gör jag för att Åsidosätt konfigurations inställningarna för begär ande alternativen i .NET SDK för Tabell-API?

Vissa inställningar hanteras på CreateCloudTableClient-metoden och andra via app.config i avsnittet appSettings i klient programmet. Information om konfigurations inställningar finns i [Azure Cosmos DB funktioner](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Finns det några ändringar för kunder som använder befintliga SDK: er för Azure Table Storage?

Inga. Det finns inga ändringar för befintliga eller nya kunder som använder befintliga SDK: er för Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hur gör jag för att Visa tabell data som lagras i Azure Cosmos DB för användning med Tabell-API?

Du kan använda Azure Portal för att söka i data. Du kan också använda Tabell-APIs koden eller de verktyg som beskrivs i nästa svar.

### <a name="which-tools-work-with-the-table-api"></a>Vilka verktyg fungerar med Tabell-API?

Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Verktyg med flexibiliteten att ta en anslutnings sträng i det tidigare formatet kan stödja den nya Tabell-API. En lista med tabell verktyg finns på sidan [Azure Storage klient verktyg](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidigheten i åtgärder kontrollerade?

Ja, optimistisk samtidighet tillhandahålls via användning av ETag-mekanismen.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds OData-fråge modellen för entiteter?

Ja, Tabell-API stöder OData-frågor och LINQ-frågor.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan jag ansluta till Azure Table Storage och Azure Cosmos DB Tabell-API sida vid sida i samma program?

Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient, vart och ett som pekar på sin egen URI via anslutnings strängen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hur gör jag för att migrera ett befintligt Azure Table Storage-program till det här erbjudandet?

[AzCopy](../storage/common/storage-use-azcopy-v10.md) och [verktyget Azure Cosmos db data migration](import-data.md) stöds båda.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur ökar expansionen av lagrings storleken för den här tjänsten om jag exempelvis börjar med *n* GB data och mina data växer till 1 TB över tid?

Azure Cosmos DB är utformad för att ge obegränsad lagrings kapacitet genom att använda horisontell skalning. Tjänsten kan övervaka och effektivt öka lagringen.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hur gör jag för att övervaka Tabell-API erbjudandet?

Du kan använda fönstret Tabell-API **mått** för att övervaka begär Anden och lagrings användning.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur gör jag för att beräkna det data flöde jag behöver?

Du kan använda kapacitets uppskattningen för att beräkna de TableThroughput som krävs för åtgärderna. Mer information finns i [Beräkna enheter för programbegäran och data lagring](https://www.documentdb.com/capacityplanner). I allmänhet kan du Visa din entitet som JSON och ange värden för dina åtgärder.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan jag använda Tabell-API SDK lokalt med emulatorn?

Inte just nu.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mitt befintliga program arbeta med Tabell-API?

Ja, samma API stöds.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Måste jag migrera mina befintliga Azure Table Storage-program till SDK om jag inte vill använda Tabell-API funktionerna?

Nej, du kan skapa och använda befintliga Azure Table Storage-tillgångar utan avbrott av något slag. Men om du inte använder Tabell-API kan du inte dra nytta av det automatiska indexet, ytterligare konsekvens alternativ eller global distribution.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hur gör jag för att du lägga till replikering av data i Tabell-API över mer än en region i Azure?

Du kan använda Azure Cosmos DB portalens [globala replikeringsinställningar](tutorial-global-distribution-sql-api.md#portal) för att lägga till regioner som är lämpliga för ditt program. Om du vill utveckla ett globalt distribuerat program bör du även lägga till programmet med PreferredLocation-informationen inställd på den lokala regionen för att ge låg Läs fördröjning.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hur gör jag för att ändra primär Skriv region för kontot i Tabell-API?

Du kan använda fönstret Azure Cosmos DB global replikeringsprovider för att lägga till en region och sedan redundansväxla till den region som krävs. Instruktioner finns i [utveckla med Azure Cosmos DB konton för flera regioner](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur gör jag för att konfigurera mina önskade Läs regioner för låg latens när jag distribuerar mina data?

Använd nyckeln PreferredLocation i app.config-filen för att läsa från den lokala platsen. För befintliga program genererar Tabell-API ett fel om LocationMode har angetts. Ta bort koden eftersom Tabell-API hämtar informationen från app.configs filen. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hur ska jag tänka på konsekvens nivåer i Tabell-API?

Azure Cosmos DB ger välgrundade kompromisser mellan konsekvens, tillgänglighet och svars tid. Azure Cosmos DB erbjuder fem konsekvens nivåer för att Tabell-API utvecklare, så du kan välja rätt konsekvens modell på tabell nivå och göra enskilda förfrågningar vid frågor mot data. När en klient ansluter kan den ange en konsekvens nivå. Du kan ändra nivån via consistencyLevel-argumentet för CreateCloudTableClient.

Tabell-API ger låg latens läsningar med "Läs dina egna skrivningar" med begränsad föråldrad konsekvens som standard. Mer information finns i [konsekvens nivåer](consistency-levels.md).

Som standard ger Azure Table Storage stark konsekvens inom en region och eventuell konsekvens på de sekundära platserna.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Erbjuder Azure Cosmos DB Tabell-API fler konsekvens nivåer än Azure Table Storage?

Ja, mer information om hur du drar nytta av den distribuerade typen av Azure Cosmos DB finns i [konsekvens nivåer](consistency-levels.md). Eftersom garantier tillhandahålls för konsekvens nivåerna kan du använda dem med förtroende.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hur lång tid tar det att replikera data när global distribution har Aktiver ATS?

Azure Cosmos DB allokerar data varaktigt i den lokala regionen och skickar data till andra regioner omedelbart på några millisekunder. Replikeringen är endast beroende av tur och retur-tid för data centret. Mer information om den globala distributions funktionen i Azure Cosmos DB finns i [Azure Cosmos DB: en globalt distribuerad databas tjänst på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvens nivån för läsning av begäran ändras?

Med Azure Cosmos DB kan du ange konsekvens nivån på behållar nivån (i tabellen). Med hjälp av .NET SDK kan du ändra nivån genom att ange värdet för TableConsistencyLevel-nyckeln i app.config-filen. Möjliga värden är: stark, begränsad föråldrad, session, konsekvent prefix och eventuell. Mer information finns i [justerbara data konsekvens nivåer i Azure Cosmos DB](consistency-levels.md). Den viktiga idén är att du inte kan ställa in konsekvens nivån för begäran på mer än inställningen för tabellen. Du kan till exempel inte ange konsekvens nivån för tabellen vid fel och konsekvens nivån för begäran på stark nivå.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hur hanterar Tabell-API redundans om en region kraschar?

Tabell-API utnyttjar den globalt distribuerade plattformen av Azure Cosmos DB. För att säkerställa att ditt program kan tolerera Data Center stillestånd, aktiverar du minst en region för kontot i Azure Cosmos DB Portal [som utvecklar med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ställa in prioriteten för regionen genom att använda portalen som [utvecklar med Azure Cosmos DB konton i flera regioner](high-availability.md).

Du kan lägga till så många regioner som du vill för kontot och styra vart det kan redundansväxla till genom att ange en växlings prioritet. Om du vill använda databasen måste du ange ett program där. När du gör det kommer kunderna inte att uppleva nedtid. Den [senaste .NET Client SDK](table-sdk-dotnet.md) är automatiskt värdar men de andra SDK: erna är inte. Det innebär att den kan identifiera den region som är nere och automatiskt växla över till den nya regionen.

### <a name="is-the-table-api-enabled-for-backups"></a>Är Tabell-API aktiverat för säkerhets kopieringar?

Ja, Tabell-API utnyttjar plattformen för Azure Cosmos DB för säkerhets kopiering. Säkerhets kopieringar görs automatiskt. Mer information finns i [säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Har Tabell-API indexera alla attribut för en entitet som standard?

Ja, alla attribut för en entitet indexeras som standard. Mer information finns i [Azure Cosmos DB: indexerings principer](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betyder det att jag inte behöver skapa fler än ett index för att uppfylla frågorna?

Ja, Azure Cosmos DB Tabell-API tillhandahåller automatisk indexering av alla attribut utan någon schema definition. Den här automationen frigör utvecklare för att fokusera på programmet i stället för att skapa och hantera index. Mer information finns i [Azure Cosmos DB: indexerings principer](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexerings principen?

Ja, du kan ändra indexerings principen genom att tillhandahålla index definitionen. Du måste koda och kringgå inställningarna korrekt.

För non-.NET SDK: er kan indexerings principen bara ställas in i portalen på **datautforskaren**, navigera till den speciella tabell som du vill ändra och sedan gå till **skalnings & inställningar**->indexerings princip, göra önskade ändringar och sedan **Spara**.

I .NET SDK kan den skickas i app.config-filen:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB som plattform verkar ha många funktioner, till exempel sortering, agg regeringar, hierarki och andra funktioner. Kommer du att lägga till dessa funktioner i Tabell-API?

Tabell-API tillhandahåller samma fråge funktioner som Azure Table Storage. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Mer information finns i [SQL-frågor](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>När ska jag ändra TableThroughput för Tabell-API?

Du bör ändra TableThroughput när något av följande villkor gäller:

* Du utför en data extrahering, transformering och inläsning (ETL), eller så vill du ladda upp en stor mängd data på kort tid.
* Du behöver mer data flöde från behållaren eller från en uppsättning behållare på Server delen. Du ser till exempel att det använda data flödet är mer än det etablerade data flödet och att du får en begränsning. Mer information finns i [set genomflödet for Azure Cosmos containers](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan jag skala upp eller ned data flödet i Tabell-APIs tabellen?

Ja, du kan använda den Azure Cosmos DB portalens skalnings fönster för att skala data flödet. Mer information finns i [set genomflödet](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standard uppsättning TableThroughput för nyligen etablerade tabeller?

Ja, om du inte åsidosätter TableThroughput via app.config och inte använder en i förväg skapad behållare i Azure Cosmos DB, skapar tjänsten en tabell med data flödet på 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Finns det någon förändring av prissättningen för befintliga kunder i Azure Table Storage-tjänsten?

Inga. Det finns ingen ändring i priset för befintliga Azure Table Storage-kunder.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hur beräknas priset för Tabell-API?

Priset beror på de allokerade TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hur gör jag för att hanterar du en hastighets begränsning för tabellerna i Tabell-API erbjudande?

Om begär ande frekvensen är större än kapaciteten för det etablerade data flödet för den underliggande behållaren eller en uppsättning behållare får du ett fel meddelande och SDK: n försöker anropa anropet igen genom att använda principen för återförsök.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Varför måste jag välja ett data flöde förutom PartitionKey och RowKey för att kunna dra nytta av Tabell-API Azure Cosmos DB?

Azure Cosmos DB anger ett standard flöde för din behållare om du inte anger någon i app.config-filen eller via portalen.

Azure Cosmos DB ger garantier för prestanda och latens, med övre gränser för åtgärd. Den här säkerheten är möjlig när motorn kan genomdriva styrning på klientens verksamhet. Genom att ange TableThroughput ser du till att du får garanterat data flöde och svars tid, eftersom plattformen reserverar den här kapaciteten och garanterar driften.

Genom att använda data flödes specifikationen kan du elastiskt ändra den för att dra nytta av säsongs beroende i ditt program, uppfylla data flödes behoven och spara kostnaderna.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table Storage har varit billigt för mig, eftersom jag bara betalar för att lagra data, och jag frågar sällan. Azure Cosmos DB Tabell-API-erbjudandet verkar vara att debitera mig även om jag inte har genomfört en enskild transaktion eller sparat något. Kan du förklara?

Azure Cosmos DB är utformat för att vara ett globalt distribuerat, SLA-baserat system med garantier för tillgänglighet, svars tid och data flöde. När du reserverar data flödet i Azure Cosmos DB, är det garanterat, till skillnad från data flödet för andra system. Azure Cosmos DB tillhandahåller ytterligare funktioner som kunder har begärt, till exempel sekundära index och global distribution.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en fullständig kvot "meddelande (som anger att en partition är full) när jag matar in data i Azure Table Storage. Med Tabell-API får jag det här meddelandet. Är det här erbjudandet som begränsar mig och tvingar mig att ändra mitt befintliga program?

Azure Cosmos DB är ett SLA-baserat system som ger obegränsad skalning, med garantier för svars tid, data flöde, tillgänglighet och konsekvens. Säkerställ att data storleken och indexet kan hanteras och vara skalbart för att garantera förstklassig prestanda. Gränsen på 20 GB för antalet entiteter eller objekt per partitionsnyckel är att se till att vi ger fantastisk sökning och fråga prestanda. För att säkerställa att ditt program skalar bra, även för Azure Storage, rekommenderar vi att du *inte* skapar en aktiv partition genom att lagra all information i en partition och fråga den.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Så PartitionKey och RowKey krävs fortfarande med Tabell-API?

Ja. Eftersom området i Tabell-API liknar det för Azure Table Storage SDK, är partitionsnyckel ett effektivt sätt att distribuera data. Rad nyckeln är unik i den partitionen. Rad nyckeln måste finnas och får inte vara null som i standard-SDK. Längden på RowKey är 255 byte och längden på PartitionKey är 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Vilka är fel meddelandena för Tabell-API?

Azure Table Storage och Azure Cosmos DB Tabell-API använda samma SDK: er så att de flesta av felen blir desamma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Varför får jag en begränsning när jag försöker skapa flera tabeller efter varandra i Tabell-API?

Azure Cosmos DB är ett SLA-baserat system som tillhandahåller svars tid, data flöde, tillgänglighet och konsekvens garantier. Eftersom det är ett allokerat system reserverar den resurser för att garantera dessa krav. Den snabba takten med att skapa tabeller identifieras och begränsas. Vi rekommenderar att du tittar på hur många tabeller som skapas och sänker dem till mindre än 5 per minut. Kom ihåg att Tabell-API är ett allokerat system. Den tidpunkt då du etablerar det börjar du betala för det.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur gör jag för att ge feedback om SDK eller buggar?

Du kan dela din feedback på något av följande sätt:

* [Röstanvändare](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Sidan Microsoft Q&en fråga](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmerings frågor. Se till att din fråga är [i ämne](https://stackoverflow.com/help/on-topic) och [Ange så många detaljer som möjligt, så att frågan blir tydlig och kan besvaras](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Nästa steg

* [Bygg en Tabell-API-app med .NET SDK och Azure Cosmos DB](create-table-dotnet.md)
* [Bygg en Java-app för att hantera Azure Cosmos DB Tabell-API data](create-table-java.md)