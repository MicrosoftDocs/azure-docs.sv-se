---
title: Azures API:er för Enterprise-fakturering
description: Lär dig mer om rapporterings-API:erna som gör det möjligt för Enterprise-kunder att hämta förbrukningsdata via programmering.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a1c420eed89b7b45ea6c50345737b8615f39ad8c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602082"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Översikt över rapporterings-API:er för Enterprise-kunder

> [!Note]
> Microsoft uppdaterar inte längre Azures fakturerings-API:er för Azure-rapportering. I stället bör du använda API:er för [Azures Consumption](/rest/api/consumption).

Med rapporterings-API:erna kan Enterprise-kunder hämta förbruknings- och faktureringsdata till önskade dataanalysverktyg. Enterprise-kunder har signerat [ett Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) med Azure om förhandlad förskottsbetalning (kallades tidigare ekonomiskt åtagande) som berättigar till anpassade priser för Azure-resurser.

Alla datum- och tidsparametrar som krävs för API:er måste anges som kombinerade UTC-värden (Coordinated Universal Time). Värden som returneras av API:er visas i UTC-format.

## <a name="enabling-data-access-to-the-api"></a>Aktivera dataåtkomst till API:et
* **Generera eller hämta API-nyckeln** – Logga in på Enterprise Portal och gå till Rapporter > Hämta användning > API-åtkomstnyckel för att generera eller hämta API-nyckeln.
* **Skicka nycklar i API:et** – API-nyckeln måste skickas för varje anrop för autentisering och auktorisering. Följande egenskap krävs med HTTP-huvudena

|Nyckel för begärandehuvud | Värde|
|-|-|
|Auktorisering| Ange värdet i det här formatet: **bearer {API_KEY}** <br/> Exempel: bearer eyr....09|

## <a name="consumption-based-apis"></a>Förbrukningsbaserade API:er
En Swagger-slutpunkt [finns här](https://consumption.azure.com/swagger/ui/index) för API:erna som beskrivs nedan och bör underlätta introspektion av API:et och möjligheten att generera klient-SDK:er med hjälp av [AutoRest](https://github.com/Azure/AutoRest) eller [Swagger CodeGen](https://swagger.io/swagger-codegen/). Data från den 1 maj 2014 är tillgängliga via det här API:et.

* **Saldo och sammanfattning** – [API:et för saldon och sammanfattningar](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) ger tillgång till en månatlig sammanställning av information om saldon, nya inköp, kostnader för Azure Marketplace-tjänster, justeringar och kostnader för överförbrukning.

* **Användningsinformation** – [API:et för användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) returnerar en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter per registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. Du kan köra frågor mot API:et baserat på faktureringsperiod eller start- och slutdatum.

* **Kostnader för Marketplace Store** – [API:et för kostnader för Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) returnerar en uppdelning av de användningsbaserade kostnaderna för Marketplace per dag för den angivna faktureringsperioden eller start- och slutdatum (engångsavgifter är inte inkluderade).

* **Prisdokument** – [API:et för prisdokument](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) returnerar relevant pris för respektive mätare för den aktuella registreringen och faktureringsperioden.

* **Information om reserverade instanser** – [API:et för användning av reserverade instanser](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) returnerar användningsdata om köpta reserverade instanser. [API:et för kostnader för reserverade instanser](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) returnerar genomförda betalningstransaktioner.

## <a name="data-freshness"></a>Dataaktualitet
ETags returneras i svaret för alla ovanstående API:er. En ändring i Etag indikerar att data har uppdaterats.  I efterföljande anrop till samma API med samma parametrar skickar du Etag-filen som hämtats med nyckeln ”If-None-Match” i huvudet för HTTP-begäran. Statuskoden för svaret är ”NotModified” om data inte har uppdaterats ytterligare och inga data returneras. API:et returnerar den fullständiga datauppsättningen för den begärda perioden när en Etag-ändring görs.

## <a name="helper-apis"></a>API:er för hjälpkomponenter
 **Lista över faktureringsperioder** – [API:et för faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) returnerar en lista över faktureringsperioder som innehåller förbrukningsdata för den angivna registreringen i omvänd kronologisk ordning. Varje period innehåller en egenskap som pekar på API-vägen för de fyra uppsättningarna med data – BalanceSummary, UsageDetails, MarketplaceCharges och PriceSheet.


## <a name="api-response-codes"></a>API-svarskoder   
|Statuskod för svar|Meddelande|Beskrivning|
|-|-|-|
|200| OK|Inget fel|
|400| Felaktig begäran| Ogiltiga parametrar – datumintervall, EA-nummer osv.|
|401| Behörighet saknas| Det gick inte att hitta API-nyckeln – ogiltig, har gått ut eller liknande|
|404| Inte tillgänglig| Det gick inte att hitta rapportslutpunkten|
|429 | TooManyRequests | Begäran begränsades. Vänta så lång tid som anges i rubriken <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> och försök igen.|
|500| Serverfel| Ett oväntat fel inträffade när förfrågan bearbetades|
| 503 | ServiceUnavailable | Tjänsten är inte tillgänglig för tillfället. Vänta så lång tid som anges i rubriken <code>Retry-After</code> och försök igen.|
