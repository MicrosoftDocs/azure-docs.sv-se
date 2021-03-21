---
title: Använda data flöden för att bearbeta data från AutoML-modeller (Automated Machine Learning)
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
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595387"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Bearbeta data från automatiserade maskin inlärnings modeller med hjälp av data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Automatisk maskin inlärning (AutoML) antas av maskin inlärnings projekt för att träna, finjustera och få bästa modeller automatiskt genom att använda mål mått som du anger för klassificering, regression och tids serie prognoser.

En utmaning för AutoML är att rå data från ett informations lager eller en transaktions databas skulle vara en stor data uppsättning, eventuellt 10 GB. En stor data uppsättning kräver längre tid för att träna modeller, så vi rekommenderar att du optimerar data bearbetningen innan du tränar Azure Machine Learning modeller. Den här självstudien går igenom hur du använder Azure Data Factory för att partitionera en data uppsättning i AutoML-filer för en Machine Learning data uppsättning.

AutoML-projektet innehåller följande tre data bearbetnings scenarier:

* Partitionera stora data till AutoML-filer innan du tränar modeller.

     [Data ramen Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) används ofta för att bearbeta data innan du tränar modeller. Data ramen Pandas fungerar bra för data storlekar som är mindre än 1 GB, men om data är större än 1 GB går det långsamt att bearbeta data ramen i en Pandas. Ibland kan du till och med få ett fel meddelande om slut på minne. Vi rekommenderar att du använder ett [Parquet-filformat](https://parquet.apache.org/) för Machine Learning eftersom det är ett binärt kolumn format.
    
     Data Factory mappa data flöden visuellt utformade data transformationer som frigör data tekniker från att skriva kod. Att mappa data flöden är ett kraftfullt sätt att bearbeta stora data eftersom pipelinen använder uppskalade Spark-kluster.

* Dela inlärnings data uppsättningen och test data uppsättningen.
    
    Data uppsättningen för träning kommer att användas för en utbildnings modell. Test data uppsättningen kommer att användas för att utvärdera modeller i ett Machine Learning-projekt. Den villkorliga delnings aktiviteten för att mappa data flöden skulle dela tränings data och testa data.

* Ta bort okvalificerade data.

    Du kanske vill ta bort okvalificerade data, till exempel en Parquet-fil med noll rader. I den här självstudien använder vi den sammanställda aktiviteten för att få fram antalet rader. Antalet rader är ett villkor för att ta bort okvalificerade data.

## <a name="preparation"></a>Förberedelse

Använd följande Azure SQL Database tabell.

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

Följande data flöde kommer att konvertera en SQL Database tabell till ett Parquet-fil format:

- **Käll data uppsättning**: transaktions tabell för SQL Database.
- **Data uppsättning för mottagare**: Blob Storage med Parquet-format.

## <a name="remove-unqualified-data-based-on-row-count"></a>Ta bort okvalificerade data baserat på antal rader

Vi antar att vi behöver ta bort ett radantal som är mindre än två.

1. Använd den sammanställda aktiviteten för att få fram antalet rader. Använd **grupperat** utifrån Col2 och **agg regeringar** med `count(1)` för radantal.

    ![Skärm bild som visar hur du konfigurerar den sammanställda aktiviteten för att få en räkning av antalet rader.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Använd Sink-aktiviteten och välj **mottagar typ** som **cache** på fliken **mottagare** . Välj sedan önskad kolumn i list rutan **nyckel kolumner** på fliken **Inställningar** .

    ![Skärm bild som visar konfiguration av CacheSink-aktiviteten för att få en räkning av antalet rader i en cachelagrad mottagare.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Använd aktiviteten härledd kolumn för att lägga till en kolumn för rad räkning i käll data strömmen. På fliken **Inställningar för härledd kolumn** använder du `CacheSink#lookup` uttrycket för att hämta ett rad antal från CacheSink.

    ![Skärm bild som visar hur du konfigurerar den härledda kolumn aktiviteten och lägger till ett antal rader i source1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Använd den villkorliga delnings aktiviteten för att ta bort okvalificerade data. I det här exemplet baseras antalet rader på kolumnen Col2. Villkoret är att ta bort ett radantal som är mindre än två, så två rader (ID = 2 och ID = 7) tas bort. Du skulle spara okvalificerade data till Blob Storage för data hantering.

    ![Skärm bild som visar hur du konfigurerar den villkorliga delnings aktiviteten för att hämta data som är större än eller lika med två.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Skapa en ny källa för att få fram ett antal rader som ska användas i den ursprungliga källan i senare steg.
>    * Använd CacheSink från en prestanda synpunkt.

## <a name="split-training-data-and-test-data"></a>Dela tränings data och testa data

Vi vill dela upp tränings data och testa data för varje partition. I det här exemplet, för samma värde för Col2, får du de två översta raderna som test data och resten av raderna som tränings data.

1. Använd fönster aktiviteten för att lägga till ett kolumn rads nummer för varje partition. På fliken **över** väljer du en kolumn för partition. I den här självstudien ska vi partitionera för Col2. Ange en order på fliken **Sortera** , som i den här självstudien baseras på ID. Ange en ordning på fliken **fönster kolumner** om du vill lägga till en kolumn som ett rad nummer för varje partition.

    ![Skärm bild som visar hur du konfigurerar fönster aktivitet för att lägga till en ny kolumn som rad nummer.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Använd den villkorliga delnings aktiviteten för att dela varje partitions översta två rader i test data uppsättningen och resten av raderna i inlärnings data uppsättningen. På fliken **Inställningar för villkorlig delning** använder du uttrycket `lesserOrEqual(RowNum,2)` som villkor.

    ![Skärm bild som visar hur du konfigurerar den villkorliga delnings aktiviteten för att dela den aktuella data mängden i inlärnings data uppsättningen och test data uppsättningen.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partitionera data uppsättningarna utbildning och test med Parquet-format

Använd Sink-aktiviteten på fliken **Optimize** och Använd **unikt värde per partition** för att ange en kolumn som en kolumn nyckel för partition.

![Skärm bild som visar hur du konfigurerar Sink-aktiviteten för att ställa in partitionen för träning-datauppsättningen.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Nu ska vi gå tillbaka i hela pipeline-logiken.

![Skärm bild som visar logiken för hela pipelinen.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Nästa steg

Skapa resten av din data flödes logik genom att använda mappningar för data flödes [omvandling](concepts-data-flow-overview.md).
