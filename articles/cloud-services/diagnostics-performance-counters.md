---
title: Samla in prestanda räknare i Azure Cloud Services (klassisk) | Microsoft Docs
description: Lär dig hur du identifierar, använder och skapar prestanda räknare i Cloud Services med Azure-diagnostik och Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fa5dd61c0764be45cdba68b73a4f55745ee5e55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585500"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Samla in prestanda räknare för Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Prestanda räknare gör det möjligt för dig att spåra hur väl ditt program och värden presterar. Windows Server tillhandahåller många olika prestanda räknare relaterade till maskin vara, program, operativ systemet och mycket annat. Genom att samla in och skicka prestanda räknare till Azure kan du analysera den här informationen för att hjälpa dig att fatta bättre beslut. 

## <a name="discover-available-counters"></a>Identifiera tillgängliga räknare

En prestanda räknare består av två delar, ett uppsättnings namn (kallas även en kategori) och en eller flera räknare. Du kan använda PowerShell för att hämta en lista över tillgängliga prestanda räknare:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName`Egenskapen representerar en uppsättning (eller kategori) och är en bra indikator på vad prestanda räknarna är relaterade till. `Paths`Egenskapen representerar en uppsättning räknare för en mängd. Du kan också hämta `Description` egenskapen för mer information om räknar uppsättningen.

Om du vill hämta alla räknare för en uppsättning använder du `CounterSetName` värdet och expanderar `Paths` samlingen. Varje Sök vägs objekt är en räknare som du kan fråga. Om du till exempel vill hämta tillgängliga räknare som är relaterade till `Processor` uppsättningen expanderar du `Paths` samlingen:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Dessa enskilda räknar Sök vägar kan läggas till i det diagnostik-ramverk som moln tjänsten använder. Mer information om hur en prestanda räknar Sök väg skapas finns i [Ange en räknar Sök väg](/windows/win32/perfctrs/specifying-a-counter-path).

## <a name="collect-a-performance-counter"></a>Samla in en prestanda räknare

Du kan lägga till en prestanda räknare i moln tjänsten för antingen Azure-diagnostik eller Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application insikter för Cloud Services gör att du kan ange vilka prestanda räknare som du vill samla in. När du har [lagt till Application Insights i projektet](../azure-monitor/app/cloudservices.md#sdk)läggs en konfigurations fil med namnet **ApplicationInsights.config** till i Visual Studio-projektet. I den här konfigurations filen definieras vilken typ av information Application Insights samlas in och skickas till Azure.

Öppna **ApplicationInsights.config** -filen och hitta **ApplicationInsights**  >  **TelemetryModules** -elementet. Varje `<Add>` underordnat element definierar en typ av telemetri som ska samlas in, tillsammans med dess konfiguration. Modulen för telemetri av prestanda räknare är `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Om det här elementet redan har definierats ska du inte lägga till det en andra gång. Varje prestanda räknare som ska samlas in definieras under en nod med namnet `<Counters>` . Här är ett exempel som samlar in enhets prestanda räknare:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Varje prestanda räknare visas som ett `<Add>` element under `<Counters>` . `PerformanceCounter`Attributet definierar vilken prestanda räknare som ska samlas in. `ReportAs`Attributet är den rubrik som ska visas i Azure Portal för prestanda räknaren. Alla prestanda räknare som du samlar in placeras i en kategori med namnet **Custom** i portalen. Till skillnad från Azure-diagnostik kan du inte ange intervallet som de här prestanda räknarna samlas in och skickas till Azure. Med Application Insights samlas prestanda räknare och skickas varje minut. 

Application Insights samlar automatiskt in följande prestanda räknare:

* \Process (?? APP_WIN32_PROC??) \% Processor tid
* \Memory\Tillgängliga byte
* \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
* \Process(??APP_WIN32_PROC??)\Privata byte
* \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
* \Processor(_Total)\% processortid

