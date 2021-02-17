---
title: Azure Monitor Application Insights arbets yta-baserat resurs schema
description: Lär dig mer om den nya tabell strukturen och schemat för Azure Monitor Application Insights arbets ytans baserade resurser.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: ef9d22cd2b45679928ee54778b2a521ea9ecab03
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575606"
---
# <a name="workspace-based-resource-changes"></a>Arbets ytans-baserade resurs ändringar

Innan du inför införandet av [arbets ytans baserade Application Insights resurser](create-workspace-resource.md), lagrades Application Insights data separat från andra loggdata i Azure Monitor. Båda baseras på Azure Datautforskaren och använder samma KQL (Kusto Query Language). Detta beskrivs i [loggarna i Azure Monitor](../logs/data-platform-logs.md).

Med arbets ytans baserade Application Insights resurs data lagras i en Log Analytics arbets yta med andra övervaknings data och program data. Detta fören klar konfigurationen genom att göra det enklare att analysera data över flera lösningar och utnyttja funktionerna i arbets ytor.

## <a name="table-structure"></a>Tabellstruktur

| Äldre tabell namn | Nytt tabellnamn | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Sammanfattnings data från tillgänglighets test.|
| browserTimings | AppBrowserTimings | Data om klient prestanda, till exempel hur lång tid det tar att bearbeta inkommande data.|
| relation | AppDependencies | Anropar från programmet till andra komponenter (inklusive externa komponenter) som registrerats via TrackDependency () – till exempel anrop till REST API, databas eller ett fil system.  |
| customEvents | AppEvents | Anpassade händelser som skapats av ditt program. |
| customMetrics | AppMetrics | Anpassade mått som skapats av ditt program. |
| pageViews | AppPageViews| Data om varje webbplats-vy med webb läsar information. |
| performanceCounters | AppPerformanceCounters | Prestanda mått från beräknings resurserna som stöder programmet, till exempel Windows prestanda räknare. |
| autentiseringsbegäran | AppRequests | Förfrågningar som tagits emot av ditt program. En separat post för begäran loggas till exempel för varje HTTP-begäran som din webbapp tar emot.  |
| undantag | AppSystemEvents | Undantag som har utlösts av program körningen och fångar både server sidans och klient sidans undantag. |
| Anden | AppTraces | Detaljerade loggar (spår) har genererats via program kod/loggnings ramverk som registrerats via TrackTrace (). |

## <a name="table-schemas"></a>Tabell scheman

I följande avsnitt visas mappningen mellan de klassiska egenskaps namnen och den nya arbets ytans baserade Application Insights egenskaps namn.  Använd den här informationen för att konvertera frågor med hjälp av äldre tabeller.

De flesta av kolumnerna har samma namn med olika Skift läge. Eftersom KQL är Skift läges känslig måste du ändra varje kolumn namn tillsammans med tabell namnen i befintliga frågor. Kolumner med ändringar förutom versaler markeras. Du kan fortfarande använda dina klassiska Application Insights frågor i fönstret **loggar** i Application Insights-resursen, även om det är en arbets yta som baseras på arbets ytan. De nya egenskaps namnen krävs för när du frågar från i kontexten för den Log Analytics arbets ytans upplevelse.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Äldre tabell: tillgänglighet

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|varaktighet|real|DurationMs|real|
|`id`|sträng|`Id`|sträng|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|Sträng|
|location|sträng|Location|sträng|
|meddelande|sträng|Meddelande|sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|performanceBucket|sträng|PerformanceBucket|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|ikoner|real|Storlek|real|
|lyckades|sträng|Klart|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Äldre tabell: browserTimings

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|name|sträng|Name|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|performanceBucket|sträng|PerformanceBucket|sträng|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|sträng|SdkVersion|sträng|
|sendDuration|real|SendDurationMs|real|
|session_Id|sträng|SessionId|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|sträng|URL|sträng|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appdependencies"></a>AppDependencies

Äldre tabell: beroenden

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|data|sträng|Data|sträng|
|varaktighet|real|DurationMs|real|
|`id`|sträng|`Id`|sträng|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|Sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|performanceBucket|sträng|PerformanceBucket|sträng|
|resultCode|sträng|ResultCode|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|lyckades|sträng|Klart|Bool|
|fokusera|sträng|Mål|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|typ|sträng|DependencyType|sträng|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appevents"></a>AppEvents

