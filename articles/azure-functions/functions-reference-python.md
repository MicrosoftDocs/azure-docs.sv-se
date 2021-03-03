---
title: Python Developer-referens för Azure Functions
description: Förstå hur du utvecklar funktioner med python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 78965a1a09272663c52469b8ccd91988f30b53cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708704"
---
# <a name="azure-functions-python-developer-guide"></a>Guide för Azure Functions python-utvecklare

Den här artikeln är en introduktion till att utveckla Azure Functions med python. Innehållet nedan förutsätter att du redan har läst [guiden Azure Functions utvecklare](functions-reference.md).

Som python-utvecklare kanske du också är intresse rad av någon av följande artiklar:

| Komma igång | Begrepp| Scenarier/exempel |
| -- | -- | -- | 
| <ul><li>[Python-funktionen med Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-funktionen med Terminal/kommando-prompt](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Utvecklarguide](functions-reference.md)</li><li>[Värdalternativ](functions-scale.md)</li><li>[Prestanda &nbsp; överväganden](functions-best-practices.md)</li></ul> | <ul><li>[Bildklassificering med PyTorch](machine-learning-pytorch.md)</li><li>[Azure Automation-exempel](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Maskininlärning med TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Sök i python-exempel](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Även om du kan [utveckla din python-baserade Azure Functions lokalt i Windows](./azure-functions/create-first-function-vs-code-python#run-the-function-locally), stöds python bara på en Linux-baserad värd plan när den körs i Azure. Se listan över kombinationer av [operativ system/körning](./azure-functions/functions-scale#operating-systemruntime) som stöds.

## <a name="programming-model"></a>Programmeringsmodell

Azure Functions förväntar sig att en funktion är en tillstånds lös metod i python-skriptet som bearbetar indata och genererar utdata. Som standard förväntar sig körningen att metoden ska implementeras som en global metod som kallas `main()` i `__init__.py` filen. Du kan också [Ange en alternativ Start punkt](#alternate-entry-point).

Data från utlösare och bindningar är kopplade till funktionen via attribut med hjälp av den `name` egenskap som definierats i *function.jsi* filen. _function.js_ nedan beskriver till exempel en enkel funktion som utlöses av en http-begäran med namnet `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Baserat på den här definitionen `__init__.py` kan den fil som innehåller funktions koden se ut som i följande exempel:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Du kan även explicit deklarera attributtyper och retur typen i funktionen med hjälp av python-typ anteckningar. På så sätt kan du använda IntelliSense-och Autoavsluta-funktionerna från många python-kod redigerare.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Använd python-anteckningarna som ingår i [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true) -paketet för att binda indata och utdata till dina metoder.

## <a name="alternate-entry-point"></a>Alternativ Start punkt

Du kan ändra standard beteendet för en funktion genom att ange- `scriptFile` och- `entryPoint` egenskaperna i *function.js* i filen. _function.js_ nedan visar till exempel körningen att använda `customentry()` metoden i _main.py_ -filen som start punkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappstruktur

Den rekommenderade mappstrukturen för ett python Functions-projekt ser ut som i följande exempel:

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

* *local.settings.jspå*: används för att lagra appinställningar och anslutnings strängar när de körs lokalt. Den här filen publiceras inte i Azure. Mer information finns i [Local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements.txt*: innehåller listan över python-paket som systemet installerar vid publicering till Azure.
* *host.jspå*: innehåller globala konfigurations alternativ som påverkar alla funktioner i en Function-app. Den här filen publiceras i Azure. Alla alternativ stöds inte när du kör lokalt. Läs mer i [host.jspå](functions-host-json.md).
* *. VSCode/*: (valfritt) innehåller konfiguration av Store-VSCode. Läs mer i VSCode- [inställningen](https://code.visualstudio.com/docs/getstarted/settings).
* *. venv/*: (valfritt) innehåller en virtuell python-miljö som används av lokal utveckling.
* *Dockerfile*: (valfritt) används vid publicering av ditt projekt i en [anpassad behållare](functions-create-function-linux-custom-image.md).
* *test/*: (valfritt) innehåller test fall för din Function-app.
* *. funcignore*: (valfritt) deklarerar filer som inte ska publiceras i Azure. Den här filen innehåller vanligt vis `.vscode/` att ignorera din redigerings inställning, `.venv/` för att ignorera den lokala python-miljön, `tests/` för att ignorera test ärenden och `local.settings.json` förhindra att lokala appinställningar publiceras.

Varje funktion har sin egen kod fil och bindnings konfigurations fil (function.jspå).

När du distribuerar projektet till en Function-app i Azure ska hela innehållet i huvudprojektet (*<project_root>*) tas med i paketet, men inte själva mappen, vilket innebär `host.json` att det ska finnas i paket roten. Vi rekommenderar att du underhåller dina tester i en mapp tillsammans med andra funktioner, i det här exemplet `tests/` . Mer information finns i [enhets testning](#unit-testing).

## <a name="import-behavior"></a>Import beteende

Du kan importera moduler i funktions koden med både absoluta och relativa referenser. Utifrån mappstrukturen som visas ovan fungerar följande importer från funktions filen *<project_root> \Mina \_ First \_ Function \\ _ \_ init \_ \_ . py*:

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
>  *Shared_code/* mappen måste innehålla en \_ \_ init \_ \_ . py-fil för att markera den som ett python-paket när du använder absolut syntax för import.

Följande \_ \_ app \_ \_ -import och bortom relativ import på översta nivån är föråldrade eftersom den inte stöds av en statisk typ kontroll och inte stöds av python test ramverk:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: Utlös indata och ytterligare indata. Även om de skiljer sig i `function.json` filen är användningen identiska i python-kod.  Anslutnings strängar eller hemligheter för utlösare och inmatade källor mappar till värden i `local.settings.json` filen när de körs lokalt och program inställningarna körs i Azure.

Till exempel visar följande kod skillnaden mellan de två:

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

När funktionen anropas skickas HTTP-begäran till funktionen som `req` . En post hämtas från Azure-Blob Storage baserat på _ID: t_ i väg-URL: en och görs tillgänglig som `obj` i funktions texten.  Här är det angivna lagrings kontot den anslutnings sträng som hittades i AzureWebJobsStorage-appens inställning, som är samma lagrings konto som används av Function-appen.


## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i retur värde och utdataparametrar. Om det bara finns en utmatning rekommenderar vi att du använder det returnerade värdet. För flera utdata måste du använda utdataparametrar.

Om du vill använda returvärdet för en funktion som värde för en utgående bindning `name` ska egenskapen för bindningen anges till `$return` i `function.json` .

Om du vill skapa flera utdata använder du `set()` metoden som tillhandahålls av [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) gränssnittet för att tilldela ett värde till bindningen. Följande funktion kan till exempel skicka ett meddelande till en kö och även returnera ett HTTP-svar.

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

Åtkomst till Azure Functions runtime-loggen är tillgänglig via en rot [`logging`](https://docs.python.org/3/library/logging.html#module-logging) hanterare i din Function-app. Den här loggen är kopplad till Application Insights och gör att du kan flagga varningar och fel som påträffas under funktions körningen.

I följande exempel loggas ett informations meddelande när funktionen anropas via en HTTP-utlösare.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Det finns ytterligare loggnings metoder som gör att du kan skriva till-konsolen på olika spårnings nivåer:

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Skriver ett meddelande med nivå kritisk på rot loggaren.  |
| **`error(_message_)`**   | Skriver ett meddelande med nivå fel på rot loggaren.    |
| **`warning(_message_)`**    | Skriver ett meddelande med nivå varning på rot loggaren.  |
| **`info(_message_)`**    | Skriver ett meddelande med nivå information på rot loggaren.  |
| **`debug(_message_)`** | Skriver ett meddelande med nivå fel sökning på rot loggaren.  |

Mer information om loggning finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-utlösaren definieras i function.jsi filen. `name`För bindningen måste matcha den namngivna parametern i funktionen.
I föregående exempel används ett bindnings namn `req` . Den här parametern är ett [HttpRequest] -objekt och ett [HttpResponse] -objekt returneras.

Du kan hämta begärandehuvuden, frågeparametrar, väg parametrar och meddelande texten från [HttpRequest] -objektet.

Följande exempel är från [http trigger-mallen för python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

I den här funktionen hämtas värdet för `name` Frågeparametern från `params` parametern för [HttpRequest] -objektet. Den JSON-kodade meddelande texten läses med hjälp av `get_json` metoden.

På samma sätt kan du ange `status_code` och `headers` för svarsmeddelandet i det returnerade [HttpResponse] -objektet.

## <a name="scaling-and-performance"></a>Skalning och prestanda

För skalnings-och prestanda metod tips för python Function-appar, se [artikeln python Scale and Performance](python-scale-performance-reference.md).

## <a name="context"></a>Kontext

Om du vill hämta anrops kontexten för en funktion under körningen ska du inkludera [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) argumentet i signaturen.

Exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Kontext**](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) klassen har följande String-attribut:

`function_directory` Katalogen där funktionen körs.

`function_name` Namnet på funktionen.

`invocation_id` ID för aktuellt funktions anrop.

## <a name="global-variables"></a>Globala variabler

Det garanterar inte att appens tillstånd kommer att bevaras för framtida körningar. Azure Functions körning återanvänder dock ofta samma process för flera körningar av samma app. För att cachelagra resultatet av en dyr beräkning, deklarera det som en global variabel.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [program inställningar](functions-app-settings.md), t. ex. tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna genom att deklarera `import os` och sedan använda `setting = os.environ["setting-name"]` .

I följande exempel hämtas [program inställningen](functions-how-to-use-azure-function-app-settings.md#settings)med nyckeln med namnet `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

För lokal utveckling underhålls program inställningarna [i local.settings.jsfilen](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-version

Azure Functions stöder följande python-versioner:

| Funktions version | Python- <sup>*</sup> versioner |
| ----- | ----- |
| 3.x | 3,9 (för hands version) <br/> 3,8<br/>3.7<br/>3,6 |
| 2x | 3.7<br/>3,6 |

<sup>*</sup>Officiella CPython-distributioner

Om du vill begära en speciell python-version när du skapar din Function-app i Azure, använder du `--runtime-version` [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) kommandots alternativ. Funktions körnings versionen anges med `--functions-version` alternativet. Python-versionen ställs in när Function-appen skapas och kan inte ändras.

När den körs lokalt använder körningen den tillgängliga python-versionen.

## <a name="package-management"></a>Pakethantering

När du utvecklar lokalt med Azure Functions Core Tools eller Visual Studio Code lägger du till namn och versioner för de nödvändiga paketen i `requirements.txt` filen och installerar dem med `pip` .

Till exempel kan följande krav fil och pip-kommando användas för att installera `requests` paketet från pypi.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicera i Azure

När du är redo att publicera ser du till att alla dina offentligt tillgängliga beroenden visas i requirements.txt-filen, som finns i rot katalogen i projekt katalogen.

Projektfiler och mappar som undantas från publicering, inklusive mappen för virtuella miljöer, visas i. funcignore-filen.

Det finns tre Bygg åtgärder som stöds för att publicera ditt python-projekt till Azure: fjärran sluten version, lokal version och build med anpassade beroenden.

Du kan också använda Azure-pipelines för att bygga dina beroenden och publicera med kontinuerlig leverans (CD). Läs mer i [kontinuerlig leverans med hjälp av Azure-DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Fjärrversion

När du använder fjärran sluten build matchas beroenden som återställs på servern och ursprungliga beroenden till produktions miljön. Detta resulterar i ett mindre distributions paket att ladda upp. Använd fjärran sluten skapande när du utvecklar python-appar i Windows. Om ditt projekt har anpassade beroenden kan du [använda fjärrbuild med extra index-URL](#remote-build-with-extra-index-url).

Beroenden hämtas via fjärr anslutning baserat på innehållet i requirements.txts filen. [Fjärran slutet](functions-deployment-technologies.md#remote-build) är den rekommenderade build-metoden. Som standard begär Azure Functions Core Tools en fjärr anslutning när du använder följande [FUNC Azure functionapp Publish](functions-run-local.md#publish) -kommando för att publicera ditt python-projekt till Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure.

[Azure Functions-tillägget för Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) begär också en fjärran sluten version som standard.

### <a name="local-build"></a>Lokal version

Beroenden hämtas lokalt baserat på innehållet i requirements.txts filen. Du kan förhindra en fjärran sluten version genom att använda följande [FUNC Azure functionapp Publish](functions-run-local.md#publish) -kommando för att publicera med en lokal version.

```command
func azure functionapp publish <APP_NAME> --build local
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure.

Med `--build local` alternativet läses projekt beroenden från requirements.txt-filen och de beroende paketen hämtas och installeras lokalt. Projektfiler och beroenden distribueras från den lokala datorn till Azure. Detta innebär att ett större distributions paket överförs till Azure. Om det av någon anledning inte går att hämta beroenden i requirements.txt-filen av kärn verktyg måste du använda alternativet anpassade beroenden för att publicera.

Vi rekommenderar inte att du använder lokala versioner när du utvecklar lokalt i Windows.

### <a name="custom-dependencies"></a>Anpassade beroenden

När projektet har beroenden som inte finns i [python-paketets index](https://pypi.org/), finns det två sätt att bygga projektet. Build-metoden beror på hur du bygger projektet.

#### <a name="remote-build-with-extra-index-url"></a>Fjärrbuild med extra index-URL

När dina paket är tillgängliga från ett tillgängligt anpassat paket index använder du en fjärran sluten version. Innan du publicerar bör du se till att [skapa en app-inställning](functions-how-to-use-azure-function-app-settings.md#settings) med namnet `PIP_EXTRA_INDEX_URL` . Värdet för den här inställningen är URL: en för det anpassade paket indexet. Med den här inställningen anger du att fjärrversionen ska köras `pip install` med `--extra-index-url` alternativet. Mer information finns i [installations dokumentationen för python pip](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Du kan också använda grundläggande autentiseringsuppgifter för autentisering med dina extra paket index-URL: er. Mer information finns i [grundläggande autentiseringsuppgifter för autentisering](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) i python-dokumentationen.

#### <a name="install-local-packages"></a>Installera lokala paket

Om ditt projekt använder paket som inte är offentligt tillgängliga för våra verktyg kan du göra dem tillgängliga för din app genom att placera dem i \_ \_ app \_ \_ /.python_packages-katalogen. Innan du publicerar kör du följande kommando för att installera beroendena lokalt:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

När du använder anpassade beroenden bör du använda `--no-build` alternativet Publicera eftersom du redan har installerat beroenden i projektmappen.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure.

## <a name="unit-testing"></a>Enhetstestning

Funktioner som skrivs i python kan testas som annan python-kod med standard testnings ramverk. För de flesta bindningar är det möjligt att skapa ett objekt av en modell genom att skapa en instans av en lämplig klass från `azure.functions` paketet. Eftersom [`azure.functions`](https://pypi.org/project/azure-functions/) paketet inte är omedelbart tillgängligt ser du till att installera det via `requirements.txt` filen enligt beskrivningen i avsnittet [paket hantering](#package-management) ovan.

Ta *my_second_function* som exempel är följande ett modell test av en http-utlöst funktion:

Först måste vi skapa *<project_root>/my_second_function/function.jsi* filen och definiera den här funktionen som en http-utlösare.

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

Nu kan vi implementera *my_second_function* och *shared_code. My _second_helper_function*.

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

Vi kan börja skriva test ärenden för vår http-utlösare.

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

I din `.venv` virtuella python-miljö installerar du ditt favorit test ramverk för python (t. ex. `pip install pytest` ). Kör bara `pytest tests` för att kontrol lera test resultatet.

## <a name="temporary-files"></a>Tillfälliga filer

`tempfile.gettempdir()`Metoden returnerar en tillfällig mapp som finns på Linux `/tmp` . Ditt program kan använda den här katalogen för att lagra temporära filer som skapas och används av funktionerna under körningen.

> [!IMPORTANT]
> Filer som skrivs till den temporära katalogen garanterar inte att de behålls i ett anrop. Temporära filer delas inte mellan instanser under utskalning.

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

Vi rekommenderar att du underhåller dina tester i en mapp separat från projektmappen. Detta gör att du inte distribuerar test koden med din app.

## <a name="preinstalled-libraries"></a>Förinstallerade bibliotek

Det finns ett par bibliotek med python Functions-körningen.

### <a name="python-standard-library"></a>Python standard bibliotek

Python-standardbiblioteket innehåller en lista över inbyggda python-moduler som levereras med varje python-distribution. De flesta av dessa bibliotek hjälper dig att komma åt systemfunktioner, t. ex. fil-I/O. I Windows-system installeras de här biblioteken med python. På UNIX-baserade system tillhandahålls de av paket samlingar.

Om du vill visa fullständig information om listan över dessa bibliotek kan du gå till länkarna nedan:

* [Python 3,6 standard bibliotek](https://docs.python.org/3.6/library/)
* [Python 3,7 standard bibliotek](https://docs.python.org/3.7/library/)
* [Python 3,8 standard bibliotek](https://docs.python.org/3.8/library/)
* [Python 3,9 standard bibliotek](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions python Worker-beroenden

Funktionen python Worker kräver en speciell uppsättning bibliotek. Du kan också använda dessa bibliotek i dina funktioner, men de är inte en del av python-standarden. Om dina funktioner är beroende av något av dessa bibliotek är de kanske inte tillgängliga för din kod när de körs utanför Azure Functions. Du hittar en detaljerad lista över beroenden i avsnittet **installations \_ krav** i [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) -filen.

> [!NOTE]
> Om Function-appens requirements.txt innehåller en `azure-functions-worker` post tar du bort den. Functions Worker hanteras automatiskt av Azure Functions plattform och vi uppdaterar den regelbundet med nya funktioner och fel korrigeringar. Att manuellt installera en gammal version av Worker i requirements.txt kan orsaka oväntade problem.

### <a name="azure-functions-python-library"></a>Azure Functions python-bibliotek

Varje python Worker-uppdatering innehåller en ny version av [Azure Functions python-bibliotek (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Den här metoden gör det enklare att kontinuerligt uppdatera dina python Function-appar eftersom varje uppdatering är bakåtkompatibel. En lista över versioner av det här biblioteket finns i [Azure-Functions PyPi](https://pypi.org/project/azure-functions/#history).

Körnings bibliotekets version korrigeras av Azure och kan inte åsidosättas av requirements.txt. `azure-functions`Posten i requirements.txt är endast för att kunna luddfri och kund medvetenhet.

Använd följande kod för att spåra den faktiska versionen av python Functions-biblioteket i din körnings miljö:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>System bibliotek för körning

Om du vill ha en lista över förinstallerade system bibliotek i python Worker Docker-avbildningar följer du länkarna nedan:

|  Functions-körning  | Debian-version | Python-versioner |
|------------|------------|------------|
| Version 2. x | Sträck  | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Version 3. x | Buster | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3,9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Cross-origin resource sharing (CORS)

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS stöds fullt ut för python-Function-appar.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

Följande är en lista över fel söknings guider för vanliga problem:

* [ModuleNotFoundError och ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Det går inte att importera ' cygrpc '](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Alla kända problem och funktions begär Anden spåras med hjälp av listan med [GitHub-problem](https://github.com/Azure/azure-functions-python-worker/issues) . Om du stöter på ett problem och inte kan hitta problemet i GitHub, öppnar du ett nytt ärende och innehåller en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Dokumentation om Azure Functions Package API](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Blob Storage-bindningar](functions-bindings-storage-blob.md)
* [HTTP-och webhook-bindningar](functions-bindings-http-webhook.md)
* [Köa lagrings bindningar](functions-bindings-storage-queue.md)
* [Timerutlösare](functions-bindings-timer.md)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python&preserve-view=true
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python&preserve-view=true