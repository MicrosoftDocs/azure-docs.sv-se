---
title: Återställa en befintlig dedikerad SQL-pool
description: Så här återställer du en befintlig dedikerad SQL-pool.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567986"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Återställa en befintlig dedikerad SQL-pool

I den här artikeln får du lära dig hur du återställer en befintlig dedikerad SQL-pool i Azure Synapse Analytics med Azure Portal och Synapse Studio. Den här artikeln gäller både återställningar och geo-återställningar. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Återställa en befintlig dedikerad SQL-pool via Synapse Studio

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till Synapse-arbetsytan. 
3. Under Komma igång -> Öppna Synapse Studio väljer du **Öppna**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Välj Data i det vänstra **navigeringsfönstret.**
5. Välj **Hantera pooler.** 
6. Välj **+ Ny för** att skapa en ny dedikerad SQL-pool. 
7. På fliken Ytterligare inställningar väljer du en återställningspunkt att återställa från. 

    Om du vill utföra en geo-återställning väljer du arbetsytan och den dedikerade SQL-pool som du vill återställa. 

8. Välj **antingen Automatiska återställningspunkter** **eller Användardefinierade återställningspunkter.** 

    ![Återställningspunkter](../media/sql-pools/restore-point.PNG)

    Om den dedikerade SQL-poolen inte har några automatiska återställningspunkter väntar du några timmar eller skapar en användardefinierad återställningspunkt innan du återställer. Om User-Defined en återställningspunkter väljer du en befintlig eller skapar en ny.

    Om du återställer en geo-säkerhetskopia väljer du helt enkelt arbetsytan som finns i källregionen och den dedikerade SQL-pool som du vill återställa. 

9. Välj **Granska + skapa**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Återställa en befintlig dedikerad SQL-pool via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till den dedikerade SQL-pool som du vill återställa från.
3. Längst upp på bladet Översikt väljer du **Återställ**.

    ![ Återställa översikt](../media/sql-pools/restore-sqlpool-01.png)

4. Välj **antingen Automatiska återställningspunkter** **eller Användardefinierade återställningspunkter.** 

    Om den dedikerade SQL-poolen inte har några automatiska återställningspunkter väntar du några timmar eller skapar en användardefinierad återställningspunkt innan du återställer. 

    Om du vill utföra en geo-återställning väljer du arbetsytan och den dedikerade SQL-pool som du vill återställa. 

5. Välj **Granska + skapa**.

## <a name="next-steps"></a>Nästa steg

- [Skapa en återställningspunkt](sqlpool-create-restore-point.md)
