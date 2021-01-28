---
title: Distributions tekniker i Azure Functions
description: Lär dig hur du kan distribuera kod till Azure Functions på olika sätt.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943631"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributions tekniker i Azure Functions

Du kan använda flera olika tekniker för att distribuera din Azure Functions projekt kod till Azure. Den här artikeln innehåller en översikt över de distributions metoder som är tillgängliga för dig och rekommendationer för den bästa metoden att använda i olika scenarier. Den innehåller också en fullständig lista över och viktig information om de underliggande distributions teknikerna. 

## <a name="deployment-methods"></a>Distributions metoder

Den distributions teknik som du använder för att publicera kod i Azure bestäms vanligt vis av hur du publicerar appen. Lämplig distributions metod bestäms av speciella behov och punkten i utvecklings cykeln. Under utveckling och testning kan du till exempel distribuera direkt från utvecklingsverktyg, till exempel Visual Studio Code. När din app är i produktion är du mer sannolik att publicera kontinuerligt från käll kontroll eller genom att använda en automatiserad publicerings pipeline, som innehåller ytterligare validering och testning.  

I följande tabell beskrivs tillgängliga distributions metoder för ditt funktions projekt.

| Distributions &nbsp; typ | Metoder | Bäst för... |
| -- | -- | -- |
| Verktyg-baserade | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; Publishing](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio-publicering](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Kärn verktyg publicera](functions-run-local.md#publish) | Distributioner under utveckling och andra AD-Hock distributioner. Distributioner hanteras lokalt av verktyget. | 
| App Service-hanterad| &bull;&nbsp;[Distributions &nbsp; Center &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Container &nbsp; distributioner](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Kontinuerlig distribution (CI/CD) från käll kontroll eller ett behållar register. Distributioner hanteras av App Service Platform (kudu).|
| Externa pipelines|&bull;&nbsp;[Azure-pipeline](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-åtgärder](functions-how-to-github-actions.md) | Produktions-och DevOps-pipeliner som innehåller ytterligare verifiering, testning och andra åtgärder körs som en del av en automatiserad distribution. Distributioner hanteras av pipelinen. |

Även om vissa funktions distributioner använder den bästa tekniken baserat på deras kontext, baseras de flesta distributions metoderna på [zip-distribution](#zip-deploy).

## <a name="deployment-technology-availability"></a>Tillgänglighet för distributions teknik

Azure Functions stöder lokal utveckling på över plattformar och är värd för Windows och Linux. För närvarande är tre värd planer tillgängliga:

+ [Förbrukning](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedikerad (App Service)](dedicated-plan.md)

Varje plan har olika beteenden. Alla distributions tekniker är inte tillgängliga för varje smak av Azure Functions. Följande diagram visar vilka distributions tekniker som stöds för varje kombination av operativ system och värd plan:

| Distributions teknik | Windows-förbrukning | Windows Premium | Windows-dedikerad  | Linux-förbrukning | Linux Premium | Linux-dedikerad |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL för externt paket<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-distribution |✔|✔|✔|✔|✔|✔|
| Docker-behållare | | | | |✔|✔|
| Webbdistribution |✔|✔|✔| | | |
| Källkontroll |✔|✔|✔| |✔|✔|
| Lokal git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Molnbaserad synkronisering<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portal redigering |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> distributions teknik som kräver [synkronisering av manuella utlösare](#trigger-syncing).
<sup>2</sup> Portal redigering är bara aktive rad för http-och timer-utlösare för funktioner i Linux med hjälp av Premium och dedikerade planer

## <a name="key-concepts"></a>Viktiga begrepp

Vissa viktiga begrepp är viktiga för att förstå hur distributioner fungerar i Azure Functions.

### <a name="trigger-syncing"></a>Utlös synkronisering

När du ändrar någon av utlösarna måste funktions infrastrukturen vara medveten om ändringarna. Synkronisering sker automatiskt för många distributions tekniker. I vissa fall måste du dock synkronisera dina utlösare manuellt. När du distribuerar dina uppdateringar genom att referera till en extern paket-URL, lokal git, molnbaserad synkronisering eller FTP, måste du synkronisera utlösarna manuellt. Du kan synkronisera utlösare på ett av tre sätt:

* Starta om din Function-app i Azure Portal
* Skicka en HTTP POST-begäran till att `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` använda [huvud nyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Skicka en HTTP POST-begäran till `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Ersätt plats hållarna med ditt prenumerations-ID, resurs gruppens namn och namnet på din Function-app.

### <a name="remote-build"></a>Fjärrversion

Azure Functions kan utföra versioner automatiskt på den kod som den tar emot efter zip-distributioner. Dessa versioner fungerar annorlunda beroende på om din app körs på Windows eller Linux. Fjärran slutet utförs inte när en app tidigare har ställts in att köras i [Kör från paket](run-functions-from-deployment-package.md) läge. Om du vill lära dig hur du använder fjärran sluten version går du till [zip Deploy](#zip-deploy).

> [!NOTE]
> Om du har problem med fjärran sluten version kan det bero på att din app skapades innan funktionen gjordes tillgänglig (1 augusti 2019). Försök att skapa en ny function-app eller köra `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` för att uppdatera din Function-app. Det kan ta två försök att utföra det här kommandot.

#### <a name="remote-build-on-windows"></a>Fjärrversion på Windows

Alla Function-appar som körs i Windows har en liten hanterings app, SCM (eller [kudu](https://github.com/projectkudu/kudu))-platsen. Den här platsen hanterar en stor del av distributions-och bygg logiken för Azure Functions.

När en app distribueras till Windows körs språkspecifika kommandon, t. ex `dotnet restore` . (C#) eller `npm install` (Java Script).

#### <a name="remote-build-on-linux"></a>Fjärran sluten version på Linux

Följande [program inställningar](functions-how-to-use-azure-function-app-settings.md#settings) måste ställas in för att aktivera fjärran sluten version på Linux:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Som standard är både [Azure Functions Core tools](functions-run-local.md) och [Azure Functions-tillägget för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) utför fjärran slutna versioner vid distribution till Linux. På grund av detta skapar båda verktygen automatiskt de här inställningarna för dig i Azure.

När appar har skapats via fjärr anslutning i Linux [körs de från distributions paketet](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Förbrukningsplan

Linux Function-appar som körs i förbruknings planen har ingen SCM/kudu-plats, vilket begränsar distributions alternativen. Men Function-appar på Linux som körs i förbruknings planen stöder fjärran slutna versioner.

##### <a name="dedicated-and-premium-plans"></a>Dedikerade och Premium planer

Function-appar som körs på Linux i [dedikerade (App Service) plan](dedicated-plan.md) och [Premium-planen](functions-premium-plan.md) har också en begränsad SCM/kudu-plats.

## <a name="deployment-technology-details"></a>Information om distributions teknik

Följande distributions metoder är tillgängliga i Azure Functions.

### <a name="external-package-url"></a>URL för externt paket

Du kan använda en extern paket-URL för att referera till en fjärrfil-fil (zip) som innehåller din Function-app. Filen hämtas från den angivna URL: en och appen körs i läget [Kör från paket](run-functions-from-deployment-package.md) .

>__Så här använder du den:__ Lägg till [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) i dina program inställningar. Värdet för den här inställningen ska vara en URL (platsen för den angivna paket filen som du vill köra). Du kan lägga till inställningar antingen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) eller med [hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).
>
>Om du använder Azure Blob Storage använder du en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att ge funktioner åtkomst till paketet. När programmet startas om, hämtas en kopia av innehållet. Din referens måste vara giltig för programmets livs längd.

>__När du ska använda den:__ URL för externt paket är den enda distributions metod som stöds för Azure Functions som körs i Linux i förbruknings planen, om användaren inte vill att en [fjärran sluten version](#remote-build) ska ske. När du uppdaterar paket filen som en Function-app refererar till måste du [manuellt synkronisera utlösare](#trigger-syncing) för att meddela Azure att ditt program har ändrats.

### <a name="zip-deploy"></a>Zip-distribution

Använd zip Deploy för att skicka en. zip-fil som innehåller din Function-app till Azure. Om du vill kan du ange att appen ska börja [köras från paketet](run-functions-from-deployment-package.md)eller ange att en [fjärran sluten version](#remote-build) sker.

>__Så här använder du den:__ Distribuera med hjälp av ditt favorit klient verktyg: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)eller från kommando raden med hjälp av [Azure Functions Core tools](functions-run-local.md#project-file-deployment). Som standard använder dessa verktyg zip-distribution och [Kör från paket](run-functions-from-deployment-package.md). Core tools och Visual Studio Code-tillägget aktiverar både [fjärran sluten skapande](#remote-build) vid distribution till Linux. Om du vill distribuera en. zip-fil manuellt till Function-appen följer du anvisningarna i [distribuera från en. zip-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>När du distribuerar med hjälp av zip-distribution kan du ange att appen ska [köras från paketet](run-functions-from-deployment-package.md). Om du vill köra från paket anger du `WEBSITE_RUN_FROM_PACKAGE` värdet [] (Function-App-Settings. MD # website_run_from_package Application Setting `1` . Vi rekommenderar zip-distribution. Det ger snabbare inläsnings tider för dina program och är standard för VS Code, Visual Studio och Azure CLI.

>__När du ska använda den:__ Zip Deploy är den rekommenderade distributions tekniken för Azure Functions.

### <a name="docker-container"></a>Docker-behållare

Du kan distribuera en Linux container-avbildning som innehåller din Function-app.

>__Så här använder du den:__ Skapa en Linux Function-app i Premium-eller dedikerade planen och ange vilken behållar avbildning som ska köras från. Du kan göra det här på två sätt:
>
>* Skapa en Linux Function-app i en Azure App Service plan i Azure Portal. För **publicera** väljer du **Docker-avbildning** och konfigurerar sedan behållaren. Ange den plats där avbildningen finns.
>* Skapa en Linux Function-app på en App Service plan med hjälp av Azure CLI. Mer information finns i [skapa en funktion i Linux med hjälp av en anpassad avbildning](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Om du vill distribuera till en befintlig app med hjälp av en anpassad behållare, använder du i [Azure Functions Core tools](functions-run-local.md) [`func deploy`](functions-run-local.md#publish) kommandot.

>__När du ska använda den:__ Använd alternativet Docker-behållare när du behöver mer kontroll över Linux-miljön där funktions programmet körs. Den här distributions mekanismen är bara tillgänglig för funktioner som körs i Linux.

### <a name="web-deploy-msdeploy"></a>Webb distribution (MSDeploy)

Webb distributions paket och distribuerar dina Windows-program till alla IIS-servrar, inklusive dina funktions program som körs på Windows i Azure.

>__Så här använder du den:__ Använd [Visual Studio-verktyg för Azure Functions](functions-create-your-first-function-visual-studio.md). Avmarkera kryss rutan **Kör från paket fil (rekommenderas)** .
>
>Du kan också hämta [webb distribution 3,6](https://www.iis.net/downloads/microsoft/web-deploy) och ringa `MSDeploy.exe` direkt.

>__När du ska använda den:__ Webb distribution stöds och har inga problem, men den rekommenderade mekanismen är [zip-distribution med kör från-paketet aktiverat](#zip-deploy). Mer information finns i [utvecklings guiden för Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Källkontroll

Använd käll kontroll för att ansluta din Function-app till en git-lagringsplats. En uppdatering av kod i den här databasen utlöser distributionen. Mer information finns i wiki- [kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Så här använder du den:__ Använd Deployment Center i funktions områden i portalen för att konfigurera publicering från käll kontroll. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Att använda käll kontroll är det bästa sättet för team som samarbetar på sina funktions appar. Käll kontroll är ett lämpligt distributions alternativ som möjliggör mer avancerade distributions pipeliner.

### <a name="local-git"></a>Lokal Git

Du kan använda lokal git för att skicka kod från den lokala datorn till Azure Functions med hjälp av git.

>__Så här använder du den:__ Följ instruktionerna i [lokal Git-distribution till Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi att du använder en annan distributions metod. När du publicerar från lokal git måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="cloud-sync"></a>Molnbaserad synkronisering

Använd Cloud Sync för att synkronisera innehållet från Dropbox och OneDrive till Azure Functions.

>__Så här använder du den:__ Följ anvisningarna i [Synkronisera innehåll från en molnbaserad mapp](../app-service/deploy-content-sync.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributions metoder. När du publicerar med hjälp av molnbaserad synkronisering måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="ftp"></a>FTP

Du kan använda FTP för att överföra filer direkt till Azure Functions.

>__Så här använder du den:__ Följ anvisningarna i [distribuera innehåll med FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributions metoder. När du publicerar med hjälp av FTP måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="portal-editing"></a>Portal redigering

I det portalbaserade redigerings programmet kan du redigera filerna som finns i din Function-app direkt (distribueras i princip varje gång du sparar ändringarna).

>__Så här använder du den:__ För att kunna redigera dina funktioner i Azure Portal måste du ha [skapat dina funktioner i portalen](./functions-get-started.md). Om du vill bevara en enda källa till sanningen kan du använda en annan distributions metod för att göra din funktion skrivskyddad och förhindra fortsatt Portal redigering. Om du vill gå tillbaka till ett tillstånd där du kan redigera dina filer i Azure Portal kan du manuellt växla redigerings läget till `Read/Write` och ta bort eventuella distributions program inställningar (t. ex [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) ..

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions. För mer intensiv utvecklings arbete rekommenderar vi att du använder något av följande klient verktyg:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (kommando rad)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativ system och språk som stöder Portal redigering:

| Språk | Windows-förbrukning | Windows Premium | Windows-dedikerad | Linux-förbrukning | Linux Premium | Linux-dedikerad |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#-skript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (förhandsversion) | | | | | | |
| PowerShell (förhandsversion) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Portal redigering är bara aktive rad för HTTP-och timer-utlösare för funktioner i Linux med hjälp av Premium och dedikerade planer.

## <a name="deployment-behaviors"></a>Distributions beteenden

När du gör en distribution kan alla befintliga körningar slutföras, och tids gränsen för den nya koden läses in för att påbörja bearbetning av begär Anden.

Om du behöver mer kontroll över den här över gången bör du använda distributions platser.

## <a name="deployment-slots"></a>Distributionsfack

När du distribuerar din Function-app till Azure kan du distribuera till en separat distributions plats i stället för direkt till produktion. Mer information om distributions platser finns i dokumentationen för [Azure Functions distributions fack](functions-deployment-slots.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Läs de här artiklarna om du vill veta mer om hur du distribuerar dina funktions appar:

+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med hjälp av Azure-DevOps](functions-how-to-azure-devops.md)
+ [Zip-distributioner för Azure Functions](deployment-zip-push.md)
+ [Köra Azure Functions från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resurs distributionen för din Function-app i Azure Functions](functions-infrastructure-as-code.md)
