---
title: 'Självstudie: kom igång utforska Synapse Knowledge Center'
description: I den här självstudien får du lära dig hur du använder Synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209210"
---
# <a name="explore-the-synapse-knowledge-center"></a>Utforska Synapse Knowledge Center

I den här självstudien får du lära dig hur du använder Synapse Studio Knowledge Center.

## <a name="getting-to-the-knowledge-center"></a>Komma till kunskaps centret

Det finns två sätt att hitta kunskaps Center i Synapse Studio:

  1. I Home Hub, längst upp till höger på sidan, klickar du på **Lär dig**.
  2. I meny fältet högst upp klickar du på **?** och sedan på **kunskaps Center**.

Välj någon av metoderna och öppna **kunskaps centret**.

## <a name="overview"></a>Översikt

I **kunskaps centret** kan du göra tre saker:
* **Använd exempel omedelbart**. Välj det här alternativet om du vill ha ett snabbt exempel på hur Synapse fungerar.
* **Bläddra i galleriet**. Med det här alternativet kan du länka exempel data uppsättningar och lägga till exempel kod i formatet SQL-skript, antecknings böcker och pipeliner.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort genom gång av de grundläggande delarna av Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Utforska Blob Storage med Server lös SQL-pool

1. Gå till **kunskaps centret** och klicka på **Använd exempel omedelbart**.
1. Välj **fråga data med SQL**.
1. Klicka på **Använd exempel**.
1. Ett nytt exempel på SQL-skript öppnas.
1. Bläddra till den första frågan (rader 28 till 32) och markera frågetexten.
1. Klicka på Kör. Den kommer bara att köra den kod som du har valt.

## <a name="loading-more-nyc-taxi-data"></a>Läser in fler NYC taxi-data
1. Gå till **kunskaps centret**, klicka på **Bläddra i galleriet**.
1. Välj fliken **SQL-skript** längst upp.
1. Välj **Läs in det nya taxidata** data inmatnings exemplet för Göteborg. Klicka på **Fortsätt**.
1. Under **SQL-pool** väljer du **Välj en befintlig pool** och väljer **SQLPOOL1** och väljer **SQLPOOL1** -databasen som du skapade tidigare.
1. Klicka på **Öppna skript**.
1. Ett nytt exempel på SQL-skript öppnas.
1. Klicka på **Kör**
1. Detta skapar flera tabeller för alla NYC taxi-data och läser in dem med hjälp av kommandot T-SQL COPY. Om du har skapat tabellerna i föregående snabb starts steg väljer och kör du endast kod för att skapa och kopiera för tabeller som inte finns.

    > [!NOTE] 
    > När du använder exempel galleriet för SQL-skript med en dedikerad SQL-pool (tidigare SQL DW) kommer du bara att kunna använda en befintlig dedikerad SQL-pool (tidigare SQL DW).

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
