---
title: Vad är en dedikerad SQL-pool (tidigare SQL DW)?
description: Dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics är företags data lager funktionen i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b8738b46359287ce1d4e410292cdce213ab80999
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115828"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Vad är en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics?

Azure Synapse Analytics är en analystjänst som sammanför informationslager i företagsklass och stordataanalys. Dedikerad SQL-pool (tidigare SQL DW) syftar på de företags data lager funktioner som är tillgängliga i Azure Synapse Analytics.



![Dedikerad SQL-pool (tidigare SQL DW) i förhållande till Azure-Synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Dedikerad SQL-pool (tidigare SQL DW) representerar en samling analys resurser som tillhandahålls när du använder Synapse SQL. Storleken på en dedikerad SQL-pool (tidigare SQL DW) bestäms av data lager enheter (DWU).

När din dedikerade SQL-pool har skapats kan du importera stora data med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-frågor och sedan använda kraften i den distribuerade Frågeredigeraren för att köra analys med höga prestanda. När du integrerar och analyserar data blir dedikerad SQL-pool (tidigare SQL DW) den enda versionen av sanningen som ditt företag kan räkna med för snabbare och mer robusta insikter.

> [!NOTE]
>Utforska [Azure Synapse Analytics-dokumentationen](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Viktig komponent i en stor data lösning

Data lager är en viktig del av en molnbaserad stor data lösning från slut punkt till slut punkt.

![Lösning för informationslager](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

I en lösning för molndata matas data in i stordatalager från olika källor. När data lagras i ett stordatalager förbereder och tränar Hadoop, Spark och maskininlärningsalgoritmer dessa data. När data är klara för komplex analys använder dedicerad SQL-pool PolyBase för att fråga stora data lager. PolyBase använder vanliga T-SQL-frågor för att hämta data till dedikerade SQL-pooler (tidigare SQL DW)-tabeller.

Dedikerad SQL-pool (tidigare SQL DW) lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar kostnaderna för datalagring avsevärt och förbättrar frågeprestanda. När data lagras kan du köra analyser på massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter, eller på några timmar i stället för flera dagar.

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitekturen](massively-parallel-processing-mpp-architecture.md)
- [Skapa en dedikerad SQL-pool](create-data-warehouse-portal.md) snabbt
- [Läs in exempel data](./load-data-from-azure-blob-storage-using-copy.md).
- Utforska [videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av de andra Azure Synapse-resurserna.

- Sök efter [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Skicka en [funktions förfrågan](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Skapa ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md)
- Sök på [sidan Microsoft Q&en fråga](/answers/topics/azure-synapse-analytics.html)
- Sök [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)