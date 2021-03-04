---
title: Genomgång av REST-API:et för Azure Monitoring
description: Autentisera begär Anden och Använd Azure Monitor REST API för att hämta tillgängliga mått definitioner och mät värden.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: a7cd6ff7c0c3b5d4bee859ef288f16673ebe0835
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033086"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Genomgång av REST-API:et för Azure Monitoring

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln visar hur du utför autentisering så att din kod kan använda [Microsoft Azure Monitor REST API referens](/rest/api/monitor/).

Azure Monitor-API: et gör det möjligt att program mässigt Hämta tillgängliga standard mått definitioner, granularitet och mät värden. Data kan sparas i ett separat data lager, till exempel Azure SQL Database, Azure Cosmos DB eller Azure Data Lake. Du kan utföra ytterligare analyser efter behov.

Förutom att arbeta med olika mått data punkter gör övervakarens API också det möjligt att lista aviserings regler, Visa aktivitets loggar och mycket mer. En fullständig lista över tillgängliga åtgärder finns i referens för [Microsoft Azure övervakare REST API](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Autentisera Azure Monitor begär Anden

Det första steget är att autentisera begäran.

Alla aktiviteter som utförs mot Azure Monitor-API: et använder modellen Azure Resource Manager autentisering. Därför måste alla begär Anden autentiseras med Azure Active Directory (Azure AD). En metod för att autentisera klient programmet är att skapa ett Azure AD-tjänstens huvud namn och hämta autentiseringen (JWT)-token. Följande exempel skript visar hur du skapar ett tjänst huvud namn för Azure AD via PowerShell. Mer detaljerad information finns i dokumentationen om hur du [använder Azure PowerShell för att skapa ett huvud namn för tjänsten för att få åtkomst till resurser](/powershell/azure/create-azure-service-principal-azureps). Det är också möjligt att [skapa ett huvud namn för tjänsten via Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Om du vill fråga Azure Monitor-API: t bör klient programmet använda det tidigare skapade tjänstens huvud namn för att autentisera. Följande exempel på PowerShell-skript visar ett tillvägagångs sätt med hjälp av [Active Directory-autentiseringsbibliotek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) för att hämta JWT-autentiseringstoken. JWT-token skickas som en del av en HTTP-auktoriseringsregel i begär anden till Azure Monitor REST API.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Efter autentiseringen kan frågor sedan utföras mot Azure Monitor REST API. Det finns två användbara frågor:

1. Visa en lista med mått definitioner för en resurs
2. Hämta mått värden

> [!NOTE]
> Mer information om hur du autentiserar med Azure REST API finns i [referens för azure REST API](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Hämta mått definitioner (Multi-dimensionell API)

Använd [Azure Monitor mått definitioner REST API](/rest/api/monitor/metricdefinitions) för att få åtkomst till listan över mått som är tillgängliga för en tjänst.

**Metod**: Hämta

**Begärande-URI**: https: \/ \/ Management.Azure.com/Subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourceName}*/providers/Microsoft.Insights/metricDefinitions? API-version =*{API version}*

Om du till exempel vill hämta mått definitionerna för ett Azure Storage konto ser begäran ut så här:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Om du vill hämta mått definitioner med flerdimensionella Azure Monitor mått REST API använder du "2018-01-01" som API-version.
>
>

Den resulterande JSON-svars texten liknar följande exempel: (Observera att det andra måttet har dimensioner)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Hämta dimensions värden (Multi-dimensionellt API)

När de tillgängliga mått definitionerna är kända kan det finnas några mått som har dimensioner. Innan du frågar efter måttet kanske du vill identifiera det värde intervall som en dimension har. Baserat på dessa dimensions värden kan du sedan välja att filtrera eller segmentera måtten baserat på dimensions värden vid frågor om mått.  Använd [Azure Monitor mått REST API](/rest/api/monitor/metrics) för att uppnå detta.

Använd måttets namn värde (inte localizedValue) för alla filtrerings begär Anden. Om inga filter anges returneras standard måttet. Användningen av detta API tillåter bara en dimension att ha ett Wildcard-filter.

> [!NOTE]
> Om du vill hämta dimensions värden med hjälp av Azure Monitor REST API använder du "2018-01-01" som API-version.
>
>

**Metod**: Hämta

**Begärande-URI**: \: https//Management.Azure.com/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-namn}*/providers/*{Resource-Provider-namespace}* / *{resurs typ}* / *{Resource-Name}*/providers/Microsoft.Insights/Metrics? metricnames =*{Metric}*&TimeSpan =*{StartTime}*&$filter =*{filter}*&resultType = metadata&API-version =*{API version}*

Om du till exempel vill hämta listan med dimensions värden som har genererats för "API-namnets dimension" för måttet "transaktioner", där typen dimension = "primär" under det angivna tidsintervallet, skulle begäran vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Den resulterande JSON-svars texten liknar följande exempel:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Hämta mått värden (Multi-dimensionellt API)

När de tillgängliga mått definitionerna och möjliga dimensions värden är kända, är det möjligt att hämta relaterade mått värden.  Använd [Azure Monitor mått REST API](/rest/api/monitor/metrics) för att uppnå detta.

Använd måttets namn värde (inte localizedValue) för alla filtrerings begär Anden. Om inga dimensions filter anges returneras det upplyfta aggregerade måttet. Om en mått fråga returnerar flera timeseries kan du använda parametrarna Top och OrderBy för att returnera en begränsad sorterad lista med timeseries.

> [!NOTE]
> Om du vill hämta flerdimensionella mått värden med Azure Monitor REST API använder du "2018-01-01" som API-version.
>
>

**Metod**: Hämta

**Begärande-URI**: https: \/ /*Management.Azure.com/Subscriptions/{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/*{Resource-Provider-namespace}* / *{resurs typ}* / *{Resource-Name}*/providers/Microsoft.Insights/Metrics? metricnames =*{Metric}*&TimeSpan =*{StartTime/* slut tid}&$filter =*{filter}*&intervall =*{timeGrain}*&agg regering =*{aggreation}*&API-version =*{API version}*

För att till exempel hämta de tre främsta API: erna, i fallande värde, efter antalet ' transaktioner ' under en 5 minuters intervall, där GeotType var "primär", skulle begäran vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Den resulterande JSON-svars texten liknar följande exempel:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Hämta mått definitioner

Använd [Azure Monitor mått definitioner REST API](/rest/api/monitor/metricdefinitions) för att få åtkomst till listan över mått som är tillgängliga för en tjänst.

**Metod**: Hämta

**Begärande-URI**: https: \/ \/ Management.Azure.com/Subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourceName}*/providers/Microsoft.Insights/metricDefinitions? API-version =*{API version}*

Om du till exempel vill hämta mått definitionerna för en Azure Logic-app ser begäran ut så här:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Om du vill hämta mått definitioner med Azure Monitor REST API använder du "2016-03-01" som API-version.
>
>

Den resulterande JSON-svars texten liknar följande exempel:

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Mer information finns i [listan mått definitioner för en resurs i Azure Monitor REST API](/rest/api/monitor/metricdefinitions) dokumentation.

## <a name="retrieve-metric-values"></a>Hämta mått värden

När de tillgängliga mått definitionerna är kända är det möjligt att hämta relaterade mått värden. Använd måttets namn värde (inte localizedValue) för alla filtrerings begär Anden (till exempel hämta mått data punkter "CpuTime" och "begär Anden"). Om inga filter anges returneras standard måttet.

> [!NOTE]
> Om du vill hämta mått värden med Azure Monitor REST API använder du "2016-09-01" som API-version.
>
>

**Metod**: `GET`

**Begärande-URI**: `https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}/providers/microsoft.insights/metrics?$filter={filter}&api-version={apiVersion}`

För att till exempel hämta RunsSucceeded Mät data punkter för det aktuella tidsintervallet och för en tids kornig het på 1 timme så skulle begäran vara följande:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Den resulterande JSON-svars texten liknar följande exempel:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Om du vill hämta flera data eller agg regerings platser lägger du till mått definitions namnen och sammansättnings typerna i filtret, som du ser i följande exempel:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Den resulterande JSON-svars texten liknar följande exempel:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Använd ARMClient

En annan metod är att använda [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. ARMClient hanterar Azure AD-autentiseringen (och den resulterande JWT-token) automatiskt. Följande steg beskriver hur du använder ARMClient för att hämta mått data:

1. Installera [choklad](https://chocolatey.org/) och [ARMClient](https://github.com/projectkudu/armclient).
2. Skriv *armclient.exe inloggning* i ett terminalfönster. Om du gör det blir du ombedd att logga in på Azure.
3. Skriv *ARMCLIENT get [your_resource_id]/providers/Microsoft.Insights/metricdefinitions? API-version = 2016-03-01*
4. Skriv *ARMCLIENT get [your_resource_id]/providers/Microsoft.Insights/Metrics? API-version = 2016-09-01*

Om du till exempel vill hämta mått definitionerna för en speciell Logi Kap par utfärdar du följande kommando:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Hämta resurs-ID

Med hjälp av REST API kan du verkligen hjälpa till att förstå tillgängliga mått definitioner, granularitet och relaterade värden. Informationen är användbar när du använder [Azures hanterings bibliotek](/previous-versions/azure/reference/mt417623(v=azure.100)).

I föregående kod är resurs-ID: t som används den fullständiga sökvägen till önskad Azure-resurs. Om du till exempel vill fråga mot en Azure-webbapp blir resurs-ID:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Följande lista innehåller några exempel på resurs-ID-format för olika Azure-resurser:

* **IoT Hub** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/Microsoft.Devices/IotHubs/*{IoT-hubb-Name}*
* **Elastisk SQL-pool** –*/Subscriptions/{Subscription-ID}*/resourceGroups/*{resurs grupp-namn}*/providers/Microsoft.SQL/Servers/*{pool-DB}*/elasticpools/*{SQL-pool-Name}*
* **SQL Database (V12)** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs grupp-namn}*/providers/Microsoft.SQL/Servers/*{Server-Name}*/databases/*{Database-Name}*
* **Service Bus** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/Microsoft.ServiceBus/*{namespace}* / *{Service Bus-Name}*
* **Skalnings uppsättningar för virtuella datorer** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{VM-Name}*
* **VM** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/Microsoft.Compute/virtualMachines/*{VM-Name}*
* **Event Hubs** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{resurs-grupp-Name}*/providers/Microsoft.EventHub/Namespaces/*{EventHub-namespace}*

Det finns alternativa metoder för att hämta resurs-ID: t, inklusive att använda Azure Resource Explorer, visa önskad resurs i Azure Portal och via PowerShell eller Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

För att hitta resurs-ID för en önskad resurs, är det en bra idé att använda [Azure Resource Explorer](https://resources.azure.com) -verktyget. Navigera till önskad resurs och titta sedan på det ID som visas, som i följande skärm bild:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Resurs-ID: t kan också hämtas från Azure Portal. Det gör du genom att navigera till önskad resurs och sedan välja egenskaper. Resurs-ID visas i avsnittet egenskaper, som visas på följande skärm bild:

![Alt "resurs-ID som visas på bladet egenskaper i Azure Portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Resurs-ID: t kan hämtas med hjälp av Azure PowerShell-cmdletar också. Om du till exempel vill hämta resurs-ID för en Azure Logic-app kör du Get-AzureLogicApp-cmdleten, som i följande exempel:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Resultatet bör likna följande exempel:

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

Om du vill hämta resurs-ID för ett Azure Storage konto med hjälp av Azure CLI kör du `az storage account show` kommandot, som visas i följande exempel:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Resultatet bör likna följande exempel:

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps är ännu inte tillgängliga via Azure CLI, vilket innebär att ett Azure Storage-konto visas i föregående exempel.
>
>

## <a name="retrieve-activity-log-data"></a>Hämta aktivitets logg data

Förutom mått definitioner och relaterade värden är det också möjligt att använda Azure Monitor REST API för att hämta ytterligare intressanta insikter relaterade till Azure-resurser. Som exempel är det möjligt att fråga efter [aktivitets logg](/rest/api/monitor/activitylogs) data. Följande exempel begär Anden använder Azure Monitor REST API för att fråga aktivitets loggen.

Hämta aktivitets loggar med filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Hämta aktivitets loggar med filter och välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitets loggar med Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitets loggar utan filter eller välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Nästa steg

* Granska [översikten över övervakningen](../overview.md).
* Visa de [mått som stöds med Azure Monitor](./metrics-supported.md).
* Granska [Microsoft Azure övervakare REST API referens](/rest/api/monitor/).
* Granska [Azures hanterings bibliotek](/previous-versions/azure/reference/mt417623(v=azure.100)).