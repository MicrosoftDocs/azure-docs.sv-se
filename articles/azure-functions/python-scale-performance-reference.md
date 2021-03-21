---
title: Förbättra data flödes prestanda för python-appar i Azure Functions
description: Lär dig hur du utvecklar Azure Functions appar med python som är mycket presterande och skalar väl under belastning.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786114"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Förbättra data flödes prestanda för python-appar i Azure Functions

När du utvecklar för Azure Functions att använda python måste du förstå hur dina funktioner fungerar och hur den påverkar prestandan som påverkar hur din funktions app skalas. Behovet är viktigare när du utformar hög presterande appar. De huvudsakliga faktorer som du bör tänka på när du utformar, skriver och konfigurerar dina Functions-appar är vågrät skalning och prestanda konfigurationer för data flöde.

## <a name="horizontal-scaling"></a>Horisontell skalning
Som standard övervakar Azure Functions automatiskt belastningen på ditt program och skapar ytterligare värd instanser för python vid behov. Azure Functions använder inbyggda tröskelvärden för olika utlösare för att bestämma när du ska lägga till instanser, till exempel ålder på meddelanden och kös Tor lek för QueueTrigger. Dessa tröskelvärden kan inte konfigureras av användaren. Mer information finns i [händelse driven skalning i Azure Functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Förbättra data flödes prestanda

Standardkonfigurationerna passar för de flesta Azure Functions-program. Du kan dock förbättra prestandan för dina programs data flöde genom att använda konfigurationer baserade på din arbets belastnings profil. Det första steget är att förstå vilken typ av arbets belastning som körs.

| Arbets belastnings typ | Egenskaper för Function-appen       | Exempel                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/O-Bound**     | • Appen måste hantera många samtidiga anrop.<br>• Appen bearbetar ett stort antal I/O-händelser, t. ex. nätverks samtal och läsning/skrivning till disk. | • Webb-API: er                                          |
| **PROCESSOR gränser**     | • Appen gör tids krävande beräkningar, till exempel bild storleks ändring.<br>• Appen utför data omvandling.                                                | • Data bearbetning<br>• Maskin inlärnings störningar<br> |

 
Eftersom verkliga arbets belastningar i världen vanligt vis är en blandning av I/O och processor gränser bör du profilera appen under realistiska produktions belastningar.


### <a name="performance-specific-configurations"></a>Prestanda-/regionsspecifika konfigurationer

När du förstår arbets belastnings profilen för din Function-app, är följande konfigurationer som du kan använda för att förbättra data flödes prestandan för dina funktioner.

* [Async](#async)
* [Flera språk arbetare](#use-multiple-language-worker-processes)
* [Max arbetare inom en språk arbets process](#set-up-max-workers-within-a-language-worker-process)
* [Händelse slinga](#managing-event-loop)
* [Lodrät skalning](#vertical-scaling)



#### <a name="async"></a>Async

Eftersom [python är en enkel tråds körning](https://wiki.python.org/moin/GlobalInterpreterLock)kan en värd instans för python endast bearbeta ett funktions anrop i taget som standard. För program som bearbetar ett stort antal I/O-händelser och/eller är I/O-bundit kan du förbättra prestanda avsevärt genom att köra funktioner asynkront.

Om du vill köra en funktion asynkront använder du `async def` instruktionen, som kör funktionen med [asyncio](https://docs.python.org/3/library/asyncio.html) direkt:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Här är ett exempel på en funktion med HTTP-utlösare som använder [aiohttp](https://pypi.org/project/aiohttp/) http-klient:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


En funktion utan `async` nyckelordet körs automatiskt i en ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

För att få full nytta av att köra funktioner asynkront måste i/O-åtgärden/-biblioteket som används i din kod också ha asynkron implementerad. Att använda synkrona I/O-åtgärder i funktioner som definieras som asynkrona **kan försämra** den övergripande prestandan. Om de bibliotek som du använder inte har en asynkron version som är implementerad, kan du fortfarande ha nytta av att köra koden asynkront genom att [Hantera händelse slingor](#managing-event-loop) i appen. 

Här följer några exempel på klient bibliotek som har implementerat asynkront mönster:
- [aiohttp](https://pypi.org/project/aiohttp/) -http-klient/server för asyncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) – asynkrona/await-klara primitiver på hög nivå för att arbeta med nätverks anslutning
- [Janus Queue](https://pypi.org/project/janus/) – tråd säker asyncio-medveten kö för python
- [pyzmq](https://pypi.org/project/pyzmq/) – python-bindningar för ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Förstå asynkrona i python Worker

När du definierar `async` en funktions-signatur kommer python att markera funktionen som en-rutin. När du anropar en-rutin kan den schemaläggas som en uppgift i en händelse slinga. När du anropar `await` i en async-funktion registrerar den en fortsättning i händelse slingan och låter händelse slingor bearbeta nästa aktivitet under vänte tiden.

I vår python-arbetare delar arbets tagaren händelse slingan med kundens `async` funktion och kan hantera flera förfrågningar samtidigt. Vi rekommenderar starkt att våra kunder använder asyncio-kompatibla bibliotek (t. ex. [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). Genom att använda dessa rekommendationer ökar funktionens data flöde avsevärt jämfört med de bibliotek som implementeras i synkront sätt.

> [!NOTE]
>  Om din funktion är deklarerad som `async` utan någon del `await` av implementeringen, kommer prestandan för funktionen att påverkas kraftigt eftersom händelse slingan blockeras, vilket förhindrar att python-arbetsbelastningen hanterar samtidiga begär Anden.

#### <a name="use-multiple-language-worker-processes"></a>Använd flera språk arbets processer

Som standard har varje Functions Host-instans en enda språk arbets process. Du kan öka antalet arbets processer per värd (upp till 10) med hjälp av inställningen [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) program. Azure Functions försöker sedan jämnt distribuera samtidiga funktions anrop över dessa arbetare.

För CPU-kopplade appar bör du ange att antalet språk arbetare ska vara samma som eller högre än antalet kärnor som är tillgängliga per Function-app. Mer information finns i [tillgängliga instanser SKU: er](functions-premium-plan.md#available-instance-skus). 

I/O-kopplade appar kan också dra nytta av att öka antalet arbets processer utöver antalet tillgängliga kärnor. Tänk på att om du anger antalet arbets uppgifter för hög kan du påverka den totala prestandan på grund av det ökade antalet kontext växlar som krävs. 

FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som fungerar när du skalar ditt program för att möta efter frågan.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Ställ in Max arbetare inom en språk arbets process

Som nämnts i [avsnittet](#understanding-async-in-python-worker)async behandlar python-språkarbetarna funktioner och [rutiner](https://docs.python.org/3/library/asyncio-task.html#coroutines) på olika sätt. En rutin körs inom samma händelse slinga som språk arbetaren kör på. Å andra sidan körs ett funktions anrop inom en [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), som underhålls av språk arbets tagaren som en tråd.

Du kan ange värdet för maximalt antal anställda som ska kunna köra synkroniserade funktioner med hjälp av inställningen [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) program. Det här värdet anger `max_worker` argumentet för ThreadPoolExecutor-objektet, vilket innebär att python använder en pool på högst `max_worker` trådar för att köra anrop asynkront. `PYTHON_THREADPOOL_THREAD_COUNT`Gäller för varje arbets tagare som fungerar som värd för skapar och python bestämmer när du ska skapa en ny tråd eller återanvända den befintliga inaktiva tråden. För äldre python-versioner (det vill säga,, `3.8` `3.7` och `3.6` ) `max_worker` anges värdet till 1. För python `3.9` -versionen `max_worker` anges till `None` .

För CPU-baserade appar bör du behålla inställningen till ett lågt nummer, med början från 1 och ökar när du experimenterar med arbets belastningen. Det här förslaget är att minska den tid som krävs för kontext byten och tillåta att CPU-kopplade uppgifter slutförs.

För I/O-kopplade appar bör du se stora vinster genom att öka antalet trådar som fungerar vid varje anrop. rekommendationen är att börja med python-standardvärdet – antalet kärnor + 4 och sedan justera baserat på de data flödes värden som du ser.

För att blanda arbets belastnings program bör du balansera både `FUNCTIONS_WORKER_PROCESS_COUNT` och `PYTHON_THREADPOOL_THREAD_COUNT` konfigurationer för att maximera data flödet. Vi rekommenderar att du profilerar och ställer in värdena enligt de funktioner som finns tillgängliga, för att förstå vad dina funktions appar tillbringar mest. Läs även det här [avsnittet](#use-multiple-language-worker-processes) om du vill veta mer om FUNCTIONS_WORKER_PROCESS_COUNT program inställningar.

> [!NOTE]
>  Även om de här rekommendationerna gäller både HTTP-och icke-HTTP-utlösta funktioner kan du behöva justera andra Utlös ande konfigurationer för funktioner som inte är HTTP-utlöst för att få den förväntade prestandan från dina funktions appar. Mer information om detta finns i den här [artikeln](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Hantera händelse slinga

Du bör använda asyncio-kompatibla bibliotek från tredje part. Om inget av biblioteken från tredje part uppfyller dina behov kan du också hantera händelse slingor i Azure Functions. Genom att hantera händelse slingor får du mer flexibilitet i hantering av beräknings resurser, och det gör det också möjligt att omsluta synkrona I/O-bibliotek till samtidiga rutiner.

Det finns många användbara python-officiella dokument som diskuterar dina [rutiner och uppgifter](https://docs.python.org/3/library/asyncio-task.html) och [händelse slingor](https://docs.python.org/3.8/library/asyncio-eventloop.html) med hjälp av det inbyggda **asyncio** -biblioteket.

Ta följande [begär](https://github.com/psf/requests) ande bibliotek som exempel, det här kodfragmentet använder **asyncio** -biblioteket för att omsluta `requests.get()` metoden till en samkörning, som kör flera webbegäranden till SAMPLE_URL samtidigt.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Vertikal skalning
För mer bearbetnings enheter, särskilt i CPU-baserad åtgärd, kan du få detta genom att uppgradera till Premium plan med högre specifikationer. Med högre bearbetnings enheter kan du justera antalet arbets process antal enligt antalet tillgängliga kärnor och uppnå högre grad av parallellitet. 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions python-utveckling finns i följande resurser:

* [Guide för Azure Functions python-utvecklare](functions-reference-python.md)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)

