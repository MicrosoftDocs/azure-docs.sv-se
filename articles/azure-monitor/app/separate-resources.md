---
title: Hur du utformar din Application Insights-distribution – en vs många resurser?
description: Dirigera telemetri till olika resurser för utveckling, testning och produktions märken.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 264cbe35e7af50577b345d686b639e47760f428d
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258722"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Hur många Application Insights-resurser ska jag distribuera

När du utvecklar nästa version av ett webb program vill du inte blanda [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetri från den nya versionen och den version som redan har släppts. Undvik förvirring genom att skicka Telemetrin från olika utvecklings steg till att separera Application Insights-resurser med separata instrumentande nycklar (ikeys). För att göra det enklare att ändra Instrumentation-nyckeln när en version flyttas från ett steg till en annan, kan det vara praktiskt att ange iKey i kod i stället för i konfigurations filen.

(Om systemet är en Azure-moln tjänst finns det [en metod för att ställa in separata ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Om resurser och instrumentande nycklar

När du konfigurerar Application Insights övervakning för din webbapp skapar du en Application Insights *resurs* i Microsoft Azure. Du öppnar den här resursen i Azure Portal för att se och analysera telemetri som samlats in från din app. Resursen identifieras av en *Instrumentation-nyckel* (iKey). När du installerar Application Insights-paketet för att övervaka din app konfigurerar du den med Instrumentation-nyckeln, så att den vet var du vill skicka telemetrit.

Varje Application Insights resurs levereras med mått som är tillgängliga direkt. Om du rapporterar helt separata komponenter till samma Application Insights resurs, kan dessa mått inte vara begripliga för instrument paneler/aviseringar.

### <a name="when-to-use-a-single-application-insights-resource"></a>När du ska använda en enda Application Insights-resurs

-   För program komponenter som distribueras tillsammans. Utvecklas vanligt vis av ett enda team, som hanteras av samma uppsättning DevOps/ITOps-användare.
-   Om det är klokt att aggregera nyckeltal (KPI: er), till exempel svars tider, frekvenser för haverier på instrument panelen osv., över alla dessa som standard (du kan välja att segmentera efter roll namn i Metrics Explorers upplevelsen).
-   Om du inte behöver hantera rollbaserad Access Control (RBAC) på ett annat sätt mellan program komponenterna.
-   Om du inte behöver några mått på aviserings villkor som skiljer sig mellan komponenterna.
-   Om du inte behöver hantera kontinuerliga exporter på olika sätt mellan komponenterna.
-   Om du inte behöver hantera fakturering/kvoter på ett annat sätt mellan komponenterna.
-   Om det är OK att ha en API-nyckel har samma åtkomst till data från alla komponenter. Och 10 API-nycklar är tillräckliga för behoven i alla.
-   Om det är bra att använda samma inställningar för smart identifiering och arbets objekts integrering i alla roller.

### <a name="other-things-to-keep-in-mind"></a>Andra saker att tänka på

-   Du kan behöva lägga till anpassad kod för att se till att meningsfulla värden anges i [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) -attributet. Utan meningsfulla värden som har angetts för det här attributet fungerar *ingen* av Portal upplevelserna.
- För Service Fabric program och klassiska moln tjänster läser SDK: n automatiskt från Azures roll miljö och ställer in dessa. För alla andra typer av appar behöver du förmodligen ange det här explicit.
-   Live Metrics-upplevelsen stöder inte delning efter roll namn.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamisk Instrumentation-nyckel

För att göra det enklare att ändra iKey när koden flyttas mellan produktions faserna, referera till nyckeln dynamiskt i kod i stället för att använda ett hårdkodad/static-värde.

Ange nyckeln i en initierings metod, till exempel global.aspx.cs i en ASP.NET-tjänst:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

I det här exemplet placeras ikeys för de olika resurserna i olika versioner av webb konfigurations filen. Växling av webb konfigurations filen – som du kan göra som en del av versions skriptet – byter mål resurs.

### <a name="web-pages"></a>Webbsidor
IKey används också i appens webb sidor, i det [skript som du har fått från snabb starts fönstret](../../azure-monitor/app/javascript.md). I stället för att koda det bokstavligen i skriptet kan du generera det från Server tillstånd. Till exempel i en ASP.NET-app:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Skapa ytterligare Application Insights resurser

Om du vill skapa en program insikts resurs följer du [guiden skapa resurs](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>Hämtar instrumentande nyckel
Instrumentation-nyckeln identifierar den resurs som du har skapat.

Du behöver Instrumentation-nycklarna för alla resurser som din app ska skicka data till.

## <a name="filter-on-build-number"></a>Filter för build-nummer
När du publicerar en ny version av din app vill du kunna separera Telemetrin från olika versioner.

Du kan ställa in egenskapen för program version så att du kan filtrera [Sök](../../azure-monitor/app/diagnostic-search.md) -och [Metric Explorer](../../azure-monitor/platform/metrics-charts.md) -resultat.

Det finns flera olika metoder för att ange program versions egenskapen.

* Ange direkt:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Radbryt raden i en [telemetri initierare](../../azure-monitor/app/api-custom-events-metrics.md#defaults) för att säkerställa att alla TelemetryClient-instanser anges konsekvent.
* [ASP.NET] Ange versionen i `BuildInfo.config` . Webbmodulen hämtar versionen från BuildLabel-noden. Ta med den här filen i projektet och kom ihåg att ange egenskapen kopiera alltid i Solution Explorer.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generera BuildInfo.config automatiskt i MSBuild. Det gör du genom att lägga till några rader i `.csproj` filen:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Detta genererar en fil med namnet *yourProjectName*.BuildInfo.config. Publicerings processen byter namn till BuildInfo.config.

    Versions etiketten innehåller en plats hållare (AutoGen_...) när du skapar med Visual Studio. Men när det har skapats med MSBuild fylls det i med rätt versions nummer.

    Om du vill tillåta att MSBuild genererar versions nummer ställer du in versionen som `1.0.*` i AssemblyReference.CS

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. I `.csproj` filen lägger du till:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md) eller när du [undersöker mätvärden](../../azure-monitor/platform/metrics-charts.md).

Observera dock att versions numret för versionen endast genereras av Microsoft build-motorn, inte av utvecklaren från Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Azure DevOps kan du [få en antecknings markör](../../azure-monitor/app/annotations.md) tillagd i dina diagram när du släpper en ny version. 

## <a name="next-steps"></a>Nästa steg

* [Delade resurser för flera roller](../../azure-monitor/app/app-map.md)
* [Skapa en telemetri-initierare för att särskilja en | B-varianter](../../azure-monitor/app/api-filtering-sampling.md#add-properties)