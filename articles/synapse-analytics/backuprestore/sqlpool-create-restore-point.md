---
title: Skapa en användardefinierad återställningspunkt för en dedikerad SQL-pool
description: Lär dig hur du använder Azure Portal för att skapa en användardefinierad återställningspunkt för dedikerad SQL-pool i Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567935"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

I den här artikeln lär du dig att skapa en ny användardefinierad återställningspunkt för en dedikerad SQL-pool i Azure Synapse Analytics med hjälp av Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Skapa användardefinierade återställningspunkter via Azure Portal

Användardefinierade återställningspunkter kan också skapas via Azure Portal.

1. Logga in [](https://portal.azure.com/) på ditt Azure Portal konto.

2. Gå till den dedikerade SQL-pool som du vill skapa en återställningspunkt för.

3. Välj **Översikt** i den vänstra rutan och välj **+ Ny återställningspunkt.** Om knappen Ny återställningspunkt inte är aktiverad kontrollerar du att den dedikerade SQL-poolen inte har pausats.

    ![Ny återställningspunkt](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställningspunkten och klicka på **Tillämpa**. Användardefinierade återställningspunkter har en standardperiod för kvarhållning på sju dagar.

    ![Namn på återställningspunkt](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool](restore-sql-pool.md)

