---
title: Översikt över bärbara Azure Synapse Analytics-datorer
description: Den här artikeln innehåller en översikt över de funktioner som är tillgängliga via Azure Synapse Analytics-anteckningsböcker.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093285"
---
# <a name="azure-synapse-analytics-notebooks"></a>Bärbara Azure Synapse Analytics-datorer

En Synapse Studio-anteckningsbok är ett webb gränssnitt där du kan skapa filer som innehåller Live-kod, visualiseringar och text. Bärbara datorer är en bra plats för att verifiera idéer och använda snabba experiment för att få insikter om dina data. 

Med en Azure Synapse Studio-anteckningsbok kan du:

* Kom igång med noll setup-arbete.
* Skydda dina data med inbyggda funktioner för företags säkerhet.
* Analysera data över RAW-format (CSV, txt, JSON osv.), bearbetade fil format (Parquet, delta Lake, ORC osv.) och SQL-datafiler i tabell mot Spark och SQL.
* Var produktiv med förbättrade redigerings funktioner och inbyggd data visualisering.

Det här avsnittet innehåller artiklar om hur du blandar språk, skapar data visualiseringar, parameterstyrda antecknings böcker, skapar pipeliner med mera. Den innehåller också referenser och självstudier om hur du kan komma igång med din bärbara utvecklings miljö.

## <a name="create-manage-and-use-notebooks"></a>Skapa, hantera och Använd antecknings böcker
Du kan hantera antecknings böcker med Synapse Studio-ANVÄNDARGRÄNSSNITTET. 

Mer information om hur du kan skapa och hantera antecknings böcker finns i följande artiklar:
  - Hantera antecknings böcker
    - [Skapa antecknings böcker](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Utveckla notebook-filer](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Hämta data till en bärbar dator](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Använd flera språk med Magic-kommandon och temporära tabeller](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Använda cell Magic-kommandon](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Utveckling
    - [Konfigurera inställningar för Spark-session](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Använda Microsoft Spark-verktyg](./spark/../microsoft-spark-utilities.md)
    - [Visualisera data med antecknings böcker och bibliotek](./spark/../apache-spark-data-visualization.md)
    - [Integrera en bärbar dator i pipelines](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Nästa steg
Antecknings böcker används också ofta i förberedelse av data, data visualisering, maskin inlärning och andra stora data scenarier. Om du vill veta mer om hur du kan använda antecknings böcker för data analys och stora data scenarier kan du gå till följande Självstudier:
  - [Skapa en notebook-fil](./spark/../../quickstart-apache-spark-notebook.md)
  - [Skapa visualiseringar med Synapse Studio-anteckningsböcker](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Bygg maskin inlärnings modeller med Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Bygg maskin inlärnings modeller med Azures automatiserade ML](./spark/../apache-spark-azure-machine-learning-tutorial.md)