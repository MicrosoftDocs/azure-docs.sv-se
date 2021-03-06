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
ms.openlocfilehash: 4b7251be220c012ca51970863ac2eed55d46d711
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751156"
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
1. **Region** – Välj den region där du har placerat dina klientprogram/tjänster (till exempel virtuell Azure-dator, Power BI, Azure Analysis Service) och lagringar som innehåller data (till exempel Azure Data Lake-lagring Azure Cosmos DB analyslagring).

> [!NOTE]
> En arbetsyta som inte är samplacerad med klientprogram eller lagring kan vara grundorsaken till många prestandaproblem. Om dina data eller klienterna placeras i flera regioner kan du skapa separata arbetsytor i olika regioner tillsammans med dina data och klienter.

Under **Välj Data Lake Storage Gen 2:**

1. Efter **Kontonamn** väljer du **Skapa nytt** och ger det nya **lagringskontot namnet contosolake** eller liknande eftersom namnet måste vara unikt.
1. Efter **Filnamn väljer du** Skapa ny **och** ger den namnet **användare**. Detta skapar en lagringscontainer med namnet **users**. Arbetsytan använder det här lagringskontot som "primärt" lagringskonto till Spark-tabeller och Spark-programloggar.
1. Markera rutan "Tilldela mig rollen Storage Blob Data-deltagare Data Lake Storage Gen2 lagringskonto". 

## <a name="completing-the-process"></a>Slutföra processen
Välj **Granska + skapa** > **Skapa**. Arbetsytan är klar om några minuter.

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
* Under kategorin **Azure Data Lake Storage Gen2** visas ett objekt med ett namn som **myworkspace ( Primary - contosolake ).**
* Välj containern med **namnet användare (primär).**
* Välj **Ladda** upp och välj den `NYCTripSmall.parquet` fil som du laddade ned.

En parquet-fil laddas upp och är tillgänglig via två motsvarande URI:er:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

I exemplen som följer i den här självstudien ersätter du **contosolake** i användargränssnittet med namnet på det primära lagringskonto som du valde för din arbetsyta.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med hjälp av en serverlös SQL-pool](get-started-analyze-sql-on-demand.md)
