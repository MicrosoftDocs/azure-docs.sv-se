---
title: Utveckla Azure Functions med hjälp av Visual Studio Code
description: Lär dig hur du utvecklar och testar Azure Functions med hjälp av Azure Functions-tillägget för Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: 33adcb853099778c4b06a9cd428f480f6138ee8b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936982"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Utveckla Azure Functions med hjälp av Visual Studio Code

Med [Azure Functions tillägget för Visual Studio Code] kan du lokalt utveckla funktioner och distribuera dem till Azure. Om den här upplevelsen är din första med Azure Functions kan du läsa mer i [Introduktion till Azure Functions](functions-overview.md).

Azure Functions tillägget ger följande fördelar:

* Redigera, skapa och kör funktioner på din lokala utvecklings dator.
* Publicera Azure Functions-projektet direkt till Azure.
* Skriv dina funktioner på olika språk och dra nytta av fördelarna med Visual Studio Code.

Tillägget kan användas med följande språk, som stöds av Azure Functions runtime från och med version 2. x:

* [C#-kompilerad](functions-dotnet-class-library.md)
* [C#-skript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Kräver att du [anger C#-skript som standard språk för projektet](#c-script-projects).

I den här artikeln är exempel för närvarande endast tillgängliga för Java Script (Node.js) och C#-klass biblioteks funktioner.  

Den här artikeln innehåller information om hur du använder Azure Functions-tillägget för att utveckla funktioner och publicera dem i Azure. Innan du läser den här artikeln bör du [skapa din första funktion med hjälp av Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Blanda inte lokal utveckling och Portal utveckling för en enda Function-app. När du publicerar från ett lokalt projekt till en Function-app skriver distributions processen över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Krav

Innan du installerar och kör tillägget [Azure Functions extension][Azure Functions för Visual Studio Code]måste du uppfylla följande krav:

* [Visual Studio Code](https://code.visualstudio.com/) installerat på någon av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage-konto, skapas i prenumerationen när du [publicerar med hjälp av Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Du kan utveckla funktioner lokalt och publicera dem i Azure utan att behöva starta och köra dem lokalt. Om du vill köra dina funktioner lokalt måste du uppfylla vissa ytterligare krav, inklusive en automatisk nedladdning av Azure Functions Core Tools. Mer information finns i [ytterligare krav för att köra ett projekt lokalt](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med funktionen Functions kan du skapa ett app-projekt med funktioner, tillsammans med din första funktion. Följande steg visar hur du skapar en HTTP-utlöst funktion i ett nytt Functions-projekt. [Http-utlösare](functions-bindings-http-webhook.md) är den enklaste funktions utlösaren att demonstrera.

1. Från **Azure: Functions** väljer du ikonen **skapa funktion** :

    ![Skapa en funktion](./media/functions-develop-vs-code/create-function.png)

1. Välj mappen för ditt Function app-projekt och välj sedan **ett språk för ditt funktions projekt**.

1. Om du inte redan har installerat kärn verktygen uppmanas du att **välja en version** av de Core-verktyg som ska installeras. Välj version 2. x eller en senare version. 

1. Välj mallen **http-utlösare** , eller så kan du välja **hoppa över för** att skapa ett projekt utan en funktion. Du kan alltid [lägga till en funktion i projektet](#add-a-function-to-your-project) senare.

    ![Välj mallen HTTP-utlösare](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Skriv **HttpExample** som funktions namn och välj Ange, och välj sedan **funktion** auktorisering. Den här behörighets nivån kräver att du anger en [funktions nyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) när du anropar funktions slut punkten.

    ![Välj funktions auktorisering](./media/functions-develop-vs-code/create-function-auth.png)

    En funktion skapas på ditt valda språk och används i mallen för en HTTP-utlöst funktion.

    ![Mall för HTTP-utlöst funktion i Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Genererade projektfiler

Projekt mal len skapar ett projekt i ditt valda språk och installerar nödvändiga beroenden. För alla språk har det nya projektet följande filer:

* **host.jspå**: låter dig konfigurera funktions värden. Dessa inställningar gäller när du kör funktioner lokalt och när du kör dem i Azure. Mer information finns i [host.jsom referens](functions-host-json.md).

* **local.settings.jspå**: behåller inställningar som används när du kör funktioner lokalt. De här inställningarna används endast när du kör funktioner lokalt. Mer information finns i [filen med lokala inställningar](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom local.settings.jsi filen kan innehålla hemligheter måste du undanta den från projekt käll kontrollen.

Dessa filer skapas, beroende på ditt språk:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs klass biblioteks fil](functions-dotnet-class-library.md#functions-class-library-project) som implementerar funktionen.

Nu kan du lägga till indata och utgående bindningar i din funktion genom att [lägga till en parameter i en C#-klass biblioteks funktion](#add-input-and-output-bindings).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* En package.jspå filen i rotmappen.

* En HttpExample-mapp som innehåller [function.jsför definitions filen](functions-reference-node.md#folder-structure) och [index.js-filen](functions-reference-node.md#exporting-a-function), en Node.js-fil som innehåller funktions koden.

Nu kan du lägga till indata och utdata-bindningar i din funktion genom att [ändra function.jsi filen](#add-input-and-output-bindings).

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Du kan också [lägga till en ny funktion i projektet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installera bindningstillägg

Med undantag för HTTP-och timer-utlösare implementeras bindningar i tilläggs paket. Du måste installera tilläggs paketen för de utlösare och bindningar som behöver dem. Processen för att installera bindnings tillägg beror på ditt projekts språk.

# <a name="c"></a>[C\#](#tab/csharp)

Kör kommandot [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att installera de tilläggs paket som du behöver i projektet. Följande kommando installerar Azure Storage-tillägget, som implementerar bindningar för BLOB-, Queue-och table-lagring.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

Du kan lägga till en ny funktion i ett befintligt projekt med hjälp av en av de fördefinierade funktionerna utlöser mallarna. Om du vill lägga till en ny funktions utlösare väljer du F1 för att öppna kommando-paletten och söker sedan efter och kör kommandot **Azure Functions: skapa funktion**. Följ anvisningarna för att välja utlösnings typ och definiera de attribut som krävs för utlösaren. Om utlösaren kräver en åtkomst nyckel eller anslutnings sträng för att ansluta till en tjänst ska du få den redo innan du skapar funktions utlösaren.

Resultatet av den här åtgärden beror på projektets språk:

# <a name="c"></a>[C\#](#tab/csharp)

En ny C#-klass biblioteks fil (. CS) läggs till i projektet.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

En ny mapp skapas i projektet. Mappen innehåller en ny function.jspå filen och den nya JavaScript-filen.

---

## <a name="add-input-and-output-bindings"></a>Lägg till indata och utgående bindningar

Du kan utöka din funktion genom att lägga till indata och utgående bindningar. Processen för att lägga till bindningar beror på ditt projekts språk. Mer information om bindningar finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I följande exempel ansluter du till en lagrings kö med namnet `outqueue` , där anslutnings strängen för lagrings kontot anges i `MyStorageConnection` program inställningen i local.settings.jspå.

# <a name="c"></a>[C\#](#tab/csharp)

Uppdatera funktions metoden för att lägga till följande parameter i `Run` metod definitionen:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Den här koden kräver att du lägger till följande `using` instruktion:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg`Parametern är en `ICollector<T>` typ som representerar en samling meddelanden som skrivs till en utgående bindning när funktionen slutförs. Du lägger till ett eller flera meddelanden i samlingen. Dessa meddelanden skickas till kön när funktionen har slutförts.

Mer information finns i dokumentationen för [kö Storage utgående bindning](functions-bindings-storage-queue-output.md) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Med Visual Studio Code kan du lägga till bindningar till function.jsi filen genom att följa en behändig uppsättning prompter. Om du vill skapa en bindning högerklickar du på (Ctrl + klicka på macOS) **function.jspå** fil i mappen funktion och väljer **Lägg till bindning**:

![Lägga till en bindning till en befintlig JavaScript-funktion ](media/functions-develop-vs-code/function-add-binding.png)

I följande exempel uppmanas du att definiera en ny bindning för lagring av utdata:

| Prompt | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindnings riktning** | `out` | Bindningen är en utgående bindning. |
| **Välj bindning med riktning** | `Azure Queue Storage` | Bindningen är en Azure Storage Queue-bindning. |
| **Namnet som används för att identifiera den här bindningen i din kod** | `msg` | Namn som identifierar den bindnings parameter som refereras till i din kod. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *queueName* inte finns skapar bindningen den när den används första gången. |
| **Välj inställning från "local.settings.jspå"** | `MyStorageConnection` | Namnet på en program inställning som innehåller anslutnings strängen för lagrings kontot. `AzureWebJobsStorage`Inställningen innehåller anslutnings strängen för det lagrings konto som du skapade med Function-appen. |

I det här exemplet läggs följande bindning till i `bindings` matrisen i function.jsfilen:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Du kan också lägga till samma bindnings definition direkt till din function.jspå.

I funktions koden `msg` nås bindningen från `context` , som i det här exemplet:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Mer information finns i [bindnings referens för kö Storage-utdata](functions-bindings-storage-queue-output.md) .

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicera till Azure

Med Visual Studio Code kan du publicera dina Functions-projekt direkt till Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

När du publicerar från Visual Studio Code till en ny function-app i Azure, erbjuds du både en snabb funktion för att skapa en sökväg och en avancerad sökväg. 

När du publicerar från Visual Studio Code kan du utnyttja [zip Deploy](functions-deployment-technologies.md#zip-deploy) -tekniken. 

### <a name="quick-function-app-create"></a>Snabb funktion appen skapa

När du väljer **+ Skapa ny function-app i Azure...** genererar tillägget automatiskt värden för de Azure-resurser som krävs av din Function-app. Dessa värden baseras på det funktionens program namn som du väljer. Ett exempel på hur du använder standardvärden för att publicera projektet till en ny function-app i Azure finns i [artikeln snabb start för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Om du vill ange explicita namn för de skapade resurserna måste du välja den avancerade skapa sökvägen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publicera ett projekt i en ny function-app i Azure med hjälp av avancerade alternativ

Följande steg publicerar projektet till en ny function-app som skapats med avancerade skapande alternativ:

1. I avsnittet **Azure: Functions** väljer du ikonen **distribuera till Funktionsapp** .

    ![Funktionsappinställningar](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Om du inte är inloggad uppmanas du att **Logga in på Azure**. Du kan också **skapa ett kostnads fritt Azure-konto**. När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code.

1. Om du har flera prenumerationer **väljer du en prenumeration** för Function-appen och väljer sedan **+ Skapa ny Funktionsapp i Azure... _Avancerat_**. Det här _avancerade_ alternativet ger dig större kontroll över de resurser som du skapar i Azure. 

1. Ange följande information i meddelandet:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj Function-app i Azure | Skapa nya Funktionsapp i Azure | I nästa prompt, ange ett globalt unikt namn som identifierar din nya Function-app och välj sedan Retur. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj ett operativ system | Windows | Function-appen körs i Windows. |
    | Välj en värd plan | Förbrukningsplan | En server lös [förbruknings Plans värd](consumption-plan.md) används. |
    | Välj en körnings miljö för den nya appen | Ditt projekt språk | Körningen måste matcha projektet som du publicerar. |
    | Välj en resurs grupp för nya resurser | Skapa ny resurs grupp | Vid nästa prompt skriver du ett resurs grupp namn, till exempel `myResourceGroup` och väljer sedan Retur. Du kan också välja en befintlig resurs grupp. |
    | Välj ett lagrings konto | Skapa ett nytt lagringskonto | Vid nästa prompt skriver du ett globalt unikt namn för det nya lagrings kontot som används av din Function-app och väljer sedan Retur. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får bara innehålla siffror och gemena bokstäver. Du kan också välja ett befintligt konto. |
    | Välj en plats för nya resurser | region | Välj en plats i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner har åtkomst till. |

    Ett meddelande visas när din Function-app har skapats och distributions paketet används. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat.

## <a name="republish-project-files"></a>Publicera projektfiler igen

När du konfigurerar [kontinuerlig distribution](functions-continuous-deployment.md)uppdateras din Function-app i Azure varje gång källfiler uppdateras på den anslutna käll platsen. Vi rekommenderar kontinuerlig distribution, men du kan också publicera om projekt fil uppdateringar från Visual Studio Code.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="get-the-url-of-the-deployed-function"></a>Hämta URL: en för den distribuerade funktionen

Om du vill anropa en HTTP-utlöst funktion behöver du URL: en för funktionen när den distribueras till Function-appen. Denna URL innehåller alla nödvändiga [funktions nycklar](functions-bindings-http-webhook-trigger.md#authorization-keys). Du kan använda tillägget för att hämta dessa URL: er för dina distribuerade funktioner.

1. Välj F1 för att öppna kommando-paletten och Sök sedan efter och kör kommandot **Azure Functions: kopierings funktions webb adress**.

1. Följ anvisningarna för att välja din Function-app i Azure och sedan den speciella HTTP-utlösare som du vill anropa.

Funktions webb adressen kopieras till Urklipp, tillsammans med eventuella obligatoriska nycklar som skickas av `code` Frågeparametern. Använd ett HTTP-verktyg för att skicka POST-förfrågningar eller en webbläsare för GET-begäranden till fjärrfunktionen.  

## <a name="run-functions-locally"></a>Köra funktioner lokalt

Med tillägget Azure Functions kan du köra ett Functions-projekt på din lokala utvecklings dator. Den lokala körnings miljön är samma körning som är värd för din Function-app i Azure. Lokala inställningar läses från [local.settings.jsi filen](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Ytterligare krav för att köra ett projekt lokalt

För att köra ett Functions-projekt lokalt måste du uppfylla följande ytterligare krav:

* Installera version 2. x eller senare av [Azure Functions Core tools](functions-run-local.md#v2). Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

* Installera de specifika kraven för ditt valda språk:

    | Språk | Krav |
    | -------- | --------- |
    | **C#** | [C#-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI-verktyg](/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Fel sökare för Java-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)<br/>[Maven 3 eller senare](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python-3.6.8](https://www.python.org/downloads/) rekommenderas|

    <sup>*</sup>Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).

### <a name="configure-the-project-to-run-locally"></a>Konfigurera projektet för att köras lokalt

Functions-körningen använder ett Azure Storage-konto internt för alla utlösare typer förutom HTTP och Webhooks. Du måste ange **Values. AzureWebJobsStorage** -nyckeln till en giltig anslutnings sträng för Azure Storage konto.

I det här avsnittet används [Azure Storage tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) med [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till och hämta anslutnings strängen för lagring.

Så här anger du anslutnings strängen för lagrings kontot:

1. Öppna **Cloud Explorer** i Visual Studio, expandera **lagrings konto**  >  **ditt lagrings konto** och välj sedan **Egenskaper** och kopiera värdet för **primär anslutnings sträng** .

2. I projektet öppnar du local.settings.jspå filen och anger värdet för nyckeln **AzureWebJobsStorage** till den anslutnings sträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i **matrisen** Arrays för alla andra anslutningar som krävs av dina funktioner.

Mer information finns i [filen med lokala inställningar](#local-settings-file).

### <a name="debugging-functions-locally"></a>Felsöka funktioner lokalt  

Om du vill felsöka funktionerna väljer du F5. Om du inte redan har hämtat [Core Tools][Azure Functions Core tools]uppmanas du att göra det. När Core Tools är installerat och körs visas utdata i terminalen. Detta är detsamma som att köra `func host start` kommandot Core tools från terminalen, men med ytterligare build-uppgifter och en bifogad fel sökare.  

När projektet körs kan du utlösa funktioner på samma sätt som när projektet distribueras till Azure. När projektet körs i fel söknings läge, trycks Bryt punkter i Visual Studio Code, som förväntat.

Begär ande-URL: en för HTTP-utlösare visas i utdata i terminalen. Funktions nycklar för HTTP-utlösare används inte när ett projekt körs lokalt. Mer information finns i [strategier för att testa koden i Azure Functions](functions-test-a-function.md).  

Mer information finns i [arbeta med Azure Functions Core Tools][Azure Functions Core tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras de här inställningarna inte automatiskt när projektet publiceras till Azure. När publiceringen är klar får du möjlighet att publicera inställningar från local.settings.jspå till din Function-app i Azure. Läs mer i  [Publicera program inställningar](#publish-application-settings).

Värden i **ConnectionString** publiceras aldrig.

Värdena för funktionen Application Setting kan också läsas i koden som miljövariabler. Mer information finns i avsnittet miljövariabler i dessa språkspecifika referens artiklar:

* [C# förkompilerad](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Program inställningar i Azure

Inställningarna i local.settings.jspå filen i projektet bör vara samma som program inställningarna i Function-appen i Azure. Alla inställningar som du lägger till local.settings.jspå måste också läggas till i Function-appen i Azure. De här inställningarna laddas inte upp automatiskt när du publicerar projektet. På samma sätt måste alla inställningar som du skapar i din Function-app [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) hämtas till det lokala projektet.

### <a name="publish-application-settings"></a>Publicera program inställningar

Det enklaste sättet att publicera de nödvändiga inställningarna till din Function-app i Azure är att använda länken **upload Settings** som visas när du har publicerat projektet:

![Ladda upp program inställningar](./media/functions-develop-vs-code/upload-app-settings.png)

Du kan också publicera inställningar genom att använda kommandot **Azure Functions: Ladda upp lokal inställning** i paletten kommando. Du kan lägga till enskilda inställningar i program inställningarna i Azure med hjälp av kommandot **Azure Functions: Lägg till ny inställning** .

> [!TIP]
> Se till att spara local.settings.jspå filen innan du publicerar den.

Om den lokala filen är krypterad dekrypteras den, publiceras och krypteras igen. Om det finns inställningar som innehåller motstridiga värden på de två platserna, uppmanas du att välja hur du vill fortsätta.

Visa befintliga appinställningar i avsnittet **Azure: Functions** genom att expandera din prenumeration, din Function-app och **program inställningar**.

![Visa funktions program inställningar i Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Hämta inställningar från Azure

Om du har skapat program inställningar i Azure kan du ladda ned dem till local.settings.jspå filen med hjälp av kommandot **Azure Functions: Ladda ned Fjärrinställningar** .

Om den lokala filen är krypterad, dekrypteras, uppdateras och krypteras igen, som vid överföring. Om det finns inställningar som innehåller motstridiga värden på de två platserna, uppmanas du att välja hur du vill fortsätta.

## <a name="monitoring-functions"></a>Övervaknings funktioner

När du [Kör funktioner lokalt](#run-functions-locally)strömmas loggdata till Terminal-konsolen. Du kan också hämta logg data när ditt Functions-projekt körs i en Function-app i Azure. Du kan antingen ansluta till strömmande loggar i Azure om du vill se logg data nästan i real tid, eller så kan du aktivera Application Insights för en mer fullständig förståelse för hur din Function-app fungerar.

### <a name="streaming-logs"></a>Direktuppspelningsloggar

När du utvecklar ett program är det ofta användbart att se loggnings information i nära real tid. Du kan visa en ström med loggfiler som genereras av dina funktioner. Dessa utdata är ett exempel på strömmande loggar för en begäran till en HTTP-utlöst funktion:

![Strömmande loggar utdata för HTTP-utlösare](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Mer information finns i [strömmande loggar](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Strömmande loggar har bara stöd för en enda instans av Functions-värden. När funktionen skalas till flera instanser visas inte data från andra instanser i logg strömmen. [Live Metrics Stream](../azure-monitor/app/live-stream.md) i Application Insights har stöd för flera instanser. Även i nära real tid baseras strömnings analysen på [exempel data](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Vi rekommenderar att du övervakar körningen av dina funktioner genom att integrera din Function-app med Application Insights. När du skapar en Function-app i Azure Portal, sker denna integrering som standard. När du skapar en Function-app under Visual Studio-publicering måste du integrera Application Insights dig själv. Mer information finns i [aktivera Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration).

Mer information om övervakning med hjälp av Application Insights finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C \# skript projekt

Som standard skapas alla C#-projekt som [c#-kompilerade klass biblioteks projekt](functions-dotnet-class-library.md). Om du föredrar att arbeta med C#-skript projekt i stället måste du välja C#-skript som standard språk i inställningarna för Azure Functions-tillägg:

1. Välj inställningar för **fil**  >  **Inställningar**  >  .

1. Gå till tillägg för **användar inställningar**  >    >  **Azure Functions**.

1. Välj **C #-skript** från **Azure Function: projekt språk**.

När du har slutfört de här stegen inkluderar anrop till de underliggande kärn verktygen `--csx` alternativet, vilket genererar och publicerar projektfiler för C#-skript (. CSX). När du har angett det här standard språket visas alla projekt som du skapar som standard i C#-skript projekt. Du uppmanas inte att välja ett projekt språk när ett standardvärde har angetts. Om du vill skapa projekt på andra språk måste du ändra den här inställningen eller ta bort den från användar settings.jsi filen. När du har tagit bort den här inställningen uppmanas du att välja språk när du skapar ett projekt.

## <a name="command-palette-reference"></a>Referens för kommando-palett

Azure Functions-tillägget ger ett användbart grafiskt gränssnitt i avsnittet för att interagera med dina funktions program i Azure. Samma funktion är också tillgänglig som kommandon på kommando paletten (F1). Följande Azure Functions kommandon är tillgängliga:

|Azure Functions kommando  | Beskrivning  |
|---------|---------|
|**Lägg till nya inställningar**  |  Skapar en ny program inställning i Azure. Läs mer i [Publicera program inställningar](#publish-application-settings). Du kan också behöva [Ladda ned den här inställningen till dina lokala inställningar](#download-settings-from-azure). |
| **Konfigurera distributions källa** | Ansluter din Function-app i Azure till en lokal git-lagringsplats. Läs mer i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). |
| **Anslut till GitHub-lagringsplatsen** | Ansluter din Function-app till en GitHub-lagringsplats. |
| **Kopiera funktions webb adress** | Hämtar den fjärranslutna URL: en för en HTTP-utlöst funktion som körs i Azure. Läs mer i [Hämta URL för den distribuerade funktionen](#get-the-url-of-the-deployed-function). |
| **Skapa Function-app i Azure** | Skapar en ny function-app i din prenumeration i Azure. Mer information finns i avsnittet om hur du [publicerar till en ny function-app i Azure](#publish-to-azure).        |
| **Dekryptera inställningar** | Dekrypterar [lokala inställningar](#local-settings-file) som har krypterats med **Azure Functions: kryptera inställningar**.  |
| **Ta bort Funktionsapp** | Tar bort en Function-app från din prenumeration i Azure. När det inte finns några andra appar i App Service plan får du möjlighet att ta bort det. Andra resurser, t. ex. lagrings konton och resurs grupper, tas inte bort. Om du vill ta bort alla resurser bör du i stället [ta bort resurs gruppen](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Ditt lokala projekt påverkas inte. |
|**Ta bort funktion**  | Tar bort en befintlig funktion från en Function-app i Azure. Eftersom den här borttagningen inte påverkar ditt lokala projekt, bör du i stället ta bort funktionen lokalt och sedan [publicera projektet](#republish-project-files)igen. |
| **Ta bort proxy** | Tar bort en Azure Functions proxy från din Function-app i Azure. Mer information om proxyservrar finns i [arbeta med Azure Functions-proxyservrar](functions-proxies.md). |
| **Ta bort inställning** | Tar bort en funktion app-inställning i Azure. Den här borttagningen påverkar inte inställningarna i local.settings.jsi filen. |
| **Koppla från lagrings platsen**  | Tar bort den [kontinuerliga distributions](functions-continuous-deployment.md) anslutningen mellan en Function-app i Azure och en lagrings plats för käll kontroll. |
| **Hämta Fjärrinställningar** | Hämtar inställningar från den valda Function-appen i Azure till din local.settings.jsi filen. Om den lokala filen är krypterad, dekrypteras, uppdateras och krypteras igen. Om det finns inställningar som innehåller motstridiga värden på de två platserna, uppmanas du att välja hur du vill fortsätta. Se till att spara ändringarna i local.settings.jspå filen innan du kör det här kommandot. |
| **Redigera inställningar** | Ändrar värdet för en befintlig funktion i appens inställning i Azure. Det här kommandot påverkar inte inställningarna i local.settings.jsi filen.  |
| **Krypterings inställningar** | Krypterar enskilda objekt i `Values` matrisen i de [lokala inställningarna](#local-settings-file). I den här filen `IsEncrypted` är även inställt på `true` , som anger att den lokala körnings miljön ska dekryptera inställningarna innan de används. Kryptera lokala inställningar för att minska risken för att värdefull information avslöjas. I Azure lagras program inställningarna alltid som krypterade. |
| **Kör funktionen nu** | Startar en [timer-utlöst funktion](functions-bindings-timer.md) manuellt i Azure. Det här kommandot används för testning. Mer information om hur du utlöser icke-HTTP-funktioner i Azure finns i [köra en icke-http-utlöst funktion manuellt](functions-manually-run-non-http.md). |
| **Initiera projekt för användning med VS Code** | Lägger till de nödvändiga Project-projektfilerna i Visual Studio-kod i ett befintligt Functions-projekt. Använd det här kommandot för att arbeta med ett projekt som du har skapat med hjälp av kärn verktyg. |
| **Installera eller uppdatera Azure Functions Core Tools** | Installerar eller uppdaterar [Azure Functions Core tools], som används för att köra funktioner lokalt. |
| **Omdistribuera**  | Gör att du kan distribuera projektfiler från en ansluten git-lagringsplats till en speciell distribution i Azure. [Publicera om projektet](#republish-project-files)om du vill publicera om lokala uppdateringar från Visual Studio Code. |
| **Byt namn på Inställningar** | Ändrar nyckel namnet för en befintlig funktion i appens inställning i Azure. Det här kommandot påverkar inte inställningarna i local.settings.jsi filen. När du har bytt namn på inställningarna i Azure bör du [Hämta ändringarna till det lokala projektet](#download-settings-from-azure). |
| **Starta om** | Startar om Function-appen i Azure. Om du distribuerar uppdateringar startas Function-appen också om. |
| **Ange AzureWebJobsStorage**| Anger värdet för `AzureWebJobsStorage` program inställningen. Den här inställningen krävs av Azure Functions. Den ställs in när en Function-app skapas i Azure. |
| **Start** | Startar en stoppad Function-app i Azure. |
| **Starta strömmande loggar** | Startar strömmande loggar för Function-appen i Azure. Använd strömmande loggar vid fjärrfelsökning i Azure om du behöver se loggnings information i nära real tid. Mer information finns i [strömmande loggar](#streaming-logs). |
| **Stopp** | Stoppar en Function-app som körs i Azure. |
| **Stoppa strömmande loggar** | Stoppar strömmande loggar för Function-appen i Azure. |
| **Växla som plats inställning** | När det är aktiverat, ser du till att en program inställning finns kvar för en specifik distributions plats. |
| **Avinstallera Azure Functions Core Tools** | Tar bort Azure Functions Core Tools, vilket krävs av tillägget. |
| **Ladda upp lokala inställningar** | Laddar upp inställningar från local.settings.jspå filen till den valda Function-appen i Azure. Om den lokala filen är krypterad, dekrypteras, laddas upp och krypteras igen. Om det finns inställningar som innehåller motstridiga värden på de två platserna, uppmanas du att välja hur du vill fortsätta. Se till att spara ändringarna i local.settings.jspå filen innan du kör det här kommandot. |
| **Visa incheckning i GitHub** | Visar de senaste incheckningarna i en speciell distribution när din Function-app är ansluten till en lagrings plats. |
| **Visa distributions loggar** | Visar loggarna för en speciell distribution till Function-appen i Azure. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [arbeta med Azure Functions Core tools](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klass bibliotek finns i [referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.

[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md