---
title: Azure Application Insights – Automatisk insamling av beroenden | Microsoft Docs
description: Application Insights automatiskt samla in och visualisera beroenden
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: aa4d39ca8964e95ca787d236223e2b475a9597c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873837"
---
# <a name="dependency-auto-collection"></a>Automatisk insamling för beroenden

Nedan visas listan över beroendeanrop som för närvarande stöds och som identifieras automatiskt som beroenden utan att ytterligare ändringar krävs i programmets kod. Dessa beroenden visualiseras i vyerna Application Insights [programkarta och](./app-map.md) [transaktionsdiagnostik.](./transaction-diagnostics.md) Om ditt beroende inte finns med i listan nedan kan du fortfarande spåra det manuellt med ett [beroendespår med anropet](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| Appramverk| Versioner |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Kommunikationsbibliotek</b> |
| [HttpClient](https://dotnet.microsoft.com) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 – senaste stabila versionen. (Se anmärkning nedan.)
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Lagringsklienter</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Det finns ett [känt problem](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) med äldre versioner av Microsoft.Data.SqlClient. Vi rekommenderar att du använder 1.1.0 eller senare för att åtgärda problemet. Entity Framework Core levereras inte nödvändigtvis med den senaste stabila versionen av Microsoft.Data.SqlClient, så vi rekommenderar att du bekräftar att du har minst 1.1.0 för att undvika det här problemet.   


## <a name="java"></a>Java
| Appservrar | Versioner |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss-EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Appramverk </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Kommunikationsbibliotek</b> |  |
| [Apache HTTP-klient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Lagringsklienter</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (betasupport)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Loggningsbibliotek</b> | |
| [Loggback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Måttbibliotek</b> |  |
| Jmx | 1.0+ |

> [!NOTE]
> *Förutom stöd för reaktiv programmering.
> <br>†Känder installation av [JVM-agenten](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Lagringsklienter</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x– 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0–2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x- 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x- 2.x |
| <b>Loggningsbibliotek</b> | |
| [Konsolen](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x- 3.x |

## <a name="javascript"></a>JavaScript

| Kommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alla |

## <a name="next-steps"></a>Nästa steg

- Konfigurera anpassad beroendespårning för [.NET](./asp-net-dependencies.md).
- Konfigurera anpassad beroendespårning för [Java](./java-agent.md).
- Konfigurera anpassad beroendespårning för [OpenCensus Python](./opencensus-python-dependency.md).
- [Skriva anpassad beroendetelemetri](./api-custom-events-metrics.md#trackdependency)
- Se [datamodell](./data-model.md) för Application Insights och datamodell.
- Kolla in [plattformar som](./platforms.md) stöds av Application Insights.