Äldre tabell: customEvents

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appmetrics"></a>AppMetrics

Äldre tabell: customMetrics

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|iKey|sträng|IKey|sträng|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|
|värde|real|bort||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Äldre tabell: pageViews

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|varaktighet|real|DurationMs|real|
|`id`|sträng|`Id`|sträng|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|Sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|performanceBucket|sträng|PerformanceBucket|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|url|sträng|URL|sträng|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Äldre tabell: performanceCounters

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|category|sträng|Kategori|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|räknare|sträng|bort||
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|iKey|sträng|IKey|sträng|
|session|sträng|Instans|sträng|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|name|sträng|Name|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|
|värde|real|Värde|real|

### <a name="apprequests"></a>AppRequests

Äldre tabell: begär Anden

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|Dynamisk|
|customMeasurements|dynamisk|Mått|Dynamisk|
|varaktighet|real|DurationMs|Verkligen|
|`id`|sträng|`Id`|Sträng|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|Sträng|
|name|sträng|Name|Sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|performanceBucket|sträng|PerformanceBucket|Sträng|
|resultCode|sträng|ResultCode|Sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|källa|sträng|Källa|Sträng|
|lyckades|sträng|Klart|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|sträng|URL|Sträng|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="appsystemevents"></a>AppSystemEvents

Äldre tabell: undantag

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|paket|sträng|Sammansättning|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|dynamisk|
|customMeasurements|dynamisk|Mått|dynamisk|
|information|dynamisk|Information|dynamisk|
|handledAt|sträng|HandledAt|sträng|
|iKey|sträng|IKey|sträng|
|innermostAssembly|sträng|InnermostAssembly|sträng|
|innermostMessage|sträng|InnermostMessage|sträng|
|innermostMethod|sträng|InnermostMethod|sträng|
|innermostType|sträng|InnermostType|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|meddelande|sträng|Meddelande|sträng|
|metod|sträng|Metod|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|outerAssembly|sträng|OuterAssembly|sträng|
|outerMessage|sträng|OuterMessage|sträng|
|outerMethod|sträng|OuterMethod|sträng|
|outerType|sträng|OuterType|sträng|
|problemId|sträng|ProblemId|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|typ|sträng|Undantag|sträng|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

### <a name="apptraces"></a>AppTraces

Äldre tabell: spår

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|sträng|\_ResourceGUID|sträng|
|application_Version|sträng|AppVersion|sträng|
|Program|sträng|\_ResourceId|sträng|
|client_Browser|sträng|ClientBrowser|sträng|
|client_City|sträng|ClientCity|sträng|
|client_CountryOrRegion|sträng|ClientCountryOrRegion|sträng|
|client_IP|sträng|ClientIP|sträng|
|client_Model|sträng|ClientModel|sträng|
|client_OS|sträng|Klientbegäran|sträng|
|client_StateOrProvince|sträng|ClientStateOrProvince|sträng|
|client_Type|sträng|ClientType|sträng|
|cloud_RoleInstance|sträng|AppRoleInstance|sträng|
|cloud_RoleName|sträng|AppRoleName|sträng|
|customDimensions|dynamisk|Egenskaper|dynamisk|
|customMeasurements|dynamisk|Mått|dynamisk|
|iKey|sträng|IKey|sträng|
|itemCount|int|ItemCount|int|
|itemId|sträng|\_ItemId|sträng|
|itemType|sträng|Typ|sträng|
|meddelande|sträng|Meddelande|sträng|
|operation_Id|sträng|OperationId|sträng|
|operation_Name|sträng|OperationName|sträng|
|operation_ParentId|sträng|OperationParentId|sträng|
|operation_SyntheticSource|sträng|OperationSyntheticSource|sträng|
|sdkVersion|sträng|SdkVersion|sträng|
|session_Id|sträng|SessionId|sträng|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|sträng|UserAccountId|sträng|
|user_AuthenticatedId|sträng|UserAuthenticatedId|sträng|
|user_Id|sträng|UserId|sträng|

## <a name="next-steps"></a>Nästa steg

* [Utforska mått](../essentials/metrics-charts.md)
* [Skriv analysfrågor](../logs/log-query-overview.md)

