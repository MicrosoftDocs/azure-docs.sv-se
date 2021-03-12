---
title: Migrera från rapporterings-API:er för Enterprise till Azure Resource Manager-API:er
description: I den här artikeln får du hjälp med att förstå skillnaderna mellan rapporterings-API:er och Azure Resource Manager-API:er, vad du kan förvänta dig när du migrerar till Azure Resource Manager-API:er och information om de nya funktioner som är tillgängliga med de nya Azure Resource Manager-API:erna.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 24fbf52c8fd0338537862a54e15e8a249541a701
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635798"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migrera från rapporterings-API:er för Enterprise till Azure Resource Manager-API:er

Den här artikeln hjälper utvecklare som har skapat anpassade lösningar med hjälp av [Azures rapporterings-API:er för Enterprise-kunder](../manage/enterprise-api.md) att migrera till Azure Resource Manager-API:er för Cost Management. Stöd för tjänstens huvudnamn för de nyare Azure Resource Manager-API:erna är nu allmänt tillgängligt. Azure Resource Manager-API:er utvecklas aktivt. Överväg att migrera till dem i stället för att använda Azures äldre rapporterings-API:er för Enterprise-kunder. De äldre API:erna kommer att bli inaktuella. I den här artikeln får du hjälp med att förstå skillnaderna mellan rapporterings-API:er och Azure Resource Manager-API:er, vad du kan förvänta dig när du migrerar till Azure Resource Manager-API:er och information om de nya funktioner som är tillgängliga med de nya Azure Resource Manager-API:erna.

## <a name="api-differences"></a>API-skillnader

Följande information beskriver skillnaderna mellan de äldre rapporterings-API:erna för Enterprise-kunder och de nyare Azure Resource Manager-API:erna.

| **Användning** | **API:er för Enterprise-avtal** | **Azure Resource Manager-API:er** |
| --- | --- | --- |
| Autentisering | API-nyckel etableras i EA-portalen (Enterprise-avtalsportalen) | Azure Active Directory (Azure AD)-autentisering med hjälp av användartoken eller tjänstens huvudnamn. Tjänstens huvudnamn ersätter API-nycklarna. |
| Omfång och behörigheter | Alla begäranden görs i registreringsomfånget. Behörighetstilldelningarna för API-nycklar avgör om data för hela registreringen, en avdelning eller ett visst konto returneras. Ingen användarautentisering. | Användare eller tjänstens huvudnamn tilldelas åtkomst till registrerings-, avdelnings- eller kontoomfånget. |
| URI-slutpunkt | https://consumption.azure.com | https://management.azure.com |
| Utvecklingsstatus | I underhållsläge. Kommer att fasas ut. | Utvecklas aktivt |
| Tillgängliga API:er | Begränsat till det som är tillgängligt för närvarande | Motsvarande API:er är tillgängliga för att ersätta alla EA-API:er. <p> Ytterligare [Cost Management-API:er](/rest/api/cost-management/) är också tillgängliga för dig, till exempel: <p> <ul><li>Budgetar</li><li>Aviseringar<li>Exporter</li></ul> |

## <a name="migration-checklist"></a>Checklista för migrering

