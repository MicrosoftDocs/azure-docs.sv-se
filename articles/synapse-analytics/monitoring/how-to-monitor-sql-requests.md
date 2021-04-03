---
title: Övervaka SQL-begäranden i Synapse Studio
description: Lär dig hur du övervakar dina SQL-begäranden med hjälp av Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467401"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Använd Synapse Studio för att övervaka dina SQL-begäranden

Med Synapse Studio kan du köra SQL-skript på SQL-pooler i din arbets yta.

Den här artikeln förklarar hur du övervakar dina SQL-begäranden, så att du kan hålla ett öga på statusen för att köra begär Anden och identifiera information om historiska begär Anden.

## <a name="access-sql-requests-list"></a>Lista över åtkomst till SQL-begäranden

Om du vill se en lista över SQL-begäranden i din arbets yta [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

![Välj övervaka hubb](./media/common/left-nav.png)

Välj **SQL-begäranden** för att visa listan över SQL-begäranden.

 ![Välj SQL-begäranden](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Välj en SQL-pool

Som standard visas historiken för SQL-begäranden för den inbyggda SQL-poolen utan server i den här vyn. Du kan välja en av dina dedikerade SQL-pooler för att se historiken för SQL-begäran för poolen.

![Välj SQL-pool](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrera dina SQL-begäranden

Du kan filtrera listan över SQL-begäranden till dem som intresserar dig. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se de SQL-begäranden som skickats av `maria@contoso.com` :

![Exempel filter](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Visa information om en speciell SQL-begäran

Om du vill visa information om en av dina SQL-begäranden öppnar du SQL-begäran för att navigera till vyn information. För de komplexa begär Anden som körs på dedikerade SQL-pooler kan du övervaka förloppet.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelines körs i Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Mer information om övervakning av Apache Spark program finns i artikeln [övervaka Apache Spark program i Synapse Studio](how-to-monitor-spark-applications.md) .