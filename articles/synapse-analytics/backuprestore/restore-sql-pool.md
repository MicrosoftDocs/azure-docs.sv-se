---
title: Återställa en befintlig dedikerad SQL-pool
description: Instruktions guide för att återställa en befintlig dedikerad SQL-pool.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94332096"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Återställa en befintlig dedikerad SQL-pool

I den här artikeln får du lära dig hur du återställer en befintlig dedikerad SQL-pool i Azure Synapse Analytics med hjälp av Azure Portal och Synapse Studio. Den här artikeln gäller både återställning och geo-Restore. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Återställa en befintlig dedikerad SQL-pool via Synapse Studio

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till din Synapse-arbetsyta. 
3. Under Komma igång-> öppna Synapse Studio väljer du **Öppna**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. I det vänstra navigerings fönstret väljer du **data**.
5. Välj **hantera pooler**. 
6. Välj **+ ny** för att skapa en ny dedikerad SQL-pool. 
7. På fliken Ytterligare inställningar väljer du en återställnings punkt att återställa från. 

    Om du vill utföra en geo-återställning väljer du den arbets yta och dedikerad SQL-pool som du vill återställa. 

8. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**. 

    ![Återställnings punkter](../media/sql-pools/restore-point.PNG)

    Om den dedikerade SQL-poolen inte har några automatiska återställnings punkter väntar du några timmar eller skapar en användardefinierad återställnings punkt innan du återställer. För User-Defined återställnings punkter väljer du en befintlig eller skapar en ny.

    Om du återställer en geo-säkerhetskopiering väljer du bara den arbets yta som finns i käll regionen och den dedikerade SQL-pool som du vill återställa. 

9. Välj **Granska + skapa**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Återställa en befintlig dedikerad SQL-pool via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till den dedikerade SQL-pool som du vill återställa från.
3. Överst på bladet översikt väljer du **Återställ**.

    ![ Återställa översikt](../media/sql-pools/restore-sqlpool-01.png)

4. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**. 

    Om den dedikerade SQL-poolen inte har några automatiska återställnings punkter väntar du några timmar eller skapar en användardefinierad återställnings punkt innan du återställer. 

    Om du vill utföra en geo-återställning väljer du den arbets yta och dedikerad SQL-pool som du vill återställa. 

5. Välj **Granska + skapa**.

## <a name="next-steps"></a>Nästa steg

- [Skapa en återställningspunkt](sqlpool-create-restore-point.md)
