---
title: Bygg integrerade lösningar
description: Lösnings verktyg och partner som integreras med en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3e55ef054d5c305937f88d6ec5b2b4453cac6792
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117766"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Integrera andra tjänster med en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.

Funktionen dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics gör det möjligt för användarna att integrera med många av de andra tjänsterna i Azure. En dedikerad SQL-pool kan använda flera ytterligare tjänster, varav vissa är:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Mer information om integrerings tjänster i Azure finns i artikeln [integration partners](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Med Power BI integration kan du kombinera beräknings kraften hos ett informations lager med den dynamiska rapporteringen och visualiseringen av Power BI. Power BI-integreringen innehåller för närvarande:

* **Direkt anslutning**: en mer avancerad anslutning med logisk mottagnings mot ett informations lager som tillhandahålls med dedikerad SQL-pool (tidigare SQL DW). Mottagningen ger snabbare analys i större skala.
* **Öppna i Power BI**: med knappen "öppna i Power BI" skickas instans information till Power BI för ett förenklat sätt att ansluta.

Mer information finns i [integrera med Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)eller [Power BI-dokumentationen](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory ger användare en hanterad plattform för att skapa komplexa extraherings-och belastnings pipeliner. Dedikerad SQL-pool (tidigare SQL DW)-integration med Azure Data Factory omfattar:

* **Lagrade procedurer**: dirigera körningen av lagrade procedurer.
* **Kopiera**: Använd ADF för att flytta data till en dedikerad SQL-pool (tidigare SQL DW). Den här åtgärden kan använda ADF: s standard funktion för data förflyttning eller PolyBase under försättsblad.

Mer information finns i [integrera med Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en helt hanterad analys tjänst som gör att du kan skapa invecklade modeller med en stor uppsättning av förutsägande verktyg. Dedikerad SQL-pool (tidigare SQL DW) stöds som både källa och mål för dessa modeller och har följande funktioner:

* **Läs data:** Enhets modeller i skala med hjälp av T-SQL mot en dedikerad SQL-pool (tidigare SQL DW).
* **Skriv data:** Genomför ändringar från alla modeller tillbaka till en dedikerad SQL-pool (tidigare SQL DW).

Mer information finns i [integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics är en komplex, fullständigt hanterad infrastruktur för bearbetning och användning av händelse data som genereras från Azure Event Hub.  Integrering med dedikerad SQL-pool (tidigare SQL DW) gör att strömmande data kan bearbetas och lagras tillsammans med relations data som möjliggör djupare och mer avancerad analys.  

* **Jobbets utdata:** Skicka utdata från Stream Analytics-jobb direkt till en dedikerad SQL-pool (tidigare SQL DW).

Mer information finns i [integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).