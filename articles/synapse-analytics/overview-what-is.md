---
title: Vad är Azure Synapse Analytics?
description: En översikt över Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537940"
---
# <a name="what-is-azure-synapse-analytics"></a>Vad är Azure Synapse Analytics?

**Azure Synapse** är en tjänst för företagsanalys som ger snabbare insikter i informationslager och stordatasystem. Azure Synapse samman de bästa **SQL-teknikerna** som används i **informationslager** för företag, **Spark-tekniker** som används för stordata, pipelines för dataintegrering och ETL/ELT samt djupgående integrering med andra Azure-tjänster som **Power BI,** **CosmosDB** och **AzureML.**

![Diagram över Azure Synapse Analytics arkitektur.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Branschledande SQL

**Synapse SQL** är ett distribuerat frågesystem för T-SQL som möjliggör scenarier med informationslager och datavirtualisering och utökar T-SQL för att hantera strömnings- och maskininlärningsscenarier.

* Synapse SQL erbjuder både **serverlösa och** **dedikerade** resursmodeller. För förutsägbara prestanda och kostnader skapar du dedikerade SQL-pooler för att reservera beräknings för data som lagras i SQL-tabeller. För oplanerade eller burst-arbetsbelastningar använder du den serverlösa SQL-slutpunkten som alltid är tillgänglig.
* Använda inbyggda **strömningsfunktioner** för att landa data från molndatakällor till SQL-tabeller
* Integrera AI med SQL med hjälp av **maskininlärningsmodeller** för att poängta data med [hjälp av funktionen T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Branschstandard Apache Spark

**Apache Spark** för Azure Synapse integrerar sömlöst Apache Spark – den populäraste stordatamotorn med öppen källkod som används för förberedelse av data, datateknik, ETL och maskininlärning.

* ML-modeller med SparkML-algoritmer och AzureML-integrering Apache Spark 2.4 med inbyggt stöd för Linux Foundation Delta Lake.
* Förenklad resursmodell som gör att du inte behöver bekymra dig om att hantera kluster.
* Snabb start av Spark och aggressiv autoskalning.
* Inbyggt stöd för .NET för Spark så att du kan återanvända dina C#-kunskaper och befintlig .NET-kod i ett Spark-program.

## <a name="working-with-your-data-lake"></a>Arbeta med din Data Lake

Azure Synapse de traditionella teknikbarriärerna mellan att använda SQL och Spark tillsammans. Du kan sömlöst blanda och matcha baserat på dina behov och expertis.

* Tabeller som definierats för filer i datasjön används sömlöst av antingen Spark eller Hive.
* SQL och Spark kan direkt utforska och analysera Parquet-, CSV-, TSV- och JSON-filer som lagras i datasjön.
* Snabb och skalbar data inläsning mellan SQL- och Spark-databaser

## <a name="built-in-data-integration"></a>Inbyggd dataintegrering

Azure Synapse innehåller samma dataintegreringsmotor och upplevelser som Azure Data Factory, så att du kan skapa omfattande ETL-pipelines i stor skala utan att lämna Azure Synapse Analytics.

* Mata in data från över 90 datakällor
* Code-Free ETL med dataflödesaktiviteter
* Orkestrera notebook-filer, Spark-jobb, lagrade procedurer, SQL-skript med mera

## <a name="unified-experience"></a>Enhetlig upplevelse 

**Synapse Studio** ett enda sätt för företag att skapa lösningar, underhålla och skydda allt i en enda användarupplevelse

* Utföra viktiga uppgifter: mata in, utforska, förbereda, orkestrera, visualisera
* Övervaka resurser, användning och användare i SQL och Spark
* Använda rollbaserad åtkomstkontroll för att förenkla åtkomsten till analysresurser
* Skriva SQL- eller Spark-kod och integrera med företagets CI/CD-processer

## <a name="engage-with-the-synapse-community"></a>Interagera med Synapse-communityn

- [Microsoft Q&A:](/answers/topics/azure-synapse-analytics.html)Ställ tekniska frågor.
- [Stack Overflow:](https://stackoverflow.com/questions/tagged/azure-synapse)Ställ utvecklingsfrågor.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
