---
title: Förstå Azure Data Factory priser via exempel
description: Den här artikeln beskriver och demonstrerar Azure Data Factory pris modell med detaljerade exempel
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: b9f163a7632ca59d4f97aef21d8d62157610ba73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372815"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Förstå Data Factory-priser genom exempel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs och demonstreras den Azure Data Factory pris modellen med detaljerade exempel.

> [!NOTE]
> Priserna som används i de här exemplen nedan är hypotetiska och är inte avsedda att innebära den faktiska prissättningen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiera data från AWS S3 till Azure Blob Storage per timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indata-datauppsättning för de data som ska kopieras från AWS S3.

2. En data uppsättning för data i Azure Storage.

3. En schema utlösare för att köra pipelinen varje timme.

   ![Diagrammet visar en pipeline med en schema utlösare. I pipeline flödar kopierings aktiviteten till en indata-datauppsättning som flödar till en länkad S3-tjänst och kopierings aktiviteten också till en data uppsättning för utdata som flödar till en Azure Storage länkad tjänst.](media/pricing-concepts/scenario1.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 2 aktivitets körningar (1 för körnings körning, 1 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: endast 1 körning inträffade | 2 övervaknings körnings poster har hämtats (1 för pipeline-körning, 1 för aktivitets körning) |

**Pris för total scenario: $0,16811**

- Data Factory åtgärder = **$0,0001**
  - Läs/Skriv = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 2 \* 000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- Körning av pipeline-dirigering &amp; = **$0,168**
  - Aktivitet körs = 001 \* 2 = 0,002 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiera data och transformera med Azure Databricks varje timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage och transformera data med Azure Databricks per tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indatamängd för data som ska kopieras från AWS S3 och en data uppsättning för data i Azure Storage.
2. En Azure Databricks-aktivitet för data omvandlingen.
3. En schema utlösare för att köra pipelinen varje timme.

![Diagrammet visar en pipeline med en schema utlösare. I pipeline kan du kopiera aktivitets flöden till en indata-datauppsättning, en data uppsättning för utdata och en DataBricks-aktivitet som körs på Azure Databricks. Indata-datauppsättningen flödar till en v-länkad S3-tjänst. Den resulterande data uppsättningen flödar till en Azure Storage länkad tjänst.](media/pricing-concepts/scenario2.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 3 aktivitets körningar (1 för körnings körning, 2 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: endast 1 körning inträffade | 3 övervaknings körnings poster har hämtats (1 för pipeline-körning, 2 för aktivitets körning) |
| Kör Databricks Activity Assumptions: körnings tid = 10 min | 10 min externa pipeline-aktivitets körning |

**Pris för total scenario: $0,16916**

- Data Factory åtgärder = **$0,00012**
  - Läs/skriv = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 3 \* 000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- Körning av pipeline-dirigering &amp; = **$0,16904**
  - Aktivitet körs = 001 \* 3 = 0,003 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)
  - Extern pipeline-aktivitet = $0,000041 (beräknat i 10 minuters körnings tid. $0.00025/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiera data och transformera med dynamiska parametrar varje timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage och transformera med Azure Databricks (med dynamiska parametrar i skriptet) enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indatamängd för data som ska kopieras från AWS S3, en data uppsättning för data i Azure Storage.
2. En söknings aktivitet för att skicka parametrar dynamiskt till omvandlings skriptet.
3. En Azure Databricks-aktivitet för data omvandlingen.
4. En schema utlösare för att köra pipelinen varje timme.

![Diagrammet visar en pipeline med en schema utlösare. I pipeline kan du kopiera aktivitets flöden till en indata-datauppsättning, en data uppsättning för utdata och en söknings aktivitet som flödar till en DataBricks-aktivitet, som körs på Azure Databricks. Indata-datauppsättningen flödar till en v-länkad S3-tjänst. Den resulterande data uppsättningen flödar till en Azure Storage länkad tjänst.](media/pricing-concepts/scenario3.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 4 aktivitets körningar (1 för körning av Utlös punkt, 3 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: endast 1 körning inträffade | 4 övervaknings körnings poster har hämtats (1 för pipeline-körning, 3 för aktivitets körning) |
| Kör söknings aktivitets antagande: körnings tid = 1 min | 1 min körning av pipeline-aktivitet |
| Kör Databricks Activity Assumptions: körnings tid = 10 min | 10 min externa pipeline-aktivitets körning |

**Pris för total scenario: $0,17020**

- Data Factory åtgärder = **$0,00013**
  - Läs/skriv = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 4 \* 000005 = $0,00002 [1 övervakning = $0,25/50000 = 0,000005]
- Körning av pipeline-dirigering &amp; = **$0,17007**
  - Aktivitet körs = 001 \* 4 = 0,004 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)
  - Pipeline-aktivitet = $0,00003 (beräknas för 1 minuters körnings tid. $0.002/timme på Azure Integration Runtime)
  - Extern pipeline-aktivitet = $0,000041 (beräknat i 10 minuters körnings tid. $0.00025/timme på Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Använda fel sökning av data flödes fel sökning för en normal arbets dag

Som data tekniker ansvarar Sam för att utforma, skapa och testa mappnings data flöden varje dag. Sam loggar in i ADF-gränssnittet i morgon och aktiverar fel söknings läge för data flöden. Standard-TTL för debug-sessioner är 60 minuter. Sam fungerar dygnet runt i 8 timmar, så att fel söknings sessionen upphör att gälla. Därför blir Sam: s avgifter för dagen:

**8 (timmar) x 8 (Compute-optimerade kärnor) x $0,193 = $12,35**

Vid samma tillfälle loggar Christer, en annan data tekniker, även in i användar gränssnittet för ADF-läsaren för data profilering och ETL-design. Christer fungerar inte i ADF hela dagen som Sam. Christer behöver bara använda data Flow-felsökaren i 1 timme under samma period och samma dag som Sam ovan. Detta är de avgifter som Chris ådrar sig för fel söknings användning:

**1 (timme) x 8 (generella användnings kärnor) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformera data i BLOB Store med mappnings data flöden

I det här scenariot vill du transformera data i BLOB Store visuellt i data flöden för ADF-mappning enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En data flödes aktivitet med omvandlings logiken.

2. En data uppsättning för data i Azure Storage.

3. En data uppsättning för data i Azure Storage.

4. En schema utlösare för att köra pipelinen varje timme.

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 2 aktivitets körningar (1 för körnings körning, 1 för aktivitets körningar) |
| Antaganden för data flöde: körnings tid = 10 min + 10 min TTL | 10 \* 16 kärnor i allmän beräkning med TTL på 10 |
| Övervaka pipeline-antagande: endast 1 körning inträffade | 2 övervaknings körnings poster har hämtats (1 för pipeline-körning, 1 för aktivitets körning) |

**Pris för total scenario: $1,4631**

- Data Factory åtgärder = **$0,0001**
  - Läs/Skriv = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 2 \* 000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- Körning av pipeline-dirigering &amp; = **$1,463**
  - Aktivitet körs = 001 \* 2 = 0,002 [1 körning = $1/1000 = 0,001]
  - Data flödes aktiviteter = $1,461 beräknat i 20 minuter (10 minuter körnings tid + 10 minuter TTL). $0.274/timme på Azure Integration Runtime med 16 kärnor allmän beräkning

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Data integrering i Azure Data Factory hanterat VNET
I det här scenariot vill du ta bort original filerna på Azure Blob Storage och kopiera data från Azure SQL Database till Azure Blob Storage. Du kommer att utföra den här körningen två gånger på olika pipeliner. Körnings tiden för dessa två pipeliner överlappar varandra.
![Scenario4 ](media/pricing-concepts/scenario-4.png) för att uppnå scenariot måste du skapa två pipeliner med följande objekt:
  - En pipeline-aktivitet – ta bort aktivitet.
  - En kopierings aktivitet med en indata-datauppsättning för de data som ska kopieras från Azure Blob Storage.
  - En data uppsättning för data i Azure SQL Database.
  - Ett schema utlöses för att köra pipelinen.


| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 4 Läs/skriv-entitet |
| Skapa data uppsättningar | 8 Läs/skriv-entiteter (4 för skapande av data uppsättning, 4 för länkade tjänst referenser) |
| Skapa pipeline | 6 Läs/skriv-entiteter (2 för skapande av pipeline, 4 för data uppsättnings referenser) |
| Hämta pipeline | 2 Läs/skriv-entitet |
| Kör pipeline | 6 aktivitets körningar (2 för körning av utlösare, 4 för aktivitets körningar) |
| Utför borttagnings aktivitet: varje körnings tid = 5 min. Körningen av borttagnings aktiviteten i den första pipelinen är från 10:00 AM UTC till 10:05 AM UTC. Körningen av borttagnings aktiviteten i den andra pipelinen är från 10:02 AM UTC till 10:07 AM UTC.|Totalt 7 min körning av pipeline-aktiviteter i hanterat VNET. Pipeline-aktiviteten har stöd för upp till 50 samtidighet i hanterat VNET. |
| Kopiera data antagande: varje körnings tid = 10 min. Kopierings körningen i första pipelinen är från 10:06 AM UTC till 10:15 AM UTC. Körningen av borttagnings aktiviteten i den andra pipelinen är från 10:08 AM UTC till 10:17 AM UTC. | 10 * 4 Azure Integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: endast 2 körningar inträffade | 6 övervaknings körnings poster har hämtats (2 för pipeline-körning, 4 för aktivitets körning) |


**Pris för total scenario: $0,45523**

- Data Factory åtgärder = $0,00023
  - Läs/skriv = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 6 * 000005 = $0,00003 [1 övervakning = $0,25/50000 = 0,000005]
- Pipeline-Orchestration & körning = $0,455
  - Aktiviteten körs = 0,001 * 6 = 0,006 [1 Run = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,333 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)
  - Pipeline-aktivitet = $0,116 (beräknas för 7 minuters körnings tid. $1/timme på Azure Integration Runtime)

> [!NOTE]
> Dessa priser är endast till exempel syfte.

**VANLIGA FRÅGOR OCH SVAR**

F: kan de här aktiviteterna köras samtidigt om jag vill köra fler än 50 pipeline-aktiviteter?

A: högst 50 samtidiga pipeline-aktiviteter kommer att tillåtas.  51th pipeline-aktiviteten placeras i kö tills en "ledig plats" öppnas. Samma för extern aktivitet. Högst 800 samtidiga externa aktiviteter kommer att tillåtas.

## <a name="next-steps"></a>Nästa steg

Nu när du förstår priserna för Azure Data Factory kan du komma igång!

- [Skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md)

- [Introduktion till Azure Data Factory](introduction.md)

- [Visuell redigering i Azure Data Factory](author-visually.md)
