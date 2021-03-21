---
title: Övervaka Azure App Services-prestanda | Microsoft Docs
description: Övervakning av program prestanda för Azure App Services. Diagrammets inläsnings-och svars tid, beroende information och ange aviseringar för prestanda.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 7661066bc2666070c8b3ed9263b1223c09d6c720
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734731"
---
# <a name="monitor-azure-app-service-performance"></a>Övervaka Azure App Service-prestanda

Nu är det enklare än någonsin att aktivera övervakning av ASP.NET, ASP.NET Core och Node.js baserade webb program som körs på [Azure App Services](../../app-service/index.yml) . Tidigare var du tvungen att installera ett plats tillägg manuellt, det senaste tillägget/agenten är nu inbyggt i App Service-avbildningen som standard. Den här artikeln vägleder dig genom att aktivera Application Insights övervakning och ge preliminär vägledning för automatisering av processen för storskaliga distributioner.

> [!NOTE]
> Att manuellt lägga till ett Application Insights webbplats tillägg via  >  **tillägg** för utvecklingsverktyg är föråldrad. Den här metoden för tilläggs installation var beroende av manuella uppdateringar för varje ny version. Den senaste stabila versionen av tillägget är nu  [förinstallerad](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) som en del av App Service avbildningen. Filerna finns i `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` och uppdateras automatiskt med varje stabil utgåva. Om du följer agentbaserade instruktioner för att aktivera övervakning nedan tas det inaktuella tillägget bort automatiskt.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera program övervakning för Azure App Services-värdbaserade program:

* **Agent-baserad program övervakning** (ApplicationInsightsAgent).  
    * Den här metoden är det enklaste sättet att aktivera, och ingen Avancerad konfiguration krävs. Det kallas ofta övervakning av körnings miljön. För Azure App tjänster rekommenderar vi att du minst aktiverar den här övervaknings nivån och sedan baserat på ditt speciella scenario kan du utvärdera om mer avancerad övervakning genom manuell Instrumentation krävs.

* Att **instrumentera programmet manuellt** genom att installera Application Insights SDK.

    * Den här metoden är mycket mer anpassningsbar, men kräver [att du lägger till ett beroende på Application Insights SDK NuGet-paket](./asp-net.md). Den här metoden innebär också att du måste hantera uppdateringarna till den senaste versionen av paketen själv.

    * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte har registrerats som standard med en agent-baserad övervakning, behöver du använda den här metoden. Mer information finns i [artikeln om API för anpassade händelser och mått](./api-custom-events-metrics.md) . Detta är för närvarande det enda alternativ som stöds för Linux-baserade arbets belastningar.

