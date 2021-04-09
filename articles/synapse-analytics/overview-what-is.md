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
ms.openlocfilehash: 78b31cb32e3df9b0d8e198d8c2122e492e609283
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625826"
---
# <a name="what-is-azure-synapse-analytics"></a>Vad är Azure Synapse Analytics?

**Azure Synapse** är en Enterprise Analytics-tjänst som påskyndar tiden för att bli inblick i informations lager och stora data system. Azure Synapse kombinerar det bästa av **SQL** -teknikerna som används i företags data lager, **Spark** -tekniker som används för Big data, **pipelines** för data integrering och ETL/ELT och djupgående integrering med andra Azure-tjänster som **Power BI**, **CosmosDB** och **azureml**.

![Diagram över Azure Synapse Analytics-arkitekturen.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Branschledande SQL

**SYNAPSE SQL** är ett distribuerat Query-system för t-SQL som möjliggör data lager-och data visualiserings scenarier och utökar T-SQL för att hantera strömning och maskin inlärnings scenarier.

* Synapse SQL erbjuder både **Server** lös och **dedikerade** resurs modeller. För förutsägbara prestanda och kostnader skapar du dedikerade SQL-pooler för att reservera beräknings för data som lagras i SQL-tabeller. För oplanerade eller burst-arbetsbelastningar använder du den alltid tillgängliga, Server lös SQL-slutpunkten.
* Använd inbyggda **strömnings** funktioner för att landa data från moln data källor till SQL-tabeller
* Integrera AI med SQL genom att använda **maskin inlärnings** modeller för att räkna data med hjälp av [funktionen T-SQL predict](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Bransch standard Apache Spark

**Apache Spark för Azure Synapse** djup och sömlöst integrera Apache Spark – den mest populära Big data motorn med öppen källkod som används för förberedelse av data, data teknik, ETL och maskin inlärning.

* ML-modeller med SparkML-algoritmer och AzureML-integrering för Apache Spark 2,4 med inbyggt stöd för Linux Foundation delta Lake.
* Förenklad resurs modell som gör att du inte behöver oroa dig för att hantera kluster.
* Snabb start av Spark och aggressiv automatisk skalning.
* Inbyggt stöd för .NET för Spark gör att du kan återanvända din C#-expertis och befintliga .NET-kod i ett Spark-program.

## <a name="working-with-your-data-lake"></a>Arbeta med din Data Lake

Azure Synapse tar bort de traditionella teknik barriärerna mellan att använda SQL och Spark tillsammans. Du kan kombinera och matcha på ett smidigt sätt utifrån dina behov och kunskaper.

* Ett delat Hive-kompatibelt metadata system gör det möjligt för tabeller som definierats på filer i data Lake att vara sömlöst förbrukade antingen av Spark eller Hive.
* SQL och Spark kan direkt utforska och analysera Parquet-, CSV-, TSV-och JSON-filer som lagras i data Lake.
* Snabb skalbar belastning och borttagning av data som går mellan SQL-och Spark-databaser

## <a name="built-in-data-integration"></a>Inbyggd data integrering

Azure Synapse innehåller samma data integrerings motor och upplevelser som Azure Data Factory, så att du kan skapa omfattande ETL-pipeliner utan att lämna Azure Synapse Analytics.

* Mata in data från 90 + data källor
* Code-Free ETL med data flödes aktiviteter
* Dirigera antecknings böcker, Spark-jobb, lagrade procedurer, SQL-skript med mera

## <a name="unified-management-monitoring-and-security"></a>Enhetlig hantering, övervakning och säkerhet

Azure Synapse erbjuder ett enda sätt för företag att hantera analys resurser, övervaka användning och aktivitet och upprätthålla säkerhet.

* Tilldela användare till roll för att förenkla åtkomst till analys resurser
* Detaljerad åtkomst kontroll för data och kod
* En enda instrument panel för att övervaka resurser, användning och användare i SQL och Spark

## <a name="unified-experience"></a>Enhetlig upplevelse

**Synapse Studio** är användar upplevelsen som kopplar ihop allt för data tekniker. Det gör att de kan göra varje uppgift som de behöver för att skapa en komplett analys lösning.

* Viktiga data ingenjörs uppgifter på en plats: mata in, utforska, förbereda, dirigera, visualisera
* Branschledande produktivitet för att skriva SQL-eller Spark-kod: redigering, fel sökning och prestanda optimering
* Integrera med Enterprise CI/CD process

## <a name="engage-with-the-synapse-engineering-team"></a>Engagera dig med Synapse Engineering-teamet

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Ställ frågor om utveckling.
- [Sidan Microsoft Q&en fråga](/answers/topics/azure-synapse-analytics.html): Ställ tekniska frågor.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
