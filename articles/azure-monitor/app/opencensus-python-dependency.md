---
title: Beroende spårning i Azure Application insikter med python-räkning python | Microsoft Docs
description: Övervaka beroende anrop för dina python-appar via python-räkningar.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585704"
---
# <a name="track-dependencies-with-opencensus-python"></a>Spåra beroenden med python-räkning python

Ett beroende är en extern komponent som anropas av ditt program. Beroende data samlas in med hjälp av openräkningar python och dess olika integreringar. Data skickas sedan till Application Insights under Azure Monitor som `dependencies` telemetri.

Börja med att Instrumenta din python-app med senaste [Openräkning python SDK](./opencensus-python.md).

## <a name="in-process-dependencies"></a>Pågående beroenden

Med hjälp av python SDK för openräkning för Azure Monitor kan du skicka "inbands beroende telemetri" i processen (information och logik som inträffar i ditt program). Beroenden i processen har `type` fältet som `INPROC` i Analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Beroenden med "förfrågningar"-integration

Spåra dina utgående begär Anden med openräkning- `requests` integrering.

Hämta och installera `opencensus-ext-requests` från [pypi](https://pypi.org/project/opencensus-ext-requests/) och Lägg till det i spårnings integrationerna. Begär Anden som skickas med hjälp av python- [begärandena](https://pypi.org/project/requests/) -biblioteket kommer att spåras.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Beroenden med "httplib"-integrering

Spåra dina utgående begär Anden med openräkning- `httplib` integrering.

Hämta och installera `opencensus-ext-httplib` från [pypi](https://pypi.org/project/opencensus-ext-httplib/) och Lägg till det i spårnings integrationerna. Begär Anden som skickas med [http. client](https://docs.python.org/3.7/library/http.client.html) för python3 eller [httplib](https://docs.python.org/2/library/httplib.html) för Python2 spåras.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Beroenden med "django"-integrering

Spåra dina utgående django-begäranden med openräkning- `django` integrering.

> [!NOTE]
> De enda utgående django-begärandena som spåras görs anrop till en databas. Begär Anden som görs till django-programmet finns i [inkommande begär Anden](./opencensus-python-request.md#tracking-django-applications).

Hämta och installera `opencensus-ext-django` från [pypi](https://pypi.org/project/opencensus-ext-django/) och Lägg till följande rad i `MIDDLEWARE` avsnittet i Django- `settings.py` filen.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Ytterligare konfiguration kan tillhandahållas, Läs [anpassningar](https://github.com/census-instrumentation/opencensus-python#customization) för en fullständig referens.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Beroenden med "MySQL"-integrering

Spåra dina MYSQL-beroenden med openräkning- `mysql` integrering. Den här integrationen stöder [MySQL-Connector-](https://pypi.org/project/mysql-connector-python/) biblioteket.

Hämta och installera `opencensus-ext-mysql` från [pypi](https://pypi.org/project/opencensus-ext-mysql/) och Lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Beroenden med "pymysql"-integrering

Spåra dina PyMySQL-beroenden med openräkning- `pymysql` integrering.

Hämta och installera `opencensus-ext-pymysql` från [pypi](https://pypi.org/project/opencensus-ext-pymysql/) och Lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Beroenden med "postgresql"-integrering

Spåra dina PostgreSQL-beroenden med openräkning- `postgresql` integrering. Den här integrationen stöder [psycopg2](https://pypi.org/project/psycopg2/) -biblioteket.

Hämta och installera `opencensus-ext-postgresql` från [pypi](https://pypi.org/project/opencensus-ext-postgresql/) och Lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Beroenden med "pymongo"-integrering

Spåra dina MongoDB-beroenden med openräkning- `pymongo` integrering. Den här integrationen stöder [pymongo](https://pypi.org/project/pymongo/) -biblioteket.

Hämta och installera `opencensus-ext-pymongo` från [pypi](https://pypi.org/project/opencensus-ext-pymongo/) och Lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Beroenden med "sqlalchemy"-integrering

Spåra dina beroenden med SQLAlchemy med hjälp av openräkning- `sqlalchemy` integrering. Den här integrationen spårar användningen av [sqlalchemy](https://pypi.org/project/SQLAlchemy/) -paketet, oavsett vilken underliggande databas som används.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Nästa steg

* [Programkarta](./app-map.md)
* [Tillgänglighet](./monitor-web-app-availability.md)
* [Sök](./diagnostic-search.md)
* [Logg (analys) fråga](../logs/log-query-overview.md)
* [Transaktionsdiagnostik](./transaction-diagnostics.md)

