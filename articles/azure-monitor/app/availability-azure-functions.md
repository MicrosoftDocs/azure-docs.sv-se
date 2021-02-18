---
title: Skapa och kör anpassade tillgänglighets test med Azure Functions
description: Det här dokumentet beskriver hur du skapar en Azure-funktion med TrackAvailability () som körs regelbundet enligt konfigurationen i funktionen TimerTrigger. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och Avisera om tillgänglighets resultat data. Med anpassade tester kan du skriva mer komplexa tillgänglighets test än vad som är möjligt med hjälp av Portal gränssnittet, övervaka en app inuti ditt Azure VNET, ändra slut punkts adressen eller skapa ett tillgänglighets test om det inte är tillgängligt i din region.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589943"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Skapa och kör anpassade tillgänglighets test med Azure Functions

Den här artikeln beskriver hur du skapar en Azure-funktion med TrackAvailability () som kommer att köras regelbundet enligt konfigurationen i TimerTrigger-funktionen med din egen affärs logik. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och Avisera om tillgänglighets resultat data. På så sätt kan du skapa anpassade tester som liknar vad du kan göra via [tillgänglighets övervakning](./monitor-web-app-availability.md) i portalen. Med anpassade tester kan du skriva mer komplexa tillgänglighets test än vad som är möjligt med hjälp av Portal gränssnittet, övervaka en app inuti ditt Azure VNET, ändra slut punkts adressen eller skapa ett tillgänglighets test även om den här funktionen inte är tillgänglig i din region.

> [!NOTE]
> Det här exemplet är utformat enbart för att Visa Mechanics för hur API-anropet TrackAvailability () fungerar i en Azure-funktion. Det går inte att skriva den underliggande koden/affärs logiken för HTTP-test som krävs för att göra detta till ett fullständigt funktionellt tillgänglighets test. Som standard om du går igenom det här exemplet kommer du att skapa ett tillgänglighets test som alltid ska generera ett haveri.

## <a name="create-timer-triggered-function"></a>Skapa timer-utlöst funktion

- Om du har en Application Insights-resurs:
    - Som standard skapar Azure Functions en Application Insights resurs, men om du vill använda en av dina redan skapade resurser måste du ange att du under genereringen.
    - Följ anvisningarna för hur du [skapar en funktion som utlöses av en Azure Functions resurs och timer](../../azure-functions/functions-create-scheduled-function.md) (stoppa före rensning) med följande val.
        -  Välj fliken **övervakning** längst upp.

            ![ Skapa en Azure Functions-app med din egen App Insights-resurs](media/availability-azure-functions/create-function-app.png)

        - Markera List rutan Application Insights och skriv eller välj namnet på din resurs.

            ![Välja befintlig Application Insights resurs](media/availability-azure-functions/app-insights-resource.png)

        - Välj **Granska + skapa**
- Om du inte har en Application Insights-resurs som har skapats ännu för din timer-utlöst funktion:
    - Som standard när du skapar ditt Azure Functions program skapas en Application Insights resurs åt dig.
    - Följ anvisningarna för hur du [skapar en Azure Functions resurs och timer-utlöst funktion](../../azure-functions/functions-create-scheduled-function.md) (stoppa före rensning).

## <a name="sample-code"></a>Exempelkod

Kopiera koden nedan till filen Run. CSX (det ersätter den befintliga koden). Det gör du genom att gå till Azure Functions programmet och välja funktionen timer-utlösare till vänster.

>[!div class="mx-imgBorder"]
>![Azure Functions Run. CSX i Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> För den slut punkts adress som du använder: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . Om inte din resurs finns i en region som Azure Government eller Azure Kina, i vilket fall läser du den här artikeln om hur [du åsidosätter standard slut punkterna](./custom-endpoints.md#regions-that-require-endpoint-modification) och väljer lämplig telemetri-kanal slut punkt för din region.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Till höger under Visa filer väljer du **Lägg till**. Anropa den nya fil **funktionen. proj** med följande konfiguration.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Till höger väljer du Lägg till. Namnge fil funktionen. proj](media/availability-azure-functions/addfile.png)

Till höger under Visa filer väljer du **Lägg till**. Anropa den nya filen **runAvailabilityTest. CSX** med följande konfiguration.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Kontrol lera tillgänglighet

För att se till att allt fungerar kan du titta på diagrammet på fliken tillgänglighet i din Application Insights-resurs.

> [!NOTE]
> Om du har implementerat din egen affärs logik i runAvailabilityTest. CSX visas lyckade resultat som i skärm bilderna nedan, om du inte gjorde det visas misslyckade resultat. Test som skapas med `TrackAvailability()` visas med **anpassad** bredvid test namnet.

>[!div class="mx-imgBorder"]
>![Fliken tillgänglighet med lyckade resultat](media/availability-azure-functions/availability-custom.png)

Om du vill se en transaktions information från slut punkt till slut punkt väljer du **lyckades** eller **misslyckades** under detalj granskning till och väljer sedan ett exempel. Du kan också komma till transaktions informationen från slut punkt till slut punkt genom att välja en data punkt i diagrammet.

>[!div class="mx-imgBorder"]
>![Välj ett exempel på tillgänglighets test](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Transaktions information från slut punkt till slut punkt](media/availability-azure-functions/end-to-end.png)

Om du körde allting som det är (utan att lägga till affärs logik) ser du att testet misslyckades.

## <a name="query-in-logs-analytics"></a>Fråga i loggar (analys)

Du kan använda loggar (analys) om du vill visa tillgänglighets resultat, beroenden och mer. Mer information om loggar finns i [Översikt över logg frågor](../logs/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Tillgänglighets resultat](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Skärm bild som visar fliken ny fråga med beroenden begränsat till 50.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Nästa steg

- [Programkarta](./app-map.md)
- [Transaktionsdiagnostik](./transaction-diagnostics.md)

