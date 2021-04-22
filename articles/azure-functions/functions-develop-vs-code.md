---
title: Utveckla Azure Functions med hjälp av Visual Studio Code
description: Lär dig hur du utvecklar och Azure Functions med hjälp av Azure Functions för Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871281"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Utveckla Azure Functions med hjälp av Visual Studio Code

Med [Azure Functions för Visual Studio Code] kan du lokalt utveckla funktioner och distribuera dem till Azure. Om den här upplevelsen är din första med Azure Functions kan du lära dig mer i [En introduktion till Azure Functions](functions-overview.md).

Tillägget Azure Functions ger följande fördelar:

* Redigera, skapa och kör funktioner på din lokala utvecklingsdator.
* Publicera ditt Azure Functions-projekt direkt till Azure.
* Skriv dina funktioner på olika språk samtidigt som du drar nytta av fördelarna med Visual Studio Code.

Tillägget kan användas med följande språk, som stöds av den Azure Functions från och med version 2.x:

* [C#-kompilerad](functions-dotnet-class-library.md)
* [C#-skript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Kräver att du [anger C#-skript som standardprojektspråk.](#c-script-projects)

I den här artikeln är exempel för närvarande endast tillgängliga för javaScript-funktioner (Node.js) och C#-klassbibliotek.  

Den här artikeln innehåller information om hur du använder Azure Functions för att utveckla funktioner och publicera dem till Azure. Innan du läser den här artikeln bör du [skapa din första funktion med hjälp av Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Blanda inte lokal utveckling och portalutveckling för en enda funktionsapp. När du publicerar från ett lokalt projekt till en funktionsapp skriver distributionsprocessen över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Förutsättningar

Innan du installerar och kör [Azure Functions-tillägget][Azure Functions för Visual Studio Code]måste du uppfylla följande krav:

* [Visual Studio Code](https://code.visualstudio.com/) installerat på någon av [plattformarna som stöds.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage-konto, skapas i din prenumeration när du publicerar med hjälp [av Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Köra lokala krav

De här förutsättningarna krävs bara för [att köra och felsöka dina funktioner lokalt.](#run-functions-locally) De behöver inte skapa eller publicera projekt till Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ Den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2.x eller senare. Core Tools-paketet laddas ned och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions, så det kan ta lite tid att ladda ned och installera.

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code. 

+ [.NET Core CLI verktyg](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ Den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2.x eller senare. Core Tools-paketet laddas ned och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions,så det kan ta lite tid att ladda ned och installera.

+ [Felsökningsprogrammet för Java-tillägget](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) rekommenderas. Andra versioner som stöds finns i [Java-versioner.](functions-reference-java.md#java-versions)

+ [Maven 3 eller senare](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ Den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2.x eller senare. Core Tools-paketet laddas ned och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions, så det kan ta lite tid att ladda ned och installera.

+ [Node.js](https://nodejs.org/)versionerna Active LTS och Maintenance LTS (10.14.1 rekommenderas). Använd kommandot `node --version` för att kontrollera din version. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ Den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2.x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions runtime, så nedladdning och installation kan ta lite tid.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) rekommenderas. Versionsinformation finns i [PowerShell-versioner.](functions-reference-powershell.md#powershell-versions)

+ Både [.NET Core 3.1-körning](https://dotnet.microsoft.com/download) och [.NET Core 2.1-körning](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ Versionen [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 2.x eller senare. Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions runtime, så nedladdning och installation kan ta lite tid.

+ [Python 3.x](https://www.python.org/downloads/). Versionsinformation finns i [Python-versioner](functions-reference-python.md#python-version) av Azure Functions runtime.

+ [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med Functions-tillägget kan du skapa ett funktionsappsprojekt tillsammans med din första funktion. Följande steg visar hur du skapar en HTTP-utlöst funktion i ett nytt Functions-projekt. [HTTP-utlösare](functions-bindings-http-webhook.md) är den enklaste funktionsutlösarmallen att demonstrera.

1. Från **Azure: Funktioner** väljer du **ikonen Skapa** funktion:

    ![Skapa en funktion](./media/functions-develop-vs-code/create-function.png)

1. Välj mappen för funktionsappsprojektet och välj sedan **ett språk för funktionsprojektet**.

1. Välj mallen HTTP-utlösarfunktion, eller så kan du **välja Hoppa över för tillfället** för att skapa ett projekt utan en funktion.  Du kan alltid [lägga till en funktion i projektet](#add-a-function-to-your-project) senare.

    ![Välj mallen HTTP-utlösare](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Skriv **HttpExample** som funktionsnamn och välj Retur. Välj sedan **Funktionsauktorisering.** Den här auktoriseringsnivån kräver att du anger [en funktionsnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) när du anropar funktionsslutpunkten.

    ![Välj Funktionsauktorisering](./media/functions-develop-vs-code/create-function-auth.png)

    En funktion skapas på ditt valda språk och i mallen för en HTTP-utlöst funktion.

    ![HTTP-utlöst funktionsmall i Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Genererade projektfiler

Projektmallen skapar ett projekt på ditt valda språk och installerar nödvändiga beroenden. Det nya projektet har följande filer för alla språk:

* **host.jspå**: Låter dig konfigurera Functions-värden. De här inställningarna gäller när du kör funktioner lokalt och när du kör dem i Azure. Mer information finns i [host.jsi referensen](functions-host-json.md).

* **local.settings.jspå**: Underhåller inställningar som används när du kör funktioner lokalt. De här inställningarna används bara när du kör funktioner lokalt. Mer information finns i [Filen Med lokala inställningar.](#local-settings-file)

    >[!IMPORTANT]
    >Eftersom filen local.settings.jsfilen kan innehålla hemligheter måste du undanta den från din projektkällkontroll.

Beroende på vilket språk du använder skapas dessa andra filer:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs-klassbiblioteksfil](functions-dotnet-class-library.md#functions-class-library-project) som implementerar funktionen.

# <a name="java"></a>[Java](#tab/java)

+ En pom.xml i rotmappen som definierar projekt- och distributionsparametrarna, inklusive projektberoenden och [Java-versionen](functions-reference-java.md#java-versions). Den pom.xml innehåller också information om de Azure-resurser som skapas under en distribution.   

+ En [Functions.java-fil](functions-reference-java.md#triggers-and-annotations) i din src-sökväg som implementerar funktionen.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* En package.jspå filen i rotmappen.

* En HttpExample-mapp [ som innehållerfunction.jspå definitionsfilen](functions-reference-node.md#folder-structure) och [index.js](functions-reference-node.md#exporting-a-function), en Node.js-fil som innehåller funktionskoden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* En HttpExample-mapp [ som innehållerfunction.jspå definitionsfilen](functions-reference-powershell.md#folder-structure) och run.ps1 som innehåller funktionskoden.
 
# <a name="python"></a>[Python](#tab/python)
    
* En fil på projektnivå requirements.txt som visar de paket som krävs av Functions.
    
* En HttpExample-mapp [som innehållerfunction.jspå definitionsfilen](functions-reference-python.md#folder-structure) och \_ \_ \_ \_ init.py-filen som innehåller funktionskoden.

---

Nu kan du lägga till [indata- och utdatabindningar](#add-input-and-output-bindings) i funktionen. Du kan också [lägga till en ny funktion i projektet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installera bindningstillägg

Bindningar implementeras i tilläggspaket förutom HTTP- och timerutlösare. Du måste installera tilläggspaketen för de utlösare och bindningar som behöver dem. Processen för att installera bindningstillägg beror på projektets språk.

# <a name="c"></a>[C\#](#tab/csharp)

Kör kommandot [dotnet add package](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att installera de tilläggspaket som du behöver i projektet. Följande kommando installerar tillägget Azure Storage, som implementerar bindningar för blob-, kö- och tabellagring.

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

## <a name="add-a-function-to-your-project"></a>Lägga till en funktion i projektet

Du kan lägga till en ny funktion i ett befintligt projekt med hjälp av en av de fördefinierade mallarna för Functions-utlösare. Om du vill lägga till en ny funktionsutlösare väljer du F1 för att öppna kommandopaletten och söker sedan efter och kör kommandot **Azure Functions: Create Function**. Följ anvisningarna för att välja utlösartyp och definiera de attribut som krävs för utlösaren. Om utlösaren kräver en åtkomstnyckel eller anslutningssträng för att ansluta till en tjänst måste du förbereda den innan du skapar funktionsutlösaren.

Resultatet av den här åtgärden beror på projektets språk:

# <a name="c"></a>[C\#](#tab/csharp)

En ny C#-klassbiblioteksfil (.cs) läggs till i projektet.

# <a name="java"></a>[Java](#tab/java)

En ny Java-fil (.java) läggs till i projektet.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

En ny mapp skapas i projektet. Mappen innehåller en ny fil function.jsfil och den nya JavaScript-kodfilen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En ny mapp skapas i projektet. Mappen innehåller en ny fil function.jsfilen och den nya PowerShell-kodfilen.

# <a name="python"></a>[Python](#tab/python)

En ny mapp skapas i projektet. Mappen innehåller en ny function.jspå filen och den nya Python-kodfilen.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Ansluta till tjänster

Du kan ansluta din funktion till andra Azure-tjänster genom att lägga till indata- och utdatabindningar. Bindningar ansluter din funktion till andra tjänster utan att du behöver skriva anslutningskoden. Processen för att lägga till bindningar beror på projektets språk. Mer information om bindningar finns i Azure Functions [utlösare och bindningar.](functions-triggers-bindings.md)

Följande exempel ansluter till en lagringskö med namnet , där anslutningssträngen för lagringskontot anges i programinställningen `outqueue` `MyStorageConnection` i local.settings.jspå.

# <a name="c"></a>[C\#](#tab/csharp)

Uppdatera funktionsmetoden för att lägga till följande parameter i `Run` metoddefinitionen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Parametern `msg` är en typ som representerar en samling meddelanden som skrivs till en `ICollector<T>` utdatabindning när funktionen har slutförts. Följande kod lägger till ett meddelande i samlingen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Meddelanden skickas till kön när funktionen har slutförts.

Mer information finns i referensartikeln [för queue storage-utdatabindning.](functions-bindings-storage-queue-output.md?tabs=csharp) Mer information om vilka bindningar som kan läggas till i en funktion finns i Lägga till bindningar till [en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Uppdatera funktionsmetoden för att lägga till följande parameter i `Run` metoddefinitionen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parametern `msg` är en `OutputBinding<T>` typ, där är en sträng som `T` skrivs till en utdatabindning när funktionen har slutförts. Följande kod anger meddelandet i utdatabindningen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i referensartikeln [om Queue Storage-utdatabindning.](functions-bindings-storage-queue-output.md?tabs=java) Mer information om vilka bindningar som kan läggas till i en funktion finns i Lägga till bindningar till [en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

I funktionskoden nås `msg` bindningen från , som `context` i det här exemplet:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i referensartikeln [om Queue Storage-utdatabindning.](functions-bindings-storage-queue-output.md?tabs=javascript) Mer information om vilka bindningar som kan läggas till i en funktion finns i Lägga till bindningar till [en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i referensartikeln [om Queue Storage-utdatabindning.](functions-bindings-storage-queue-output.md?tabs=powershell) Mer information om vilka bindningar som kan läggas till i en funktion finns i Lägga till bindningar till [en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Uppdatera definitionen `Main` för att lägga till en `msg: func.Out[func.QueueMessage]` utdataparameter så att definitionen ser ut som i följande exempel:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Följande kod lägger till strängdata från begäran i utdatakön:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Det här meddelandet skickas till kön när funktionen har slutförts.

Mer information finns i referensartikeln [om Queue Storage-utdatabindning.](functions-bindings-storage-queue-output.md?tabs=python) Mer information om vilka bindningar som kan läggas till i en funktion finns i Lägga till bindningar till [en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicera till Azure

Visual Studio Code kan du publicera functions-projektet direkt till Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

När du publicerar från Visual Studio Code till en ny funktionsapp i Azure kan du välja antingen en snabbsökväg för att skapa en funktionsapp med hjälp av standardvärden eller en avancerad sökväg, där du har mer kontroll över de fjärrresurser som skapats. 

När du publicerar från Visual Studio Code kan du dra nytta av [zip-distributionstekniken.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Snabbfunktionsapp – skapa

När du väljer **+ Skapa ny funktionsapp** i Azure... genererar tillägget automatiskt värden för de Azure-resurser som krävs av funktionsappen. Dessa värden baseras på funktionsappens namn som du väljer. Ett exempel på hur du använder standardvärden för att publicera projektet till en ny funktionsapp i Azure finns i [snabbstartsartikeln för Visual Studio Code.](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)

Om du vill ange explicita namn för de skapade resurserna måste du välja den avancerade sökvägen för att skapa.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publicera ett projekt till en ny funktionsapp i Azure med hjälp av avancerade alternativ

Följande steg publicerar projektet till en ny funktionsapp som skapats med avancerade alternativ för att skapa:

1. I kommandopaletten anger du **Azure Functions: Distribuera till funktionsappen**.

1. Om du inte är inloggad uppmanas du att logga **in på Azure**. Du kan också **skapa ett kostnadsfritt Azure-konto.** När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code.

1. Om du har flera prenumerationer väljer **du en prenumeration** för funktionsappen och sedan + Skapa ny **funktionsapp i Azure... _Avancerat_**. Med _det_ här alternativet Avancerat får du mer kontroll över de resurser som du skapar i Azure. 

1. Ange följande information genom att följa anvisningarna:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj funktionsapp i Azure | Skapa ny funktionsapp i Azure | I nästa meddelande anger du ett globalt unikt namn som identifierar den nya funktionsappen och väljer sedan Retur. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj ett operativsystem | Windows | Funktionsappen körs i Windows. |
    | Välj en värdplan | Förbrukningsplan | En värd för [en serverlös förbrukningsplan](consumption-plan.md) används. |
    | Välj en körning för den nya appen | Ditt projektspråk | Körningen måste matcha projektet som du publicerar. |
    | Välj en resursgrupp för nya resurser | Skapa ny resursgrupp | I nästa uppmaning skriver du ett resursgruppsnamn, till `myResourceGroup` exempel , och väljer sedan Retur. Du kan också välja en befintlig resursgrupp. |
    | Välj ett lagringskonto | Skapa ett nytt lagringskonto | I nästa uppmaning skriver du ett globalt unikt namn för det nya lagringskonto som används av funktionsappen och väljer sedan Retur. Lagringskontonamn måste vara mellan 3 och 24 tecken långa och får bara innehålla siffror och gemener. Du kan också välja ett befintligt konto. |
    | Välj en plats för nya resurser | region | Välj en plats i en [region nära](https://azure.microsoft.com/regions/) dig eller nära andra tjänster som dina funktioner har åtkomst till. |

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet tillämpas. Välj **Visa utdata** i det här meddelandet för att visa skapande- och distributionsresultat, inklusive de Azure-resurser som du skapade.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Hämta URL:en för en HTTP-utlöst funktion i Azure

Om du vill anropa en HTTP-utlöst funktion från en klient behöver du url:en för funktionen när den distribueras till funktionsappen. Den här URL:en innehåller alla nödvändiga funktionsnycklar. Du kan använda tillägget för att hämta dessa URL:er för dina distribuerade funktioner. Om du bara vill köra fjärrfunktionen i Azure använder [du funktionen Kör nu](#run-functions-in-azure) i tillägget.

1. Välj F1 för att öppna kommandopaletten och sök sedan efter och kör **kommandot Azure Functions: Copy Function URL (Kopiera funktions-URL).**

1. Följ anvisningarna för att välja din funktionsapp i Azure och sedan den specifika HTTP-utlösare som du vill anropa.

Funktions-URL:en kopieras till Urklipp, tillsammans med alla nödvändiga nycklar som skickas av `code` frågeparametern. Använd ett HTTP-verktyg för att skicka POST-begäranden eller en webbläsare för GET-begäranden till fjärrfunktionen.  

När du hämtar URL:en för funktioner i Azure använder tillägget ditt Azure-konto för att automatiskt hämta de nycklar som behövs för att starta funktionen. [Läs mer om åtkomstnycklar för funktioner.](security-concepts.md#function-access-keys) För att starta icke-HTTP-utlösta funktioner måste du använda administratörsnyckeln.

## <a name="republish-project-files"></a>Publicera om projektfiler

När du ställer in [kontinuerlig distribution](functions-continuous-deployment.md)uppdateras funktionsappen i Azure när du uppdaterar källfilerna på den anslutna källplatsen. Vi rekommenderar kontinuerlig distribution, men du kan även publicera om dina projektfiluppdateringar från Visual Studio Code.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Kör funktioner

Med Azure Functions kan du köra enskilda funktioner, antingen i projektet på din lokala utvecklingsdator eller i din Azure-prenumeration. 

För HTTP-utlösarfunktioner anropar tillägget HTTP-slutpunkten. För andra typer av utlösare anropas administratörs-API:er för att starta funktionen. Meddelandetexten i begäran som skickas till funktionen beror på typen av utlösare. När en utlösare kräver testdata uppmanas du att ange data i ett specifikt JSON-format.

### <a name="run-functions-in-azure"></a>Köra funktioner i Azure

Köra en funktion i Azure från Visual Studio Code. 

1. På kommandopaletten anger **du Azure Functions: Kör nu och** väljer din Azure-prenumeration. 

1. Välj din funktionsapp i Azure i listan. Om du inte ser funktionsappen kontrollerar du att du är inloggad på rätt prenumeration. 

1. Välj den funktion som du vill köra från listan och skriv meddelandetexten för begäran i Ange **begärandetext**. Tryck på Retur för att skicka det här begärandemeddelandet till din funktion. Standardtexten i **Enter request body (Ange** begärandetext) ska ange formatet för brödtexten. Om funktionsappen inte har några funktioner visas ett meddelandefel med det här felet. 

1. När funktionen körs i Azure och returnerar ett svar utlöses ett meddelande i Visual Studio Code.
 
Du kan också köra funktionen från **området Azure: Funktioner** genom att högerklicka (Ctrl-klicka på Mac) på den funktion som du vill köra från funktionsappen i din Azure-prenumeration och välja **Kör funktion nu...**.

När du kör funktioner i Azure använder tillägget ditt Azure-konto för att automatiskt hämta de nycklar som behövs för att starta funktionen. [Läs mer om åtkomstnycklar för funktioner.](security-concepts.md#function-access-keys) Om du vill starta icke-HTTP-utlösta funktioner måste du använda administratörsnyckeln.

### <a name="run-functions-locally"></a>Köra funktioner lokalt

Den lokala körningen är samma körning som är värd för din funktionsapp i Azure. Lokala inställningar läses från [local.settings.jspå filen](#local-settings-file). Om du vill köra Functions-projektet lokalt måste du uppfylla [ytterligare krav.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>Konfigurera projektet att köras lokalt

Functions-körningen använder ett Azure Storage internt för alla utlösartyper förutom HTTP och webhooks. Därför måste du ange nyckeln **Values.AzureWebJobsStorage** till en giltig Azure Storage-kontoanslutningssträng.

Det här avsnittet använder [Azure Storage för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) [med Azure Storage Explorer](https://storageexplorer.com/) att ansluta till och hämta lagringsanslutningssträngen.

Så här anger du anslutningssträngen för lagringskontot:

1. I Visual Studio du **Cloud Explorer,** **expanderar Lagringskonto** Ditt lagringskonto och väljer egenskaper och kopierar värdet primär  >   **anslutningssträng.** 

2. I projektet öppnar du filen local.settings.jsoch anger värdet för **nyckeln AzureWebJobsStorage** till den anslutningssträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i **matrisen Värden** för alla andra anslutningar som krävs av dina funktioner.

Mer information finns i [Filen med lokala inställningar.](#local-settings-file)

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Felsöka funktioner lokalt  

Om du vill felsöka dina funktioner väljer du F5. Om du inte redan har hämtat [Core Tools][Azure Functions Core Tools]uppmanas du att göra det. När Core Tools har installerats och körs visas utdata i Terminal. Det här är samma sak som att `func host start` köra Core Tools-kommandot från Terminal, men med extra bygguppgifter och ett anslutet felsökningsverktyg.  

När projektet körs kan du använda funktionen Kör funktion **nu...** i tillägget för att utlösa dina funktioner på samma sätt som när projektet distribueras till Azure. När projektet körs i felsökningsläge kommer brytpunkter till i Visual Studio Code som förväntat. 

1. På kommandopaletten anger **du Azure Functions: Execute function now (Kör funktion nu)** och väljer Local project ( **Lokalt projekt).** 

1. Välj den funktion som du vill köra i projektet och skriv meddelandetexten i begäran i Ange **begärandetexten**. Tryck på Retur för att skicka det här begärandemeddelandet till din funktion. Standardtexten i **Enter-begärandetexten** ska ange formatet för brödtexten. Om funktionsappen inte har några funktioner visas ett meddelandefel med det här felet. 

1. När funktionen körs lokalt och när svaret har tagits emot, utlöses ett meddelande i Visual Studio Code. Information om funktionskörningen visas i **terminalpanelen.**

Att köra funktioner lokalt kräver inte att nycklar används. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras inte de här inställningarna automatiskt när projektet publiceras till Azure. När publiceringen är klar kan du välja att publicera inställningar från local.settings.jstill din funktionsapp i Azure. Mer information finns i [Publicera programinställningar.](#publish-application-settings)

Värden i **ConnectionStrings publiceras** aldrig.

Funktionsprogrammets inställningsvärden kan också läsas i koden som miljövariabler. Mer information finns i avsnitten miljövariabler i dessa språkspecifika referensartiklar:

* [Förkompilerad C#](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Programinställningar i Azure

Inställningarna i filen local.settings.jsi projektet ska vara samma som programinställningarna i funktionsappen i Azure. Alla inställningar som du lägger local.settings.jspå måste du också lägga till i funktionsappen i Azure. De här inställningarna laddas inte upp automatiskt när du publicerar projektet. På samma sätt måste alla inställningar som du skapar i [funktionsappen i portalen](functions-how-to-use-azure-function-app-settings.md#settings) laddas ned till ditt lokala projekt.

### <a name="publish-application-settings"></a>Publicera programinställningar

Det enklaste sättet att publicera de nödvändiga inställningarna till funktionsappen i Azure är att använda länken **Ladda** upp inställningar som visas när du har publicerat projektet:

![Ladda upp programinställningar](./media/functions-develop-vs-code/upload-app-settings.png)

Du kan också publicera inställningar med hjälp av **kommandot Azure Functions: Ladda upp lokal** inställning i kommandopaletten. Du kan lägga till enskilda inställningar i programinställningarna i Azure med hjälp **av Azure Functions: Lägg till ny** inställning.

> [!TIP]
> Se till att spara local.settings.jspå filen innan du publicerar den.

Om den lokala filen krypteras dekrypteras den, publiceras och krypteras igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du vill fortsätta.

Visa befintliga appinställningar i **området Azure: Funktioner** genom att expandera prenumerationen, funktionsappen och **programinställningarna**.

![Visa funktionsappinställningar i Visual Studio Kod](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Ladda ned inställningar från Azure

Om du har skapat programinställningar i Azure kan du ladda ned dem till din fil local.settings.jsmed hjälp **av kommandot Azure Functions: Ladda ned fjärrinställningar.**

Precis som vid uppladdning så dekrypteras, uppdateras och krypteras den igen om den lokala filen krypteras. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du vill fortsätta.

## <a name="monitoring-functions"></a>Övervakningsfunktioner

När du [kör funktioner lokalt](#run-functions-locally)strömmas loggdata till terminalkonsolen. Du kan också hämta loggdata när functions-projektet körs i en funktionsapp i Azure. Du kan antingen ansluta till strömningsloggar i Azure för att se loggdata nästan i realtid, eller så kan du aktivera Application Insights för en mer fullständig förståelse av hur funktionsappen fungerar.

### <a name="streaming-logs"></a>Direktuppspelningsloggar

När du utvecklar ett program är det ofta användbart att se loggningsinformation i nära realtid. Du kan visa en dataström med loggfiler som genereras av dina funktioner. Dessa utdata är ett exempel på strömmande loggar för en begäran till en HTTP-utlöst funktion:

![Utdata från strömningsloggar för HTTP-utlösare](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Mer information finns i [Strömningsloggar.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Strömningsloggar stöder bara en enda instans av Functions-värden. När funktionen skalas till flera instanser visas inte data från andra instanser i loggströmmen. [Live Metrics Stream](../azure-monitor/app/live-stream.md) i Application Insights har stöd för flera instanser. Även om analyserna i nära realtid är baserade på [exempeldata](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Vi rekommenderar att du övervakar körningen av dina funktioner genom att integrera funktionsappen med Application Insights. När du skapar en funktionsapp i Azure Portal sker den här integreringen som standard. När du skapar funktionsappen under Visual Studio publicering måste du integrera Application Insights själv. Mer information finns i [Aktivera Application Insights integrering.](configure-monitoring.md#enable-application-insights-integration)

Mer information om övervakning med Application Insights finns i [Övervaka Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>\#C-skriptprojekt

Som standard skapas alla C#-projekt som [C#-kompilerade klassbiblioteksprojekt](functions-dotnet-class-library.md). Om du föredrar att arbeta med C#-skriptprojekt i stället måste du välja C#-skript som standardspråk i Azure Functions för tillägg:

1. Välj **Inställningar**  >  **för**  >  **filinställningar.**

1. Gå till **Tillägg för**  >  **användarinställningar**  >  **Azure Functions**.

1. Välj **C#Script** från **Azure Function: Project Language**.

När du har slutfört de här stegen innehåller anrop till de underliggande Core Tools alternativet som genererar och publicerar `--csx` C#-skriptfiler (.csx). När du har angett det här standardspråket kommer alla projekt som du skapar som standard att ha C#-skriptprojekt. Du uppmanas inte att välja ett projektspråk när en standardinställning har angetts. Om du vill skapa projekt på andra språk måste du ändra den här inställningen eller ta bort den från settings.jspå filen. När du har tagit bort den här inställningen uppmanas du återigen att välja språk när du skapar ett projekt.

## <a name="command-palette-reference"></a>Referens för kommandopalett

Tillägget Azure Functions ett användbart grafiskt gränssnitt i området för att interagera med dina funktionsappar i Azure. Samma funktioner är också tillgängliga som kommandon i kommandopaletten (F1). De Azure Functions kommandona är tillgängliga:

|Azure Functions kommandot  | Description  |
|---------|---------|
|**Lägg till nya inställningar**  |  Skapar en ny programinställning i Azure. Mer information finns i [Publicera programinställningar.](#publish-application-settings) Du kan också behöva ladda [ned den här inställningen till dina lokala inställningar.](#download-settings-from-azure) |
| **Konfigurera distributionskälla** | Ansluter funktionsappen i Azure till en lokal Git-lagringsplats. Mer information finns i [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). |
| **Ansluta till GitHub-lagringsplatsen** | Ansluter funktionsappen till en GitHub-lagringsplats. |
| **Kopiera funktions-URL** | Hämtar fjärr-URL:en för en HTTP-utlöst funktion som körs i Azure. Mer information finns i [Hämta URL:en för den distribuerade funktionen](#get-the-url-of-the-deployed-function). |
| **Skapa funktionsapp i Azure** | Skapar en ny funktionsapp i din prenumeration i Azure. Mer information finns i avsnittet om hur du publicerar [till en ny funktionsapp i Azure](#publish-to-azure).        |
| **Dekryptera inställningar** | Dekrypterar [lokala inställningar](#local-settings-file) som har krypterats av **Azure Functions: Kryptera inställningar**.  |
| **Ta bort funktionsapp** | Tar bort en funktionsapp från din prenumeration i Azure. När det inte finns några andra App Service plan kan du även ta bort dem. Andra resurser, till exempel lagringskonton och resursgrupper, tas inte bort. Om du vill ta bort alla resurser bör du [i stället ta bort resursgruppen](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Ditt lokala projekt påverkas inte. |
|**Ta bort funktion**  | Tar bort en befintlig funktion från en funktionsapp i Azure. Eftersom den här borttagningen inte påverkar ditt lokala projekt bör du i stället ta bort funktionen lokalt och [sedan publicera om projektet](#republish-project-files). |
| **Ta bort proxy** | Tar bort en Azure Functions proxy från funktionsappen i Azure. Mer information om proxys finns i [Arbeta med Azure Functions-proxyservrar](functions-proxies.md). |
| **Ta bort inställning** | Tar bort en funktionsappinställning i Azure. Den här borttagningen påverkar inte inställningarna i local.settings.jspå filen. |
| **Koppla från lagringsplatsen**  | Tar bort den [kontinuerliga distributionsanslutningen](functions-continuous-deployment.md) mellan en funktionsapp i Azure och en lagringsplats för källkontroll. |
| **Ladda ned fjärrinställningar** | Hämtar inställningar från den valda funktionsappen i Azure till din local.settings.jspå filen. Om den lokala filen är krypterad dekrypteras den, uppdateras och krypteras igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du vill fortsätta. Se till att spara ändringarna i filen local.settings.jsinnan du kör det här kommandot. |
| **Redigera inställningar** | Ändrar värdet för en befintlig funktionsappinställning i Azure. Det här kommandot påverkar inte inställningarna i local.settings.jspå filen.  |
| **Kryptera inställningar** | Krypterar enskilda objekt i `Values` matrisen i de [lokala inställningarna](#local-settings-file). I den här filen `IsEncrypted` är också inställt på , vilket anger att den lokala körningen dekrypterar `true` inställningarna innan de används. Kryptera lokala inställningar för att minska risken för att värdefull information läcker. I Azure lagras alltid programinställningar krypterade. |
| **Execute Function Now** | Startar en funktion manuellt med hjälp av administratörs-API:er. Det här kommandot används för testning, både lokalt under felsökning och mot funktioner som körs i Azure. När du utlöser en funktion i Azure hämtar tillägget först automatiskt en administratörsnyckel som används för att anropa fjärradministratörs-API:erna som startar funktioner i Azure. Brödtexten i meddelandet som skickas till API:et beror på typen av utlösare. Timerutlösare kräver inte att du skickar några data. |
| **Initiera projekt för användning med VS Code** | Lägger till nödvändiga Visual Studio Code-projektfiler i ett befintligt Functions-projekt. Använd det här kommandot för att arbeta med ett projekt som du har skapat med hjälp av Core Tools. |
| **Installera eller uppdatera Azure Functions Core Tools** | Installerar eller uppdaterar [Azure Functions Core Tools], som används för att köra funktioner lokalt. |
| **Omdistribuera**  | Gör att du kan omdistribuera projektfiler från en ansluten Git-lagringsplats till en specifik distribution i Azure. Publicera om lokala uppdateringar från Visual Studio Code genom [att publicera om projektet](#republish-project-files). |
| **Byt namn på inställningar** | Ändrar nyckelnamnet för en befintlig funktionsappinställning i Azure. Det här kommandot påverkar inte inställningarna i local.settings.jspå filen. När du har bytt namn på inställningarna i Azure bör [du ladda ned ändringarna till det lokala projektet](#download-settings-from-azure). |
| **Starta om** | Startar om funktionsappen i Azure. När du distribuerar uppdateringar startas även funktionsappen om. |
| **Ange AzureWebJobsStorage**| Anger värdet för `AzureWebJobsStorage` programinställningen. Den här inställningen krävs av Azure Functions. Den anges när en funktionsapp skapas i Azure. |
| **Start** | Startar en stoppad funktionsapp i Azure. |
| **Starta strömningsloggar** | Startar strömningsloggarna för funktionsappen i Azure. Använd strömningsloggar vid fjärrfelsökning i Azure om du behöver se loggningsinformation i nära realtid. Mer information finns i [Strömningsloggar.](#streaming-logs) |
| **Stopp** | Stoppar en funktionsapp som körs i Azure. |
| **Stoppa strömningsloggar** | Stoppar strömningsloggarna för funktionsappen i Azure. |
| **Växla till fackinställning** | När den här inställningen är aktiverad ser du till att programinställningen finns kvar för ett visst distributionsfack. |
| **Avinstallera Azure Functions Core Tools** | Tar Azure Functions Core Tools, vilket krävs av tillägget. |
| **Ladda upp lokala inställningar** | Laddar upp inställningar från din local.settings.jstill den valda funktionsappen i Azure. Om den lokala filen är krypterad dekrypteras den, laddas upp och krypteras igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du vill fortsätta. Se till att spara ändringarna i filen local.settings.jsinnan du kör det här kommandot. |
| **Visa genomförande i GitHub** | Visar den senaste genomfören i en specifik distribution när funktionsappen är ansluten till en lagringsplats. |
| **Visa distributionsloggar** | Visar loggarna för en specifik distribution till funktionsappen i Azure. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [Arbeta med Azure Functions Core Tools](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klassbibliotek finns [i Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använder attribut för att deklarera olika typer av bindningar som stöds av Azure Functions.

[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md