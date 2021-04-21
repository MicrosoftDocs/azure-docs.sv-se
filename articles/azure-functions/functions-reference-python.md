---
title: Python-utvecklarreferens för Azure Functions
description: Förstå hur du utvecklar funktioner med Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0c87be334847974627299f8e21109fe201675f0c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762181"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions utvecklarhandbok för Python

Den här artikeln är en introduktion till att utveckla Azure Functions med Python. Innehållet nedan förutsätter att du redan har läst guiden för [Azure Functions utvecklare.](functions-reference.md)

Som Python-utvecklare kan du också vara intresserad av någon av följande artiklar:

| Komma igång | Begrepp| Scenarier/exempel |
| -- | -- | -- | 
| <ul><li>[Python-funktion med Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-funktion med terminal/kommandotolk](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Utvecklarguide](functions-reference.md)</li><li>[Värdalternativ](functions-scale.md)</li><li>[&nbsp;Prestandaöverväganden](functions-best-practices.md)</li></ul> | <ul><li>[Bildklassificering med PyTorch](machine-learning-pytorch.md)</li><li>[Azure Automation-exempel](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Maskininlärning med TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Bläddra bland Python-exempel](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Du kan utveckla [din Python-baserade Azure Functions lokalt](create-first-function-vs-code-python.md#run-the-function-locally)i Windows, men Python stöds endast på en Linux-baserad värdplan när du kör i Azure. Se listan över kombinationer [av operativsystem/körning](functions-scale.md#operating-systemruntime) som stöds.

## <a name="programming-model"></a>Programmeringsmodell

Azure Functions förväntar sig att en funktion är en tillståndslös metod i Python-skriptet som bearbetar indata och genererar utdata. Som standard förväntar sig körningen att metoden implementeras som en global metod som `main()` kallas i `__init__.py` filen . Du kan också [ange en alternativ startpunkt.](#alternate-entry-point)

Data från utlösare och bindningar binds till funktionen via metodattribut med hjälp av egenskapen som definieras `name` *ifunction.jspå* filen. Till exempel beskriver  _function.jsnedan en_ enkel funktion som utlöses av en HTTP-begäran med namnet `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Baserat på den här definitionen `__init__.py` kan filen som innehåller funktionskoden se ut som i följande exempel:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Du kan också uttryckligen deklarera attributtyperna och returtypen i funktionen med hjälp av Anteckningar av Python-typ. Detta hjälper dig att använda funktionerna IntelliSense och automatisk komplettering som tillhandahålls av många Python-kodredigerare.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Använd Python-anteckningarna som ingår i [paketet azure.functions.*](/python/api/azure-functions/azure.functions) för att binda indata och utdata till dina metoder.

## <a name="alternate-entry-point"></a>Alternativ startpunkt

Du kan ändra standardbeteendet för en funktion genom att välja att ange egenskaperna `scriptFile` och ifunction.js`entryPoint` *på* filen. Till exempel _uppmanarfunction.js_ nedan körningen att använda metoden i `customentry()` _main.py-filen_ som startpunkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappstrukturen

Den rekommenderade mappstrukturen för ett Python Functions-projekt ser ut som i följande exempel:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
Huvudprojektmappen (<project_root>) kan innehålla följande filer:

* *local.settings.jspå*: Används för att lagra appinställningar och anslutningssträngar när du kör lokalt. Den här filen publiceras inte till Azure. Mer information finns i [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt:* Innehåller listan över Python-paket som systemet installerar vid publicering till Azure.
* *host.jspå*: Innehåller globala konfigurationsalternativ som påverkar alla funktioner i en funktionsapp. Den här filen publiceras till Azure. Alla alternativ stöds inte när du kör lokalt. Mer information finns i [host.jspå](functions-host-json.md).
* *.vscode/*: (Valfritt) Innehåller VSCode-lagringskonfiguration. Mer information finns i [VSCode-inställningen](https://code.visualstudio.com/docs/getstarted/settings).
* *.venv/*: (Valfritt) Innehåller en virtuell Python-miljö som används av lokal utveckling.
* *Dockerfile:*(Valfritt) Används när du publicerar projektet i en [anpassad container](functions-create-function-linux-custom-image.md).
* *tests/*: (Valfritt) Innehåller testfallen för din funktionsapp.
* *.funcignore:*(Valfritt) Deklarerar filer som inte ska publiceras till Azure. Den här filen innehåller vanligtvis för att ignorera redigeringsinställningen, ignorera lokal virtuell Python-miljö, ignorera testfall och förhindra att `.vscode/` `.venv/` lokala `tests/` `local.settings.json` appinställningar publiceras.

Varje funktion har en egen kodfil och en bindningskonfigurationsfil (function.jspå).

När du distribuerar projektet till en funktionsapp i Azure bör hela innehållet i huvudprojektet (*<project_root>*) inkluderas i paketet, men inte själva mappen, vilket innebär att det ska finnas i `host.json` paketroten. Vi rekommenderar att du underhåller dina tester i en mapp tillsammans med andra funktioner, i det här exemplet `tests/` . Mer information finns i [Enhetstestning.](#unit-testing)

## <a name="import-behavior"></a>Importbeteende

Du kan importera moduler i funktionskoden med både absoluta och relativa referenser. Baserat på mappstrukturen som visas ovan fungerar följande importer inifrån funktionsfilen *<project_root>\my \_ first function _ \_ \\ \_ init \_ \_ .py*:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  Mappen *shared_code/måste* innehålla en init.py-fil för att markera den som \_ \_ ett \_ \_ Python-paket när du använder absolut importsyntax.

Följande appimport och en relativ import på den översta nivån är inaktuell eftersom den inte stöds av statisk typkontroll och inte stöds av \_ \_ \_ \_ Python-testramverk:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: utlösarindata och ytterligare indata. Även om de skiljer sig åt `function.json` i filen är användningen identisk i Python-koden.  Anslutningssträngar eller hemligheter för utlösare och indatakällor mappar till värden i filen när de körs lokalt `local.settings.json` och programinställningarna när de körs i Azure.

Följande kod visar till exempel skillnaden mellan de två:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

När funktionen anropas skickas HTTP-begäran till funktionen som `req` . En post hämtas från den Azure Blob Storage baserat på _ID:t_ i väg-URL:en och görs tillgänglig som `obj` i funktionstexten.  Här är det angivna lagringskontot anslutningssträngen i appinställningen AzureWebJobsStorage, som är samma lagringskonto som används av funktionsappen.


## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i returvärden och utdataparametrar. Om det bara finns en utdata, rekommenderar vi att du använder returvärdet. För flera utdata måste du använda utdataparametrar.

Om du vill använda returvärdet för en funktion som värdet för en utdatabindning ska `name` bindningens egenskap anges till `$return` i `function.json` .

Om du vill skapa flera utdata använder `set()` du metoden som tillhandahålls av gränssnittet för att tilldela ett värde till [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) bindningen. Följande funktion kan till exempel skicka ett meddelande till en kö och även returnera ett HTTP-svar.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Loggning

Åtkomst till Azure Functions runtime-loggaren är tillgänglig via en [`logging`](https://docs.python.org/3/library/logging.html#module-logging) rothanterare i funktionsappen. Den här loggaren är kopplad Application Insights och gör att du kan flagga varningar och fel som påträffas under funktionskörningen.

I följande exempel loggas ett informationsmeddelande när funktionen anropas via en HTTP-utlösare.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Det finns ytterligare loggningsmetoder som gör att du kan skriva till konsolen på olika spårningsnivåer:

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Skriver ett meddelande med nivån KRITISK i rotloggaren.  |
| **`error(_message_)`**   | Skriver ett meddelande med nivåFEL i rotloggaren.    |
| **`warning(_message_)`**    | Skriver ett meddelande med nivåVARNING på rotloggaren.  |
| **`info(_message_)`**    | Skriver ett meddelande med nivå INFO på rotloggaren.  |
| **`debug(_message_)`** | Skriver ett meddelande med nivåFELSÖKNING på rotloggaren.  |

Mer information om loggning finns i [Övervaka Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-utlösaren definieras i function.jspå filen. För `name` bindningen måste matcha den namngivna parametern i funktionen .
I föregående exempel används ett `req` bindningsnamn. Den här parametern är [ett HttpRequest-objekt] och [ett HttpResponse-objekt] returneras.

Från [HttpRequest-objektet] kan du hämta begärandehuvuden, frågeparametrar, vägparametrar och meddelandetexten.

Följande exempel är från [HTTP-utlösarmallen för Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

I den här funktionen hämtas värdet för `name` frågeparametern från `params` parametern för [HttpRequest-objektet.] Den JSON-kodade meddelandetexten läses med hjälp av `get_json` metoden .

På samma sätt kan du ange `status_code` och `headers` för svarsmeddelandet i det returnerade [HttpResponse-objektet.]

## <a name="scaling-and-performance"></a>Skalning och prestanda

Metodtips för skalning och prestanda för Python-funktionsappar finns i artikeln [Om Python-skalning och prestanda.](python-scale-performance-reference.md)

## <a name="context"></a>Kontext

Om du vill hämta anropskontexten för en funktion under körningen inkluderar du [`context`](/python/api/azure-functions/azure.functions.context) argumentet i dess signatur.

Exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klassen [**Context**](/python/api/azure-functions/azure.functions.context) har följande strängattribut:

`function_directory` Den katalog där funktionen körs.

`function_name` Namnet på funktionen.

`invocation_id` ID för den aktuella funktionsanropet.

## <a name="global-variables"></a>Globala variabler

Det är inte säkert att appens tillstånd bevaras för framtida körningar. Körningen Azure Functions återanvänder dock ofta samma process för flera körningar av samma app. Om du vill cachelagra resultatet av en dyr beräkning deklarerar du den som en global variabel.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Miljövariabler

I Functions [exponeras programinställningar,](functions-app-settings.md)till exempel tjänstanslutningssträngar, som miljövariabler under körningen. Du kan komma åt de här inställningarna genom `import os` att deklarera och sedan använda `setting = os.environ["setting-name"]` .

I följande exempel hämtar [programinställningen](functions-how-to-use-azure-function-app-settings.md#settings), med nyckeln med namnet `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

För lokal utveckling underhålls programinställningarna [i local.settings.jspå filen](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-version

Azure Functions stöder följande Python-versioner:

| Functions-version | <sup>*</sup>Python-versioner |
| ----- | ----- |
| 3.x | 3.9 (förhandsversion) <br/> 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Officiella CPython-distributioner

Om du vill begära en specifik Python-version när du skapar funktionsappen i Azure `--runtime-version` använder du alternativet för kommandot [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) . Körningsversionen för Functions anges med `--functions-version` alternativet . Python-versionen anges när funktionsappen skapas och kan inte ändras.

När du kör lokalt använder körningen den tillgängliga Python-versionen.

## <a name="package-management"></a>Pakethantering

När du utvecklar lokalt med Azure Functions Core Tools eller Visual Studio Code lägger du till namn och versioner av de nödvändiga paketen i filen `requirements.txt` och installerar dem med `pip` .

Följande kravfil och pip-kommando kan till exempel användas för att installera `requests` paketet från PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicera i Azure

När du är redo att publicera kontrollerar du att alla dina offentligt tillgängliga beroenden visas i requirements.txt-filen, som finns i roten av projektkatalogen.

Projektfiler och mappar som undantas från publicering, inklusive mappen för den virtuella miljön, visas i .funcignore-filen.

Det finns tre byggåtgärder som stöds för att publicera Python-projektet till Azure: fjärrbygge, lokal version och byggen med hjälp av anpassade beroenden.

Du kan också använda Azure Pipelines för att skapa dina beroenden och publicera med hjälp av kontinuerlig leverans (CD). Mer information finns i Kontinuerlig [leverans med hjälp av Azure DevOps.](functions-how-to-azure-devops.md)

### <a name="remote-build"></a>Fjärrbygge

När du använder fjärrbygge återställs beroenden på servern och interna beroenden matchar produktionsmiljön. Detta resulterar i ett mindre distributionspaket att ladda upp. Använd fjärrbygge när du utvecklar Python-appar i Windows. Om projektet har anpassade beroenden kan du använda fjärrbygge [med extra index-URL.](#remote-build-with-extra-index-url)

Beroenden hämtas via fjärrlagring baserat på innehållet i den requirements.txt filen. [Fjärrbygge](functions-deployment-technologies.md#remote-build) är den rekommenderade byggmetoden. Som standard begär Azure Functions Core Tools en fjärrbygge när du använder följande [func azure functionapp publish-kommando](functions-run-local.md#publish) för att publicera python-projektet till Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Kom ihåg att `<APP_NAME>` ersätta med namnet på din funktionsapp i Azure.

Tillägget [Azure Functions för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) begär också en fjärrbygge som standard.

### <a name="local-build"></a>Lokal version

Beroenden hämtas lokalt baserat på innehållet i den requirements.txt filen. Du kan förhindra att göra en fjärrbygge med hjälp av följande [func azure functionapp publish-kommando](functions-run-local.md#publish) för att publicera med en lokal version.

```command
func azure functionapp publish <APP_NAME> --build local
```

Kom ihåg att `<APP_NAME>` ersätta med namnet på din funktionsapp i Azure.

Med hjälp `--build local` av alternativet läses projektberoenden från requirements.txt och de beroende paketen laddas ned och installeras lokalt. Projektfiler och beroenden distribueras från din lokala dator till Azure. Detta resulterar i att ett större distributionspaket laddas upp till Azure. Om beroenden i din requirements.txt av någon anledning inte kan förvärvas av Core Tools måste du använda alternativet för anpassade beroenden för publicering.

Vi rekommenderar inte att du använder lokala versioner när du utvecklar lokalt i Windows.

### <a name="custom-dependencies"></a>Anpassade beroenden

När projektet har beroenden som inte finns i [Python Package Index](https://pypi.org/)finns det två sätt att skapa projektet. Byggmetoden beror på hur du skapar projektet.

#### <a name="remote-build-with-extra-index-url"></a>Fjärrbygge med extra index-URL

När dina paket är tillgängliga från ett anpassat paketindex kan du använda en fjärrbygge. Se till att skapa en [appinställning med](functions-how-to-use-azure-function-app-settings.md#settings) namnet innan du `PIP_EXTRA_INDEX_URL` publicerar. Värdet för den här inställningen är URL:en för ditt anpassade paketindex. Med den här inställningen anger att fjärrbygget ska `pip install` köras med hjälp av alternativet `--extra-index-url` . Mer information finns i [installationsdokumentationen för Python pip.](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)

Du kan också använda grundläggande autentiseringsuppgifter med dina extra paketindex-URL:er. Mer information finns i Grundläggande [autentiseringsuppgifter i](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) Python-dokumentationen.

#### <a name="install-local-packages"></a>Installera lokala paket

Om ditt projekt använder paket som inte är offentligt tillgängliga för våra verktyg kan du göra dem tillgängliga för din app genom att placera dem i \_ \_ appen \_ \_ /.python_packages-katalogen. Innan du publicerar kör du följande kommando för att installera beroendena lokalt:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

När du använder anpassade beroenden bör du använda publiceringsalternativet eftersom du redan har `--no-build` installerat beroendena i projektmappen.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Kom ihåg att `<APP_NAME>` ersätta med namnet på din funktionsapp i Azure.

## <a name="unit-testing"></a>Enhetstestning

Funktioner som skrivits i Python kan testas som andra Python-kod med hjälp av standardramverk för testning. För de flesta bindningar går det att skapa ett fingerat indataobjekt genom att skapa en instans av en lämplig klass från `azure.functions` paketet. Eftersom paketet inte är omedelbart tillgängligt måste du installera det via filen enligt [`azure.functions`](https://pypi.org/project/azure-functions/) `requirements.txt` beskrivningen i avsnittet [pakethantering](#package-management) ovan.

Som *my_second_function* exempel är följande ett test av en HTTP-utlöst funktion:

Först måste vi skapa<project_root>*/my_second_function/function.jspå filen* och definiera den här funktionen som en HTTP-utlösare.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Nu kan vi implementera *my_second_function* och *shared_code.my_second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

Vi kan börja skriva testfall för http-utlösaren.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Installera `.venv` python-testramverket (t.ex. ) i din virtuella `pip install pytest` Python-miljö. Kör bara `pytest tests` för att kontrollera testresultatet.

## <a name="temporary-files"></a>Tillfälliga filer

Metoden `tempfile.gettempdir()` returnerar en tillfällig mapp, som i Linux är `/tmp` . Programmet kan använda den här katalogen för att lagra temporära filer som genereras och används av dina funktioner under körningen.

> [!IMPORTANT]
> Filer som skrivs till den tillfälliga katalogen bevaras inte under anrop. Under utskalning delas inte temporära filer mellan instanser.

I följande exempel skapas en namngiven temporär fil i den tillfälliga katalogen ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Vi rekommenderar att du underhåller dina tester i en mapp som är separat från projektmappen. Detta hindrar dig från att distribuera testkod med din app.

## <a name="preinstalled-libraries"></a>Förinstallerade bibliotek

Det finns några bibliotek som har Python Functions-körningen.

### <a name="python-standard-library"></a>Pythons standardbibliotek

Pythons standardbibliotek innehåller en lista över inbyggda Python-moduler som levereras med varje Python-distribution. De flesta av de här biblioteken hjälper dig att komma åt systemfunktioner, till exempel fil-I/O. I Windows-system installeras dessa bibliotek med Python. I Unix-baserade system tillhandahålls de av paketsamlingar.

Om du vill visa fullständig information om listan över dessa bibliotek kan du gå till länkarna nedan:

* [Python 3.6-standardbibliotek](https://docs.python.org/3.6/library/)
* [Python 3.7-standardbibliotek](https://docs.python.org/3.7/library/)
* [Python 3.8-standardbibliotek](https://docs.python.org/3.8/library/)
* [Python 3.9-standardbibliotek](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python-arbetsberoenden

Functions Python-arbetsrollen kräver en specifik uppsättning bibliotek. Du kan också använda dessa bibliotek i dina funktioner, men de är inte en del av Python-standarden. Om dina funktioner förlitar sig på något av dessa bibliotek kanske de inte är tillgängliga för din kod när de körs utanför Azure Functions. Du hittar en detaljerad lista över beroenden i avsnittet **installera \_ kräver** i [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) fil.

> [!NOTE]
> Om funktionsappens namn requirements.txt en post `azure-functions-worker` tar du bort den. Functions-arbetsrollen hanteras automatiskt av Azure Functions plattform och vi uppdaterar den regelbundet med nya funktioner och felkorrigeringar. Att manuellt installera en gammal arbetsversion i en requirements.txt kan orsaka oväntade problem.

### <a name="azure-functions-python-library"></a>Azure Functions Python-bibliotek

Varje Python-arbetsuppdatering innehåller en ny version [Azure Functions Python-biblioteket (azure.functions).](https://github.com/Azure/azure-functions-python-library) Den här metoden gör det enklare att kontinuerligt uppdatera dina Python-funktionsappar, eftersom varje uppdatering är bakåtkompatibel. En lista över versioner av det här biblioteket finns i [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

Körningsbiblioteksversionen åtgärdas av Azure och kan inte åsidosättas av requirements.txt. Posten `azure-functions` i requirements.txt endast för linting och kundmedvetenhet.

Använd följande kod för att spåra den faktiska versionen av Python Functions-biblioteket i din körning:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Runtime-systembibliotek

En lista över förinstallerade systembibliotek i Docker-avbildningar för Python-arbetare finns via länkarna nedan:

|  Functions-körning  | Debian-version | Python-versioner |
|------------|------------|------------|
| Version 2.x | Sträck  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Version 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Cross-origin resource sharing (CORS)

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS stöds fullt ut för Python-funktionsappar.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

Följande är en lista över felsökningsguider för vanliga problem:

* [ModuleNotFoundError och ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Det går inte att importera "cygrpc"](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Alla kända problem och funktionsbegäranden spåras med hjälp av [listan över GitHub-problem.](https://github.com/Azure/azure-functions-python-worker/issues) Om du får problem och inte kan hitta problemet i GitHub öppnar du ett nytt ärende och ger en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [API Azure Functions paketsdokumentation](/python/api/azure-functions/azure.functions)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Blob Storage-bindningar](functions-bindings-storage-blob.md)
* [HTTP- och Webhook-bindningar](functions-bindings-http-webhook.md)
* [Queue Storage-bindningar](functions-bindings-storage-queue.md)
* [Timerutlösare](functions-bindings-timer.md)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse