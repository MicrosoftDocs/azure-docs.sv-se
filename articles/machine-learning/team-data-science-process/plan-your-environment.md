---
title: Identifiera scenarier och planera analys processen – team data science-processen | Azure Machine Learning
description: Identifiera scenarier och planera för avancerad analys av data behandling genom att beakta en serie viktiga frågor.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322394"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Så här identifierar du scenarier och planerar för avancerade analyser vid databearbetning

Vilka resurser krävs för att du ska kunna skapa en miljö som kan utföra avancerad analys bearbetning på en data uppsättning? Den här artikeln föreslår en rad frågor som kan hjälpa dig att identifiera uppgifter och resurser som är relevanta för ditt scenario.

Läs mer om ordningen på de övergripande stegen för förutsägelse analys i [Vad är TDSP (Team data science process)](overview.md). Varje steg kräver specifika resurser för de uppgifter som är relevanta för ditt specifika scenario.

Besvara viktiga frågor i följande områden för att identifiera ditt scenario:

* data logistik
* data egenskaper
* data mängds kvalitet
* prioriterade verktyg och språk

## <a name="logistic-questions-data-locations-and-movement"></a>Logistiska frågor: data platser och transport

Logistik frågorna avser följande objekt:

* plats för data Källa
* mål mål i Azure
* krav för att flytta data, inklusive schema, mängd och resurser som ingår

Du kan behöva flytta data flera gånger under analys processen. Ett vanligt scenario är att flytta lokala data till någon form av lagring på Azure och sedan till Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Vad är din data Källa?

Är dina data lokalt eller i molnet? Möjliga platser är:

* en offentligt tillgänglig HTTP-adress
* en lokal plats eller nätverks fil plats
* en SQL Server-databas
* en Azure Storage behållare

### <a name="what-is-the-azure-destination"></a>Vad är Azure-målet?

Var måste dina data vara för bearbetning eller modellering? 

* Azure Blob Storage
* SQL Azure-databaser
* SQL Server på virtuell Azure-dator
* HDInsight (Hadoop på Azure) eller Hive-tabeller
* Azure Machine Learning
* Monterings bara virtuella hård diskar i Azure

### <a name="how-are-you-going-to-move-the-data"></a>Hur kommer du att flytta data?

Procedurer och resurser för att mata in eller läsa in data i en mängd olika lagrings-och bearbetnings miljöer finns i:

* [Läs in data i lagringsmiljöer för analys](ingest-data.md)
* [Importera dina utbildnings data till Azure Machine Learning Studio (klassisk) från olika data källor](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Måste data flyttas enligt ett reguljärt schema eller ändras under migreringen?

Överväg att använda Azure Data Factory (ADF) när data måste migreras kontinuerligt. ADF kan vara till hjälp för att:

* ett hybrid scenario som omfattar både lokala och molnbaserade resurser
* ett scenario där data har överförts, modifierats eller ändrats av affärs logik i samband med migreringen

Mer information finns i [Flytta data från en SQL Server databas till SQL Azure med Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Hur mycket data ska flyttas till Azure?

Stora data uppsättningar kan överstiga lagrings kapaciteten för vissa miljöer. Ett exempel finns i diskussionen om storleks gränser för Machine Learning Studio (klassisk) i nästa avsnitt. I sådana fall kan du använda ett exempel på data under analysen. Mer information om hur du desamplar en data uppsättning i olika Azure-miljöer finns i [exempel data i team data science process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Frågor om data egenskaper: typ, format och storlek

Dessa frågor är viktiga för att planera lagrings-och bearbetnings miljöer. De hjälper dig att välja rätt scenario för din datatyp och förstå eventuella begränsningar.

### <a name="what-are-the-data-types"></a>Vilka är data typerna?

* Numeriska
* Kategoriska
* Strängar
* Binär

### <a name="how-is-your-data-formatted"></a>Hur formateras dina data?

* Kommaavgränsade (CSV) eller tabbavgränsade (tabbavgränsade) flata filer
* Komprimerad eller okomprimerad
* Azure-blobbar
* Hadoop Hive-tabeller
* SQL Server tabeller

### <a name="how-large-is-your-data"></a>Hur stora är dina data?

* Liten: mindre än 2 GB
* Medel: större än 2 GB och mindre än 10 GB
* Stor: större än 10 GB

Ta den Azure Machine Learning Studio (klassiska) miljön till exempel:

* En lista över de data format och typer som stöds av Azure Machine Learning Studio finns i avsnittet [data format och data typer som stöds](../classic/import-data.md#supported-data-formats-and-data-types) .
* Information om begränsningarna för andra Azure-tjänster som används i analys processen finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Data kvalitets frågor: utforskning och för bearbetning

### <a name="what-do-you-know-about-your-data"></a>Vad vet du om dina data?

Förstå grundläggande egenskaper för dina data:

* Vilka mönster eller trender som det visar
* Vad avviker
* Hur många värden som saknas

Det här steget är viktigt för att hjälpa dig:

* Ta reda på hur mycket för bearbetning som behövs
* Formulera Hypotheses som föreslår de lämpligaste funktionerna eller typen av analys
* Formulera planer för ytterligare data insamling

Användbara tekniker för data granskning innehåller beskrivande statistik beräkning och visualiserings områden. Mer information om hur du utforskar en data uppsättning i olika Azure-miljöer finns i [utforska data i team data science process](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Kräver data för bearbetning eller rensning?

Du kan behöva Förbearbeta och rensa dina data innan du kan använda data uppsättningen effektivt för Machine Learning. Rå data är ofta störningar och är otillförlitliga. Det kan vara saknade värden. Att använda sådana data för modellering kan producera missvisande resultat. För en beskrivning, se [uppgifter för att förbereda data för förbättrad maskin inlärning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Frågor om verktyg och språk

Det finns många alternativ för språk, utvecklings miljöer och verktyg. Tänk på dina behov och önskemål.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Vilka språk vill du använda för analys?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Vilka verktyg bör du använda för data analys?

* [Microsoft Azure PowerShell](/powershell/azure/) – ett skript språk som används för att administrera dina Azure-resurser i ett skript språk
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution analys](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools för Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter-anteckningsböcker](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifiera ditt avancerade analys scenario

När du har besvarat frågorna i föregående avsnitt är du redo att avgöra vilket scenario som passar bäst för ditt ärende. Exempel scenarierna beskrivs i [scenarier för avancerad analys i Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är TDSP (Team Data Science Process)?](overview.md)