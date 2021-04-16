---
title: 'Självstudie: Kom igång med att utforska Synapse Knowledge Center'
description: I den här självstudien lär du dig att använda Synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517359"
---
# <a name="explore-the-synapse-knowledge-center"></a>Utforska Synapse-Kunskapscenter

I den här självstudien lär du dig att använda Synapse Studio Knowledge Center.

## <a name="finding-to-the-knowledge-center"></a>Hitta till Kunskapscenter

Det finns två sätt att hitta kunskapscentret i Synapse Studio:

  1. Klicka på Lär dig längst upp till höger på sidan i **starthubben.**
  2. I menyraden längst upp klickar du på **?** och sedan **Knowledge Center**.

Välj någon av metoderna och öppna **Knowledge Center**.

## <a name="exploring-the-knowledge-center"></a>Utforska Kunskapscenter

När det är synligt ser du att **Kunskapscenter** du kan göra tre saker:
* **Använd exempel omedelbart**. Om du vill ha ett snabbt exempel på hur Synapse fungerar väljer du det här alternativet.
* **Bläddra i galleriet**. Med det här alternativet kan du länka exempeldatauppsättningar och lägga till exempelkod i form av SQL-skript, notebook-datorer och pipelines.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort rundtur i de grundläggande delarna Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Använd exempel direkt: Tre exempel som hjälper dig att komma igång snabbt

Det finns tre objekt i det här avsnittet:
* Utforska exempeldata med Spark
* Fråga efter data med SQL
* Skapa extern tabell med SQL

1. I den **Kunskapscenter** du på **Använd exempel direkt.**
1. Välj **Fråga efter data med SQL**.
1. Klicka **på Använd exempel**.
1. Ett nytt SQL-exempelskript öppnas.
1. Bläddra till den första frågan (raderna 28 till 32) och välj frågetexten.
1. Klicka på Kör. Den kör bara den kod som du har valt.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Galleri: En insamling av exempeldatauppsättningar och exempelkod

1. Gå till **Kunskapscenter och klicka** på Bläddra i **galleriet**.
1. Välj **fliken SQL-skript** högst upp.
1. Välj Load the New York Taxicab dataset Data ingestion sample (Läs in **Datainmatningsexempel för New York Taxicab)** och klicka **på Fortsätt.**
1. Under **SQL-pool** väljer **du Välj en befintlig pool** och sedan **SQLPOOL1** och sedan **den SQLPOOL1-databas** som du skapade tidigare.
1. Klicka **på Öppna skript.**
1. Ett nytt SQL-exempelskript öppnas.
1. Klicka på **Kör**
1. Detta skapar flera tabeller för alla NYC Taxi-data och läser in dem med hjälp av T-SQL COPY-kommandot. Om du har skapat dessa tabeller i föregående snabbstartssteg väljer du och kör endast kod för att SKAPA och KOPIERA för tabeller som inte finns.

    > [!NOTE] 
    > När du använder exempelgalleriet för SQL-skript med en dedikerad SQL-pool (tidigare SQL DW) kan du bara använda en befintlig dedikerad SQL-pool (tidigare SQL DW).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en administratör](get-started-add-admin.md)

