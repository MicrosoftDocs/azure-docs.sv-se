---
title: Övervaka SQL-pooler i Synapse Studio
description: Lär dig hur du övervakar SQL-pooler med hjälp av Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467424"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Använd Synapse Studio för att övervaka dina SQL-pooler

Med Synapse Studio kan du köra SQL-skript på SQL-pooler i din arbets yta.

Den här artikeln förklarar hur du övervakar SQL-pooler, så att du kan hålla ett öga på Poolernas status och aktivitet.

## <a name="access-sql-pools-list"></a>Åtkomst till listan med SQL-pooler

Om du vill se en lista över SQL-pooler i din arbets yta [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

![Välj övervaka hubb](./media/common/left-nav.png)

Välj **SQL-pooler** om du vill visa listan över SQL-pooler.

 ![Välj SQL-pooler](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrera SQL-pooler

Du kan filtrera listan över SQL-pooler till dem som intresserar dig. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se de SQL-pooler som innehåller namnet "salesrecords":

![Exempel filter](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Visa information om en angiven SQL-pool

Om du vill visa information om en av dina SQL-pooler väljer du SQL-poolen för att visa information.

![Information om SQL-pool](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelines körs i Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Mer information om övervakning av SQL-begäranden finns i artikeln [Övervaka SQL-begäranden i Synapse Studio](how-to-monitor-sql-requests.md) .