---
title: Program karta i Azure Application Insights | Microsoft Docs
description: Övervaka komplexa programtopologier med program kartan
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: d301ad1a9f5f55a41f87cd1316c9e5e4b38afd51
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980090"
---
# <a name="application-map-triage-distributed-applications"></a>Program karta: prioritering distribuerade program

Med en programkarta kan du lätt identifiera flaskhalsar i prestanda eller felpunkter i alla komponenter i ditt distribuerade program. Varje nod på kartan representerar en program komponent eller dess beroenden. och har hälso-KPI och aviserings status. Du kan klicka dig igenom från valfri komponent till mer detaljerad diagnostik, till exempel Application Insights händelser. Om din app använder Azure-tjänster kan du också klicka dig till Azure Diagnostics, till exempel SQL Database Advisor rekommendationer.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenterna är oberoende distributions bara delar av ditt distribuerade/mikrotjänster-program. Utvecklare och drifts grupper har synlighet på kod nivå eller åtkomst till telemetri som genereras av dessa program komponenter. 

* Komponenterna skiljer sig från "observerade" externa beroenden som SQL, EventHub osv. som ditt team/din organisation inte har åtkomst till (kod eller telemetri).
* Komponenter körs på valfritt antal server-/roll-/container instanser.
* Komponenter kan vara separata Application Insights Instrumentation-nycklar (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights Instrumentation-nyckel. I för hands versions kartan visas komponenterna oavsett hur de har kon figurer ATS.

## <a name="composite-application-map"></a>Sammansatt program karta

Du kan se hela programtopologin över flera nivåer av relaterade program komponenter. Komponenter kan vara olika Application Insights resurser eller olika roller i en och samma resurs. App-kartan hittar komponenter genom att följa de HTTP-beroende anrop som görs mellan servrar med Application Insights SDK installerat. 

Den här upplevelsen börjar med progressiv identifiering av komponenterna. När du först läser in program kartan utlöses en uppsättning frågor för att identifiera de komponenter som är relaterade till den här komponenten. En knapp i det övre vänstra hörnet uppdateras med antalet komponenter i ditt program så att de upptäcks. 

När du klickar på Uppdatera kart komponenter uppdateras kartan med alla komponenter som identifieras tills den punkten. Beroende på hur komplex ditt program är kan det ta en minut att läsa in.

Om alla komponenter är roller inom en enda Application Insights resurs krävs inte det här identifierings steget. Den första belastningen för ett sådant program kommer att ha alla dess komponenter.

![Skärm bild som visar ett exempel på en program karta.](media/app-map/app-map-001.png)

Ett av de viktigaste målen med den här upplevelsen är att kunna visualisera komplexa topologier med hundratals komponenter.

Klicka på en komponent om du vill se relaterade insikter och gå till prioritering för prestanda och haverihet för den komponenten.

![Fällbar](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Undersök felen

Välj **Undersök haverier** för att starta fönstret problem.

![Skärm bild av knappen Undersök felaktiga](media/app-map/investigate-failures.png)

![Skärm bild av problem](media/app-map/failures.png)

### <a name="investigate-performance"></a>Undersök prestanda

Du kan felsöka prestanda problem genom att välja **Undersök prestanda**.

![Skärm bild av knappen Undersök prestanda](media/app-map/investigate-performance.png)

![Skärm bild av prestanda upplevelse](media/app-map/performance.png)

### <a name="go-to-details"></a>Gå till information

Välj **gå till information** för att utforska den slutliga transaktions upplevelsen, som kan erbjuda vyer nedåt till anrops stacknivå.

![Skärm bild av knappen gå till information](media/app-map/go-to-details.png)

![Skärm bild av transaktions information från slut punkt till slut punkt](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Visa loggar (analys)

Om du vill fråga efter och undersöka program data ytterligare klickar du på **Visa i loggar (analys)**.

![Skärm bild av knappen Visa i Analytics](media/app-map/view-logs.png)

![Skärm bild av analys upplevelse. Linje diagram som sammanfattar den genomsnittliga svars tiden för en begäran under de senaste 12 timmarna.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Aviseringar

Om du vill visa aktiva aviseringar och underliggande regler som orsakar att aviseringarna utlöses väljer du **aviseringar**.

![Skärm bild av knappen aviseringar](media/app-map/alerts.png)

![Skärm bild av analys upplevelse](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Ange eller Åsidosätt moln roll namn

Program kartan använder namn egenskapen för **moln rollen** för att identifiera komponenterna på kartan. Ange eller åsidosätta namnet på moln rollen manuellt och ändra vad som visas i program kartan:

> [!NOTE]
> Application Insights SDK eller agent lägger automatiskt till egenskapen namn för moln rollen till den telemetri som genereras av komponenter i en Azure App Service miljö.

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

**Skriv anpassade TelemetryInitializer enligt nedan.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET appar: läsa in initieraren till den aktiva TelemetryConfiguration**

I ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

En alternativ metod för ASP.NET-webbappar är att instansiera initieraren i kod, till exempel i Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Det går inte att lägga till initieraren med `ApplicationInsights.config` eller använda `TelemetryConfiguration.Active` är ogiltig för ASP.net Core-program. 

**ASP.NET Core appar: läsa in initieraren till TelemetryConfiguration**

Om du vill [ASP.net Core](asp-net-core.md#adding-telemetryinitializers) program lägger du till en ny `TelemetryInitializer` genom att lägga till den i behållaren för beroende insprutning, som du ser nedan. Detta görs i- `ConfigureServices` metoden för din `Startup.cs` klass.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java-agent**

För [Java-agent 3,0](./java-in-process-agent.md) anges moln roll namnet enligt följande:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

Du kan också ange namnet på moln rollen med hjälp av miljövariabeln ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Om du använder SDK, från och med Application Insights Java SDK 2.5.0, kan du ange namnet på moln rollen genom att lägga till `<RoleName>` i `ApplicationInsights.xml` filen, t. ex.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Om du använder våren boot med Application Insights våren Boot starter, är den enda nödvändiga ändringen att ange ditt anpassade namn för programmet i filen Application. Properties.

`spring.application.name=<name-of-app>`

Våren Boot starter tilldelar automatiskt namnet på moln rollen till det värde som du anger för egenskapen spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternativ metod för Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

För python kan du använda [python-telemetri för openräkning](api-filtering-sampling.md#opencensus-python-telemetry-processors) .

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
// AzureLogHandler
handler.add_telemetry_processor(callback_function)

// AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Förstå moln roll namn inom kontexten för program kartan

Så långt som möjligt att tänka på **namn på moln rollen**, kan det vara bra att titta på en program karta som har flera namn på moln roller:

![Skärm bild för program karta](media/app-map/cloud-rolename.png)

I program kartan ovanför var och en av namnen i gröna rutor är namn värden för moln roller för olika aspekter av just det distribuerade programmet. Så för den här appen består dess roller av: `Authentication` , `acmefrontend` , `Inventory Management` , `Payment Processing Worker Role` . 

När det gäller den här appen representerar var och en av dessa namn på moln rollerna också en annan unik Application Insights resurs med sina egna instrument nycklar. Eftersom ägaren av det här programmet har åtkomst till var och en av dessa fyra olika Application Insights-resurser kan program kartan sammanfoga en karta över de underliggande relationerna.

För [officiella definitioner](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativt, **moln Rolls instansen** kan vara till hjälp för scenarier där **moln roll namnet** visar att problemet ligger någonstans i din webb klient del, men du kanske kör din webb klient del på flera belastningsutjämnade servrar, så att du kan gå igenom ett lager djupare via Kusto-frågor och veta om problemet påverkar alla frontend-servrar/instanser eller bara ett kan vara mycket viktigt.

Ett scenario där du kanske vill åsidosätta värdet för moln roll instansen kan vara om din app körs i en behållare miljö där det inte finns tillräckligt med information för att hitta ett visst problem.

Mer information om hur du åsidosätter egenskapen namn för moln roll med telemetri initierare finns i [Lägg till egenskaper: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att få program kartan att fungera som förväntat kan du prova följande steg:

### <a name="general"></a>Allmänt

1. Kontrollera att du använder en SDK som stöds officiellt. SDK:er som inte stöds eller community-SDK:er har kanske inte stöd för korrelation.

    I den här [artikeln](./platforms.md) finns en lista över SDK:er som stöds.

2. Uppgradera alla komponenter till den senaste SDK-versionen.

3. Om du använder Azure Functions med C# uppgraderar du till [Functions v2](../../azure-functions/functions-versions.md).

4. Bekräfta att [namnet på moln rollen](#set-or-override-cloud-role-name) har kon figurer ATS korrekt.

5. Om du saknar ett beroende kontrollerar du att det finns i listan över [automatiskt insamlade beroenden](./auto-collect-dependencies.md). Om inte så kan du ändå spåra det manuellt med ett [beroendespårningsanrop](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>För många noder på kartan

Program kartan konstruerar en programnod för varje unikt moln roll namn som finns i din begäran telemetri och en beroende nod för varje unik kombination av typ, mål och moln roll namn i din beroende telemetri. Om det finns fler än 10 000 noder i din telemetri kommer inte program mappning att kunna hämta alla noder och länkar, så att kartan inte är fullständig. Om detta inträffar visas ett varnings meddelande när kartan visas.

Dessutom stöder program kartan bara upp till 1000 separata uppdelade noder som återges samtidigt. Program kartan minskar den visuella komplexiteten genom att gruppera beroenden som har samma typ och anropare, men om Telemetrin har för många unika moln roll namn eller för många beroende typer är den grupperingen otillräcklig och kartan kommer inte att kunna återges.

För att åtgärda detta måste du ändra Instrumentation till rätt sätt i fälten namn, beroende typ och beroende mål för molnet.

* Beroende mål ska representera det logiska namnet för ett beroende. I många fall motsvarar den server-eller resurs namnet för beroendet. Om till exempel HTTP-beroenden är inställd på värd namnet. Det får inte innehålla unika ID: n eller parametrar som ändras från en begäran till en annan.

* Beroende typen ska representera den logiska typen för ett beroende. Till exempel är HTTP, SQL eller Azure Blob typiska beroende typer. Det får inte innehålla unika ID: n.

* Syftet med namnet på moln rollen beskrivs i [avsnittet ovan](#set-or-override-cloud-role-name).

## <a name="portal-feedback"></a>Portal feedback

Använd feedback-alternativet för att ge feedback.

![MapLink-1-bild](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur korrelation fungerar i Application Insights kan du läsa [artikeln telemetri korrelation](correlation.md).
* Den [diagnostiska transaktionen från slut punkt till slut punkt](transaction-diagnostics.md) motsvarar telemetri från Server sidan från alla dina Application Insights övervakade komponenter till en enda vy.
* Avancerade korrelations scenarier i ASP.NET Core och ASP.NET finns i artikeln [spåra anpassade åtgärder](custom-operations-tracking.md) .
