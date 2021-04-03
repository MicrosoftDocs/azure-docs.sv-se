---
title: Skapa en användardefinierad återställnings punkt för en dedikerad SQL-pool
description: Lär dig hur du använder Azure Portal för att skapa en användardefinierad återställnings punkt för dedikerad SQL-pool i Azure Synapse Analytics.
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
ms.openlocfilehash: 21fd20100095040fda9f72b00e17147ff560fbca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94579546"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställnings punkter

I den här artikeln lär du dig att skapa en ny användardefinierad återställnings punkt för en dedikerad SQL-pool i Azure Synapse Analytics med hjälp av Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Skapa användardefinierade återställnings punkter via Azure Portal

Användardefinierade återställnings punkter kan också skapas via Azure Portal.

1. Logga in på ditt [Azure Portal](https://portal.azure.com/) -konto.

2. Navigera till den dedikerade SQL-pool som du vill skapa en återställnings punkt för.

3. Välj **Översikt** i den vänstra rutan och välj **+ ny återställnings punkt**. Om knappen Ny återställnings punkt inte är aktive rad kontrollerar du att den dedikerade SQL-poolen inte är pausad.

    ![Ny återställnings punkt](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställnings punkten och klicka på **Använd**. Användardefinierade återställnings punkter har en standard kvarhållningsperiod på sju dagar.

    ![Namn på återställnings punkt](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool](restore-sql-pool.md)

