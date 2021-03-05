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
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171491"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här själv studie kursen måste du ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för. Skapa arbets ytan Synapse i den här resurs gruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com)i Sök fältet och ange **Synapse** utan att trycka på RETUR.
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics**.
1. Välj **Lägg till** för att skapa en arbets yta.
1. Fliken **grunder** , under **projekt information**, Fyll i följande fält:
      1. **Prenumeration** – Välj en prenumeration.
      2. **Resurs grupp** – Använd valfri resurs grupp.
      3. **Resurs grupp** – lämna det här tomt.
1. På fliken **grundläggande** , under **arbets ytans information**, fyller du i följande fält:
      1. **Namn på arbets yta** – Välj ett globalt unikt namn. I den här självstudien använder vi min **arbets yta**.
      1. **Region** – Välj en region.
      1. **Välj Data Lake Storage gen 2**
        1. Klicka på knappen för **från prenumerationen**.
        1. Efter **konto namn** klickar du på **Skapa nytt** och namnger det nya lagrings kontot **contosolake** eller liknar det här namnet måste vara unikt.
        1. Efter **fil system namn** klickar du på **Skapa nytt** och ge den namnet **användare**. Detta skapar en lagrings behållare som heter **Users**. Arbets ytan kommer att använda det här lagrings kontot som det primära lagrings kontot för Spark-tabeller och Spark-programloggarna.
        1. Markera rollen "tilldela själv Storage BLOB data Contributor i Data Lake Storage Gen2 konto". 
1. Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.

> [!NOTE]
> Om du vill aktivera arbets ytans funktioner från en befintlig dedikerad SQL-pool (tidigare SQL DW) läser du så [här aktiverar du en arbets yta för din dedikerade SQL-pool (tidigare SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta [Azure Portal](https://portal.azure.com)i avsnittet **Översikt** på arbets ytan Synapse väljer du **Öppna** i rutan Öppna Synapse Studio.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.


## <a name="the-built-in-serverless-sql-pool"></a>Den inbyggda SQL-poolen utan Server

Varje arbets yta levereras med en fördefinierad Server lös SQL-pool som kallas **inbyggd**. Det går inte att ta bort poolen. SQL-pooler utan Server gör att du kan använda SQL utan att behöva reservera kapacitet med dedikerade SQL-pooler. Till skillnad från dedikerade SQL-pooler baseras faktureringen för en server lös SQL-pool på mängden data som genomsöks för att köra frågan, inte antalet allokerade kapacitet till poolen.


## <a name="create-a-dedicated-sql-pool"></a>Skapa en dedikerad SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler** i det vänstra fönstret.
1. Välj **nytt**
1. Välj **SQLPOOL1** för **SQL-poolnamn**
1. Välj **DW100C** för **prestanda nivå**
1. Välj **Granska + skapa** > **Skapa**. Din dedikerade SQL-pool är klar om några minuter. Din dedikerade SQL-pool är kopplad till en dedikerad SQL-adresspool som också kallas **SQLPOOL1**.

En dedikerad SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

> [!NOTE] 
> När du skapar en ny dedikerad SQL-pool (tidigare SQL DW) på din arbets yta öppnas sidan dedikerad etablering av SQL-pool. Etableringen sker på den logiska SQL-servern.


## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **nytt** 
1. Ange **Spark1** som **namn på Apache Spark pool** .
1. För **Node-storlek** anger du **liten**.
1. För **antal noder** ställer du in minst 3 och maximalt 3
1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

Spark-poolen meddelar Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en dedikerad SQL-pool](get-started-analyze-sql-pool.md)
