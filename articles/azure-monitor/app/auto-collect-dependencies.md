---
title: Azure Application Insights-beroende automatisk insamling | Microsoft Docs
description: Application Insights automatiskt samla in och visualisera beroenden
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: 8a4d79e52465e93fb4db2625217cb37a06917218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91930874"
---
# <a name="dependency-auto-collection"></a>Automatisk insamling för beroenden

Nedan visas en lista över beroende anrop som identifieras automatiskt som beroenden, utan att det krävs någon ytterligare ändring av programmets kod. Dessa beroenden visualiseras i vyerna Application Insights [program karta](./app-map.md) och [transaktions-diagnostik](./transaction-diagnostics.md) . Om ditt beroende inte finns med i listan nedan kan du fortfarande spåra det manuellt med ett [spår beroende anrop](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| App-ramverk| Versioner |
| ------------------------|----------|
| ASP.NET-webbformulär | 4,5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4,5 + |
| ASP.NET Core | 1.1 + |
| <b> Kommunikations bibliotek</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4,5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET core 1.0 +, NuGet 4.3.0 |
| [Microsoft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 – senaste stabila versionen. (Se kommentaren nedan.)
| [EventHubs-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Service Bus-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Lagrings klienter</b>|  |
| ADO.NET | 4,5 + |

> [!NOTE]
> Det finns ett [känt problem](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) med äldre versioner av Microsoft. data. SqlClient. Vi rekommenderar att du använder 1.1.0 eller senare för att undvika det här problemet. Entity Framework Core levereras inte nödvändigt vis med den senaste stabila versionen av Microsoft. data. SqlClient så vi rekommenderar att du bekräftar att du har minst 1.1.0 för att undvika det här problemet.   


## <a name="java"></a>Java
| App-servrar | Versioner |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss-EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>App-ramverk </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Java-servlet | 3.1 + |
| <b>Kommunikations bibliotek</b> |  |
| [Apache HTTP-klient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Lagrings klienter</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (beta support)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Loggnings bibliotek</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Mått bibliotek</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> * Förutom stöd för omaktivering av program.
> <br>† Kräver installation av [JVM-agenten](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikations bibliotek | Versioner |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0,10 + |
| <b>Lagrings klienter</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [MongoDB-kärna](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 – 2.16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Loggnings bibliotek</b> | |
| [konsolomdirigering](https://nodejs.org/api/console.html) | 0,10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Kommunikations bibliotek | Versioner |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alla |

## <a name="next-steps"></a>Nästa steg

- Konfigurera anpassad beroende spårning för [.net](./asp-net-dependencies.md).
- Konfigurera anpassad beroende spårning för [Java](./java-agent.md).
- Konfigurera anpassad beroende spårning för python- [räkningar](./opencensus-python-dependency.md).
- [Skriv anpassad beroende telemetri](./api-custom-events-metrics.md#trackdependency)
- Se [data modell](./data-model.md) för Application Insights typer och data modell.
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