> [!NOTE]
> Om både agentbaserade övervakning och manuellt SDK-baserad Instrumentation identifieras, kommer endast de manuella Instrumentation-inställningarna att användas. Detta är för att förhindra att duplicerade data skickas. Mer information om detta finns i [fel söknings avsnittet](#troubleshooting) nedan.

## <a name="enable-agent-based-monitoring"></a>Aktivera agent-baserad övervakning

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> Kombinationen av APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte. Mer information finns i förklaringen i [fel söknings avsnittet](#troubleshooting).


1. **Välj Application Insights** på kontroll panelen i Azure för din app service.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, om du inte redan har konfigurerat en Application Insights resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervaknings inställningarna**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din app service. då **aktive ras även en omstart av App Service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill att Application Insights ska samla in data per plattform för ditt program. ASP.NET app Monitoring är aktiverat som standard med två olika nivåer av samlingen.

    ![Skärm bild som visar sidan Application Insights webbplats tillägg där skapa ny resurs har valts.](./media/azure-web-apps/choose-options-new.png)
 
 Nedan visas en sammanfattning av data som samlas in för varje väg:
        
| Data | ASP.NET Basic-samling | ASP.NET-rekommenderad samling |
| --- | --- | --- |
| Lägger till trender för processor-, minnes- och I/O-användning |Ja |Ja |
| Samlar in användningstrender och aktiverar korrelation från tillgänglighetsresultat till transaktioner | Ja |Ja |
| Samlar in undantag som hanteras av värdprocessen | Ja |Ja |
| Förbättrar precisionen för APM-mått under belastning när sampling används | Ja |Ja |
| Korrelerar mikrotjänster över begärande-/beroendegränser | Inga (endast Single-Instance APM-funktioner) |Ja |

3. Om du vill konfigurera inställningar som sampling, som du tidigare kan kontrol lera via applicationinsights.config-filen kan du nu interagera med samma inställningar via program inställningar med ett motsvarande prefix. 

    * Om du till exempel vill ändra den inledande samplings procenten kan du skapa en program inställning av: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` och värdet `100` .

    * Om du vill visa en lista över stödda alternativ för adaptiva typer av telemetri för telemetri, kan du läsa igenom [koden](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) och [tillhör ande dokumentation](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> Följande versioner av ASP.NET Core stöds: ASP.NET Core 2,1 och 3,1. Versionerna 2,0, 2,2 och 3,0 har dragits tillbaka och stöds inte längre. Uppgradera till en version av .NET Core som [stöds](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) för automatisk Instrumentation att fungera.

Det finns för närvarande **inte stöd** för att rikta in hela framework från ASP.net Core, fristående distribution och Linux-baserade program med hjälp av agent/tillägg-baserad övervakning. ([Manuell instrumentering](./asp-net-core.md) via kod fungerar i alla tidigare scenarier.)

1. **Välj Application Insights** på kontroll panelen i Azure för din app service.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, om du inte redan har konfigurerat en Application Insights resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervaknings inställningarna**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din app service. då **aktive ras även en omstart av App Service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill Application Insights Samla in data per plattform för ditt program. ASP.NET Core erbjuder **rekommenderad samling** eller **inaktive rad** för ASP.net Core 2,1 och 3,1.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Windows agent-baserad övervakning stöds inte, för att aktivera med Linux går du till [Node.js App Service-dokumentationen](../../app-service/configure-language-nodejs.md?pivots=platform-linux#monitor-with-application-insights).

# <a name="java"></a>[Java](#tab/java)

Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md) för att aktivera automatisk instrumentering för dina Java-appar utan att ändra koden.
Den automatiska integreringen är inte tillgänglig ännu för App Service.

# <a name="python"></a>[Python](#tab/python)

Python App Service-baserade webb program stöder för närvarande inte automatisk agent/tillägg-baserad övervakning. Om du vill aktivera övervakning för ditt python-program måste du [manuellt instrumentera ditt program](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Aktivera övervakning på klient Sidan

# <a name="aspnet"></a>[ASP.NET](#tab/net)

Övervakning på klient sidan är valbar för ASP.NET. Aktivera övervakning på klient sidan:

* **Inställningar** **>** **Konfiguration**
   * Under program inställningar skapar du en **ny program inställning**:

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde: `true`

   * **Spara** inställningarna och **starta om** din app.

Om du vill inaktivera övervakning på klient sidan tar du antingen bort det associerade nyckel värdes paret från program inställningarna eller anger värdet till falskt.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

Övervakning på klient sidan är **aktiverat som standard** för ASP.net Core appar med den **rekommenderade samlingen**, oavsett om app-inställningen APPINSIGHTS_JAVASCRIPT_ENABLED finns.

Om du av någon anledning vill inaktivera övervakning på klient sidan:

* **Inställningar** **>** **Konfiguration**
   * Under program inställningar skapar du en **ny program inställning**:

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde: `false`

   * **Spara** inställningarna och **starta om** din app.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Om du vill aktivera övervakning på klient sidan för ditt Node.js-program måste du [manuellt lägga till SDK SDK för klient sidan till ditt program](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

Om du vill aktivera övervakning på klient sidan för ditt Java-program måste du [manuellt lägga till SDK SDK för klient sidan till ditt program](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

Om du vill aktivera övervakning på klient sidan för python-programmet måste du [manuellt lägga till SDK SDK för klient sidan till ditt program](./javascript.md).

---

## <a name="automate-monitoring"></a>Automatisera övervakning

För att kunna aktivera telemetri-samling med Application Insights, behöver du bara ange program inställningarna:

   ![App Service program inställningar med tillgängliga Application Insights inställningar](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definitioner av program inställningar

|Namn på App-inställning |  Definition | Värde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Huvud tillägg som styr körnings övervakning. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  I standard läget är de viktigaste funktionerna aktiverade för att säkerställa optimala prestanda. | `default` eller `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Kontrollerar om den binära omskrivnings motorn `InstrumentationEngine` aktive ras. Den här inställningen har prestanda konsekvenser och påverkar start-och start tid för kall. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Kontrollerar om SQL & Azure Table text kommer att samlas in tillsammans med beroende anrop. Prestanda varning: tidpunkten för kall start av program kommer att påverkas. Den här inställningen kräver `InstrumentationEngine` . | `~1` |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Endast för ASP.NET Core appar. Aktiverar interop (interoperation) med Application Insights SDK. Läser in tillägget sida vid sida med SDK och använder det för att skicka telemetri (inaktiverar Application Insights SDK). |`1`|

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service program inställningar med Azure Resource Manager

Program inställningar för App Services kan hanteras och konfigureras med [Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md). Den här metoden kan användas när du distribuerar nya App Service-resurser med Azure Resource Manager Automation, eller för att ändra inställningarna för befintliga resurser.

Den grundläggande strukturen i program inställnings-JSON för en app service är nedan:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

För ett exempel på en Azure Resource Manager-mall med program inställningar som kon figurer ATS för Application Insights, kan den här [mallen](https://github.com/Andrew-MSFT/BasicImageGallery) vara till hjälp, särskilt avsnittet som börjar på [rad 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisera skapandet av en Application Insights-resurs och länka till din nyligen skapade App Service.

Om du vill skapa en Azure Resource Manager-mall med alla standardinställningar för Application Insights som kon figurer ATS startar du processen som om du skulle skapa en ny webbapp med Application Insights aktive rad.

Välj **automatiserings alternativ**

   ![App Service-menyn för att skapa webbappar](./media/azure-web-apps/create-web-app.png)

Med det här alternativet skapas den senaste Azure Resource Manager-mallen med alla obligatoriska konfigurerade inställningar.

  ![Mall för App Service-webbapp](./media/azure-web-apps/arm-template.png)

Nedan visas ett exempel som ersätter alla instanser av  `AppMonitoredSite` med namnet på din webbplats:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Aktivera via PowerShell

För att kunna aktivera program övervakning via PowerShell måste bara de underliggande program inställningarna ändras. Nedan visas ett exempel som aktiverar program övervakning för en webbplats med namnet "AppMonitoredSite" i resurs gruppen "AppMonitoredRG" och konfigurerar data som ska skickas till Instrumentation-nyckeln "012345678-ABCD-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Uppgradera övervaknings tillägg/agent

### <a name="upgrading-from-versions-289-and-up"></a>Uppgradera från versioner 2.8.9 och uppåt

Uppgradering från version 2.8.9 sker automatiskt, utan ytterligare åtgärder. De nya övervaknings bitarna levereras i bakgrunden till mål program tjänsten och när programmet startas om kommer de att hämtas.

Kontrol lera vilken version av tillägget som du kör på `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Skärm bild av URL-sökväg http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uppgradera från versioner 1.0.0 – 2.6.5

Från och med version 2.8.9 används det förinstallerade webbplats tillägget. Om du har en tidigare version kan du uppdatera via ett av följande två sätt:

* [Uppgradera genom att aktivera via portalen](#enable-application-insights). (Även om du har Application Insights tillägget för Azure App Service installerat visas bara knappen **Aktivera** i användar gränssnittet. Under bakgrunden tas det gamla privata webbplats tillägget bort.)

* [Uppgradera via PowerShell](#enabling-through-powershell):

    1. Ange program inställningarna för att aktivera det förinstallerade ApplicationInsightsAgent för webbplats tillägget. Se [Aktivera via PowerShell](#enabling-through-powershell).
    2. Ta bort det privata webbplats tillägget manuellt med namnet Application Insights tillägget för Azure App Service.

Om uppgraderingen görs från en version före 2.5.1 kontrollerar du att DLL-filerna för ApplicationInsigths tas bort från mappen för program bin [se fel söknings steg](#troubleshooting).

## <a name="troubleshooting"></a>Felsökning

Nedan finns vår stegvisa fel söknings guide för tillägg/agent-baserad övervakning av ASP.NET och ASP.NET Core baserade program som körs på Azure App Services.

> [!NOTE]
> Den rekommenderade metoden för att övervaka Java-program är att använda den automatiska Instrumentation utan att ändra koden. Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md).


1. Kontrol lera att programmet övervakas via `ApplicationInsightsAgent` .
    * Kontrol lera att `ApplicationInsightsAgent_EXTENSION_VERSION` appens inställning har värdet "~ 2".
2. Kontrol lera att programmet uppfyller de krav som ska övervakas.
    * Gå till `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Skärm bild av https://yoursitename.scm.azurewebsites/applicationinsights resultat Sidan](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bekräfta att `Application Insights Extension Status` är `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Om den inte körs följer du anvisningarna för [att aktivera Application Insights övervakning](#enable-application-insights)

    * Bekräfta att status källan finns och ser ut så här: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Om ett liknande värde inte finns, innebär det att programmet inte körs eller inte stöds. För att säkerställa att programmet körs kan du försöka gå till programmets URL/program slut punkter manuellt, vilket gör att körnings informationen blir tillgänglig.

    * Bekräfta att `IKeyExists` är `true`
        * Om så är `false` fallet lägger du till `APPINSIGHTS_INSTRUMENTATIONKEY` och `APPLICATIONINSIGHTS_CONNECTION_STRING` med ditt iKey-GUID i dina program inställningar.

    * Bekräfta att det inte finns några poster för `AppAlreadyInstrumented` , `AppContainsDiagnosticSourceAssembly` , och `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Om någon av dessa poster finns tar du bort följande paket från programmet: `Microsoft.ApplicationInsights` , `System.Diagnostics.DiagnosticSource` och `Microsoft.AspNet.TelemetryCorrelation` .
        * För ASP.NET Core endast appar: om ditt program refererar till ett Application Insights paket, till exempel om du tidigare har instrumenterat (eller försökt att instrumentera) din app med [ASP.net Core SDK](./asp-net-core.md), kan det vara möjligt att aktivera App Service-integrering och data kanske inte visas i Application Insights. Åtgärda problemet genom att aktivera "interop with Application Insights SDK" i portalen och du kommer att börja se data i Application Insights 
        > [!IMPORTANT]
        > Den här funktionen är i för hands version 

        ![Aktivera inställningen för den befintliga appen](./media/azure-web-apps/netcore-sdk-interop.png)

        Data skickas nu med hjälp av kod metoden även om Application Insights SDK ursprungligen användes eller försökte användas.

        > [!IMPORTANT]
        > Om programmet som används Application Insights SDK för att skicka telemetri, kommer sådan telemetri att inaktive ras – med andra ord, anpassad telemetri – om några, t. ex. alla spår * ()-metoder, och eventuella anpassade inställningar, till exempel sampling, kommer att inaktive ras. 


### <a name="php-and-wordpress-are-not-supported"></a>PHP och WordPress stöds inte

PHP-och WordPress-platser stöds inte. Det finns för närvarande inget officiellt stöd för SDK/agent för övervakning på Server sidan av dessa arbets belastningar. Du kan dock utföra manuell instrumentering av transaktioner på klient sidan på en PHP-eller WordPress-webbplats genom att lägga till Java Script på klient sidan på dina webb sidor med hjälp av [Java Script SDK](./javascript.md).

Tabellen nedan innehåller en mer detaljerad förklaring av vad dessa värden innebär, deras underliggande orsaker och rekommenderade korrigeringar:

|Problem värde|Förklaring|Åtgärda
|---- |----|---|
| `AppAlreadyInstrumented:true` | Det här värdet anger att tillägget har identifierat att en aspekt av SDK: n redan finns i programmet och kommer att stängas av. Det kan bero på en referens till `System.Diagnostics.DiagnosticSource` ,  `Microsoft.AspNet.TelemetryCorrelation` eller `Microsoft.ApplicationInsights`  | Ta bort referenserna. Vissa av dessa referenser läggs till som standard från vissa Visual Studio-mallar och äldre versioner av Visual Studio kan lägga till referenser till `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Om programmet är riktat ASP.NET Core 2,1 eller 2,2, anger det här värdet att tillägget har identifierat att en viss aspekt av SDK: n redan finns i programmet och kommer att återställas | Kunder på .NET Core 2.1, 2.2 [rekommenderar](https://github.com/aspnet/Announcements/issues/287) att du använder Microsoft.AspNetCore.app-meta-paket i stället. Aktivera dessutom "interop with Application Insights SDK" i portalen (se anvisningarna ovan).|
|`AppAlreadyInstrumented:true` | Det här värdet kan också orsakas av förekomsten av ovanstående DLL-filer i mappen app från en tidigare distribution. | Rensa mappen app för att se till att dessa DLL-filer tas bort. Kontrol lera både den lokala appens bin-katalog och katalogen wwwroot på App Service. (Om du vill kontrol lera katalogen wwwroot i App Service webbappen: avancerade verktyg (kudu) > fel söknings konsolen > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Det här värdet anger att tillägget har identifierat referenser till `Microsoft.AspNet.TelemetryCorrelation` i programmet och kommer att återställas. | Ta bort referensen.
|`AppContainsDiagnosticSourceAssembly**:true`|Det här värdet anger att tillägget har identifierat referenser till `System.Diagnostics.DiagnosticSource` i programmet och kommer att återställas.| Ta bort referensen för ASP.NET. 
|`IKeyExists:false`|Det här värdet anger att Instrumentation-nyckeln inte finns i AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY` . Möjliga orsaker: värdena kan ha tagits bort av misstag och har glömt att ange värden i Automation-skriptet osv. | Kontrol lera att inställningen finns i App Service programmets inställningar.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED-och urlCompression stöds inte

Om du använder APPINSIGHTS_JAVASCRIPT_ENABLED = sant i fall där innehåll är kodat kan du få fel som: 

- 500 Skriv åtgärds fel för URL
- 500,53-URL: en Skriv över modul fel med regler för utgående omskrivning av meddelanden kan inte tillämpas när innehållet i HTTP-svaret är kodat (gzip). 

Detta beror på att APPINSIGHTS_JAVASCRIPT_ENABLED program inställningen är true och att innehålls kodningen finns på samma tidpunkt. Det här scenariot stöds inte ännu. Lösningen är att ta bort APPINSIGHTS_JAVASCRIPT_ENABLED från program inställningarna. Det innebär att om det fortfarande krävs ett JavaScript-instrument för klient-och webb läsar sidan krävs manuella SDK-referenser för dina webb sidor. Följ [instruktionerna](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) för manuell Instrumentation med Java Script SDK.

Den senaste informationen om Application Insights agent/tillägg finns i [viktig](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)information.

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Standard webbplatsen som distribueras med Web Apps stöder inte automatisk övervakning på klient Sidan

När du skapar en webbapp med- `ASP.NET` eller- `ASP.NET Core` körningarna i Azure App-tjänster distribuerar den en statisk HTML-sida som en start webbplats. Den statiska webb sidan läser också in en ASP.NET-hanterad webbdel i IIS. Detta möjliggör testning av kod lös övervakning på Server sidan, men stöder inte automatisk övervakning på klient sidan.

Om du vill testa en kod lös Server och övervakning på klient sidan för ASP.NET eller ASP.NET Core i en Azure App Services-webbapp rekommenderar vi att du följer de officiella guiderna för att [skapa en ASP.net Core webbapp](../../app-service/quickstart-dotnetcore.md) och [skapar en ASP.NET Framework-webbapp](../../app-service/quickstart-dotnet-framework.md) och använder sedan instruktionerna i den aktuella artikeln för att aktivera övervakning.

### <a name="connection-string-and-instrumentation-key"></a>Anslutnings sträng och Instrumentation-nyckel

När kod enbart övervakning används krävs bara anslutnings strängen. Vi rekommenderar dock fortfarande att ställa in Instrumentation-nyckeln för att bevara bakåtkompatibilitet med äldre versioner av SDK när manuell Instrumentation utförs.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Skillnaden mellan standard mått från Application Insights vs Azure App Service mått?

Application Insights samlar in telemetri för de begär Anden som gjorde det till programmet. Om felet uppstod i webapps/IIS, och begäran inte har nått användar programmet, kommer Application Insights inte att ha någon telemetri om det.

Varaktigheten för `serverresponsetime` beräknad av Application Insights matchar inte nödvändigt vis Server svars tiden som observerats av Web Apps. Detta beror på att Application Insights bara räknar varaktigheten när begäran faktiskt når användar programmet. Om begäran har fastnat i kö i IIS, kommer den vänte tiden att inkluderas i webbappens mått, men inte i Application Insights mått.

## <a name="release-notes"></a>Viktig information

De senaste uppdateringarna och fel korrigeringarna [finns i viktig information](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](./profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../data-platform.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../alerts/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.
