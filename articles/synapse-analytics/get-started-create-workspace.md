---
title: 'Snabb start: kom igång – skapa en Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 695ad1463bb207e9dd61d7b0147adbe15b44df37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108068"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här själv studie kursen måste du ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för. Skapa arbets ytan Synapse i den här resurs gruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

### <a name="start-the-process"></a>Starta processen
1. Öppna [Azure Portal](https://portal.azure.com)i Sök fältet och ange **Synapse** utan att trycka på RETUR.
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics**.
1. Välj **Lägg till** för att skapa en arbets yta.

## <a name="basics-tab--project-details"></a>Fliken grundläggande > projekt information
Fyll i följande fält:
    1. **Prenumeration** – Välj en prenumeration.
    1. **Resurs grupp** – Använd valfri resurs grupp.
    1. **Resurs grupp** – lämna det här tomt.


## <a name="basics-tab--workspace-details"></a>Fliken grunder > information om arbets ytan
Fyll i följande fält: 1. **Namn på arbets yta** – Välj ett globalt unikt namn. I den här självstudien använder vi min **arbets yta**.
1. **Region** – Välj en region.
1. **Välj Data Lake Storage gen 2** 81.1. Klicka på knappen för **från prenumerationen**.
1. Efter **konto namn** klickar du på **Skapa nytt** och namnger det nya lagrings kontot **contosolake** eller liknar det här namnet måste vara unikt.
1. Efter **fil system namn** klickar du på **Skapa nytt** och ge den namnet **användare**. Detta skapar en lagrings behållare som heter **Users**. Arbets ytan kommer att använda det här lagrings kontot som det primära lagrings kontot för Spark-tabeller och Spark-programloggarna.
1. Markera rollen "tilldela själv Storage BLOB data Contributor i Data Lake Storage Gen2 konto". 

## <a name="completing-the-process"></a>Processen slutförs
Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.

> [!NOTE]
> Om du vill aktivera arbets ytans funktioner från en befintlig dedikerad SQL-pool (tidigare SQL DW) läser du så [här aktiverar du en arbets yta för din dedikerade SQL-pool (tidigare SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta [Azure Portal](https://portal.azure.com)i avsnittet **Översikt** på arbets ytan Synapse väljer du **Öppna** i rutan Öppna Synapse Studio.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.











## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en server lös SQL-pool](get-started-analyze-sql-on-demand.md)