- Bekanta dig med [REST-API:erna för Azure Resource Manager](/rest/api/azure).
- Fastställ vilka EA-API:er du använder och se vilka Azure Resource Manager-API:er du ska flytta till i [Mappning av EA-API:er till nya Azure Resource Manager-API:er](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Konfigurera auktorisering och autentisering av tjänsten för Azure Resource Manager-API:erna
  - Om du inte redan använder API:erna för Azure Resource Manager ska du [registrera din klientapp i Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Registreringen skapar ett tjänsthuvudnamn som du använder för att anropa API:erna.
  - Tilldela tjänstens huvudnamn åtkomst till de omfång som krävs, enligt beskrivningen nedan.
  - Uppdatera eventuell programmeringskod så att du använder [Azure AD-autentisering](/rest/api/azure/#create-the-request) med tjänstens huvudnamn.
- Testa API:erna och uppdatera sedan eventuell programmeringskod och ersätt API-anrop för EA med API-anrop för Azure Resource Manager.
- Uppdatera felhanteringen så att du använder de nya felkoderna. Några saker att tänka på:
  - Azure Resource Manager-API:er har en timeoutperiod på 60 sekunder.
  - Frekvensbegränsning tillämpas för Azure Resource Manager-API:er. Du får ett 429-begränsningsfel om frekvensen överskrids. Utforma dina lösningar så att du inte gör för många API-anrop under en kort tidsperiod.
- Granska de andra Cost Management-API:erna som är tillgängliga via Azure Resource Manager och utvärdera för användning senare. Mer information finns i [Använda ytterligare Cost Management-API:er](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Tilldela tjänstens huvudnamn åtkomst till Azure Resource Manager-API:er

När du har skapat ett tjänsthuvudnamn för att programmatiskt anropa Azure Resource Manager-API:er måste du tilldela det rätt behörighet för att auktorisera mot och köra begäranden i Azure Resource Manager. Det finns två behörighetsramverk för olika scenarier.

### <a name="azure-billing-hierarchy-access"></a>Hierarkiåtkomst för Azure-fakturering

Information om hur du tilldelar tjänst huvud namn till företagets fakturerings konto, avdelningar eller registrerings konto omfattningar finns i [tilldela roller till Azure Enterprise-avtal tjänstens huvud namn](../manage/assign-roles-azure-service-principals.md).

### <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Nytt stöd för tjänstens huvudnamn omfattar Azure-specifika omfång, till exempel hanteringsgrupper, prenumerationer och resursgrupper. Du kan tilldela behörigheter för tjänstens huvudnamn till dessa omfång direkt [i Azure-portalen](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) eller genom att använda [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mappning av EA-API:er till nya Azure Resource Manager-API:er

Använd tabellen nedan för att identifiera de EA-API:er du för närvarande använder och de ersättande Azure Resource Manager-API:er som ska användas i stället.

| **Scenario** | **EA-API:er** | **Azure Resource Manager-API:er** |
| --- | --- | --- |
| Saldosammanfattning | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Prisdokument | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – använd för förhandlade priser <p> [API för återförsäljarpriser](/rest/api/cost-management/retail-prices/azure-retail-prices) – använd för återförsäljarpriser |
| Information om reserverad instans | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Sammanfattning av reserverad instans | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Rekommendationer för reserverad instans | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Avgifter för reserverad instans | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Migreringsinformation efter API

I följande avsnitt visas exempel för gamla API-begäranden med exempel för nya ersättande API:er.

### <a name="balance-summary-api"></a>API för saldosammanfattning

Använd följande begärande-URI:er när du anropar det nya API:et för saldosammanfattning. Ditt registreringsnummer ska användas som billingAccountId.

#### <a name="supported-requests"></a>Begäranden som stöds

[Get för registrering](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Ändringar i svarstext

_Gammal brödtext i svar_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Ny brödtext i svar_:

Samma data är nu tillgängliga i fältet `properties` i det nya API-svaret. Stavningen för vissa fältnamn kan ha ändrats något.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Prisdokument

Använd följande begärande-URI:er när du anropar det nya API:et för prisdokument.

#### <a name="supported-requests"></a>Begäranden som stöds

 Du kan anropa API:et med följande omfång:

- Registrering: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Prenumeration: `subscriptions/{subscriptionId}`

[_Get för aktuell faktureringsperiod_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Get för angiven faktureringsperiod_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Ändringar i svarstext

_Gammalt svar_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
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
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Nytt svar_:

Gamla data finns nu i fältet `pricesheets` i det nya API-svaret. Information om mätare anges också.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Användningsinformation om reserverad instans

Microsoft arbetar inte aktivt med synkront baserade API:er för reservationsinformation. Vi rekommenderar att du flyttar till det nyare asynkrona API-anropsmönstret med SPN-stöd som en del av migreringen. Asynkrona begäranden ger bättre hantering av stora mängder data och minskar timeoutfel.

#### <a name="supported-requests"></a>Begäranden som stöds

Använd följande begärande-URI:er när du anropar det nya API:et för asynkron reservationsinformation. Ditt registreringsnummer ska användas som `billingAccountId`. Du kan anropa API:et med följande omfång:

- Registrering: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Exempelbegäran för att skapa en rapport med reservationsinformation

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Exempelbegäran för att kontrollera status för rapportgenerering

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Exempel på avsökningssvar

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Ändringar i svarstext

Svaret för det äldre synkront baserade API:et för reservationsinformation finns nedan.

_Gammalt svar_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Nytt svar_:

Det nya API:et skapar en CSV-fil åt dig. Se följande filfält.

| **Gammal egenskap** | **Ny egenskap** | **Kommentarer** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Ny egenskap för instansflexibilitet. |
|  | InstanceFlexibilityRatio | Ny egenskap för instansflexibilitet. |
| instanceId | InstanceName |  |
|  | Typ | Det är en ny egenskap. Värdet är `None`, `Reservation` eller `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Reserved Instance Usage Summary

Använd följande begärande-URI:er för att anropa det nya API:et för reservationssammanfattningar.

#### <a name="supported-requests"></a>Begäranden som stöds

 Anropa API:et med följande omfång:

- Registrering: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Hämta reservationssammanfattning dagligen_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Hämta reservationssammanfattning månatligen_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Ändringar i svarstext

_Gammalt svar_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Nytt svar_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Rekommendationer för reserverad instans

Använd följande begärande-URI:er för att anropa det nya API:et för reservationsrekommendationer.

#### <a name="supported-requests"></a>Begäranden som stöds

 Anropa API:et med följande omfång:

- Registrering: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Prenumeration: `subscriptions/{subscriptionId}`
- Resursgrupper: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Hämta rekommendationer_](/rest/api/consumption/reservationrecommendations/list)

Både de delade och enskilda omfångsrekommendationerna är tillgängliga via det här API:et. Du kan också filtrera med omfånget som en valfri API-parameter.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Ändringar i svarstext

Rekommendationer för delade och enskilda omfång kombineras till ett API.

_Gammalt svar_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Nytt svar_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Avgifter för reserverad instans

Använd följande begärande-URI:er för att anropa det nya API:et för avgifter för reserverad instans.

#### <a name="supported-requests"></a>Begäranden som stöds

[_Hämta reservationsavgifter per datumintervall_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Ändringar i svarstext

_Gammalt svar_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Nytt svar_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Använd ytterligare Cost Management-API:er

När du har migrerat till Azure Resource Manager-API:er för dina befintliga rapporteringsscenarier kan du även använda många andra API:er. API:erna är också tillgängliga via Azure Resource Manager och kan automatiseras med hjälp av autentisering baserad på tjänstens huvudnamn. Här är en kort sammanfattning av de nya funktionerna som du kan använda.

- [Budgetar](/rest/api/consumption/budgets/createorupdate) – Används för att ställa in tröskelvärden för att proaktivt övervaka dina kostnader, avisera relevanta intressenter och automatisera åtgärder som svar på tröskelöverträdelser.

- [Aviseringar](/rest/api/cost-management/alerts) – Används för att visa aviseringsinformation inklusive, men inte begränsat till, budgetaviseringar, fakturaaviseringar, kreditaviseringar och kvotaviseringar.

- [Exporter](/rest/api/cost-management/exports) – Används för att schemalägga återkommande dataexport av dina avgifter till ett Azure Storage-konto som du väljer. Det är den rekommenderade lösningen för kunder med en stor Azure-närvaro som vill analysera sina data och använda dem i sina egna interna system.

## <a name="next-steps"></a>Nästa steg

- Bekanta dig med [REST-API:erna för Azure Resource Manager](/rest/api/azure).
- Fastställ vid behov vilka EA-API:er du använder och se vilka Azure Resource Manager-API:er du ska flytta till i [Mappning av EA-API:er till nya Azure Resource Manager-API:er](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Om du inte redan använder API:erna för Azure Resource Manager ska du [registrera din klientapp i Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Uppdatera vid behov eventuell programmeringskod så att du använder [Azure AD-autentisering](/rest/api/azure/#create-the-request) med tjänstens huvudnamn.