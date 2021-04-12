---
title: Minnes profilering på python-appar i Azure Functions
description: Lär dig hur du använder minnes användning för python-appar och identifiera Flask halsar i minnet.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258817"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profil python Apps minnes användning i Azure Functions

Under utvecklingen eller när du har distribuerat ditt lokala python Function-projekt till Azure, är det en bra idé att analysera för potentiella minnes Flask halsar i funktionerna. Sådana Flask halsar kan minska prestandan för dina funktioner och leda till fel. Följande instruktioner visar hur du använder python-paketet för [minnes-](https://pypi.org/project/memory-profiler) i-profilering, som innehåller analys av funktioner för rad-till-rad-minnes användning för dina funktioner när de körs.

> [!NOTE]
> Minnes profilering är endast avsett för analys av minnes storlek i utvecklings miljön. Använd inte minnes profiler i appar för produktions funktioner.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar utveckla en python Function-app måste du uppfylla följande krav:

* [Python 3.6. x eller senare](https://www.python.org/downloads/release/python-374/). Om du vill kontrol lera den fullständiga listan med python-versioner som stöds i Azure Functions går du till [python Developer Guide](functions-reference-python.md#python-version).

* [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

* [Visual Studio Code](https://code.visualstudio.com/) installerat på någon av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Minnes profilerings process

1. I requirements.txt lägger du till `memory-profiler` för att se till att paketet paketeras med distributionen. Om du utvecklar på den lokala datorn kanske du vill [Aktivera en virtuell python-miljö](create-first-function-cli-python.md#create-venv) och göra en paket matchning av `pip install -r requirements.txt` .

2. I funktions skriptet (vanligt vis \_ \_ init \_ \_ . py) lägger du till följande rader ovanför `main()` funktionen. Detta säkerställer att rot loggarna rapporterar namnen på underordnade loggar, så att minnes profilerings loggarna kan särskiljas av prefixet `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Exempel

Här är ett exempel på hur du utför minnes profilering på en asynkron och en synkron HTTP-utlösare med namnet "HttpTriggerAsync" respektive "HttpTriggerSync". Vi kommer att skapa en python Function-app som helt enkelt skickar GET-begäranden till Microsofts hem sida.

### <a name="create-a-python-function-app"></a>Skapa en python Function-app

En python Function-app bör följa Azure Functions angivna [mappstrukturen](functions-reference-python.md#folder-structure). För att Autogenerera projektet rekommenderar vi att du använder Azure Functions Core Tools genom att köra följande kommandon:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Uppdatera fil innehåll

requirements.txt definierar de paket som ska användas i projektet. Förutom Azure Functions SDK och minnes profiler, introducerar vi `aiohttp` för asynkrona HTTP-begäranden och `requests` för SYNKRONA http-anrop.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Vi måste också skriva om den asynkrona HTTP-utlösaren `HttpTriggerAsync/__init__.py` och konfigurera minnes profilerings-, rot logg format och bindning för logg strömning.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

För synkron HTTP-utlösare, se följande `HttpTriggerSync/__init__.py` kod avsnitt:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profilera python Function-appen i den lokala utvecklings miljön

När du har gjort ovanstående ändringar finns det några fler steg att initiera en virtuell python-envionment för Azure Functions Runtime.

1. Öppna en Windows PowerShell-eller Linux-Shell som du föredrar.
2. Skapa en virtuell python-miljö i `py -m venv .venv` Windows eller `python3 -m venv .venv` Linux.
3. Aktivera python virtuellt-miljön med `.venv\Scripts\Activate.ps1` i Windows PowerShell eller `source .venv/bin/activate` i Linux-gränssnittet.
4. Återställa python-beroenden med `pip install requirements.txt`
5. Starta Azure Functions runtime lokalt med Azure Functions Core Tools `func host start`
6. Skicka en GET-begäran till `https://localhost:7071/api/HttpTriggerAsync` eller `https://localhost:7071/api/HttpTriggerSync` .
7. Den ska visa en minnes profilerings rapport som liknar nedan i Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions python-utveckling finns i följande resurser:

* [Guide för Azure Functions python-utvecklare](functions-reference-python.md)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
