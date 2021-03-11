---
title: Gör ditt första API-anrop till att få åtkomst till kommersiella Marketplace Analytics-data
description: 'Exempel för att lära dig att använda API: et för att komma åt data för kommersiella Marketplace-analyser.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 2d0c0e7322ecb92fd371f5bf7924a370dd29fe85
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584186"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Gör ditt första API-anrop till att få åtkomst till kommersiella Marketplace Analytics-data

En lista över API: er för åtkomst till kommersiella data för Marketplace-analys finns i [API: er för åtkomst till kommersiella Marketplace Analytics-data](analytics-available-apis.md). Innan du gör ditt första API-anrop måste du kontrol lera att du har uppfyllt [kraven](analytics-prerequisites.md) för att program mässigt komma åt data för kommersiella Marketplace-analyser.

## <a name="token-generation"></a>Generering av token

Innan du anropar någon av metoderna måste du först skaffa en Azure Active Directory (Azure AD)-åtkomsttoken. Du måste skicka Azure AD-åtkomsttoken till Authorization-huvudet för varje metod i API: et. När du har skaffat en åtkomsttoken har du 60 minuter att använda den innan den upphör att gälla. När token har gått ut kan du uppdatera token och fortsätta att använda den för ytterligare anrop till API: et.

Se en exempel förfrågan nedan för att skapa en token. De tre värdena som krävs för att generera token är `clientId` , `clientSecret` och `tenantId` . `resource`Parametern ska vara inställd på `https://graph.windows.net` .

***Exempel på begäran***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Svars exempel***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Mer information om hur du skaffar en Azure AD-token för ditt program finns i [Access Analytics-data med Store-tjänster](https://docs.microsoft.com/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>API-anrop i programmering

När du har fått Azure AD-token enligt beskrivningen i föregående avsnitt, följer du de här stegen för att skapa din första program mässig åtkomst rapport.

Data kan hämtas från följande data uppsättningar (Datasetname spatialdata):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

I följande avsnitt ser vi exempel på hur du program mässigt kommer åt `OrderId` från ISVOrder-datauppsättningen.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Steg 1: gör ett REST-anrop med API: t get data uppsättningar

API-svaret tillhandahåller data uppsättnings namnet där du kan ladda ned rapporten. För den aktuella data uppsättningen tillhandahåller API-svaret även listan över valbara kolumner som kan användas för din anpassade rapportmall. Du kan också se följande tabeller för att hämta namnen på kolumnerna:

- [Tabellen Order Detaljer](orders-dashboard.md#orders-details-table)
- [Tabellen användnings information](usage-dashboard.md#usage-details-table)
- [Tabellen Kund information](customer-dashboard.md#customer-details-table)
- [Informations tabell för Marketplace](insights-dashboard.md#marketplace-insights-details-table)

***Exempel på begäran***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Svars exempel***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Steg 2: skapa den anpassade frågan

I det här steget ska vi använda order-ID: t från rapporten Orders för att skapa en anpassad fråga för rapporten som vi vill. Standardvärdet `timespan` om det inte anges i frågan är sex månader.

***Exempel på begäran***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Svars exempel***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Vid lyckad körning av frågan `queryId` genereras en som måste användas för att generera rapporten.

### <a name="step-3-execute-test-query-api"></a>Steg 3: kör API för test frågor

I det här steget ska vi använda testfrågans API för att hämta de översta 100 raderna för den fråga som skapades.

***Exempel på begäran***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Svars exempel***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Steg 4: skapa rapporten

I det här steget ska vi använda den tidigare genererade `QueryId` rapporten för att skapa rapporten.

***Exempel på begäran***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabell 1: Beskrivning av parametrar som används i det här förfrågan exemplet**_

| Parameter | Beskrivning |
| ------------ | ------------- |
| `Description` | Ange en kort beskrivning av rapporten som genereras. |
| `QueryId` | Detta är den `queryId` som genererades när frågan skapades i steg 2: skapa anpassad fråga. |
| `StartTime` | Tiden då den första körningen av rapporten startades. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas. |
| `RecurrenceCount` | Antal upprepningar som anges när rapporten skapas. |
| `Format` | CSV-och TSV-filformat stöds. |
|||

***Svars exempel***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Vid lyckad körning genereras en `reportId` som måste användas för att schemalägga en nedladdning av rapporten.

### <a name="step-5-execute-report-executions-api"></a>Steg 5: kör API för rapport körningar

För att få en säker plats (URL) för rapporten kommer vi nu att köra API: et för rapport körning.

***Exempel på begäran***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Svars exempel***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Nästa steg

- Du kan prova API: erna via [URL: en för Swagger-API](https://partneranalytics-api.azure-api.net/analytics/cmp/swagger/index.html)
- [Paradigm för program mässig åtkomst](analytics-programmatic-access.md)
