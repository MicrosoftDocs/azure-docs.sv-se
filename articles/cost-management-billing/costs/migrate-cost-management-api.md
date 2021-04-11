---
title: Migrera från EA till API:er för Microsoft-kundavtal – Azure
description: Den här artikeln hjälper dig att förstå konsekvenserna av att migrera ett Microsoft Enterprise-avtal (EA) till ett Microsoft-kundavtal.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078357"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrera från Enterprise-avtal till API:er för Microsoft-kundavtal

Den här artikeln hjälper dig att förstå skillnaderna vad gäller datastrukturer, API:er och annan systemintegrering mellan konton med Enterprise-avtal (EA) och Microsoft-kundavtal (MCA). Azure Cost Management har API-stöd för båda kontotyperna. Läs gärna artikeln [Konfigurera faktureringskontot för](../manage/mca-setup-account.md) Microsoft-kundavtal innan du fortsätter.

Organisationer med ett befintligt EA-konto bör läsa den här artikeln när de ska konfigurera sitt MCA-konto. När du tidigare skulle förnya ett EA-konto krävdes en liten mängd arbete för att flytta från en gammal registrering till en ny. Det krävs dock mer arbete för att migrera till ett MCA-konto. Det här beror på ändringarna i det underliggande delsystemet för fakturering, som påverkar alla kostnadsrelaterade API:er och tjänsterbjudanden.

## <a name="mca-apis-and-integration"></a>API:er för MCA och integrering

Med API:erna för MCA och den nya integreringen kan du:

- Få fullständig API-tillgänglighet via API:er som är inbyggda i Azure.
- Konfigurera flera fakturor på ett enda faktureringskonto.
- Få åtkomst till ett kombinerat API med användning av Azure-tjänster samt använda tredjepartstjänster och göra inköp på Marketplace.
- Visa kostnader för flera faktureringsprofiler (samma sak som registreringar) med hjälp av Azure Cost Management.
- Få till gång till nya API:er som visar kostnader, få aviseringar när kostnaderna överstiger fördefinierade tröskelvärden och exportera rådata automatiskt.

## <a name="migration-checklist"></a>Checklista för migrering

De här punkterna hjälper dig med övergången till API:erna för MCA.

