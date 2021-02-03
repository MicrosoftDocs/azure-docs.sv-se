---
title: Utveckla Azure Functions med hjälp av Visual Studio Code
description: Lär dig hur du utvecklar och testar Azure Functions med hjälp av Azure Functions-tillägget för Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: d4353e6be313d61716933879efa930e22472781b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493962"
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

## <a name="prerequisites"></a>Förutsättningar

Innan du installerar och kör tillägget [Azure Functions extension][Azure Functions för Visual Studio Code]måste du uppfylla följande krav:

* [Visual Studio Code](https://code.visualstudio.com/) installerat på någon av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage-konto, skapas i prenumerationen när du [publicerar med hjälp av Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Kör lokala krav

Dessa krav krävs bara för att [köra och felsöka dina funktioner lokalt](#run-functions-locally). De behövs inte för att skapa eller publicera projekt i Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2. x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code. 

+ [.Net Core CLI verktyg](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2. x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

+ [Fel söknings program för Java-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) rekommenderas. Andra versioner som stöds finns i [Java-versioner](functions-reference-java.md#java-versions).

+ [Maven 3 eller senare](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2. x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

+ [Node.js](https://nodejs.org/), aktiva LTS och underhåll LTS-versioner (10.14.1 rekommenderas). Använd `node --version` kommandot för att kontrol lera din version. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2. x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) rekommenderas. Versions information finns i [PowerShell-versioner](functions-reference-powershell.md#powershell-versions).

+ Både [.net core 3,1 runtime](https://www.microsoft.com/net/download) och [.net Core 2,1 runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2. x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Kärn verktyg omfattar hela Azure Functions runtime, så hämtning och installation kan ta lite tid.

+ [Python 3. x](https://www.python.org/downloads/). Versions information finns i [python-versioner](functions-reference-python.md#python-version) av Azure Functions Runtime.

+ [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med funktionen Functions kan du skapa ett app-projekt med funktioner, tillsammans med din första funktion. Följande steg visar hur du skapar en HTTP-utlöst funktion i ett nytt Functions-projekt. [Http-utlösare](functions-bindings-http-webhook.md) är den enklaste funktions utlösaren att demonstrera.

1. Från **Azure: Functions** väljer du ikonen **skapa funktion** :

    ![Skapa en funktion](./media/functions-develop-vs-code/create-function.png)

1. Välj mappen för ditt Function app-projekt och välj sedan **ett språk för ditt funktions projekt**.

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

# <a name="java"></a>[Java](#tab/java)

+ En pom.xml-fil i rotmappen som definierar projekt-och distributions parametrar, inklusive projekt beroenden och [Java-versionen](functions-reference-java.md#java-versions). pom.xml innehåller också information om de Azure-resurser som skapas under en distribution.   

+ En Functions [. java-fil](functions-reference-java.md#triggers-and-annotations) i din src-sökväg som implementerar-funktionen.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* En package.jspå filen i rotmappen.

* En HttpExample-mapp som innehåller [function.jsför definitions filen](functions-reference-node.md#folder-structure) och [index.js-filen](functions-reference-node.md#exporting-a-function), en Node.js-fil som innehåller funktions koden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* En HttpExample-mapp som innehåller [function.jspå definitions filen](functions-reference-powershell.md#folder-structure) och run.ps1s filen, som innehåller funktions koden.
 
# <a name="python"></a>[Python](#tab/python)
    
* En requirements.txt-fil på projekt nivå som visar paket som krävs av functions.
    
* En HttpExample-mapp som innehåller [function.jspå definitions filen](functions-reference-python.md#folder-structure) och \_ \_ \_ \_ filen init. py, som innehåller funktions koden.

---

I det här läget kan du [lägga till indata och utgående bindningar](#add-input-and-output-bindings) i din funktion. Du kan också [lägga till en ny funktion i projektet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installera bindningstillägg

Med undantag för HTTP-och timer-utlösare implementeras bindningar i tilläggs paket. Du måste installera tilläggs paketen för de utlösare och bindningar som behöver dem. Processen för att installera bindnings tillägg beror på ditt projekts språk.

# <a name="c"></a>[C\#](#tab/csharp)

Kör kommandot [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att installera de tilläggs paket som du behöver i projektet. Följande kommando installerar Azure Storage-tillägget, som implementerar bindningar för BLOB-, Queue-och table-lagring.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

Du kan lägga till en ny funktion i ett befintligt projekt med hjälp av en av de fördefinierade funktionerna utlöser mallarna. Om du vill lägga till en ny funktions utlösare väljer du F1 för att öppna kommando-paletten och söker sedan efter och kör kommandot **Azure Functions: skapa funktion**. Följ anvisningarna för att välja utlösnings typ och definiera de attribut som krävs för utlösaren. Om utlösaren kräver en åtkomst nyckel eller anslutnings sträng för att ansluta till en tjänst ska du få den redo innan du skapar funktions utlösaren.

Resultatet av den här åtgärden beror på projektets språk:

# <a name="c"></a>[C\#](#tab/csharp)

En ny C#-klass biblioteks fil (. CS) läggs till i projektet.

# <a name="java"></a>[Java](#tab/java)

En ny Java-fil (. Java) har lagts till i projektet.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

En ny mapp skapas i projektet. Mappen innehåller en ny function.jspå filen och den nya JavaScript-filen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En ny mapp skapas i projektet. Mappen innehåller en ny function.jsi filen och den nya PowerShell-koden.

# <a name="python"></a>[Python](#tab/python)

En ny mapp skapas i projektet. Mappen innehåller en ny function.jspå filen och den nya python-koden.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Ansluta till tjänster

Du kan ansluta din funktion till andra Azure-tjänster genom att lägga till indata och utgående bindningar. Bindningar ansluter din funktion till andra tjänster utan att du behöver skriva anslutnings koden. Processen för att lägga till bindningar beror på ditt projekts språk. Mer information om bindningar finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I följande exempel ansluter du till en lagrings kö med namnet `outqueue` , där anslutnings strängen för lagrings kontot anges i `MyStorageConnection` program inställningen i local.settings.jspå.

# <a name="c"></a>[C\#](#tab/csharp)

Uppdatera funktions metoden för att lägga till följande parameter i `Run` metod definitionen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametern är en `ICollector<T>` typ som representerar en samling meddelanden som skrivs till en utgående bindning när funktionen slutförs. Följande kod lägger till ett meddelande i samlingen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Meddelanden skickas till kön när funktionen slutförs.

Mer information finns i artikel dokumentationen för [kön lagrings data bindnings referens](functions-bindings-storage-queue-output.md?tabs=csharp) . Mer allmän information om vilka bindningar som kan läggas till i en funktion finns i [lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Uppdatera funktions metoden för att lägga till följande parameter i `Run` metod definitionen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametern är en `OutputBinding<T>` typ, där är `T` en sträng som skrivs till en utgående bindning när funktionen slutförs. Följande kod ställer in meddelandet i utgående bindning:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i artikel dokumentationen för [kön lagrings data bindnings referens](functions-bindings-storage-queue-output.md?tabs=java) . Mer allmän information om vilka bindningar som kan läggas till i en funktion finns i [lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

I funktions koden `msg` nås bindningen från `context` , som i det här exemplet:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i artikel dokumentationen för [kön lagrings data bindnings referens](functions-bindings-storage-queue-output.md?tabs=javascript) . Mer allmän information om vilka bindningar som kan läggas till i en funktion finns i [lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i artikel dokumentationen för [kön lagrings data bindnings referens](functions-bindings-storage-queue-output.md?tabs=powershell) . Mer allmän information om vilka bindningar som kan läggas till i en funktion finns i [lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Uppdatera `Main` definitionen för att lägga till en utdataparameter `msg: func.Out[func.QueueMessage]` så att definitionen ser ut som i följande exempel:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Följande kod lägger till sträng data från begäran till kön utdata:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i artikel dokumentationen för [kön lagrings data bindnings referens](functions-bindings-storage-queue-output.md?tabs=python) . Mer allmän information om vilka bindningar som kan läggas till i en funktion finns i [lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicera till Azure

Med Visual Studio Code kan du publicera dina Functions-projekt direkt till Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

När du publicerar från Visual Studio Code till en ny function-app i Azure, kan du välja antingen en snabb funktion för att skapa en sökväg med hjälp av standardvärden eller en avancerad sökväg där du får mer kontroll över de fjär resurser som skapats. 

När du publicerar från Visual Studio Code kan du utnyttja [zip Deploy](functions-deployment-technologies.md#zip-deploy) -tekniken. 

### <a name="quick-function-app-create"></a>Snabb funktion appen skapa

När du väljer **+ Skapa ny function-app i Azure...** genererar tillägget automatiskt värden för de Azure-resurser som krävs av din Function-app. Dessa värden baseras på det funktionens program namn som du väljer. Ett exempel på hur du använder standardvärden för att publicera projektet till en ny function-app i Azure finns i [artikeln snabb start för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Om du vill ange explicita namn för de skapade resurserna måste du välja den avancerade skapa sökvägen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publicera ett projekt i en ny function-app i Azure med hjälp av avancerade alternativ

Följande steg publicerar projektet till en ny function-app som skapats med avancerade skapande alternativ:

1. I kommandots lastpall anger **Azure Functions: distribuera till Function-appen**.

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

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Hämta URL: en för en HTTP-utlöst funktion i Azure

Om du vill anropa en HTTP-utlöst funktion från en klient behöver du URL-adressen till funktionen när den distribueras till Function-appen. Denna URL innehåller alla nödvändiga funktions nycklar. Du kan använda tillägget för att hämta dessa URL: er för dina distribuerade funktioner. Om du bara vill köra fjärrfunktionen i Azure [använder du funktionen kör funktion nu](#run-functions-in-azure) i tillägget.

1. Välj F1 för att öppna kommando-paletten och Sök sedan efter och kör kommandot **Azure Functions: kopierings funktions webb adress**.

1. Följ anvisningarna för att välja din Function-app i Azure och sedan den speciella HTTP-utlösare som du vill anropa.

Funktions webb adressen kopieras till Urklipp, tillsammans med eventuella obligatoriska nycklar som skickas av `code` Frågeparametern. Använd ett HTTP-verktyg för att skicka POST-förfrågningar eller en webbläsare för GET-begäranden till fjärrfunktionen.  

När du hämtar URL: er för funktioner i Azure använder tillägget ditt Azure-konto för att automatiskt hämta de nycklar som krävs för att starta funktionen. [Läs mer om funktions åtkomst nycklar](security-concepts.md#function-access-keys). Att starta icke-HTTP-utlösta funktioner kräver att du använder administratörs nyckeln.

## <a name="republish-project-files"></a>Publicera projektfiler igen

När du ställer in [kontinuerlig distribution](functions-continuous-deployment.md)uppdateras din Function-app i Azure när du uppdaterar källfilerna på den anslutna käll platsen. Vi rekommenderar kontinuerlig distribution, men du kan också publicera om projekt fil uppdateringar från Visual Studio Code.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Köra funktioner

Med tillägget Azure Functions kan du köra enskilda funktioner, antingen i ditt projekt på din lokala utvecklings dator eller i din Azure-prenumeration. 

För HTTP-utlösare anropar tillägget HTTP-slutpunkten. För andra typer av utlösare anropar den administratörs-API: er för att starta funktionen. Meddelande texten för begäran som skickas till funktionen beror på typen av utlösare. När en utlösare kräver test data uppmanas du att ange data i ett särskilt JSON-format.

### <a name="run-functions-in-azure"></a>Köra funktioner i Azure

Köra en funktion i Azure från Visual Studio Code. 

1. I kommandots lastpall anger **Azure Functions: kör funktion nu** och välj din Azure-prenumeration. 

1. Välj din Function-app i Azure från listan. Om du inte ser din Function-App kontrollerar du att du är inloggad på rätt prenumeration. 

1. Välj den funktion som du vill köra från listan och skriv meddelande texten för begäran i **Ange brödtext för begäran**. Skicka meddelandet till din funktion genom att trycka på RETUR. Standard texten i retur texten i **begäran** ska ange formatet på bröd texten. Om Function-appen saknar funktioner visas ett meddelande om detta fel. 

1. När funktionen körs i Azure och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code.
 
Du kan också köra funktionen från **Azure: Functions** -ytan genom att högerklicka (Ctrl-klicka på Mac) den funktion som du vill köra från din Function-app i din Azure-prenumeration och välja **Kör funktion nu..**..

När du kör funktioner i Azure använder tillägget ditt Azure-konto för att automatiskt hämta de nycklar som krävs för att starta funktionen. [Läs mer om funktions åtkomst nycklar](security-concepts.md#function-access-keys). Att starta icke-HTTP-utlösta funktioner kräver att du använder administratörs nyckeln.

### <a name="run-functions-locally"></a>Köra funktioner lokalt

Den lokala körnings miljön är samma körning som är värd för din Function-app i Azure. Lokala inställningar läses från [local.settings.jsi filen](#local-settings-file). Om du vill köra ditt Functions-projekt lokalt måste du uppfylla [ytterligare krav](#run-local-requirements).

#### <a name="configure-the-project-to-run-locally"></a>Konfigurera projektet för att köras lokalt

Functions-körningen använder ett Azure Storage-konto internt för alla utlösare typer förutom HTTP och Webhooks. Du måste ange **Values. AzureWebJobsStorage** -nyckeln till en giltig anslutnings sträng för Azure Storage konto.

I det här avsnittet används [Azure Storage tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) med [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till och hämta anslutnings strängen för lagring.

Så här anger du anslutnings strängen för lagrings kontot:

1. Öppna **Cloud Explorer** i Visual Studio, expandera **lagrings konto**  >  **ditt lagrings konto** och välj sedan **Egenskaper** och kopiera värdet för **primär anslutnings sträng** .

2. I projektet öppnar du local.settings.jspå filen och anger värdet för nyckeln **AzureWebJobsStorage** till den anslutnings sträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i **matrisen** Arrays för alla andra anslutningar som krävs av dina funktioner.

Mer information finns i [filen med lokala inställningar](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Felsök funktioner lokalt  

Om du vill felsöka funktionerna väljer du F5. Om du inte redan har hämtat [Core Tools][Azure Functions Core tools]uppmanas du att göra det. När Core Tools är installerat och körs visas utdata i terminalen. Detta är detsamma som att köra `func host start` kommandot Core tools från terminalen, men med extra build-uppgifter och en bifogad fel sökare.  

När projektet körs kan du använda funktionen **Kör funktion nu...** i tillägget för att utlösa funktioner som när projektet distribueras till Azure. När projektet körs i fel söknings läge, trycks Bryt punkter i Visual Studio-kod som du förväntar dig. 

1. I kommandots lastpall anger **Azure Functions: kör funktion nu** och välj **lokalt projekt**. 

1. Välj den funktion som du vill köra i projektet och skriv meddelande texten för begäran i **Ange brödtext för begäran**. Skicka meddelandet till din funktion genom att trycka på RETUR. Standard texten i retur texten i **begäran** ska ange formatet på bröd texten. Om Function-appen saknar funktioner visas ett meddelande om detta fel. 

1. När funktionen körs lokalt och när svaret tas emot, aktive ras ett meddelande i Visual Studio Code. Information om funktions körningen visas på panelen **Terminal** .

Att köra funktioner lokalt behöver inte använda nycklar. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras de här inställningarna inte automatiskt när projektet publiceras till Azure. När publiceringen är klar får du möjlighet att publicera inställningar från local.settings.jspå till din Function-app i Azure. Läs mer i  [Publicera program inställningar](#publish-application-settings).

Värden i **ConnectionString** publiceras aldrig.

Värdena för funktionen Application Setting kan också läsas i koden som miljövariabler. Mer information finns i avsnittet miljövariabler i dessa språkspecifika referens artiklar:

* [C# förkompilerad](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Program inställningar i Azure

Inställningarna i local.settings.jspå filen i projektet bör vara samma som program inställningarna i Function-appen i Azure. Alla inställningar som du lägger till i local.settings.jsmåste också läggas till i Function-appen i Azure. De här inställningarna laddas inte upp automatiskt när du publicerar projektet. På samma sätt måste alla inställningar som du skapar i din Function-app [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) hämtas till det lokala projektet.

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
| **Kör funktionen nu** | Startar en funktion manuellt med hjälp av administrations-API: er. Det här kommandot används för testning, både lokalt vid fel sökning och mot funktioner som körs i Azure. När du utlöser en funktion i Azure får tillägget först automatiskt en administratörs nyckel som används för att anropa de fjärr administrations-API: er som startar funktioner i Azure. Bröd texten i meddelandet som skickas till API: et beror på typen av utlösare. Timer-utlösare kräver inte att du skickar några data. |
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