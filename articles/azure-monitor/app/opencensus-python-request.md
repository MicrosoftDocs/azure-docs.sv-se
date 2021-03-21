---
title: Spårning av inkommande begär anden i Azure Application insikter med python-räkning python | Microsoft Docs
description: Övervaka begär ande anrop för dina python-appar via python-räkningar.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585667"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Spåra inkommande begär Anden med openräkningar python

Inkommande begär ande data samlas in med hjälp av openräkningar python och dess olika integreringar. Spåra inkommande begär ande data som skickas till dina webb program som skapats ovanpå de populära webb ramverken `django` `flask` och `pyramid` . Data skickas sedan till Application Insights under Azure Monitor som `requests` telemetri.

Börja med att Instrumenta din python-app med senaste [Openräkning python SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Spåra django-program

1. Hämta och installera `opencensus-ext-django` från [pypi](https://pypi.org/project/opencensus-ext-django/) och instrumentera ditt program med `django` mellanprogram. Inkommande begär Anden som skickas till ditt `django` program spåras.

2. Inkludera `opencensus.ext.django.middleware.OpencensusMiddleware` i `settings.py` filen under `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Kontrol lera att AzureExporter har kon figurer ATS korrekt i `settings.py` under `OPENCENSUS` . För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Spåra program i kolven

1. Hämta och installera `opencensus-ext-flask` från [pypi](https://pypi.org/project/opencensus-ext-flask/) och instrumentera ditt program med `flask` mellanprogram. Inkommande begär Anden som skickas till ditt `flask` program spåras.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Du kan också konfigurera ditt `flask` program genom `app.config` . För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Spåra program i pyramiden

1. Hämta och installera `opencensus-ext-django` från [pypi](https://pypi.org/project/opencensus-ext-pyramid/) och instrumentera ditt program med `pyramid` interpoleringen. Inkommande begär Anden som skickas till ditt `pyramid` program spåras.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Du kan konfigurera `pyramid` interpoleringen direkt i koden. För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Spåra FastAPI-program

Openräkning har inget tillägg för FastAPI. Gör så här om du vill skriva egna FastAPI mellanprodukter:

1. Följande beroenden krävs: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Lägg till [FastAPI mellanprogram](https://fastapi.tiangolo.com/tutorial/middleware/). Se till att du ställer in server för spänn Natura: `span.span_kind = SpanKind.SERVER` .

3. Kör ditt program. Anrop som görs till ditt FastAPI-program bör spåras automatiskt och telemetri ska loggas direkt till Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Nästa steg

* [Programkarta](./app-map.md)
* [Tillgänglighet](./monitor-web-app-availability.md)
* [Sök](./diagnostic-search.md)
* [Logg (analys) fråga](../logs/log-query-overview.md)
* [Transaktionsdiagnostik](./transaction-diagnostics.md)

