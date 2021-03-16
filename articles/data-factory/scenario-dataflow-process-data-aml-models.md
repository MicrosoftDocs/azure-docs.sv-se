---
title: Använda data flöde för att bearbeta data från AutoML-modeller (Automated Machine Learning)
description: Lär dig hur du använder Azure Data Factory data flöden för att bearbeta data från AutoML-modeller (Automated Machine Learning).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: e8352b687a3cdfac7ea2a819e1217906598a6837
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563274"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Bearbeta data från AutoML-modeller (automachine Learning) med data flöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

AutoML (Automated Machine Learning) antas av maskin inlärnings projekt för att träna, finjustera och få bästa möjliga modell automatiskt med målmått som du anger för klassificering, regression och tids serie prognoser. 

En utmaning är att rå data från data lagret eller transaktions databasen skulle vara enorma data uppsättningar, t. ex.: 10 GB, den stora data uppsättningen kräver längre tid för att träna modeller, så optimera data bearbetning rekommenderas innan du tränar Azure Machine Learning modeller. Den här självstudien går igenom hur du använder ADF för att partitionera data uppsättningen till Parquet-filer för Azure Machine Learning data uppsättning. 

I AutoML-projektet (automachine Learning) används följande tre data bearbetnings scenarier:

* Partitionera stora data till Parquet-filer före utbildnings modeller. 

     [Pandas data ram](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) används ofta för att bearbeta data innan inlärnings modeller. Pandas data ram fungerar bra för data storlekar som är mindre än 1 GB, men om data är större än 1 GB går det snabbare att bearbeta data i data ramen för Pandas. [Parquet-filformat](https://parquet.apache.org/) rekommenderas för Machine Learning eftersom det är ett binärt kolumn format.
    
    Data flöden för kart läggning av Azure-datafabriker är visuellt utformade med kod fria till data tekniker. Det är kraftfullt att bearbeta stora data eftersom pipelinen använder utskalade Spark-kluster.

* Data uppsättning för delad utbildning och test data uppsättning.
    
    Data uppsättningen för träning används för utbildnings modellen, test data uppsättningen används för att utvärdera modeller i Machine Learning-projekt. Genom att mappa data flöden villkorlig delnings aktivitet delas tränings data och test data. 

* Ta bort okvalificerade data.

    Du kanske vill ta bort okvalificerade data, t. ex.: Parquet-fil med noll rader. I den här självstudien använder vi mängd aktivitet för att räkna antalet rader, antalet rader är ett villkor för att ta bort okvalificerade data. 


## <a name="preparation"></a>Förberedelse
Använd följande tabell för Azure SQL Database. 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Konvertera data format till Parquet

Data flödet kommer att konvertera en tabell med Azure SQL Database till fil formatet Parquet. 

**Käll data uppsättning**: transaktions tabell för Azure SQL Database

**Data uppsättning för mottagare**: Blob Storage med Parquet-format


## <a name="remove-unqualified-data-based-on-row-count"></a>Ta bort okvalificerade data baserat på antal rader

Låt oss anta att ta bort antalet rader mindre än 2. 

1. Använd mängd aktivitet för att hämta antal rader: **Gruppera efter** baserat på Col2 och **agg regeringar** med Count (1) för radantal. 

    ![Konfigurera mängd aktivitet för att hämta antal rader](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Använd Sink-aktivitet, Välj **mottagar typ** som cache på fliken **mottagare** och välj önskad kolumn från List rutan **nyckel kolumner** på fliken **Inställningar** . 

    ![Konfigurera CacheSink-aktivitet för att hämta antal rader i cachelagrad mottagare](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Använd härledd kolumn aktivitet för att lägga till kolumnen antal rader i käll data strömmen. I den **härledda kolumnens inställning** -flik använder du CacheSink # lookup-uttryck för att hämta rad antal från SinkCache.
    ![Konfigurera härledd kolumn aktivitet för att lägga till antal rader i källa 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Använd villkorlig delad aktivitet för att ta bort okvalificerade data. I det här exemplet är rad antalet baserat på kolumnen Col2 och villkoret är att ta bort antalet rader mindre än 2, så två rader (ID = 2 och ID = 7) tas bort. Du skulle spara okvalificerade data till en blob-lagring för data hantering. 

    ![Konfigurera villkorlig delnings aktivitet för att hämta data som är större än eller lika med 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Skapa en ny källa för att hämta antal rader som ska användas i den ursprungliga källan i senare steg. 
>    *    Använd CacheSink från prestanda synpunkt. 

## <a name="split-training-data-and-test-data"></a>Dela tränings data och testa data 

1. Vi vill dela upp utbildnings data och testa data för varje partition. I det här exemplet, för samma värde för Col2, kan du hämta de 2 översta raderna som test data och resten av raderna som tränings data. 

    Använd fönster aktivitet för att lägga till ett kolumn rads nummer för varje partition. I fliken **över** väljer du kolumn för partition (i den här självstudien partitionerar för Col2), anger du sortera på fliken **Sortera** (i den här självstudien baseras på ID för att ordna) och på fliken **fönster kolumner** kan du lägga till en kolumn som rad nummer för varje partition. 
    ![Konfigurera fönster aktivitet för att lägga till en ny kolumn som rad nummer](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Använd villkorlig delad aktivitet för att dela upp varje partition de två översta raderna för att testa data uppsättningen och resten av raderna till Training-datauppsättningen. På fliken **Inställningar för villkorlig delning** använder du Expression LesserOrEqual (RowNum, 2) som villkor. 

    ![Konfigurera villkorlig delnings aktivitet för att dela aktuell data uppsättning till tränings data uppsättning och test data uppsättning](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Data uppsättning för partition utbildning och test data uppsättning med Parquet-format

1. Använd Sink-aktivitet, på fliken **optimera** , med **unikt värde per partition** för att ange en kolumn som en kolumn nyckel för partition. 
    ![Konfigurera Sink-aktivitet för att ange partition för data uppsättning för utbildning](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Nu ska vi gå tillbaka till hela pipeline-logiken.
    ![Logiken för hela pipelinen](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
