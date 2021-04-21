---
title: Distributionstekniker i Azure Functions
description: Lär dig olika sätt att distribuera kod till Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: ca81067fa60836d77c4d8af121ebf415c772a1d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789221"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributionstekniker i Azure Functions

Du kan använda några olika tekniker för att distribuera Azure Functions projektkod till Azure. Den här artikeln innehåller en översikt över de distributionsmetoder som är tillgängliga för dig och rekommendationer för den bästa metoden att använda i olika scenarier. Den innehåller också en fullständig lista över och viktig information om de underliggande distributionsteknikerna. 

## <a name="deployment-methods"></a>Distributionsmetoder

Vilken distributionsteknik du använder för att publicera kod till Azure avgörs vanligtvis av hur du publicerar din app. Lämplig distributionsmetod bestäms av specifika behov och punkten i utvecklingscykeln. Under utveckling och testning kan du till exempel distribuera direkt från ditt utvecklingsverktyg, till exempel Visual Studio Code. När din app är i produktion är det mer troligt att du publicerar kontinuerligt från källkontrollen eller genom att använda en automatiserad publiceringspipeline, vilket innefattar ytterligare validering och testning.  

I följande tabell beskrivs de tillgängliga distributionsmetoderna för funktionsprojektet.

| &nbsp;Distributionstyp | Metoder | Bäst för... |
| -- | -- | -- |
| Verktygsbaserad | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; &nbsp; Code-publicering](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio publicera](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Publicera Core Tools](functions-run-local.md#publish) | Distributioner under utveckling och andra ad-hock-distributioner. Distributioner hanteras lokalt av verktygen. | 
| App Service hanterad| &bull;&nbsp;[Distributionscenter &nbsp; &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[&nbsp;Containerdistributioner](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Kontinuerlig distribution (CI/CD) från källkontroll eller från ett containerregister. Distributioner hanteras av App Service plattformen (Kudu).|
| Externa pipelines|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-åtgärder](functions-how-to-github-actions.md) | Produktions- och DevOps-pipelines som innehåller ytterligare validering, testning och andra åtgärder körs som en del av en automatiserad distribution. Distributioner hanteras av pipelinen. |

Vissa Functions-distributioner använder den bästa tekniken baserat på deras kontext, men de flesta distributionsmetoder baseras på [zip-distribution.](#zip-deploy)

## <a name="deployment-technology-availability"></a>Tillgänglighet för distributionsteknik

Azure Functions stöd för plattformsoberoende lokal utveckling och värdtjänster i Windows och Linux. För närvarande finns det tre värdplaner:

+ [Förbrukning](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedikerad (App Service)](dedicated-plan.md)

Varje plan har olika beteenden. Alla distributionstekniker är inte tillgängliga för varje variant av Azure Functions. Följande diagram visar vilka distributionstekniker som stöds för varje kombination av operativsystem och värdplan:

| Distributionsteknik | Windows-förbrukning | Windows Premium | Windows Dedicated  | Linux-förbrukning | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL för externt paket<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-distribution |✔|✔|✔|✔|✔|✔|
| Docker-container | | | | |✔|✔|
| Webbdistribution |✔|✔|✔| | | |
| Källkontroll |✔|✔|✔| |✔|✔|
| Lokal Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Molnsynkronisering<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portalredigering |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Distributionsteknik som kräver [manuell synkronisering av utlösare.](#trigger-syncing)
<sup>2</sup> Portalredigering är endast aktiverat för HTTP- och timerutlösare för Functions i Linux med premium- och dedikerade planer.

## <a name="key-concepts"></a>Viktiga begrepp

Vissa viktiga begrepp är viktiga för att förstå hur distributioner fungerar i Azure Functions.

### <a name="trigger-syncing"></a>Synkronisering av utlösare

När du ändrar någon av dina utlösare måste Functions-infrastrukturen vara medveten om ändringarna. Synkronisering sker automatiskt för många distributionstekniker. I vissa fall måste du dock synkronisera utlösarna manuellt. När du distribuerar dina uppdateringar genom att referera till en extern paket-URL, lokal Git, molnsynkronisering eller FTP måste du synkronisera utlösarna manuellt. Du kan synkronisera utlösare på något av tre sätt:

* Starta om funktionsappen i Azure Portal
* Skicka en HTTP POST-begäran till `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` med hjälp av [huvudnyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Skicka en HTTP POST-begäran till `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Ersätt platshållarna med ditt prenumerations-ID, resursgruppens namn och namnet på din funktionsapp.

### <a name="remote-build"></a>Fjärrbygge

Azure Functions kan automatiskt utföra byggen på den kod som tas emot efter zip-distributioner. Dessa versioner fungerar lite annorlunda beroende på om din app körs på Windows eller Linux. Fjärrbyggen utförs inte när en app tidigare har ställts in för att köras i [läget Kör från](run-functions-from-deployment-package.md) paket. Om du vill lära dig hur du använder fjärrbygge går du till [zip deploy](#zip-deploy).

> [!NOTE]
> Om du har problem med fjärrbygget kan det beror på att appen skapades innan funktionen blev tillgänglig (1 augusti 2019). Prova att skapa en ny funktionsapp eller kör `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` för att uppdatera funktionsappen. Det här kommandot kan ta två försök att lyckas.

#### <a name="remote-build-on-windows"></a>Fjärrbygge i Windows

Alla funktionsappar som körs i Windows har en liten hanteringsapp, webbplatsen SCM (eller [Kudu).](https://github.com/projectkudu/kudu) Den här platsen hanterar mycket av distributions- och bygglogiken för Azure Functions.

När en app distribueras till Windows körs språkspecifika kommandon som `dotnet restore` (C#) eller `npm install` (JavaScript).

#### <a name="remote-build-on-linux"></a>Fjärrbygge i Linux

Om du vill aktivera fjärrbygge i Linux [måste du ange](functions-how-to-use-azure-function-app-settings.md#settings) följande programinställningar:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Som standard utför [både Azure Functions Core Tools](functions-run-local.md) och [Azure Functions-tillägget för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) fjärrbyggen vid distribution till Linux. Därför skapar båda verktygen automatiskt de här inställningarna åt dig i Azure.

När appar fjärrstyrs i Linux körs [de från distributionspaketet](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Förbrukningsplan

Linux-funktionsappar som körs i förbrukningsplanen har ingen SCM/Kudu-webbplats, vilket begränsar distributionsalternativen. Funktionsappar i Linux som körs i förbrukningsplanen stöder dock fjärrbyggen.

##### <a name="dedicated-and-premium-plans"></a>Dedikerade planer och Premium-planer

Funktionsappar som körs på Linux i [den dedikerade App Service-planen](dedicated-plan.md) och [Premium-planen](functions-premium-plan.md) har också en begränsad SCM/Kudu-webbplats.

## <a name="deployment-technology-details"></a>Information om distributionsteknik

Följande distributionsmetoder är tillgängliga i Azure Functions.

### <a name="external-package-url"></a>URL för externt paket

Du kan använda en extern paket-URL för att referera till en fjärrpaketfil (.zip) som innehåller din funktionsapp. Filen laddas ned från den angivna URL:en och appen körs i [läget Kör från](run-functions-from-deployment-package.md) paket.

>__Så här använder du den:__ Lägg [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) till i programinställningarna. Värdet för den här inställningen ska vara en URL (platsen för den specifika paketfil som du vill köra). Du kan lägga till inställningar [antingen i portalen](functions-how-to-use-azure-function-app-settings.md#settings) [eller med hjälp av Azure CLI.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)
>
>Om du använder Azure Blob Storage använder du en privat container med en signatur för delad åtkomst [(SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att ge Functions åtkomst till paketet. När programmet startas om hämtar det en kopia av innehållet. Din referens måste vara giltig under hela programmets livslängd.

>__När du ska använda den:__ Extern paket-URL är den enda distributionsmetoden som stöds för Azure Functions som körs på Linux i förbrukningsplanen, om användaren inte vill att en [fjärrbygge](#remote-build) ska ske. När du uppdaterar paketfilen som en funktionsapp refererar till måste du manuellt synkronisera utlösare för att meddela Azure att ditt program har ändrats. [](#trigger-syncing)

### <a name="zip-deploy"></a>Zip-distribution

Använd zip deploy för att skicka en .zip-fil som innehåller funktionsappen till Azure. Du kan också ange att din app ska börja [köras från paketet](run-functions-from-deployment-package.md), eller ange att en [fjärrbygge](#remote-build) ska ske.

>__Så här använder du den:__ Distribuera med hjälp av ditt favoritklientverktyg: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure) [, Visual Studio](functions-develop-vs.md#publish-to-azure)eller från kommandoraden med hjälp av [Azure Functions Core Tools](functions-run-local.md#project-file-deployment). Som standard använder de här verktygen zip-distribution och [kör från paketet](run-functions-from-deployment-package.md). Core Tools och Visual Studio Code aktiverar både [fjärrbygge vid](#remote-build) distribution till Linux. Om du vill distribuera en ZIP-fil manuellt till funktionsappen följer du anvisningarna i [Distribuera från en ZIP-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>När du distribuerar med hjälp av zip-distribution kan du ange att appen ska [köras från paketet](run-functions-from-deployment-package.md). Om du vill köra från paketet anger [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) du värdet för programinställningen till `1` . Vi rekommenderar zip-distribution. Det ger snabbare inläsningstider för dina program och är standard för VS Code, Visual Studio och Azure CLI.

>__När du ska använda den:__ Zip-distribution är den rekommenderade distributionstekniken för Azure Functions.

### <a name="docker-container"></a>Docker-container

Du kan distribuera en Linux-containeravbildning som innehåller funktionsappen.

>__Så här använder du den:__ Skapa en Linux-funktionsapp i Premium- eller Dedicated-planen och ange vilken containeravbildning som ska köras från. Du kan göra det här på två sätt:
>
>* Skapa en Linux-funktionsapp på Azure App Service plan i Azure Portal. För **Publicera** väljer du **Docker-avbildning** och konfigurerar sedan containern. Ange den plats där avbildningen finns.
>* Skapa en Linux-funktionsapp på en App Service plan med hjälp av Azure CLI. Mer information finns i Skapa [en funktion i Linux med hjälp av en anpassad avbildning.](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Om du vill distribuera till en befintlig app med hjälp av en [anpassad container i Azure Functions Core Tools](functions-run-local.md)använder du kommandot [`func deploy`](functions-run-local.md#publish) .

>__När du ska använda den:__ Använd alternativet Docker-container när du behöver mer kontroll över Linux-miljön där funktionsappen körs. Den här distributionsmekanismen är endast tillgänglig för funktioner som körs på Linux.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy paketerar och distribuerar dina Windows-program till valfri IIS-server, inklusive funktionsappar som körs på Windows i Azure.

>__Så här använder du den:__ Använd [Visual Studio för att Azure Functions](functions-create-your-first-function-visual-studio.md). Avmarkera kryssrutan **Kör från paketfil (rekommenderas).**
>
>Du kan också ladda ned [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) och anropa `MSDeploy.exe` direkt.

>__När du ska använda den:__ Web Deploy stöds och har inga problem, men den önskade mekanismen är [zip-distribution med Kör från paket aktiverat.](#zip-deploy) Mer information finns i Visual Studio [utvecklingsguide.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Källkontroll

Använd källkontroll för att ansluta funktionsappen till en Git-lagringsplats. En uppdatering av koden på lagringsplatsen utlöser distribution. Mer information finns i [Kudu Wiki.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Så här använder du den:__ Använd Distributionscenter i området Funktioner i portalen för att konfigurera publicering från källkontroll. Mer information finns i [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Att använda källkontroll är bästa praxis för team som samarbetar med sina funktionsappar. Källkontroll är ett bra distributionsalternativ som möjliggör mer avancerade distributionspipelines.

### <a name="local-git"></a>Lokal Git

Du kan använda lokal Git för att skicka kod från din lokala dator till Azure Functions med hjälp av Git.

>__Så här använder du den:__ Följ instruktionerna i [Lokal Git-distribution för att Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi att du använder en annan distributionsmetod. När du publicerar från lokal Git måste du [synkronisera utlösare manuellt.](#trigger-syncing)

### <a name="cloud-sync"></a>Molnsynkronisering

Använd molnsynkronisering för att synkronisera innehåll från Dropbox och OneDrive till Azure Functions.

>__Så här använder du den:__ Följ anvisningarna i [Synkronisera innehåll från en molnmapp.](../app-service/deploy-content-sync.md)

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributionsmetoder. När du publicerar med hjälp av molnsynkronisering måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="ftp"></a>FTP

Du kan använda FTP för att direkt överföra filer till Azure Functions.

>__Så här använder du den:__ Följ instruktionerna i [Distribuera innehåll med hjälp av FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributionsmetoder. När du publicerar med FTP måste du synkronisera [utlösare manuellt.](#trigger-syncing)

### <a name="portal-editing"></a>Portalredigering

I den portalbaserade redigeraren kan du redigera de filer som finns i funktionsappen direkt (distribueras i princip varje gång du sparar ändringarna).

>__Så här använder du den:__ För att kunna redigera dina funktioner i Azure Portal måste du [ha skapat dina funktioner i portalen](./functions-get-started.md). Om du vill bevara en enda sanningskälla blir funktionen skrivskyddat om du använder någon annan distributionsmetod och förhindrar fortsatt redigering av portalen. Om du vill återgå till ett tillstånd där du kan redigera dina filer i Azure Portal kan du manuellt ändra tillbaka redigeringsläget till och ta bort eventuella `Read/Write` distributionsrelaterade programinställningar (till exempel [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions. För mer intensivt utvecklingsarbete rekommenderar vi att du använder något av följande klientverktyg:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (kommandorad)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativsystem och språk som stöder redigering av portalen:

| Språk | Windows-förbrukning | Windows Premium | Windows Dedicated | Linux-förbrukning | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#-skript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (förhandsversion) | | | | | | |
| PowerShell (förhandsversion) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Portalredigering är endast aktiverat för HTTP- och timerutlösare för Functions på Linux med premium- och dedikerade planer.

## <a name="deployment-behaviors"></a>Distributionsbeteenden

När du gör en distribution tillåts alla befintliga körningar slutföras eller time out, varför den nya koden läses in för att börja bearbeta begäranden.

Om du behöver mer kontroll över den här övergången bör du använda distributionsfack.

## <a name="deployment-slots"></a>Distributionsfack

När du distribuerar funktionsappen till Azure kan du distribuera till ett separat distributionsfack i stället för direkt till produktion. Mer information om distributionsfack finns i dokumentationen [Azure Functions distributionsfack](functions-deployment-slots.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Läs de här artiklarna om du vill veta mer om hur du distribuerar dina funktionsappar:

+ [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med hjälp av Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip-distributioner för Azure Functions](deployment-zip-push.md)
+ [Kör ditt Azure Functions från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resursdistributionen för funktionsappen i Azure Functions](functions-infrastructure-as-code.md)
