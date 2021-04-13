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
ms.date: 04/04/2021
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307902"
---
# <a name="explore-the-synapse-knowledge-center"></a>Utforska Synapse Knowledge Center

I den här självstudien får du lära dig hur du använder Synapse Studio Knowledge Center.

## <a name="introduction-to-the-knowledge-center"></a>Introduktion till kunskaps centret

Det finns två sätt att hitta kunskaps Center i Synapse Studio:

  1. I Home Hub, längst upp till höger på sidan, klickar du på **Lär dig**.
  2. I meny fältet högst upp klickar du på **?** och sedan på **kunskaps Center**.

Välj någon av metoderna och öppna **kunskaps centret**.

När det är synligt ser du att **kunskaps centret** gör det möjligt att göra tre saker:
* **Använd exempel omedelbart**. Välj det här alternativet om du vill ha ett snabbt exempel på hur Synapse fungerar.
* **Bläddra i galleriet**. Med det här alternativet kan du länka exempel data uppsättningar och lägga till exempel kod i formatet SQL-skript, antecknings böcker och pipeliner.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort genom gång av de grundläggande delarna av Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="exploring-use-samples-immediately"></a>Utforska: använda exempel omedelbart

Det finns tre objekt i det här avsnittet:
* Utforska exempel data med Spark
* Fråga efter data med SQL
* Skapa extern tabell med SQL

1. I **kunskaps Center** klickar du på **Använd exempel omedelbart**.
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

> [!div class="nextstepaction"]
> [Lägg till en administratör](get-started-add-admin.md)

