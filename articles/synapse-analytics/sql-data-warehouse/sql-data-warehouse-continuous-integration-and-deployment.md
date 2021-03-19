---
title: Kontinuerlig integrering och distribution för dedikerad SQL-pool
description: Databas DevOps-upplevelse i företags klass för dedikerad SQL-pool i Azure Synapse Analytics med inbyggt stöd för kontinuerlig integrering och distribution med hjälp av Azure-pipeliner.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d43039c98e991cd23a8e5c4fb866a8e3dcab6fc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585646"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Kontinuerlig integrering och distribution för dedikerad SQL-pool i Azure Synapse Analytics

Den här enkla själv studie kursen beskriver hur du integrerar SQL Server ditt SSDT-databas projekt med Azure DevOps och utnyttjar Azure-pipeliner för att konfigurera kontinuerlig integrering och distribution. Den här självstudien är det andra steget i att skapa en pipeline för kontinuerlig integrering och distribution för data lager.

## <a name="before-you-begin"></a>Innan du börjar

- Gå igenom [självstudien för käll kontrolls integrering](sql-data-warehouse-source-control-integration.md)

- Konfigurera och Anslut till Azure-DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Kontinuerlig integrering med Visual Studio build

1. Navigera till Azure-pipeliner och skapa en ny versions pipeline.

      ![Ny pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Ny pipeline")

2. Välj din käll kods lagrings plats (Azure databaser git) och välj mallen .NET Desktop app.

      ![Pipeline-installation](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pipeline-installation")

3. Redigera din YAML-fil och Använd rätt pool för din agent. Din YAML-fil bör se ut ungefär så här:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Nu har du en enkel miljö där all incheckning av huvud grenen för käll kontroll lagret ska utlösa en lyckad Visual Studio-version av ditt databas projekt automatiskt. Verifiera att automatiseringen fungerar och slutar genom att göra en ändring i ditt lokala databas projekt och kontrol lera ändringen i huvud grenen.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Kontinuerlig distribution med distributions uppgiften Azure Synapse Analytics (eller Database)

1. Lägg till en ny aktivitet med [Azure SQL Database distributions uppgift](/azure/devops/pipelines/targets/azure-sqldb) och fyll i de obligatoriska fälten för att ansluta till mål informations lagret. När den här aktiviteten körs distribueras DACPAC som genererades från den tidigare Bygg processen till mål informations lagret. Du kan också använda [distributions uppgiften för Azure Synapse Analytics](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Distributions aktivitet](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Distributions aktivitet")

2. Om du använder en lokal agent, se till att du ställer in din miljö variabel så att den använder rätt SqlPackage.exe för Azure Synapse Analytics. Sökvägen bör se ut ungefär så här:

      ![Miljö variabel](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Miljö variabel")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Kör och verifiera din pipeline. Du kan göra ändringar lokalt och kontrol lera ändringar i din käll kontroll som ska generera en automatisk version och distribution.

## <a name="next-steps"></a>Nästa steg

- Utforska en [dedikerad SQL-pool (tidigare SQL DW)-arkitektur](massively-parallel-processing-mpp-architecture.md)
- [Skapa en dedikerad SQL-pool snabbt (tidigare SQL DW)](create-data-warehouse-portal.md)
- [Läsa in exempeldata](./load-data-from-azure-blob-storage-using-copy.md)
- Utforska [videor](sql-data-warehouse-videos.md)