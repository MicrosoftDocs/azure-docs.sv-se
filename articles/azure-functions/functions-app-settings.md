---
title: Referens för appinställningar för Azure Functions
description: Referens dokumentation för Azure Functions app-inställningar eller miljövariabler.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: b17db828aeb19c3347c0db4babf0eee2b9d5f280
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589308"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för appinställningar för Azure Functions

App-inställningar i en Function-app innehåller globala konfigurations alternativ som påverkar alla funktioner för den Function-appen. När du kör lokalt öppnas de här inställningarna som lokala [miljövariabler](functions-run-local.md#local-settings-file). Den här artikeln innehåller appinställningar som är tillgängliga i Function Apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurations alternativ i [host.jspå](functions-host-json.md) filen och i [local.settings.js](functions-run-local.md#local-settings-file) filen.

> [!NOTE]  
> Du kan använda program inställningarna för att åsidosätta host.jsatt ange värden utan att behöva ändra host.jspå själva filen. Detta är användbart för scenarier där du behöver konfigurera eller ändra vissa host.jspå Inställningar för en speciell miljö. Detta gör det också möjligt att ändra host.jspå inställningar utan att behöva publicera projektet på annat sätt. Mer information finns i [ referens artikelnhost.js](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Instrumentation-nyckeln för Application Insights. Använd bara en av `APPINSIGHTS_INSTRUMENTATIONKEY` eller `APPLICATIONINSIGHTS_CONNECTION_STRING` . Mer information finns i [övervaka Azure Functions](functions-monitoring.md). 

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Anslutnings strängen för Application Insights. Använd `APPLICATIONINSIGHTS_CONNECTION_STRING` i stället för `APPINSIGHTS_INSTRUMENTATIONKEY` när din funktions app kräver tillagda anpassningar som stöds med hjälp av anslutnings strängen. Mer information finns i [anslutnings strängar](../azure-monitor/app/sdk-connection-string.md). 

|Nyckel|Exempelvärde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [key]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Som standard använder [Functions-proxyservrar](functions-proxies.md) en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma Function-app. Den här genvägen används i stället för att skapa en ny HTTP-begäran. Med den här inställningen kan du inaktivera gen vägs beteendet.

|Tangent|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Anrop med en server dels-URL som pekar på en funktion i den lokala Function-appen skickas inte direkt till funktionen. I stället omdirigeras begär Anden tillbaka till HTTP-frontend för Function-appen.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|falskt|Anrop med en server dels-URL som pekar på en funktion i den lokala Function-appen vidarebefordras direkt till funktionen. Detta är standardvärdet. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om tecknen `%2F` avkodas som snedstreck i väg parametrar när de infogas i Server dels-URL: en. 

|Tangent|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Väg parametrar med kodade snedstreck avkodas. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|falskt|Alla väg parametrar skickas längs oförändrade, vilket är standard beteendet. |

Du kan till exempel ta proxies.jspå filen för en Function-app på `myfunction.com` domänen.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

När `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` är inställt på `true` , `example.com/api%2ftest` matchas URL: en till `example.com/api/test` . Som standard förblir URL: en oförändrad som `example.com/test%2fapi` . Mer information finns i [Function-proxy](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

I version 2. x och senare versioner av Functions-körningen konfigurerar du appens beteende baserat på körnings miljön. Det här värdet [läses vid initiering](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Du kan ange `AZURE_FUNCTIONS_ENVIRONMENT` valfritt värde, men [tre värden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) stöds: [utveckling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [mellanlagring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)och [produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). När `AZURE_FUNCTIONS_ENVIRONMENT` inte har angetts används standardvärdet `Development` i en lokal miljö och `Production` på Azure. Den här inställningen ska användas i stället för `ASPNETCORE_ENVIRONMENT` att ställa in körnings miljön. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

I version 2. x och senare versioner av Functions-körningen kan program inställningarna åsidosätta [host.jspå](functions-host-json.md) inställningar i den aktuella miljön. Dessa åsidosättningar uttrycks som program inställningar med namnet `AzureFunctionsJobHost__path__to__setting` . Mer information finns i [åsidosätt host.jspå värden](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfri anslutnings sträng för lagrings konto för att lagra loggar och visa dem på fliken **övervakning** i portalen. Den här inställningen är endast giltig för appar som är riktade till version 1. x i Azure Functions Runtime. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Mer information finns i [krav för lagrings konton](storage-considerations.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = <name> ; AccountKey =<key>|

> [!NOTE]
> För bättre prestanda och erfarenhet använder kör som version 2. x och senare versioner APPINSIGHTS_INSTRUMENTATIONKEY och App Insights för övervakning i stället för `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` innebär att inaktivera standard sidan för landning som visas för rot-URL: en för en Function-app. Standardvärdet är `false`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHomepage|true|

När den här appens inställning utelämnas eller anges till `false` visas en sida som liknar följande exempel som svar på URL: en `<functionappname>.azurewebsites.net` .

![Funktion för programmets landnings sida](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` innebär användning av versions läge när .NET-kod kompileras. `false` betyder Använd fel söknings läge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta funktioner som ska aktive ras. Beta funktioner som aktive ras av de här flaggorna är inte produktion klara, men kan aktive ras för experimentell användning innan de går live.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger lagrings platsen eller providern som ska användas för nyckel lagring. För närvarande är de databaser som stöds Blob Storage ("BLOB") och det lokala fil systemet ("filer"). Standardvärdet är BLOB i version 2 och fil system i version 1.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|Files|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions runtime använder den här anslutnings strängen för lagrings kontot för alla funktioner utom för HTTP-utlösta funktioner. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Se krav för [lagrings konto](functions-infrastructure-as-code.md#storage-account) och [lagrings konto](storage-considerations.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till den kompilator som används för TypeScript. Gör att du kan åsidosätta standardvärdet om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>\_ \_ redigerings \_ läge för Function-appen

Avgör om redigering i Azure Portal är aktive rad. Giltiga värden är "readwrite" och "ReadOnly".

|Nyckel|Exempelvärde|
|---|------------|
|\_ \_ redigerings \_ läge för Function-appen|ReadOnly|

## <a name="functions_extension_version"></a>FUNKTIONS \_ tilläggs \_ version

Den version av Functions runtime som ska användas i den här Function-appen. Ett tilde med huvud version innebär att använda den senaste versionen av den högre versionen (till exempel "~ 2"). När nya versioner av samma huvud version är tillgängliga installeras de automatiskt i Function-appen. Använd det fullständiga versions numret (till exempel "2.0.12345") för att fästa appen till en angiven version. Standardvärdet är "~ 2". Ett värde för `~1` PIN-modulen till version 1. x av körnings miljön.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONS \_ tilläggs \_ version|~ 2|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONs \_ v2- \_ kompatibilitetsläge \_

Den här inställningen gör att din Function-app kan köras i version 2. x-kompatibelt läge på version 3. x-körningsmiljön. Använd endast den här inställningen om du stöter på problem när [du uppgraderar din Function-app från version 2. x till 3. x i körnings miljön](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Den här inställningen är endast avsedd som en kortsiktig lösning medan du uppdaterar appen så att den körs korrekt på version 3. x. Den här inställningen stöds så länge [2. x-körningen stöds](functions-versions.md). Om du stöter på problem som förhindrar att din app körs på version 3. x utan att använda den här inställningen kan du [rapportera problemet](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Kräver att [ \_ tilläggs \_ versionen av funktioner](functions-app-settings.md#functions_extension_version) anges till `~3` .

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONs \_ v2- \_ kompatibilitetsläge \_|true|

## <a name="functions_worker_process_count"></a>FUNKTIONER \_ arbets \_ process \_ antal

Anger det maximala antalet språk arbets processer, med standardvärdet `1` . Det högsta tillåtna värdet är `10` . Funktions anrop distribueras jämnt mellan olika språk arbets processer. Språk arbets processer har skapats var 10: e sekund tills antalet arbets processer som har angetts av funktioner \_ \_ \_ har nåtts. Användning av flera språk arbets processer är inte samma sak som [skalning](functions-scale.md). Överväg att använda den här inställningen när din arbets belastning har en blandning av processor gränser och I/O-kopplade anrop. Den här inställningen gäller för alla non-.NET-språk.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER \_ arbets \_ process \_ antal|2|


## <a name="functions_worker_runtime"></a>FUNKTIONER \_ Worker \_ runtime

Språk arbets körningen som ska läsas in i Function-appen.  Detta motsvarar det språk som används i ditt program (till exempel "dotNet"). För funktioner på flera språk måste du publicera dem till flera appar, var och en med motsvarande arbetares körnings värde.  Giltiga värden är `dotnet` (C#/f #), `node` (Java Script/typescript), `java` (Java), `powershell` (PowerShell) och `python` (python).

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER \_ Worker \_ runtime|dotnet|

## <a name="pip_extra_index_url"></a>\_URL för extra \_ index \_ för pip

Värdet för den här inställningen indikerar en anpassad URL för paket index för python-appar. Använd den här inställningen när du behöver köra en fjärran sluten version med anpassade beroenden som finns i ett extra paket index.   

|Nyckel|Exempelvärde|
|---|------------|
|\_URL för extra \_ index \_ för pip|http://my.custom.package.repo/simple |

Mer information finns i [anpassade beroenden](functions-reference-python.md#remote-build-with-extra-index-url) i python Developer reference.

## <a name="scale_controller_logging_enable"></a>\_ \_ Aktivera loggning av skalnings styrenhet \_

_Den här inställningen är för närvarande en för hands version._  

Den här inställningen styr loggning från den Azure Functions skalnings styrenheten. Mer information finns i [loggar för Scale controllers](functions-monitoring.md#scale-controller-logs-preview).

|Nyckel|Exempelvärde|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: utförlig|

Värdet för den här nyckeln anges i formatet `<DESTINATION>:<VERBOSITY>` , som definieras enligt följande:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBBPLATS \_ CONTENTAZUREFILECONNECTIONSTRING

Endast för förbruknings & Premium-planer. Anslutnings sträng för lagrings kontot där programmets kod och konfiguration lagras. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="website_contentshare"></a>WEBBPLATS \_ CONTENTSHARE

Endast för förbruknings & Premium-planer. Fil Sök vägen till programmets kod och konfiguration. Används med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standard är en unik sträng som börjar med namnet på Function-appen. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBBPLATS \_ högsta \_ dynamiska \_ program \_ skala \_ ut

Det maximala antalet instanser som Function-appen kan skala ut till. Standardvärdet är ingen gräns.

> [!IMPORTANT]
> Den här inställningen är i för hands version.  En [app-egenskap för funktionen högsta skalbarhet](./functions-scale.md#limit-scale-out) har lagts till och är det rekommenderade sättet att begränsa skalan.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS \_ högsta \_ dynamiska \_ program \_ skala \_ ut|5|

## <a name="website_node_default_version"></a>\_NODEN webbplats \_ DEFAULT_VERSION

_Endast Windows._  
Anger vilken version av Node.js som ska användas när du kör din Function-app i Windows. Du bör använda tilde (~) om du vill att körnings miljön ska använda den senaste tillgängliga versionen av mål versionen. Om till exempel är inställt på `~10` , används den senaste versionen av Node.js 10. När en högre version är riktad mot ett tilde behöver du inte uppdatera den lägre versionen manuellt. 

|Nyckel|Exempelvärde|
|---|------------|
|\_NODEN webbplats \_ DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBBPLATS som \_ körs \_ från \_ paket

Gör att din Function-app kan köras från en monterad paket fil.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS som \_ körs \_ från \_ paket|1|

Giltiga värden är antingen en URL som matchar platsen för en distributions paket fil eller `1` . När det är inställt på `1` måste paketet finnas i `d:\home\data\SitePackages` mappen. När du använder zip-distribution med den här inställningen överförs paketet automatiskt till den här platsen. I för hands versionen hette den här inställningen `WEBSITE_RUN_FROM_ZIP` . Mer information finns i [köra funktioner från en paket fil](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>WEBBPLATS \_ \_ tidszon

Gör att du kan ange tids zonen för din Function-app. 

|Nyckel|Operativsystem|Exempelvärde|
|---|--|------------|
|WEBBPLATS \_ \_ tidszon|Windows|Eastern, normal tid|
|WEBBPLATS \_ \_ tidszon|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdaterar appinställningar](functions-how-to-use-azure-function-app-settings.md#settings)

[Se globala inställningar i host.jspå filen](functions-host-json.md)

[Se andra app-inställningar för App Service appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