- Bekanta dig med det nya [faktureringskontot för Microsoft-kundavtal](../understand/mca-overview.md).
- Fastställ vilka API:er du använder och se vilka som ersätts i följande avsnitt.
- Bekanta dig med [REST-API:erna för Azure Resource Manager](/rest/api/azure).
- Om du inte redan använder API:erna för Azure Resource Manager ska du [registrera din klientapp i Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Uppdatera eventuell programmeringskod så att du använder [Azure AD-autentisering](/rest/api/azure/#create-the-request).
- Uppdatera eventuell programmeringskod och ersätt API-anrop för EA med API-anrop för MCA.
- Uppdatera felhanteringen så att du använder de nya felkoderna.
- Gå igenom eventuella andra integreringserbjudanden som Cloudyn och Power BI, och se efter vad du behöver göra.

## <a name="ea-apis-replaced-with-mca-apis"></a>API:er för EA som ersätts med API:er för MCA

I API:erna för EA används en API-nyckel autentisering och auktorisering. I API:erna för MCA används Azure AD-autentisering.

| Syfte | EA-API | MCA-API |
| --- | --- | --- |
| Saldo och krediter | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Användning (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Användning (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-användning (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Faktureringsperioder | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Prisdokument | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. fakturering/billingAccounts/billingProfiles/pris dokument/default/Download format = JSON \| CSV Microsoft. fakturering/billingAccounts/.../billingProfiles/.../fakturor/... /pricesheet/default/Download-format = JSON \| CSV Microsoft. fakturering/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Reservationsköp | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Reservationsrekommendationer | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reservationsanvändning | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure-tjänster och Marketplace-användning från tredje part är tillgängliga i [API:et för användningsdetaljer](/rest/api/consumption/usagedetails).

De här API:erna är tillgängliga för MCA-faktureringskonton:

| Syfte | MCA-API (Microsoft-kundavtal) |
| --- | --- |
| Faktureringskonton<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Faktureringsprofiler<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Fakturaavsnitt<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Fakturor | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Faktureringsprenumerationer | {scope}/billingSubscriptions |

<sup>2</sup> API:erna returnerar listor med objekt, som är omfång, för Cost Management-miljön i Azure-portalen och API:erna. Du kan läsa mer om Cost Management-omfång i [Förstå och arbeta med omfång](understand-work-scopes.md).

Om du använder några befintliga EA-API:er måste du uppdatera dem så att de fungerar med MCA-faktureringskonton. I den här tabellen visas ytterligare integreringsändringar:

| Syfte | Gammalt erbjudande | Nytt erbjudande |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Innehållspaket och anslutningsprogram för [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Anslutningsprogram för Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API:er för att få saldo och krediter

Via API:et för att [hämta saldoöversikt](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) får du en månatlig översikt över följande:

- Saldon
- Nya inköp
- Avgifter för tjänster på Azure Marketplace
- Justeringar
- Avgifter för överförbrukning av tjänster

Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request).

API:et för att hämta saldoöversikt ersätts av API:et Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Så här hämtar du tillgängliga saldon via API:et för tillgängligt saldo:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API:er för att hämta kostnader och användning

Få en daglig översikt över kostnader för användning av Azure-tjänster, Marketplace-användning från tredje part och andra Marketplace-inköp via följande API:er. Följande separata API:er har slagits samman för Azure-tjänster och Marketplace-användning från tredje part. De gamla API:erna ersätts med API:et [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Här har Marketplace-inköp lagts till, som tidigare endast visades i saldoöversikten fram till aktuellt datum.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request).

Alla föregående API:er ersätts av API:et Consumption/Usage Details.

Så här hämtar du användningsinformation med API:et Usage Details:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

API:et Usage Details är precis som alla Cost Management-API:er tillgängligt för flera omfång. När det gäller fakturerade kostnader som du normalt skulle debiteras på registreringsnivå ska du använda faktureringsprofilen som omfång.  Du kan läsa mer om Cost Management-omfång i [Förstå och arbeta med omfång](understand-work-scopes.md).

| Typ | ID-format |
| --- | --- |
| Faktureringskonto | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Faktureringsprofil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Prenumeration | `/subscriptions/{subscriptionId}` |
| Resursgrupp | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Använd följande frågesträngsparametrar när du ska uppdatera eventuell programmeringskod.

| Gamla parametrar | Nya parametrar |
| --- | --- |
| `billingPeriod={billingPeriod}` | Stöds inte |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Brödtexten i svaret är också ny.

Gammal brödtext i svar:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Ny brödtext i svar:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Egenskapsnamnet som innehåller arrayen med användningsposter har ändrats från data till _values_. Varje post brukade ha en rak lista med detaljerade egenskaper. Nu har dock varje post all information i en kapslad egenskap med namnet _properties_, förutom taggar. Den nya strukturen är mer lik övriga Azure-API:er. En del egenskapsnamn har ändrats. I den här tabellen visas motsvarande egenskaper.

| Tidigare egenskap | Ny egenskap | Kommentarer |
| --- | --- | --- |
| AccountId | Ej tillämpligt | Den som skapat prenumerationen spåras inte. Använd invoiceSectionId (samma som departmentId). |
| AccountNameAccountOwnerId och AccountOwnerEmail | Ej tillämpligt | Den som skapat prenumerationen spåras inte. Använd invoiceSectionName (samma som departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observera att de här egenskaperna är motsatser. Om isAzureCreditEnabled är sann skulle ChargesBilledSeparately vara falsk. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | De exakta strängvärdena kan variera. |
| ConsumedServiceId | Inget | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date och usageStartDate | date | &nbsp;  |
| Dag | Inget | Parsar dag och datum. |
| DepartmentId | invoiceSectionId | Exakta värden kan variera. |
| DepartmentName | invoiceSectionName | De exakta strängvärdena kan variera. Konfigurera fakturaavsnitten så att de matchar avdelningarna om det behövs. |
| ExtendedCost och Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Är en återkommande avgift | Ingen | &nbsp;  |
| Plats | location | &nbsp;  |
| MeterCategory | meterCategory | De exakta strängvärdena kan variera. |
| MeterId | meterId | De exakta strängvärdena varierar. |
| MeterName | meterName | De exakta strängvärdena kan variera. |
| MeterRegion | meterRegion | De exakta strängvärdena kan variera. |
| MeterSubCategory | meterSubCategory | De exakta strängvärdena kan variera. |
| Månad | Inget | Parsar månad från datum. |
| Erbjudandets namn | Inget | Använd publisherName och productOrderName. |
| OfferID | Inget | &nbsp;  |
| Ordernummer | Inget | &nbsp;  |
| PartNumber | Inget | Använd meterId och productOrderName till att identifiera priser unikt. |
| Plannamn | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | De exakta strängvärdena varierar. |
| Utgivarens namn | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | De exakta strängvärdena varierar. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Inget | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Ej tillämpligt | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | De exakta strängvärdena kan variera. |
| ServiceTier | meterSubCategory | De exakta strängvärdena kan variera. |
| StoreServiceIdentifier | Ej tillämpligt | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Taggar | tags | Egenskapen tags gäller för rotobjektet, inte den kapslade egenskapen properties. |
| UnitOfMeasure | unitOfMeasure | De exakta strängvärdena varierar. |
| usageEndDate | date | &nbsp;  |
| Year | Inget | Parsar år från datum. |
| (nytt) | billingCurrency | Valuta som använts för avgiften. |
| (nytt) | billingProfileId | Unikt ID för faktureringsprofilen (samma som registreringen). |
| (nytt) | billingProfileName | Namn för faktureringsprofilen (samma som registreringen). |
| (nytt) | chargeType | Använd för att differentiera användningen av Azure-tjänster, Marketplace-användning och inköp. |
| (nytt) | invoiceId | Unikt ID för fakturan. Tomt för den aktuella, öppna månaden. |
| (nytt) | publisherType | Typ av utgivare för inköp. Tom för användning. |
| (nytt) | serviceFamily | Typ av köp. Tom för användning. |
| (nytt) | servicePeriodEndDate | Slutdatum för den köpta tjänsten. |
| (nytt) | servicePeriodStartDate | Startdatum för den köpta tjänsten. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API:et Billing Periods ersätts med API:et Invoices

MCA-faktureringskonton använder inte faktureringsperioder. I stället används fakturor till att matcha kostnader mot specifika faktureringsperioder. [API:et Billing Periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) ersätts av API:et Invoices. Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request).

Så här hämtar du fakturor med API:et Invoices:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API:er för prisdokument

I det här avsnittet går vi igenom befintliga API:er för prisdokument och ger rekommendationer för övergången till API:et Price Sheet för Microsoft-kundavtal. Dessutom går vi igenom API:et Price Sheet för Microsoft-kundavtal och fälten i prisdokumenten. API:erna [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) och [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) ersätts av API:et Price Sheet för Microsoft-kundavtal (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). Det nya API:et har stöd för både JSON- och CSV-format i asynkrona REST-format. Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>API:er för Enterprise-fakturering

Tidigare har du använt API:erna för Enterprise-fakturering med Enterprise-registreringar för att få information om priser och faktureringsperioder. Till autentisering och auktorisering användes webbtoken för Azure Active Directory.

Så här hämtar du gällande priser för den angivna Enterprise-registreringen med API:erna Price Sheet och Billing Period:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API:et Price Sheet för Microsoft-kundavtal

Använd API:et Price Sheet för Microsoft-kundavtal till att se priser för alla förbrukningstjänster i Azure och på Marketplace. Priserna som visas för faktureringsprofilen gäller för alla prenumerationer som tillhör faktureringsprofilen.

Använd API:et Price Sheet till att visa prisdokumentsdata för alla Azure-förbrukningstjänster i CSV-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Använd API:et Price Sheet till att visa prisdokumentsdata för alla Azure-förbrukningstjänster i CSV-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Med API:et returneras prisdokumentet för hela kontot. Men du kan också få en komprimerad version av prisdokumentet i PDF-format. I sammanfattningen ingår Azure-förbrukning och Marketplace-tjänster som faktureras på en viss faktura. Fakturan identifieras av {invoiceId}, som är samma som **fakturanumret** i PDF-filerna med fakturasammanfattningen. Här är ett exempel.

![Exempelbild där fakturanumret matchar InvoiceId-värdet](./media/migrate-cost-management-api/invoicesummary.png)

Så här visar du fakturainformation via API:et Price Sheet i CSV-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Så här visar du fakturainformation via API:et Price Sheet i JSON-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Du kan också se uppskattade priser för förbrukning i Azure och på Marketplace i den aktuella öppna faktureringsperioden eller tjänstperioden.

Så här visar du uppskattade priser för förbrukningstjänster via API:et Price Sheet i CSV-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Så här visar du uppskattade priser för förbrukningstjänster via API:et Price Sheet i JSON-format:

| Metod | URI för förfrågan |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

API:erna för prisdokument i Microsoft-kundavtal är *asynkrona REST-API:er*. Svaren från API:erna har ändrats jämfört med de äldre synkrona API:erna. Brödtexten i API-svaret är också ny.

#### <a name="old-response-body"></a>Gammal brödtext i svar

Här är ett exempel på ett svar från ett synkront REST-API:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Ny brödtext i svar

API:erna har stöd för [det asynkrona REST-formatet i Azure](../../azure-resource-manager/management/async-operations.md). Om du anropa API:et med GET får du följande svar:

```
No Response Body

HTTP Status 202 Accepted
```

De här rubrikerna skickas med utdataplatsen:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Gör ett annat GET-anrop till platsen. Svaret på GET-anropet är detsamma tills åtgärden når en slutförd eller misslyckad status. När den är slutförd returnerar svaret till GET-anropsplatsen nedladdningsadressen. Precis som om åtgärden utfördes samtidigt. Här är ett exempel:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Klienten kan också göra ett GET-anrop för `Azure-AsyncOperation`. Slutpunkten returnerar åtgärdens status.

I den här tabellen ser du fälten i det gamla Enterprise-API:et för att hämta prisdokument. Dessutom ser du motsvarande fält i det nya prisdokumentet för Microsoft-kundavtal:

| Tidigare egenskap | Ny egenskap | Kommentarer |
| --- | --- | --- |
| billingPeriodId  | _Ej tillämpligt_ | Inte tillämpligt. Fakturan och det tillhörande prisdokumentet har ersatt begreppet billingPeriodId för Microsoft-kundavtal. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | De exakta strängvärdena kan variera. |
| includedQuantity  | includedQuantity | Ej tillämpligt för tjänster under Microsoft-kundavtal. |
| partNumber  | _Ej tillämpligt_ | Använd i stället en kombination av productOrderName (samma som offerID) och meterID. |
| unitPrice  | unitPrice | Enhetspris gäller för tjänster som förbrukas under Microsoft-kundavtal. |
| currencyCode  | pricingCurrency | I Microsoft-kundavtal representeras priser med hjälp av prissättningsvalutor och faktureringsvalutor. currencyCode motsvarar pricingCurrency i Microsoft-kundavtal. |
| offerID | productOrderName | Du kan använda productOrderName i stället för OfferID, men det är inte samma sak som OfferID. productOrderName och meter fastställer dock priser i Microsoft-kundavtal som gällde meterId och OfferID i äldre registreringar. |

## <a name="consumption-price-sheet-api-operations"></a>Åtgärder i API:et Consumption Price Sheet

För Enterprise-avtal använde du åtgärderna [Get](/rest/api/consumption/pricesheet/get) och [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) i API:et Consumption Price Sheet för omfång per subscriptionId eller faktureringsperiod. API:et använder autentisering via Azure Resource Management.

Så här hämtar du prisdokumentinformation för ett omfång via API:et Price Sheet:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Så här hämtar du prisdokumentinformation för en faktureringsperiod via API:et Price Sheet:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

I stället för API-slutpunkterna ovan använder du följande för Microsoft-kundavtal:

**API:et Price Sheet för Microsoft-kundavtal (asynkront REST-API)**

Det här API:et gäller för Microsoft-kundavtal och har ytterligare attribut.

**Pris dokument för en fakturerings profil omfattning i ett fakturerings konto**

Detta API är det befintliga API:et. Det har uppdaterats för att tillhandahålla prisdokumentet för en faktureringsprofil i ett faktureringskonto.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Prisdokument för ett faktureringskonto som omfång

Autentiseringen görs via Azure Resource Manager när du hämtar prisdokumentet för en registrering som omfång i ett faktureringskonto.

Så här hämtar du prisdokumentet för en registrering i ett faktureringskonto:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Använd informationen i följande avsnitt för ett Microsoft-kundavtal. Det innehåller fältegenskaper som används för Microsoft-kundavtal.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Prisdokument för en faktureringsprofil som omfång i ett faktureringskonto

Det uppdaterade API:et Price Sheet by billing account hämtar prisdokumentet i CSV-format. Så här hämtar du prisdokumentet för en faktureringsprofil som omfång i ett MCA:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

API-svar och egenskaper är identiska för EA-registreringen som omfång. Egenskaperna motsvarar samma MCA-egenskaper.

Du hittar de äldre egenskaperna för [Azure Resource Manager-API:et Price Sheet](/rest/api/consumption/pricesheet) och motsvarande nya egenskaper i följande tabell.

| Gammal egenskap i Azure Resource Manager-API:et Price Sheet  | Ny egenskap i MCA-API:et Price Sheet   | Beskrivning |
| --- | --- | --- |
| Meter ID | _meterId_ | Unik identifierare för mätaren. Samma som meterID. |
| Mätarnamn | meterName | Namnet på mätaren. Mätaren representerar den distribuerbara resursen för en Azure-tjänst. |
| Mätarkategori  | tjänst | Namn på mätarens klassificeringskategori. Samma som tjänsten i prisdokumentet för ett Microsoft-kundavtal. De exakta strängvärdena varierar. |
| Mätarunderkategori | meterSubCategory | Namn på kategorin för mätarens underklassificering. Baserat på klassificeringen av tjänstens differentiering av högnivåfunktioner. Till exempel Basic SQL DB jämfört med Standard SQL DB. |
| Mätarregion | meterRegion | &nbsp;  |
| Enhet | _Ej tillämpligt_ | Kan parsas från unitOfMeasure. |
| Måttenhet | unitOfMeasure | &nbsp;  |
| Artikelnummer | _Ej tillämpligt_ | Använd productOrderName och MeterID i stället för artikelnummer när du ska identifiera priset för en faktureringsprofil unikt. De här fälten visas på MCA-fakturan i stället för artikelnummer. |
| Enhetspris | unitPrice | Enhetspris för Microsoft-kundavtal. |
| Valutakod | pricingCurrency | I Microsoft-kundavtal representeras priser med hjälp av prissättningsvalutor och faktureringsvalutor. Valutakod motsvarar pricingCurrency i Microsoft-kundavtal. |
| Inkluderad mängd | includedQuantity | Gäller inte tjänster i Microsoft-kundavtal. Visa med nollvärden. |
|  Erbjudande-ID  | productOrderName | Använd productOrderName i stället för OfferID. productOrderName är inte detsamma som OfferID, men tillsammans med meter fastställer det priser i Microsoft-kundavtal. Relaterat till meterId och OfferID i äldre registreringar. |

Priset för Microsoft-kundavtal definieras annorlunda än för Enterprise-avtal. Priset för tjänster i Enterprise-registreringar är unikt för produkten, artikelnumret, mätaren och erbjudandet. I Microsoft-kundavtal används inga artikelnummer.

Priset för Azure-förbrukningstjänsten som ingår i ett Microsoft-kundavtal är unikt sett till productOrderName och meterID. De representerar tjänstmätaren och produktplanen.

Om du vill stämma av mellan prisdokumentet och användningen i API:et Usage Details kan du använda productOrderName och meterID.

Användare med behörighet som faktureringsprofilägare, deltagare, läsare och fakturaansvarig kan ladda ned prisdokumentet.

I prisdokumentet står priser för tjänster vars pris baseras på användning. Det är bland annat förbrukningstjänster i Azure och på Marketplace. Det senaste priset i slutet av varje tjänstperiod låses och används för all användning under tjänstperioden. För förbrukning i Azure är tjänstperioden normalt en kalendermånad.

### <a name="retired-price-sheet-api-fields"></a>Borttagna fält i API:et Price Sheet

De här fälten är antingen inte tillgängliga i API:et Price Sheet för Microsoft-kundavtal, eller så finns samma fält med.

|Borttaget fält| Beskrivning|
|---|---|
| billingPeriodId | Inte tillämpligt. Motsvarar InvoiceId för MCA. |
| offerID | Inte tillämpligt. Motsvarar productOrderName för MCA. |
| meterCategory  | Inte tillämpligt. Motsvarar Service för MCA. |
| unit | Inte tillämpligt. Kan parsas från unitOfMeasure. |
| currencyCode | Samma som pricingCurrency för MCA. |
| meterLocation | Samma som meterRegion för MCA. |
| partNumber | Inte tillämpligt eftersom artikelnummer inte står med på MCA-fakturor. I stället för artikelnummer identifierar du priser unikt med en kombination av meterId och productOrderName. |
| totalIncludedQuantity | Inte tillämpligt. |
| pretaxStandardRate  | Inte tillämpligt. |

## <a name="reservation-instance-charge-api-replaced"></a>API:et Reservation Instance Charge har bytts ut

Du kan hämta faktureringstransaktioner för reservationsköp med [API:et Reserved Instance Charge](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Det nya API:et innehåller alla inköp, inklusive Marketplace-erbjudanden från tredje part. Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request). API:et Reserved Instance Charge har bytts ut mot API:et Transactions.

Så här hämtar du transaktioner för reservationsköp via API:et Transactions:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>API:erna för rekommendationer är utbytta

API:erna för rekommenderade inköp av reserverade instanser visar användning av virtuella datorer under de senaste 7, 30 eller 60 dagarna. API:erna ger även rekommendationer för reservationsköp. De omfattar:

- [API:et Shared Reserved Instance Recommendation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API:et Single Reserved Instance Recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request). API:erna för reservationsrekommendationer ovan ersätts av API:et [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Så här hämtar du reservationsrekommendationer via API:et:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API:erna för reservationsanvändning är utbytta

Du kan hämta reservationsanvändning för en registrering via API:et Reserved Instance Usage. Om det finns fler än en reserverad instans i en registrering kan du också få användningen av alla reserverade instanser via API:et.

De omfattar:

- [Reserverade instanser – användning information](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Reserved Instance Usage Summary](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alla förbruknings-API:er ersätts av interna Azure-API:er som använder Azure AD för autentisering och auktorisering. Du kan läsa mer om att anropa Azures REST-API:er i [Komma igång med REST](/rest/api/azure/#create-the-request). API:erna för reservationsrekommendationer ovan ersätts av API:erna [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) och [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Så här hämtar du reservationsinformation via API:et Reservation Details:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Så här hämtar du reservationsinformation via API:et Reservation Summaries:

| Metod | URI för förfrågan |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Flytta från Cloudyn till Cost Management

Organisationer som använder [Cloudyn](https://cloudyn.com) bör börja hantera sin kostnadshantering i [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) i stället. Cost Management är tillgängligt i Azure-portalen utan registrering och med en eftersläpning på åtta timmar. Mer information finns i [dokumentationen till Cost Management](../index.yml).

Med Azure Cost Management kan du göra följande:

- Visa kostnader över tid mot en fördefinierad budget. Analysera dagliga kostnadsmönster för att identifiera och stoppa avvikande utgifter. Dela upp kostnaderna per tagg, resursgrupp, tjänst och plats.
- Skapa budgetar för att ange gränser för användning och kostnader, och få aviseringar när användningen närmar sig viktiga tröskelvärden. Konfigurera automatisering med åtgärdsgrupper för att utlösa anpassade händelser och tillämpa fasta gränser på dina villkor.
- Optimera kostnader och användning med rekommendationer från Azure Advisor. Identifiera köpoptimeringar med reservationer, nedgradera underutnyttjade virtuella datorer och ta bort resurser som inte används för att hålla budgeten.
- Schemalägg export av kostnads- och användningsdata och publicera en CSV-fil på ditt lagringskonto dagligen. Automatisera integreringen med externa system för att hålla faktureringsdata synkroniserade och uppdaterade.

## <a name="power-bi-integration"></a>Power BI-integrering

Du kan också rapportera kostnader med hjälp av Power BI. Du kan använda [Azure Cost Management-anslutningsprogrammet](/power-bi/desktop-connect-azure-cost-management) för Power BI Desktop till att skapa kraftfulla och anpassade rapporter som hjälper dig att förstå dina Azure-utgifter bättre. För närvarande är det kunder med ett Microsoft-kundavtal eller ett Enterprise-avtal (EA) som kan använda Azure Cost Management-anslutningsprogrammet.

## <a name="next-steps"></a>Nästa steg

- Läs i [Cost Management-dokumentationen](../index.yml) om du vill lära dig att övervaka och kontrollera dina Azure-utgifter. Där står även hur du kan optimera resursanvändningen med Cost Management.
