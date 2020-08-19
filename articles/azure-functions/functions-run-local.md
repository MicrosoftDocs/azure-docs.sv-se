---
title: Arbeta med Azure Functions Core Tools
description: Lär dig hur du kodar och testar Azure Functions från kommando tolken eller terminalen på den lokala datorn innan du kör dem på Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: bbdc05d2b5a770791bb81f26a71b9dc3eb7523d5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505724"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeta med Azure Functions Core Tools

Med Azure Functions Core Tools kan du utveckla och testa funktioner på din lokala dator från kommandotolken eller terminalen. Dina lokala funktioner kan ansluta till Live Azure-tjänster, och du kan felsöka funktionerna på din lokala dator med hjälp av körnings funktionen för fullständiga funktioner. Du kan även distribuera en Function-app till din Azure-prenumeration.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Genom att utveckla funktioner på den lokala datorn och publicera dem på Azure med hjälp av kärn verktygen följer du dessa grundläggande steg:

> [!div class="checklist"]
> * [Installera kärn verktygen och beroenden.](#v2)
> * [Skapa ett Function-appaket från en språkspecifik mall.](#create-a-local-functions-project)
> * [Registrera utlösare och bindnings tillägg.](#register-extensions)
> * [Definiera lagring och andra anslutningar.](#local-settings-file)
> * [Skapa en funktion från en utlösare och en språkspecifik mall.](#create-func)
> * [Kör funktionen lokalt.](#start)
> * [Publicera projektet till Azure.](#publish)

## <a name="core-tools-versions"></a>Core Tools-versioner

Det finns tre versioner av Azure Functions Core Tools. Vilken version du använder beror på din lokala utvecklings miljö, [Val av språk](supported-languages.md)och support nivå som krävs:

+ [**Version 3. x/2. x**](#v2): stöder antingen [version 3. x eller 2. x av Azure Functions runtime](functions-versions.md). Dessa versioner stöder [Windows](?tabs=windows#v2), [MacOS](?tabs=macos#v2)och [Linux](?tabs=linux#v2) och använder plattformsspecifika paket hanterare eller NPM för installation.

+ **Version 1. x**: stöder version 1. x av Azure Functions Runtime. Den här versionen av verktygen stöds endast på Windows-datorer och installeras från ett [NPM-paket](https://www.npmjs.com/package/azure-functions-core-tools).

Om inget annat anges är exemplen i den här artikeln för version 3. x.

## <a name="prerequisites"></a>Krav

Azure Functions Core Tools är för närvarande beroende av Azure CLI för autentisering med ditt Azure-konto. Det innebär att du måste [Installera Azure CLI lokalt](/cli/azure/install-azure-cli) för att kunna [Publicera till Azure](#publish) från Azure Functions Core tools. 

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions Core tools] innehåller en version av samma körnings miljö som har behörighet Azure Functions runtime som du kan köra på din lokala utvecklings dator. Den innehåller också kommandon för att skapa funktioner, ansluta till Azure och distribuera funktions projekt.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Version 3. x och 2. x

Version 3. x/2. x av verktygen använder den Azure Functions runtime som bygger på .NET Core. Den här versionen stöds på alla plattformar som .NET Core stöder, inklusive [Windows](?tabs=windows#v2), [MacOS](?tabs=macos#v2)och [Linux](?tabs=linux#v2). 

> [!IMPORTANT]
> Du kan kringgå kravet för att installera .NET Core SDK med hjälp av [tilläggs paket].

# <a name="windows"></a>[Windows](#tab/windows)

Följande steg använder en Windows Installer (MSI) för att installera Core tools v3. x. Mer information om andra paketbaserade installations program, som krävs för att installera Core tools v2. x, finns i viktigt om [viktiga verktyg](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Hämta och kör installations programmet för huvud verktyg, baserat på din version av Windows:

    - [v3. x-Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (rekommenderas. [Visual Studio Code-felsökning](functions-develop-vs-code.md#debugging-functions-locally) kräver 64-bitars.)
    - [v3. x-Windows 32-bitars](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Om du inte planerar att använda [tilläggs paket](functions-bindings-register.md#extension-bundles)installerar du [.net Core 3. x SDK för Windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

I följande steg används homebrew för att installera kärn verktygen på macOS.

1. Installera [homebrew](https://brew.sh/)om den inte redan är installerad.

1. Installera paketet core tools:

    ##### <a name="v3x-recommended"></a>v3. x (rekommenderas)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Om du inte planerar att använda [tilläggs paket](functions-bindings-register.md#extension-bundles)installerar du [.net Core 3. x SDK för MacOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

I följande steg används [apt](https://wiki.debian.org/Apt) för att installera kärn verktyg på din Ubuntu/Debian Linux-distribution. Andra Linux-distributioner finns i [README-verktyg](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installera GPG-nyckeln för Microsoft Package-lagringsplatsen för att verifiera paket integritet:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Konfigurera APT-källistan innan du gör en APT-uppdatering.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Kontrol lera `/etc/apt/sources.list.d/dotnetdev.list` filen för någon av de lämpligaste Linux-versions strängarna som anges nedan:

    | Linux-distribution | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20,04    | `focal`     |
    | Ubuntu 19,04    | `disco`     |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mintgrön 18    | `xenial`  |

1. Starta APT-käll uppdateringen:

    ```bash
    sudo apt-get update
    ```

1. Installera paketet core tools:

    ##### <a name="v3x-recommended"></a>v3. x (rekommenderas)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2. x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Om du inte planerar att använda [tilläggs paket](functions-bindings-register.md#extension-bundles)installerar du [.net Core 3. x SDK för Linux](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Projekt katalogen Functions innehåller de filer som [host.jspå](functions-host-json.md) och [local.settings.jspå](#local-settings-file), tillsammans med undermappar som innehåller koden för enskilda funktioner. Den här katalogen är motsvarigheten till en Function-app i Azure. Mer information om mappstrukturen för functions finns i [guiden för Azure Functions utvecklare](functions-reference.md#folder-structure).

Version 3. x/2. x kräver att du väljer ett standard språk för projektet när det initieras. I version 3. x/2. x använder alla funktioner standard språk mal linor. I version 1. x anger du språket varje gången du skapar en funktion.

I terminalfönstret eller från en kommando tolk kör du följande kommando för att skapa projektet och den lokala git-lagringsplatsen:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java använder en maven-archetype för att skapa de lokala Functions-projektet, tillsammans med din första HTTP-utlöst funktion. Använd följande kommando för att skapa ett Java-projekt: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . Ett exempel på hur du använder maven-archetype finns i snabb starten av [kommando raden](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).  

När du anger ett projekt namn skapas och initieras en ny mapp med det namnet. Annars initieras den aktuella mappen.  
När du kör kommandot måste du välja en körning för projektet i version 3. x/2. x. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Använd piltangenterna för att välja ett språk och tryck sedan på RETUR. Om du planerar att utveckla Java Script-eller TypeScript-funktioner väljer du **Node**och väljer sedan språket. TypeScript har [vissa ytterligare krav](functions-reference-node.md#typescript). 

Utdata ser ut som i följande exempel för ett JavaScript-projekt:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` stöder följande alternativ, som är version 3. x/2. x-Only, om inget annat anges:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Skapar .NET-funktioner som C#-skript, vilket är beteendet för version 1. x. Endast giltig med `--worker-runtime dotnet` . |
| **`--docker`** | Skapar en Dockerfile för en behållare med hjälp av en bas avbildning som baseras på vald `--worker-runtime` . Använd det här alternativet när du planerar att publicera till en anpassad Linux-behållare. |
| **`--docker-only`** |  Lägger till en Dockerfile i ett befintligt projekt. Prompter för Worker-körning om inget anges eller anges i local.settings.jspå. Använd det här alternativet när du planerar att publicera ett befintligt projekt i en anpassad Linux-behållare. |
| **`--force`** | Initiera projektet även när det finns befintliga filer i projektet. Den här inställningen skriver över befintliga filer med samma namn. Andra filer i projektmappen påverkas inte. |
| **`--language`** | Initierar ett språkspecifikt projekt. Stöds för närvarande när det är `--worker-runtime` inställt på `node` . Alternativen är `typescript` och `javascript` . Du kan också använda `--worker-runtime javascript` eller `--worker-runtime typescript` .  |
| **`--managed-dependencies`**  | Installerar hanterade beroenden. För närvarande stöder bara PowerShell Worker runtime den här funktionen. |
| **`--source-control`** | Anger om en git-lagringsplats skapas. Som standard skapas inte en lagrings plats. När `true` skapas en lagrings plats. |
| **`--worker-runtime`** | Anger språk körning för projektet. De värden som stöds är: `csharp` , `dotnet` , `javascript` , `node` (Java Script), `powershell` , `python` , och `typescript` . För Java använder du [maven](functions-reference-java.md#create-java-functions). När du inte har angett uppmanas du att välja din körning under initieringen. |
|
> [!IMPORTANT]
> Som standard skapar version 2. x och senare versioner av kärn verktygen Function app-projekt för .NET-körningen som [C#-klass projekt](functions-dotnet-class-library.md) (. CSPROJ). Dessa C#-projekt, som kan användas med Visual Studio eller Visual Studio Code, kompileras under testning och vid publicering till Azure. Om du i stället vill skapa och arbeta med samma C#-skript (. CSX) som skapats i version 1. x och i portalen måste du inkludera `--csx` parametern när du skapar och distribuerar funktioner.

## <a name="register-extensions"></a>Registrera tillägg

Med undantag för HTTP-och timer-utlösare, är funktions bindningar i körnings version 2. x och högre implementerade som tilläggs paket. HTTP-bindningar och timer-utlösare kräver inte tillägg. 

För att minska inkompatibilitet mellan de olika paketen med tillägg kan du referera till ett tilläggs paket i host.jsi projekt filen. Om du väljer att inte använda paket för tillägg måste du också installera .NET Core 2. x SDK lokalt och underhålla ett tillägg. CSPROJ med ditt Functions-projekt.  

I version 2. x och senare av Azure Functions runtime måste du explicit registrera tilläggen för de bindnings typer som används i funktionerna. Du kan välja att installera bindnings tillägg individuellt, eller så kan du lägga till en tilläggs paket referens till host.jsi projekt filen. Paket för tillägg tar bort risken för problem med att paketera kompatibilitetsproblem när du använder flera bindnings typer. Det är den rekommenderade metoden för att registrera bindnings tillägg. Tilläggs paketen tar också bort kravet på att installera .NET Core 2. x SDK. 

### <a name="use-extension-bundles"></a>Använda tilläggs paket

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Läs mer i [registrera Azure Functions bindnings tillägg](functions-bindings-register.md#extension-bundles). Du bör lägga till paket för tillägg i host.jsinnan du lägger till bindningar i function.jsfilen.

### <a name="explicitly-install-extensions"></a>Installera tillägg uttryckligen

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras de här inställningarna inte automatiskt när projektet publiceras till Azure. Använd `--publish-local-settings` växeln [när du publicerar](#publish) för att se till att dessa inställningar läggs till i Function-appen i Azure. Observera att värden i **ConnectionString** aldrig publiceras.

Värdena för funktionen app-inställningar kan också läsas i koden som miljövariabler. Mer information finns i avsnittet miljövariabler i de här språkspecifika referens avsnitten:

* [C# förkompilerad](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

När ingen giltig lagrings anslutnings sträng har angetts för [`AzureWebJobsStorage`] och emulatorn inte används visas följande fel meddelande:

> Värde saknas för AzureWebJobsStorage i local.settings.js. Detta krävs för alla andra utlösare än HTTP. Du kan köra "FUNC Azure functionapp Fetch-App-Settings \<functionAppName\> " eller ange en anslutnings sträng i local.settings.jspå.

### <a name="get-your-storage-connection-strings"></a>Hämta anslutnings strängar för lagring

Även när du använder Microsoft Azure Storage-emulator för utveckling kanske du vill testa med en faktisk lagrings anslutning. Förutsatt att du redan har [skapat ett lagrings konto](../storage/common/storage-account-create.md)kan du hämta en giltig lagrings anslutnings sträng på något av följande sätt:

- Sök efter och välj **lagrings konton**från [Azure Portal]. 
  ![Välj lagrings konton från Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Välj ditt lagrings konto, Välj **åtkomst nycklar** i **Inställningar**och kopiera sedan ett av värdena för **anslutnings strängen** .
  ![Kopiera anslutnings strängen från Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Använd [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till ditt Azure-konto. I **Utforskaren**expanderar du din prenumeration, expanderar **lagrings konton**, väljer ditt lagrings konto och kopierar den primära eller sekundära anslutnings strängen.

  ![Kopiera anslutnings strängen från Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Använd kärn verktyg för att hämta anslutnings strängen från Azure med något av följande kommandon:

  + Hämta alla inställningar från en befintlig Function-app:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Hämta anslutnings strängen för ett angivet lagrings konto:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    När du inte redan har loggat in på Azure uppmanas du att göra det.

## <a name="create-a-function"></a><a name="create-func"></a>Skapa en funktion

Skapa en funktion genom att köra följande kommando:

```
func new
```

När du kör i version 3. x/2. x `func new` uppmanas du att välja en mall på standard språket för din Function-app. därefter uppmanas du också att välja ett namn för din funktion. I version 1. x uppmanas du också att välja språk.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

Funktions koden skapas i en undermapp med det tillhandahållna funktions namnet, som du kan se i följande utlösare för kön:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Du kan också ange alternativen i kommandot med följande argument:

| Argument     | Beskrivning                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2. x och senare versioner.) Genererar samma C#-skript (. CSX) mallar som används i version 1. x och i portalen. |
| **`--language`**, **`-l`**| Programmeringsspråket för mallar, till exempel C#, F # eller Java Script. Det här alternativet krävs i version 1. x. I version 2. x och senare versioner ska du inte använda det här alternativet eller välja ett språk som matchar Worker-körningen. |
| **`--name`**, **`-n`** | Funktions namnet. |
| **`--template`**, **`-t`** | Använd `func templates list` kommandot för att se en fullständig lista över tillgängliga mallar för varje språk som stöds.   |


Om du till exempel vill skapa en JavaScript-HTTP-utlösare i ett enda kommando kör du:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

För att skapa en kö-utlöst funktion i ett enda kommando, kör:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Köra funktioner lokalt

Kör Functions-värden om du vill köra ett Functions-projekt. Värden aktiverar utlösare för alla funktioner i projektet. Start kommandot varierar beroende på ditt projekt språk.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Det här kommandot måste [köras i en virtuell miljö](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Version 1. x av Functions-körningen kräver `host` kommandot, som i följande exempel:
>
> ```
> func host start
> ```

`func start` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Skapa inte aktuellt projekt innan det körs. Endast för dotNET-projekt. Standardvärdet är inställt på falskt. Stöds inte för version 1. x. |
| **`--cors-credentials`** | Tillåt kors ursprung autentiserade begär Anden (dvs. cookies och Authentication-huvudet) stöds inte för version 1. x. |
| **`--cors`** | En kommaavgränsad lista med CORS-ursprung, utan blank steg. |
| **`--language-worker`** | Argument för att konfigurera språk arbets tagaren. Du kan till exempel aktivera fel sökning för språk arbetare genom att tillhandahålla [fel söknings port och andra obligatoriska argument](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Stöds inte för version 1. x. |
| **`--cert`** | Sökvägen till en. pfx-fil som innehåller en privat nyckel. Används endast med `--useHttps` . Stöds inte för version 1. x. |
| **`--password`** | Antingen lösen ordet eller en fil som innehåller lösen ordet för en PFX-fil. Används endast med `--cert` . Stöds inte för version 1. x. |
| **`--port`**, **`-p`** | Den lokala porten att lyssna på. Standardvärde: 7071. |
| **`--pause-on-error`** | Pausa för ytterligare indatatyper innan du avslutar processen. Används endast när du startar kärn verktyg från en Integrated Development Environment (IDE).|
| **`--script-root`**, **`--prefix`** | Används för att ange sökvägen till roten för Function-appen som ska köras eller distribueras. Detta används för kompilerade projekt som genererar projektfiler i en undermapp. När du till exempel skapar ett C#-klass biblioteks projekt skapas host.jspå, local.settings.jspå och function.jspå filer i en *rotmapp* med en sökväg som `MyProject/bin/Debug/netstandard2.0` . I det här fallet ställer du in prefixet som `--script-root MyProject/bin/Debug/netstandard2.0` . Detta är roten i Function-appen när du kör i Azure. |
| **`--timeout`**, **`-t`** | Tids gränsen för funktionens värd att starta, i sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Bind till i `https://localhost:{port}` stället för till `http://localhost:{port}` . Som standard skapar det här alternativet ett betrott certifikat på din dator.|

När Functions-värden startar matar den in URL: en för HTTP-utlösta funktioner:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>När du kör lokalt tillämpas inte auktorisering för HTTP-slutpunkter. Det innebär att alla lokala HTTP-begäranden hanteras som `authLevel = "anonymous"` . Mer information finns i artikeln om [http-bindning](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Skicka test data till en funktion

Om du vill testa dina funktioner lokalt [startar du Functions-värden](#start) och anropar slut punkter på den lokala servern med HTTP-begäranden. Slut punkten som du anropar beror på typen av funktion.

>[!NOTE]
> Exemplen i det här avsnittet använder verktyget för att skicka HTTP-förfrågningar från terminalen eller en kommando tolk. Du kan använda ett verktyg som du väljer för att skicka HTTP-begäranden till den lokala servern. Verktyget vänd är tillgängligt som standard på Linux-baserade system och Windows 10 version 17063 och senare. I äldre fönster måste du först hämta och installera [verktyget sväng](https://curl.haxx.se/).

Mer allmän information om testning av funktioner finns [i strategier för att testa koden i Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funktioner som utlöses av HTTP och webhook

Du anropar följande slut punkt för att köra HTTP-och webhook-utlösta funktioner lokalt:

```
http://localhost:{port}/api/{function_name}
```

Se till att använda samma server namn och port som värden lyssnar på. Du ser detta i de utdata som genereras när du startar funktions värden. Du kan anropa den här URL: en med hjälp av en HTTP-metod som stöds av utlösaren.

Följande spiral kommando utlöser `MyHttpTrigger` snabb starts funktionen från en get-begäran med den _namn_ parameter som angavs i frågesträngen.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Följande exempel är samma funktion som anropas från ett POST-begärans överförings _namn_ i begär ande texten:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Du kan göra GET-begäranden från en webbläsare som skickar data i frågesträngen. För alla andra HTTP-metoder måste du använda sväng, Fiddler, Postman eller ett liknande verktyg för HTTP-testning.

#### <a name="non-http-triggered-functions"></a>Funktioner som inte är HTTP-utlösta

För alla typer av funktioner än HTTP-utlösare och Webhooks och Event Grid utlösare kan du testa dina funktioner lokalt genom att anropa en administrations slut punkt. Anropet till den här slut punkten med en HTTP POST-begäran på den lokala servern utlöser funktionen. 

Om du vill testa Event Grid utlösta funktioner lokalt, se [lokal testning med visnings program för webb program](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Du kan också skicka test data till körningen i bröd texten i POST-begäran. Den här funktionen liknar fliken **test** i Azure Portal.

Du anropar följande administratörs slut punkt för att utlösa icke-HTTP-funktioner:

```
http://localhost:{port}/admin/functions/{function_name}
```

Om du vill skicka test data till administratörs slut punkten för en funktion måste du ange data i bröd texten i ett meddelande om POST-begäran. Meddelande texten måste ha följande JSON-format:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>`Värdet innehåller data i ett format som förväntas av funktionen. Följande spiral exempel är ett inlägg i en `QueueTriggerJS` funktion. I det här fallet är indatamängden en sträng som motsvarar det meddelande som förväntas finnas i kön.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Använda `func run` kommandot (endast version 1. x)

>[!IMPORTANT]
> `func run`Kommandot stöds endast i version 1. x av verktygen. Mer information finns i avsnittet [How to target Azure Functions runtime-versioner](set-runtime-version.md).

I version 1. x kan du också anropa en funktion direkt genom att använda `func run <FunctionName>` och ange indata för funktionen. Det här kommandot liknar att köra en funktion med hjälp av fliken **test** i Azure Portal.

`func run` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Infogat innehåll. |
| **`--debug`**, **`-d`** | Koppla en fel sökare till värd processen innan du kör funktionen.|
| **`--timeout`**, **`-t`** | Vänte tiden (i sekunder) tills den lokala funktionens värd är klar.|
| **`--file`**, **`-f`** | Fil namnet som ska användas som innehåll.|
| **`--no-interactive`** | Kräver inte någon indatamängd. Användbart för automatiserings scenarier.|

Om du till exempel vill anropa en HTTP-utlöst funktion och skicka innehålls texten kör du följande kommando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publicera till Azure

Azure Functions Core Tools stöder två typer av distribution: Distribuera Function Project-filer direkt till din Function-app via [zip Deploy](functions-deployment-technologies.md#zip-deploy) och [distribuera en anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Du måste redan ha [skapat en Function-app i din Azure-prenumeration](functions-cli-samples.md#create), som du ska distribuera din kod till. Projekt som kräver kompilering bör skapas så att binärfilerna kan distribueras.

>[!IMPORTANT]
>Du måste ha installerat [Azure CLI](/cli/azure/install-azure-cli) lokalt för att kunna publicera till Azure från kärn verktyg.  

En projektmapp kan innehålla språkspecifika filer och kataloger som inte ska publiceras. Undantagna objekt visas i en. funcignore-fil i rotmappen.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Distribuera projektfiler

Om du vill publicera din lokala kod i en Function-app i Azure använder du `publish` kommandot:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java använder Maven för att publicera ditt lokala projekt till Azure. Använd följande kommando för att publicera till Azure: `mvn azure-functions:deploy` . Azure-resurser skapas vid den första distributionen.

Det här kommandot publicerar till en befintlig Function-app i Azure. Du får ett fel meddelande om du försöker publicera till en `<FunctionAppName>` som inte finns i din prenumeration. Information om hur du skapar en Function-app från kommando tolken eller terminalfönstret med hjälp av Azure CLI finns i [skapa en Funktionsapp för Server lös körning](./scripts/functions-cli-create-serverless.md). Som standard använder detta kommando [fjärrversion](functions-deployment-technologies.md#remote-build) och distribuerar din app för [körning från distributions paketet](run-functions-from-deployment-package.md). Om du vill inaktivera det rekommenderade distributions läget använder du `--nozip` alternativet.

>[!IMPORTANT]
> När du skapar en Function-app i Azure Portal, använder den version 3. x av funktionens körning som standard. Om du vill att Function-appen ska använda version 1. x av körnings miljön följer du anvisningarna i [köra på version 1. x](functions-versions.md#creating-1x-apps).
> Du kan inte ändra körnings versionen för en Function-app som har befintliga funktioner.

Följande publicerings alternativ gäller för alla versioner:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publicera inställningar i local.settings.jspå till Azure och ange överskrivning om inställningen redan finns. Om du använder Microsoft Azure Storage-emulator måste du först ändra appens inställning till en [faktisk lagrings anslutning](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Utelämna uppmaningen att skriva över app-inställningar när `--publish-local-settings -i` används.|

Följande publicerings alternativ stöds bara för version 2. x och senare versioner:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Publicera bara inställningar och hoppa över innehållet. Standardvärdet är prompt. |
|**`--list-ignored-files`** | Visar en lista över filer som ignoreras under publicering, som baseras på. funcignore-filen. |
| **`--list-included-files`** | Visar en lista över publicerade filer, som baseras på. funcignore-filen. |
| **`--nozip`** | Stänger av standard `Run-From-Package` läget. |
| **`--build-native-deps`** | Hoppar över genereringen av. Wheels-mappen när du publicerar python Function-appar. |
| **`--build`**, **`-b`** | Utför Bygg åtgärd när du distribuerar till en Linux Function-app. Accepterar: `remote` och `local` . |
| **`--additional-packages`** | Lista över paket som ska installeras när du skapar interna beroenden. Till exempel: `python3-dev libevent-dev`. |
| **`--force`** | Ignorera för publicerings verifiering i vissa scenarier. |
| **`--csx`** | Publicera ett C#-skript (. CSX)-projekt. |
| **`--no-build`** | Projektet har inte skapats under publiceringen. För python `pip install` utförs inte. |
| **`--dotnet-cli-params`** | När du publicerar kompilerade C#-funktioner (. CSPROJ) anropar kärn verktygen "dotNet build--output bin/Publish". Alla parametrar som skickas till detta läggs till i kommando raden. |

### <a name="deploy-custom-container"></a>Distribuera anpassad behållare

Med Azure Functions kan du distribuera ett funktions projekt i en [anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Mer information finns i [skapa en funktion i Linux med en anpassad avbildning](functions-create-function-linux-custom-image.md). Anpassade behållare måste ha en Dockerfile. Om du vill skapa en app med en Dockerfile använder du alternativet--Dockerfile på `func init` .

```
func deploy
```

Följande alternativ för distribution av anpassade behållare är tillgängliga:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Namnet på ett Docker-register som den aktuella användaren har loggat in på. |
| **`--platform`** | Värd plattform för Function-appen. Giltiga alternativ är `kubernetes` |
| **`--name`** | Funktionens namn på appen. |
| **`--max`**  | Om du vill kan du ange det maximala antalet funktions-App-instanser som ska distribueras till. |
| **`--min`**  | Om du vill kan du ange det minsta antalet funktions-App-instanser som ska distribueras till. |
| **`--config`** | Anger en valfri distributions konfigurations fil. |

## <a name="monitoring-functions"></a>Övervaknings funktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera med Azure Application insikter. Du kan också strömma körnings loggar till den lokala datorn. Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights-integrering

Application Insights integration ska vara aktive rad när du skapar din Function-app i Azure. Om din funktions app inte är ansluten till en Application Insights instans, är det enkelt att integrera i Azure Portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Aktivera strömnings loggar

Du kan visa en ström med loggfiler som genereras av dina funktioner i en kommando rad session på den lokala datorn. 

#### <a name="native-streaming-logs"></a>Interna strömmande loggar

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Den här typen av strömnings loggar kräver att Application Insights integration aktive ras för din Function-app.   


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar, testar och publicerar Azure Functions med Azure Functions Core Tools [Microsoft lära](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools är [öppen källkod och finns på GitHub](https://github.com/azure/azure-functions-cli).  
Om du vill skicka en fel-eller funktions förfrågan [öppnar du ett GitHub-problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portalen]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[paket för tillägg]: functions-bindings-register.md#extension-bundles
