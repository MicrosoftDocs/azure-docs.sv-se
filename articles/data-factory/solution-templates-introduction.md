---
title: Översikt över mallar
description: Lär dig hur du använder en fördefinierad mall för att komma igång snabbt med Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375399"
---
# <a name="templates"></a>Mallar

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mallar är fördefinierade Azure Data Factory pipelines som gör att du snabbt kommer igång med Data Factory. Mallar är användbara när du är nybörjare på Data Factory och vill komma igång snabbt. Dessa mallar minskar utvecklings tiden för att skapa data integrerings projekt och därigenom förbättra produktiviteten för utvecklare.

## <a name="create-data-factory-pipelines-from-templates"></a>Skapa Data Factory pipelines från mallar

Du kan komma igång med att skapa en Data Factory pipeline från en mall på följande två sätt:

1.  Välj **skapa pipeline från mall** på sidan Översikt för att öppna mallgalleriet.

    ![Öppna mallgalleriet från översikts Sidan](media/solution-templates-introduction/templates-intro-image1.png)

1.  På fliken författare i Resursläsaren väljer du **+** och sedan **pipeline från mallen** för att öppna mallgalleriet.

    ![Öppna mallgalleriet från fliken författare](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Mallgalleriet

![Mallgalleriet](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>I rutan Data Factory mallar

Data Factory använder Azure Resource Manager mallar för att spara mallar för Data Factory-pipeline. Du kan se alla Resource Manager-mallar, tillsammans med manifest filen som används för Data Factory mallar, i [officiella Azure Data Factory GitHub lagrings platsen](https://github.com/Azure/Azure-DataFactory/tree/master/templates). De fördefinierade mallar som tillhandahålls av Microsoft inkluderar men är inte begränsade till följande objekt:

-   Kopiera mallar:

    -   [Mass kopiering från databas](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopiera nya filer genom LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopiera flera fil behållare mellan filbaserade Arkiv](solution-template-copy-files-multiple-containers.md)

    -   [Flytta filer](solution-template-move-files.md)

    -   [Delta kopia från databas](solution-template-delta-copy-with-control-table.md)

    -   Kopiera från \<source\> till \<destination\>

        -   [Från Amazon S3 till Azure Data Lake Store gen 2](solution-template-migration-s3-azure.md)

        -   Från Google stor fråga till Azure Data Lake Store gen 2

        -   Från HDF till Azure Data Lake Store gen 2

        -   Från Netezza till Azure Data Lake Store gen 1

        -   Från SQL Server lokalt till Azure SQL Database

        -   Från SQL Server lokalt till Azure Synapse Analytics

        -   Från Oracle lokalt till Azure Synapse Analytics

-   SSIS-mallar

    -   Schemalägg Azure-SSIS Integration Runtime för att köra SSIS-paket

-   Transformera mallar

    -   [ETL med Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mina mallar

Du kan också spara en pipeline som en mall genom att välja **Spara som mall** på fliken pipeline.

![Spara en pipeline som en mall](media/solution-templates-introduction/templates-intro-image4.png)

Du kan visa pipeliner som har sparats som mallar i avsnittet **Mina mallar** i mallgalleriet. Du kan också se dem i avsnittet **mallar** i Resursläsaren.

![Mina mallar](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Om du vill använda funktionen Mina mallar måste du aktivera GIT-integrering. Både Azure DevOps GIT och GitHub stöds.
