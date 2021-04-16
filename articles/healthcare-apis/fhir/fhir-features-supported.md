---
title: FHIR-funktioner som stöds i Azure – Azure API for FHIR
description: Den här artikeln förklarar vilka funktioner i FHIR-specifikationen som implementeras i Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530633"
---
# <a name="features"></a>Funktioner

Azure API for FHIR en fullständigt hanterad distribution av Microsoft FHIR Server för Azure. Servern är en implementering av [FHIR-standarden.](https://hl7.org/fhir) Det här dokumentet innehåller de viktigaste funktionerna i FHIR-servern.

## <a name="fhir-version"></a>FHIR-version

Senaste versionen som stöds: `4.0.1`

Tidigare versioner som stöds för närvarande är: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| läsa                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| uppdatera med optimistisk låsning | Ja       | Ja       | Ja       |                                                     |
| uppdatera (villkorsstyrd)           | Ja       | Ja       | Ja       |                                                     |
| Patch                          | Inga        | Inga        | Inga        |                                                     |
| delete                         | Ja       | Ja       | Ja       |  Se Anmärkning nedan.                                   |
| ta bort (villkorlig)           | Inga        | Inga        | Inga        |                                                     |
| historik                        | Ja       | Ja       | Ja       |                                                     |
| skapa                         | Ja       | Ja       | Ja       | Stöd för både POST/PUT                               |
| skapa (villkorlig)           | Ja       | Ja       | Ja       | Problem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| sök                         | Delvis   | Delvis   | Delvis   | Se sökavsnittet nedan.                           |
| länkad sökning                 | Delvis       | Yes       | Delvis   | Se anmärkning 2 nedan.                                   |
| omvänd länkad sökning         | Delvis       | Yes       | Delvis   | Se anmärkning 2 nedan.                                   |
| funktioner                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| Transaktionen                    | Inga        | Ja       | Inga        |                                                     |
| Personsökning                         | Delvis   | Delvis   | Delvis   | `self` och `next` stöds                     |
| Mellanhänder                 | Inga        | Inga        | Inga        |                                                     |

> [!Note]
> Borttagning som definieras av FHIR-specifikationen kräver att efterföljande icke-versionsspecifika läsningar av en resurs returnerar en 410 HTTP-statuskod och att resursen inte längre hittas genom sökning. Med Azure API for FHIR kan du också ta bort resursen helt (inklusive all historik). Om du vill ta bort resursen helt kan du skicka parameterinställningarna `hardDelete` till true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Om du inte skickar den här parametern eller `hardDelete` anger till false är de historiska versionerna av resursen fortfarande tillgängliga.


 **Anmärkning 2**
* Lägger till MVP-stöd för kedjad och omvänd kedjad FHIR-sökning i CosmosDB. 

  I Azure API for FHIR och FHIR-servern med öppen källkod som backas upp av Cosmos är den kedjebaserade sökningen och omvänd kedjeked sökning en MVP-implementering. För att utföra länkad sökning Cosmos DB går implementeringen igenom sökuttrycket och utfärdar underfrågor för att lösa matchade resurser. Detta görs för varje nivå av uttrycket. Om en fråga returnerar fler än 100 resultat returneras ett fel. Som standard finns länkad sökning bakom en funktionsflagga. Om du vill använda den länkade sökningen Cosmos DB använder du rubriken `x-ms-enable-chained-search: true` . Mer information finns i [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Sök

Alla typer av sökparametrar stöds. 

| Sökparametertyp | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
| Antal                | Ja       | Ja       | Ja       |         |
| Date/DateTime         | Ja       | Ja       | Ja       |         |
| Sträng                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referens             | Ja       | Ja       | Ja       |         |
| Sammansatt             | Ja       | Ja       | Ja       |         |
| Kvantitet              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Särskilda               | Inga        | Inga        | Inga        |         |


| Modifierare             | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:[type]` (referens)  | Ja       | Ja       | Ja       |         |
|`:not`                 | Ja       | Ja       | Ja       |         |
|`:below` (uri)         | Ja       | Ja       | Ja       |         |
|`:above` (uri)         | Inga        | Inga        | Inga        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (token)          | Inga        | Inga        | Inga        |         |
|`:below` (token)       | Inga        | Inga        | Inga        |         |
|`:above` (token)       | Inga        | Inga        | Inga        |         |
|`:not-in` (token)      | Inga        | Inga        | Inga        |         |

| Vanlig sökparameter | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_type`                 | Ja       | Ja       | Ja       | Problem [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_profile`              | Delvis   | Delvis   | Delvis   | Stöds i STU3. Om du skapade databasen **efter** den 20 februari 2021 har du även stöd i R4. Vi arbetar med att _profile databaser som skapats före den 20 februari 2021. |
| `_text`                 | Inga        | Inga        | Inga        |         |
| `_content`              | Inga        | Inga        | Inga        |         |
| `_has`                  | Inga        | Inga        | Inga        |         |
| `_query`                | Inga        | Inga        | Inga        |         |
| `_filter`               | Inga        | Inga        | Inga        |         |

| Parametrar för sökresultat | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Ja       | Ja       | Ja       | Problem [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Ja       | Ja       | Ja       | `_count` är begränsat till 1 000 tecken. Om det är inställt på högre än 1 000 returneras endast 1 000 och en varning returneras i paketet. |
| `_include`              | Ja       | Ja       | Ja       |Inkluderade objekt är begränsade till 100. Inkludera på PaaS och OSS på Cosmos DB inkluderar inte :iterate support.|
| `_revinclude`           | Ja       | Ja       | Ja       | Inkluderade objekt är begränsade till 100. Inkludera på PaaS och OSS på Cosmos DB [inkluderar inte :iterate support](https://github.com/microsoft/fhir-server/issues/1313). Problem [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Delvis   | Delvis   | Delvis   | `_summary=count` stöds |
| `_total`                | Delvis   | Delvis   | Delvis   | `_total=none` och `_total=accurate`      |
| `_sort`                 | Delvis   | Delvis   | Delvis   |   `_sort=_lastUpdated` stöds       |
| `_contained`            | Inga        | Inga        | Inga        |         |
| `containedType`         | Inga        | Inga        | Inga        |         |
| `_score`                | Inga        | Inga        | Inga        |         |

## <a name="extended-operations"></a>Utökade åtgärder

Alla åtgärder som stöds som utökar RESTful-API:et.

| Sökparametertyp | Stöds – PaaS | Stöds – OSS (SQL) | Stöds – OSS (Cosmos DB) | Kommentar |
|------------------------|-----------|-----------|-----------|---------|
| $export (hela systemet) | Ja       | Ja       | Ja       |         |
| Patient/$export        | Ja       | Ja       | Ja       |         |
| Grupp/$export          | Ja       | Ja       | Ja       |         |
| $convert-data          | Ja       | Ja       | Ja       |         |


## <a name="persistence"></a>Bevarande

Microsoft FHIR-servern har en modul för pluggbar beständighet (se [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

För närvarande innehåller FHIR-serverns öppen källkod en implementering för [Azure Cosmos DB](../../cosmos-db/index-overview.md) och [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB är en globalt distribuerad flermodellsdatabas (SQL API, MongoDB API osv.). Den stöder olika [konsekvensnivåer.](../../cosmos-db/consistency-levels.md) Standarddistributionsmallen konfigurerar FHIR-servern med konsekvens, men konsekvensprincipen kan ändras (vanligtvis avslappnad) på begäran med `Strong` `x-ms-consistency-level` begärandehuvudet.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

FHIR-servern använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för åtkomstkontroll. Mer specifikt tillämpas rollbaserad åtkomstkontroll (RBAC) om konfigurationsparametern är inställd på , och alla begäranden (utom ) till FHIR-servern måste ha begärandehuvudet `FhirServer:Security:Enabled` `true` `/metadata` `Authorization` inställt på `Bearer <TOKEN>` . Token måste innehålla en eller flera roller enligt definitionen i `roles` anspråket. En begäran tillåts om token innehåller en roll som tillåter den angivna åtgärden på den angivna resursen.

För närvarande tillämpas tillåtna åtgärder för en viss roll *globalt på* API:et.

## <a name="service-limits"></a>Tjänstbegränsningar

* [**Enheter för begäran (RU:er)**](../../cosmos-db/concepts-limits.md) – Du kan konfigurera upp till 10 000 RU:er i portalen för Azure API for FHIR. Du behöver minst 400 RU:er eller 40 RU:er/GB, beroende på vilket som är större. Om du behöver fler än 10 000 RU:er kan du skapa en supportbiljett för att öka detta. Det maximala antalet är 1 000 000.

* **Samtidiga** anslutningar **och instanser** – Som standard har du fem samtidiga anslutningar på två instanser i klustret (totalt 10 samtidiga begäranden). Om du tror att du behöver fler samtidiga begäranden öppnar du en supportbegäran med information om dina behov.

* **Paketstorlek** – Varje paket är begränsat till 500 objekt.

* **Datastorlek** – Data/dokument måste vara något mindre än 2 MB vardera.

## <a name="performance-expectations"></a>Prestandaförväntningar

Systemets prestanda beror på antalet RU:er, samtidiga anslutningar och vilken typ av åtgärder du utför (Put, Post osv.). Nedan visas några allmänna intervall för vad du kan förvänta dig baserat på konfigurerade RU:er. I allmänhet skalas prestanda linjärt med en ökning av RU:er:

| Antal RU:er | Resurser/sek |    Maximalt lagringsutrymme (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1 000    | 100-150       |      25  |
| 10 000   | 225-400       |      250  |
| 100 000  | 2,500-4,000   |      2 500  |

Obs! Cosmos DB krav är att det finns ett minsta dataflöde på 40 RU/s per GB lagringsutrymme. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst om de FHIR-funktioner som stöds i Azure API for FHIR. Distribuera sedan Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)
