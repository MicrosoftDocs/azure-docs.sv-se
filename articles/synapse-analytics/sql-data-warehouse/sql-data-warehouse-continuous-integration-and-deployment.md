---
title: Kontinuerlig integrering och distribution för dedikerad SQL-pool
description: Database DevOps-upplevelse i företagsklass för dedikerad SQL-pool i Azure Synapse Analytics med inbyggt stöd för kontinuerlig integrering och distribution med Hjälp av Azure Pipelines.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568292"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Kontinuerlig integrering och distribution för dedikerad SQL-pool i Azure Synapse Analytics

Den här enkla självstudien beskriver hur du integrerar ditt SQL Server Data Tools-databasprojekt (SSDT) med Azure DevOps och använder Azure Pipelines för att konfigurera kontinuerlig integrering och distribution. Den här självstudien är det andra steget i att skapa en pipeline för kontinuerlig integrering och distribution för informationslager.

## <a name="before-you-begin"></a>Innan du börjar

- Gå igenom [självstudien om källkontrollsintegrering](sql-data-warehouse-source-control-integration.md)

- Konfigurera och ansluta till Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Kontinuerlig integrering med Visual Studio build

1. Gå till Azure Pipelines och skapa en ny bygg-pipeline.

      ![Ny pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Ny pipeline")

2. Välj din källkodslagringsplats (Azure Repos Git) och välj appmallen .NET Desktop.

      ![Pipelineinstallation](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pipelineinstallation")

3. Redigera YAML-filen för att använda rätt pool för din agent. YAML-filen bör se ut ungefär så här:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Nu har du en enkel miljö där alla incheckningar till källkontrolldatabasens huvudgren automatiskt utlöser en lyckad Visual Studio av databasprojektet. Verifiera att automatiseringen fungerar från slut till slut genom att göra en ändring i det lokala databasprojektet och checka in ändringen i din main-gren.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Kontinuerlig distribution med Azure Synapse Analytics (eller databas)-distributionsuppgiften

1. Lägg till en ny aktivitet med [hjälp Azure SQL Database aktivitetsdistributionsaktiviteten](/azure/devops/pipelines/targets/azure-sqldb) och fyll i de obligatoriska fälten för att ansluta till målinformationslagret. När den här uppgiften körs distribueras DACPAC som genererades från den tidigare byggprocessen till målinformationslagret. Du kan också använda [Azure Synapse Analytics distributionsaktiviteten](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Distributionsuppgift](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Distributionsuppgift")

2. Om du använder en lokala agent måste du ange miljövariabeln så att den använder rätt SqlPackage.exe för Azure Synapse Analytics. Sökvägen bör se ut ungefär så här:

      ![Miljövariabel](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Miljövariabel")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Kör och verifiera din pipeline. Du kan göra ändringar lokalt och checka in ändringar i källkontrollen som ska generera en automatisk version och distribution.

## <a name="next-steps"></a>Nästa steg

- Utforska [arkitekturen för dedikerad SQL-pool (tidigare SQL DW)](massively-parallel-processing-mpp-architecture.md)
- Skapa [snabbt en dedikerad SQL-pool (tidigare SQL DW)](create-data-warehouse-portal.md)
- [Läsa in exempeldata](./load-data-from-azure-blob-storage-using-copy.md)
- Utforska [videor](sql-data-warehouse-videos.md)