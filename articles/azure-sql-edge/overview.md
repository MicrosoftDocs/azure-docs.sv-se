---
title: Vad är Azure SQL Edge?
description: Läs mer om Azure SQL Edge
keywords: Introduktion till SQL Edge, vad är SQL Edge, SQL Edge-översikt
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395078"
---
# <a name="what-is-azure-sql-edge"></a>Vad är Azure SQL Edge?

Azure SQL Edge är en optimerad Relations databas motor som är avsedd för IoT och IoT Edge distributioner. Det innehåller funktioner för att skapa ett högpresterande lagrings-och bearbetnings lager för IoT-program och-lösningar. Azure SQL Edge innehåller funktioner för att strömma, bearbeta och analysera Relations-och icke-relationella data, till exempel JSON, graf-och Time Series-data, vilket gör det till det rätta valet för en mängd moderna IoT-program.

Azure SQL Edge bygger på de senaste versionerna av [SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation), som tillhandahåller branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Eftersom Azure SQL Edge bygger på samma motor som [SQL Server](/sql/sql-server/sql-server-technical-documentation) och [Azure SQL](../azure-sql/index.yml), innehåller den samma PROGRAMMERINGSSPRÅK för Transact-SQL (T-SQL) som gör det enklare och snabbare att utveckla program eller lösningar, och gör programportabiliteten mellan IoT Edge enheter, data Center och molnet rakt framåt.

Vad är Azure SQL Edge-video på Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Distributions modeller

Azure SQL Edge stöder två distributions lägen.

- Ansluten distribution via Azure IoT Edge: Azure SQL Edge är tillgängligt på Azure Marketplace och kan distribueras som en modul för [Azure IoT Edge](../iot-edge/about-iot-edge.md). Mer information finns i [Distribuera Azure SQL Edge](deploy-portal.md).<br>

![Översikts diagram över SQL Edge](media/overview/overview.png)

- Frånkopplad distribution: bilder i Azure SQL Edge-behållare kan hämtas från Docker Hub och distribueras antingen som en fristående Docker-behållare eller i ett Kubernetes-kluster. Mer information finns i [Distribuera Azure SQL Edge med Docker](disconnected-deployment.md) och [distribuera en Azure SQL Edge-behållare i Kubernetes](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Utgåvor av SQL Edge

SQL Edge är tillgängligt med två olika utgåvor eller program varu planer. Dessa versioner har identiska funktions uppsättningar och skiljer sig bara åt när det gäller användnings rättigheter och hur mycket processor/minne som de stöder.

   |**Planera**  |**Beskrivning**  |
   |---------|---------|
   |Azure SQL Edge-utvecklare  |  SKU för endast utveckling, varje SQL Edge-behållare är begränsad till upp till 4 kärnor och 32 GB minne  |
   |Azure SQL Edge    |  Produktions-SKU: n, varje SQL Edge-behållare är begränsad till upp till 8 kärnor och 64 GB minne. |

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Azure SQL Edge är nu allmänt tillgänglig. Mer information om priser och tillgänglighet i vissa regioner finns i [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Information om skillnaderna mellan Azure SQL Edge och SQL Server, samt skillnaderna mellan olika alternativ för Azure SQL Edge, finns i [funktioner som stöds i Azure SQL Edge](features.md).

## <a name="streaming-capabilities"></a>Strömmande funktioner  

Azure SQL Edge innehåller inbyggda strömnings funktioner för analys i real tid och komplex händelse bearbetning. Strömnings funktionen skapas med samma konstruktioner som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och liknande funktioner som [Azure Stream Analytics på IoT Edge](../stream-analytics/stream-analytics-edge.md).

Strömnings motorn för Azure SQL Edge är utformad för låg latens, återhämtning, effektiv användning av bandbredd och efterlevnad. 

Mer information om data strömning i SQL Edge finns i [strömma data](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Funktioner för Machine Learning och artificiell intelligens

Azure SQL Edge innehåller inbyggda funktioner för maskin inlärning och analys genom att integrera ONNX-körningen med öppen formatering (Open neurala Network Exchange), som möjliggör utbyte av djup inlärning och neurala nätverks modeller mellan olika ramverk. Mer information om ONNX finns [här](https://onnx.ai/). ONNX runtime ger flexibiliteten att utveckla modeller på ett språk eller valfritt verktyg som sedan kan konverteras till ONNX-formatet för körning inom SQL Edge. Mer information finns i [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Arbeta med Azure SQL Edge

Med Azure SQL Edge kan du utveckla och underhålla program enklare och mer produktivt. Användarna kan använda alla välkända verktyg och kunskaper för att bygga fantastiska appar och lösningar för sina IoT Edge behov. Användaren kan utveckla i SQL Edge med hjälp av verktyg som följande:

- [Azure Portal](https://portal.azure.com/) -ett webbaserat program för att hantera alla Azure-tjänster.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – ett kostnads fritt, nedladdnings Bart klient program för att hantera alla SQL-infrastrukturer från SQL Server till SQL Database.
- [SQL Server data tools i Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – ett kostnads fritt, nedladdnings Bart klient program för att utveckla SQL Server Relations databaser, SQL-databaser, Integration Services-paket, Analysis Services data modeller och repor ting Services-rapporter.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – ett kostnads fritt, nedladdnings Bart plattforms oberoende databas verktyg för data Professional med Microsoft-serien lokala och molnbaserade data plattformar på Windows, MacOS och Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) – en kostnads fri, nedladdnings bar kod redigerare med öppen källkod för Windows, MacOS och Linux. Det stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för frågor Microsoft SQL Server, Azure SQL Database och Azure Synapse Analytics.


## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Edge via Azure Portal](deploy-portal.md)
- [Machine Learning och artificiell intelligens med SQL Edge](onnx-overview.md)
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge](tutorial-deploy-azure-resources.md)