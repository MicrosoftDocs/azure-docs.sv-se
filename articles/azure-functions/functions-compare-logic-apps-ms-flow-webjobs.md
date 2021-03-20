---
title: Alternativ för integrering och automatiserings plattform i Azure
description: 'Jämför Microsofts moln tjänster som är optimerade för integrations åtgärder: automatisera, Logic Apps, funktioner och WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 95167791efe13526b0a70c28fa89771542a9d220
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685569"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Välja rätt integrerings- och automationstjänster i Azure

Den här artikeln jämför följande Microsoft-molntjänster:

* [Microsoft Power automatisering](https://flow.microsoft.com/) (har Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Alla dessa tjänster kan lösa integrationsproblem och automatisera affärsprocesser. Samtliga tjänster definierar indata, åtgärder, villkor och utdata. Du kan välja att köra dem enligt ett schema eller med utlösare. Varje tjänst har unika fördelar, och den här artikeln förklarar skillnaderna. 

Om du vill ha en mer allmän jämförelse mellan Azure Functions och andra Azure Compute-alternativ, se [kriterier för att välja en Azure Compute-tjänst](/azure/architecture/guide/technology-choices/compute-comparison) och [välja ett Azure Compute-alternativ för mikrotjänster](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Jämför Microsoft Power automatisering och Azure Logic Apps

Automatisera och Logic Apps är både *Designer-första* integrerings tjänster som kan skapa arbets flöden. Båda tjänsterna integreras med olika SaaS- och företagsprogram. 

Automatisk energi förbrukning bygger på Logic Apps. De delar samma arbetsflödesdesigner och samma [anslutningsappar](../connectors/apis-list.md). 

Automatiserad energi gör att alla kontors arbetare kan utföra enkla integreringar (till exempel en godkännande process på ett SharePoint-dokumentbibliotek) utan att gå via utvecklare eller det. Logic Apps kan även användas för att göra avancerade integreringar (till exempel B2B-processer) där Azure DevOps och säkerhetsåtgärder på företagsnivå krävs. Det är vanligt att företagsarbetsflöden växer i komplexitet med tiden. Därför går det bra att börja med ett enkelt flöde och sedan konvertera det till en logikapp efter behov.

I följande tabell får du hjälp att avgöra om Power-eller Logic Apps är bäst för en viss integrering:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Användare** |Kontorsarbetarna, användare i verksamheten, SharePoint-administratörer |Professionella integratörer och utvecklare, IT-proffs |
| **Scenarier** |Självbetjäning |Avancerade integreringar |
| **Designverktyg** |I webbläsaren och mobilappen, endast användargränssnitt |I webbläsaren och [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), [kodvyer](../logic-apps/logic-apps-author-definitions.md) är tillgängliga |
| **Application Lifecycle Management (ALM)** |Utforma och testa i icke-produktionsmiljöer, skicka till produktion när det är klart |Azure DevOps: källkontroll, testning, support, automatisering och hanterbarhet i [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Administratörsupplevelse** |Hantera energi automatiserade miljöer och principer för data förlust skydd (DLP), spåra licensiering: [administrations Center](https://admin.flow.microsoft.com) |Hantera resurs grupper, anslutningar, åtkomst hantering och loggning: [Azure Portal](https://portal.azure.com) |
| **Säkerhet** |Microsoft 365 säkerhets gransknings loggar, DLP, [kryptering i vila](https://wikipedia.org/wiki/Data_at_rest#Encryption) för känsliga data |Säkerhets garanti för Azure: [Azure-säkerhet](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [gransknings loggar](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Jämföra Azure Functions och Azure Logic Apps

Functions och Logic Apps är Azure-tjänster som möjliggör serverlösa arbetsbelastningar. Azure Functions är en serverlös beräkningstjänst, medan Azure Logic Apps tillhandahåller serverlösa arbetsflöden. Båda kan skapa komplexa *orkestreringar*. En orkestrering är en samling funktioner eller steg, som kallas *åtgärder* som utförs i Logic Apps, som körs för att utföra en komplicerad uppgift. För att till exempel bearbeta en batch med ordrar kör du kanske flera instanser av en funktion parallellt, väntar tills alla instanser har slutförts och kör sedan en funktion som beräknar ett resultat för aggregatet.

Med Azure Functions utvecklar du orkestreringarna genom att skriva kod och använda [tillägget Varaktiga funktioner](durable/durable-functions-overview.md). Med Logic Apps skapar du orkestreringarna genom att använda ett grafiskt användargränssnitt eller redigera konfigurationsfiler.

Du kan kombinera tjänsterna när du skapar en orkestrering och anropa funktioner från logikappar och logikappar från funktioner. Välj hur du skapar varje orkestrering utifrån tjänsternas funktioner eller vad du föredrar. I följande tabell visas några av de viktigaste skillnaderna mellan dessa:

|  | Bestående funktioner | Logic Apps |
| --- | --- | --- |
| **Utveckling** | Kod först (imperativt) | Design först (deklarativt) |
| **Anslutningsmöjligheter** | [Om ett dussin inbyggda bindnings typer](functions-triggers-bindings.md#supported-bindings)skriver du kod för anpassade bindningar | [Stor mängd anslutningar](../connectors/apis-list.md), [Enterprise-integrationspaket för B2B-scenarier](../logic-apps/logic-apps-enterprise-integration-overview.md), [Bygg anpassade anslutningar](../logic-apps/custom-connector-overview.md) |
| **Åtgärder** | Varje åtgärd är en Azure-funktion. Skriv kod för åtgärdsfunktioner |[Stor samling färdiga åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Övervakning** | [Azure Application insikter](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) [Azure Monitor loggar](../logic-apps/monitor-logic-apps.md)|
| **Hantering** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2019) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](/rest/api/logic/), [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Körningskontext** | Kan köras [lokalt](functions-runtime-overview.md) eller i molnet | Körs bara i molnet|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Jämföra Functions och WebJobs

Liksom Azure Functions är Azure App Service WebJobs med WebJobs-SDK:n en *kodfokuserad* integreringstjänst som är avsedd för utvecklare. Båda bygger på [Azure App Service](../app-service/overview.md) och stöder funktioner som [källkontrollsintegration](../app-service/deploy-continuous-deployment.md), [autentisering](../app-service/overview-authentication-authorization.md) och [övervakning med Application Insights-integration](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs och WebJobs-SDK:t

Du kan använda *WebJobs*-funktionen i App Service för att köra ett skript eller kod i kontexten för en App Service-webbapp. *WebJobs-SDK:n* är ett ramverk som utformats för WebJobs och som förenklar den kod du skriver för att besvara händelser i Azure-tjänsterna. Till exempel kan du svara på skapandet av en avbildningsblob i Azure Storage genom att skapa en miniatyrbild. WebJobs-SDK:n körs som ett .NET-konsolprogram som du kan distribuera till ett WebJob. 

WebJobs och WebJobs-SDK:n fungerar bäst tillsammans, men du kan använda WebJobs utan WebJobs-SDK:n och vice versa. Ett WebJob kan köra vilket program eller skript som helst som körs i App Service-sandboxen. Ett WebJobs-SDK-konsolprogram kan köras var som helst där konsolprogram körs, t.ex. på lokala servrar.

### <a name="comparison-table"></a>Jämförelsetabell

Azure Functions bygger på WebJobs-SDK:n, och delar därför många händelseutlösare och anslutningsappar med andra Azure-tjänster. Här är några faktorer som du bör tänka på när du väljer mellan Azure Functions och WebJobs med WebJobs-SDK:n:

|  | Functions | WebJobs med Webjobs-SDK:n |
| --- | --- | --- |
|**[Program modell utan server](https://azure.microsoft.com/solutions/serverless/) med [automatisk skalning](event-driven-scaling.md)**|✔||
|**[Utveckla och testa i webbläsare](./functions-get-started.md)** |✔||
|**[Betala per användning](consumption-plan.md)**|✔||
|**[Integrering med Logic Apps](functions-twitter-email.md)**|✔||
| **Utlösarhändelser** |[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och blobar](functions-bindings-storage-blob.md)<br>[Azure Service Bus köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och blobar](functions-bindings-storage-blob.md)<br>[Azure Service Bus köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Filsystem](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Språk som stöds**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Pakethanterare**|NPM och NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (utan WebJobs SDK) stöder C#, Java, Java Script, bash,. cmd,. bat, POWERSHELL, php, typescript, python med mera. Den här listan är inte omfattande. Ett WebJob kan köra vilket program eller skript som helst som kan köras i App Service-sandboxen.

<sup>2</sup> WebJobs (utan WebJobsSDK:n) stöder NPM och NuGet.

### <a name="summary"></a>Sammanfattning

Azure Functions ger mer produktivitet för utvecklare än Azure App Service WebJobs. Det har även fler alternativ för programmeringsspråk, utvecklingsmiljöer och Azure-tjänstintegrering och priser. I de flesta scenarierna är detta det bästa alternativet.

Här följer två scenarier för vilka WebJobs kan vara det bästa valet:

* Du behöver mer kontroll över den kod som lyssnar efter händelser, `JobHost`-objektet. Functions erbjuder ett begränsat antal olika sätt för att anpassa `JobHost`-beteendet i [host.json](functions-host-json.md)-filen. Ibland måste du behöva göra saker som inte kan anges av en sträng i en JSON-fil. Endast WebJobs-SDK:n låter dig konfigurera din egen återförsöksprincip för Azure Storage.
* Du har en App Service-app för vilken du vill köra kodavsnitt, och du vill hantera dem tillsammans i samma Azure DevOps-miljö.

När det gäller andra scenarier där du vill köra kodfragment för att integrera Azure eller tjänster från tredje part, kan du välja Azure Functions via WebJobs med WebJobs-SDK:n.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Automatisera, Logic Apps, Functions och WebJobs tillsammans

Du behöver inte välja endast en av dessa tjänster. De integreras med varandra lika bra som med externa tjänster.

Ett flöde kan anropa en logikapp. En logikapp kan anropa en funktion, och en funktion kan anropa en logikapp. Läs t.ex. [Skapa en funktion som kan integreras med Azure Logic Apps](functions-twitter-email.md).

Integreringen mellan automatiserad energi, Logic Apps och funktioner fortsätter att förbättras med tiden. Det går bra att skapa något i en tjänst och sedan använda detsamma i de andra tjänsterna.

Det finns mer information om integreringstjänster via följande länkar:

* [Artikel av Christopher Anderson om hur man utnyttjar Azure Functions och Azure App Service i integreringsscenarier](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Artikel av Charles Lamanna om hur man förenklar integreringar](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webbsändning med Logic Apps](https://aka.ms/logicappslive)
* [Automatisera vanliga frågor och svar om energispar funktioner](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Nästa steg

Kom igång genom att skapa ditt första flöde, din första logikapp eller din första funktionsapp. Välj någon av följande länkar:

* [Kom igång med Power Automate](/power-automate/getting-started)
* [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa din första Azure-funktion](./functions-get-started.md)