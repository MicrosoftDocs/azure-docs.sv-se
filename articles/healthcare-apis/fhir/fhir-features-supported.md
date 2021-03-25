---
title: FHIR funktioner som stöds i Azure – Azure API för FHIR
description: I den här artikeln beskrivs vilka funktioner i FHIR-specifikationen som implementeras i Azure API för FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 9bd61d65d6d64dac6081d3491deb8a15efc4a45b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048427"
---
# <a name="features"></a>Funktioner

Azure API för FHIR tillhandahåller en fullständigt hanterad distribution av Microsoft FHIR-servern för Azure. Servern är en implementering av [FHIR](https://hl7.org/fhir) -standarden. Det här dokumentet listar huvud funktionerna i FHIR-servern.

## <a name="fhir-version"></a>FHIR-version

Senaste version som stöds: `4.0.1`

Tidigare versioner som också stöds är: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| läsa                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| uppdatera med optimistisk låsning | Ja       | Ja       | Ja       |                                                     |
| uppdatering (villkorligt)           | Ja       | Ja       | Ja       |                                                     |
| 9.0a                          | Inga        | Inga        | Inga        |                                                     |
| delete                         | Ja       | Ja       | Ja       |  Se kommentaren nedan.                                   |
| ta bort (villkorligt)           | Inga        | Inga        | Inga        |                                                     |
| historik                        | Ja       | Ja       | Ja       |                                                     |
| skapa                         | Ja       | Ja       | Ja       | Stöd för både efter-och-placering                               |
| Skapa (villkorlig)           | Ja       | Ja       | Ja       | Problem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| sök                         | Delvis   | Delvis   | Delvis   | Se avsnittet Sök nedan.                           |
| kedjad sökning                 | Ja       | Ja       | Delvis   | Se anmärkning 2 nedan.                                   |
| omvänd länkad sökning         | Ja       | Ja       | Delvis   | Se anmärkning 2 nedan.                                   |
| funktioner                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| transaktionen                    | Inga        | Ja       | Inga        |                                                     |
| växlings                         | Delvis   | Delvis   | Delvis   | `self` och `next` stöds                     |
| mellanhand                 | Inga        | Inga        | Inga        |                                                     |

> [!Note]
> Borttagning som definieras av FHIR-specifikationen kräver att efter borttagningen, returnerar efterföljande icke-versions läsningar av en resurs en HTTP-statuskod på 410 och resursen hittas inte längre genom sökning. Med Azure API för FHIR kan du också helt ta bort (inklusive all historik) resursen. Om du vill ta bort resursen fullständigt kan du skicka en parameter inställning `hardDelete` till true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Om du inte skickar den här parametern eller anger `hardDelete` till false är den historiska versionen av resursen fortfarande tillgänglig.


 **Anmärkning 2**
* Lägger till MVP-stöd för kedjad och omvänd kedjad FHIR-sökning i CosmosDB. 

  I Azure API för FHIR och FHIR-servern med öppen källkod som backas upp av Cosmos, är den kedjade sökningen och den omvända länkade sökningen en MVP-implementering. Om du vill göra en kedjad sökning på Cosmos DB visar implementeringen Sök uttrycket och utfärdar under frågor för att lösa de matchade resurserna. Detta görs för varje nivå i uttrycket. Om en fråga returnerar fler än 100 resultat kommer ett fel att genereras. Som standard är den kedjade sökningen bakom en funktions flagga. Använd rubriken om du vill använda en kedjad sökning på Cosmos DB `x-ms-enable-chained-search: true` . Mer information finns i [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Sök

Alla typer av Sök parametrar stöds. 

| Typ av Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
| Antal                | Ja       | Ja       | Ja       |         |
| Datum/DateTime         | Ja       | Ja       | Ja       |         |
| Sträng                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referens             | Ja       | Ja       | Ja       |         |
| Sammansatt             | Ja       | Ja       | Ja       |         |
| Kvantitet              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Speciella               | Inga        | Inga        | Inga        |         |


| Modifierare             | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:[type]` förhållande  | Ja       | Ja       | Ja       |         |
|`:not`                 | Ja       | Ja       | Ja       |         |
|`:below` URI         | Ja       | Ja       | Ja       |         |
|`:above` URI         | Inga        | Inga        | Inga        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` åtkomsttokenbegäran          | Inga        | Inga        | Inga        |         |
|`:below` åtkomsttokenbegäran       | Inga        | Inga        | Inga        |         |
|`:above` åtkomsttokenbegäran       | Inga        | Inga        | Inga        |         |
|`:not-in` åtkomsttokenbegäran      | Inga        | Inga        | Inga        |         |

| Vanlig Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_type`                 | Ja       | Ja       | Ja       | Problem [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_profile`              | Delvis   | Delvis   | Delvis   | Stöds i STU3. Om du skapade din databas **efter** 20 februari 2021 har du även support i R4. Vi arbetar för att aktivera _profile på databaser som skapats före 20 februari 2021. |
| `_text`                 | Inga        | Inga        | Inga        |         |
| `_content`              | Inga        | Inga        | Inga        |         |
| `_has`                  | Inga        | Inga        | Inga        |         |
| `_query`                | Inga        | Inga        | Inga        |         |
| `_filter`               | Inga        | Inga        | Inga        |         |

| Sök Resultat parametrar | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Ja       | Ja       | Ja       | Problem [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Ja       | Ja       | Ja       | `_count` är begränsat till 1000 tecken. Om värdet är högre än 1000 kommer endast 1000 att returneras och en varning returneras i paketet. |
| `_include`              | Ja       | Ja       | Ja       |Inkluderade objekt är begränsade till 100. Ta med på PaaS och OSS på Cosmos DB omfattar inte: stöd för iterering.|
| `_revinclude`           | Ja       | Ja       | Ja       | Inkluderade objekt är begränsade till 100. Ta med på PaaS och OSS på Cosmos DB [omfattar inte: stöd för iterering](https://github.com/microsoft/fhir-server/issues/1313). Problem [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Delvis   | Delvis   | Delvis   | `_summary=count` stöds |
| `_total`                | Delvis   | Delvis   | Delvis   | `_total=none` och `_total=accurate`      |
| `_sort`                 | Delvis   | Delvis   | Delvis   |   `_sort=_lastUpdated` stöds       |
| `_contained`            | Inga        | Inga        | Inga        |         |
| `containedType`         | Inga        | Inga        | Inga        |         |
| `_score`                | Inga        | Inga        | Inga        |         |

## <a name="extended-operations"></a>Utökade åtgärder

Alla åtgärder som stöds och som utökar RESTful-API: et.

| Typ av Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|------------------------|-----------|-----------|-----------|---------|
| $export (hela systemet) | Ja       | Ja       | Ja       |         |
| Patient/$export        | Ja       | Ja       | Ja       |         |
| Grupp/$export          | Ja       | Ja       | Ja       |         |
| $convert-data          | Ja       | Ja       | Ja       |         |


## <a name="persistence"></a>Bevarande

Microsoft FHIR-servern har en pluggad beständig modul (se [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

För närvarande inkluderar FHIR-serverns kod med öppen källkod en implementering för [Azure Cosmos DB](../../cosmos-db/index-overview.md) och [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB är en globalt distribuerad multi-Model-databas (SQL API, MongoDB API osv.). Den har stöd för olika [konsekvens nivåer](../../cosmos-db/consistency-levels.md). Standardmallen för distribution konfigurerar FHIR-servern med `Strong` konsekvens, men konsekvens policyn kan ändras (i allmänhet avslappnadt) på en begäran med hjälp av begär `x-ms-consistency-level` ande huvudet.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

FHIR-servern använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för åtkomst kontroll. Särskild rollbaserade åtkomst kontroll (RBAC) tillämpas, om `FhirServer:Security:Enabled` konfigurations parametern har angetts till `true` och alla begär Anden (utom `/metadata` ) till FHIR-servern måste ha `Authorization` begär ande huvud inställt på `Bearer <TOKEN>` . Token måste innehålla en eller flera roller som definieras i `roles` anspråket. En begäran kommer att tillåtas om token innehåller en roll som tillåter den angivna åtgärden på den angivna resursen.

För närvarande tillämpas de tillåtna åtgärderna för en specifik roll *globalt* på API: et.

## <a name="service-limits"></a>Tjänstbegränsningar

* [**Ru: er (Request units)**](../../cosmos-db/concepts-limits.md) – du kan konfigurera upp till 10 000 ru: er i portalen för Azure API för FHIR. Du behöver minst 400 ru: er eller 10 ru: er/GB, beroende på vilket som är störst. Om du behöver mer än 10 000 ru: er kan du ställa in ett support ärende så att det ökar. Det högsta tillgängliga värdet är 1 000 000.

* **Samtidiga anslutningar** och **instanser** – som standard har du fem samtidiga anslutningar på två instanser i klustret (totalt 10 samtidiga förfrågningar). Om du tror att du behöver fler samtidiga förfrågningar kan du öppna ett support ärende med information om dina behov.

* **Paket storlek** -varje paket är begränsat till 500 objekt.

* **Data storlek** – data/dokument måste vara något mindre än 2 MB.

## <a name="performance-expectations"></a>Prestanda förväntningar

Systemets prestanda beror på antalet ru: er, samtidiga anslutningar och typen av åtgärder som du utför (placering, post osv.). Nedan visas några allmänna intervall av vad du kan förväntar dig baserat på konfigurerade ru: er. I allmänhet skalas prestanda linjärt med en ökning av ru: er:

| antal ru: er | Resurser/SEK |    Högsta lagrings utrymme (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1 000    | 100-150       |      100  |
| 10 000   | 225-400       |      1 000  |
| 100 000  | 2500-4000   |      10 000  |

Obs: per Cosmos DB krav finns ett krav på ett minsta data flöde på 10 RU/s per GB lagrings utrymme. Mer information finns i [Cosmos DB tjänst kvoter](../../cosmos-db/concepts-limits.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst om de FHIR-funktioner som stöds i Azure API för FHIR. Distribuera sedan Azure-API: et för FHIR.
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)
