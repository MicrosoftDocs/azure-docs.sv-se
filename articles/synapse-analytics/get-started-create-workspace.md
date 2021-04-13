---
title: 'Snabbstart: Kom igång – skapa en Synapse-arbetsyta'
description: I den här självstudien lär du dig att skapa en Synapse-arbetsyta, en dedikerad SQL-pool och en serverlös Apache Spark pool.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: b22954edf4f3a5a935c470326aa43bd24ee2d708
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366070"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien lär du dig att skapa en Synapse-arbetsyta, en dedikerad SQL-pool och en serverlös Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien måste du ha åtkomst till en resursgrupp som du har tilldelats **rollen** Ägare för. Skapa Synapse-arbetsytan i den här resursgruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

### <a name="start-the-process"></a>Starta processen
1. Öppna [Azure Portal](https://portal.azure.com), i sökfältet anger du **Synapse** utan att trycka på Retur.
1. I sökresultaten går du till **Tjänster** och väljer **Azure Synapse Analytics**.
1. Välj **Lägg till** för att skapa en arbetsyta.

## <a name="basics-tab--project-details"></a>Fliken Grundläggande inställningar > projektinformation
Fyll i följande fält:

1. **Prenumeration** – Välj valfri prenumeration.
1. **Resursgrupp** – Använd valfri resursgrupp.
1. **Hanterad resursgrupp** – Lämna det tomt.

## <a name="basics-tab--workspace-details"></a>Fliken Grundläggande inställningar > arbetsyteinformation
Fyll i följande fält:

1. **Namn på arbetsyta** – Välj ett globalt unikt namn. I den här självstudien använder vi **myworkspace**.
1. **Region** – Välj valfri region.

Under **Välj Data Lake Storage Gen 2:**

1. Efter **Kontonamn** väljer du **Skapa nytt** och ger det nya **lagringskontot namnet contosolake** eller liknande eftersom namnet måste vara unikt.
1. Efter **Filnamn väljer du** Skapa ny **och** ger den namnet **användare**. Detta skapar en lagringscontainer med namnet **users**. Arbetsytan använder det här lagringskontot som "primärt" lagringskonto för Spark-tabeller och Spark-programloggar.
1. Markera rutan "Tilldela mig rollen Storage Blob Data-deltagare Data Lake Storage Gen2 lagringskonto". 

## <a name="completing-the-process"></a>Slutföra processen
Välj **Granska + skapa** > **Skapa**. Arbetsytan är klar på några minuter.

> [!NOTE]
> Om du vill aktivera arbetsytefunktioner från en befintlig dedikerad SQL-pool (tidigare SQL DW) kan du gå till Aktivera en arbetsyta för din dedikerade [SQL-pool (tidigare SQL DW).](./sql-data-warehouse/workspace-connected-create.md)


## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När arbetsytan Azure Synapse har skapats har du två sätt att öppna Synapse Studio:

* Öppna Synapse-arbetsytan [i Azure Portal](https://portal.azure.com). I  avsnittet Översikt på Synapse-arbetsytan väljer du Öppna i rutan Öppna Synapse Studio. 
* Gå till och `https://web.azuresynapse.net` logga in på din arbetsyta.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Placera exempeldata i det primära lagringskontot
Vi ska använda en liten exempeldatamängd på 100 000 rader med NYX Taxi Cab-data för många exempel i den här kom igång-guiden. Vi börjar med att placera den i det primära lagringskonto som du skapade för arbetsytan.

* Ladda ned den här filen till datorn: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* I Synapse Studio navigerar du till datahubben. 
* Välj **Länkad**.
* Under kategorin **Azure Data Lake Storae Gen2** visas ett objekt med ett namn som **myworkspace ( Primary - contosolake )**.
* Välj containern med **namnet användare (primär).**
* Välj **Ladda** upp och välj den `NYCTripSmall.parquet` fil som du laddade ned.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med hjälp av en serverlös SQL-pool](get-started-analyze-sql-on-demand.md)