Mer information finns i [system prestanda räknare i Application Insights](../azure-monitor/app/performance-counters.md) och [Application Insights för Azure-Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

> [!IMPORTANT]
> Även om alla dessa data sammanställs i lagrings kontot, ger portalen **inget** inbyggt sätt att skapa diagram över data. Vi rekommenderar starkt att du integrerar en annan diagnostisk tjänst, t. ex. Application Insights, i ditt program.

Med Azure-diagnostik tillägget för Cloud Services kan du ange vilka prestanda räknare som du vill samla in. Information om hur du konfigurerar Azure-diagnostik finns i [Översikt över Cloud Service Monitoring](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

De prestanda räknare som du vill samla in definieras i filen **Diagnostics. wadcfgx** . Öppna den här filen (den definieras per roll) i Visual Studio och hitta **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters** -elementet. Lägg till ett nytt **PerformanceCounterConfiguration** -element som underordnat. Det här elementet har två attribut: `counterSpecifier` och `sampleRate` . `counterSpecifier`Attributet definierar vilken system prestanda räknare som angetts (beskrivs i föregående avsnitt) för att samla in. `sampleRate`Värdet anger hur ofta det värdet ska avsökas. Som helhet överförs alla prestanda räknare till Azure enligt det överordnade `PerformanceCounters` elementets `scheduledTransferPeriod` attributvärde.

Mer information om `PerformanceCounters` schema elementet finns i [Azure-diagnostik schemat](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element).

Den period som definieras av `sampleRate` attributet använder data typen XML-varaktighet för att ange hur ofta prestanda räknaren ska avsökas. I exemplet nedan är frekvensen inställd på `PT3M` , vilket innebär att `[P]eriod[T]ime[3][M]inutes` : var tredje minut.

Mer information om hur `sampleRate` och `scheduledTransferPeriod` är definierade finns i avsnittet **varaktighets data typ** i självstudierna [w3 XML datum-och tid datum typer](https://www.w3schools.com/XML/schema_dtypes_date.asp) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Skapa en ny perf-räknare

En ny prestanda räknare kan skapas och användas av din kod. Din kod som skapar en ny prestanda räknare måste köra förhöjt, annars kommer den att Miss det. Din moln tjänst `OnStart` Start kod kan skapa prestanda räknaren som kräver att du kör rollen i en upphöjd kontext. Eller så kan du skapa en start aktivitet som körs med förhöjd behörighet och skapar prestanda räknaren. Mer information om start åtgärder finns i [så här konfigurerar och kör du Start åtgärder för en moln tjänst](cloud-services-startup-tasks.md).

Om du vill konfigurera att rollen ska köras med förhöjt tillägg lägger du till ett- `<Runtime>` element i [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) -filen.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Du kan skapa och registrera en ny prestanda räknare med några rader kod. Använd `System.Diagnostics.PerformanceCounterCategory.Create` metoden överlagring som skapar både kategorin och räknaren. Följande kod kontrollerar först om kategorin finns, och om den saknas skapas både kategorin och räknaren.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

När du vill använda räknaren anropar du- `Increment` eller- `IncrementBy` metoden.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Nu när ditt program använder din anpassade räknare måste du konfigurera Azure-diagnostik eller Application Insights för att spåra räknaren.


### <a name="application-insights"></a>Application Insights

Som tidigare angetts definieras prestanda räknare för Application Insights i **ApplicationInsights.configs** filen. Öppna **ApplicationInsights.config** och hitta elementet **ApplicationInsights**  >  **TelemetryModules**  >  **Add**  >  **Counters** . Skapa ett `<Add>` underordnat element och ange `PerformanceCounter` attributet till kategori och namn för den prestanda räknare som du skapade i din kod. Ange `ReportAs` attributet till ett eget namn som du vill ska visas i portalen.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Som tidigare angavs definieras de prestanda räknare som du vill samla in i filen **Diagnostics. wadcfgx** . Öppna den här filen (den definieras per roll) i Visual Studio och hitta **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters** -elementet. Lägg till ett nytt **PerformanceCounterConfiguration** -element som underordnat. Ange `counterSpecifier` attributet till kategori och namn för den prestanda räknare som du skapade i din kod. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Mer information

- [Application Insights för Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [System prestanda räknare i Application Insights](../azure-monitor/app/performance-counters.md)
- [Ange en räknar Sök väg](/windows/win32/perfctrs/specifying-a-counter-path)
- [Azure-diagnostik schema – prestanda räknare](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)