---
title: Övervaka Apache Spark pooler i Synapse Studio
description: Lär dig hur du övervakar Apache Spark pooler med hjälp av Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467241"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Använd Synapse Studio för att övervaka Apache Spark pooler

Med Azure Synapse Analytics kan du använda Spark för att köra antecknings böcker, jobb och andra typer av program på Spark-pooler i din arbets yta.

Den här artikeln förklarar hur du övervakar Apache Spark pooler, så att du kan hålla ett öga på status för dina pooler, inklusive hur många virtuella kärnor som används av olika arbets ytans användare.

## <a name="access-spark-pools-list"></a>Åtkomst till Spark-pooler lista

Om du vill se en lista över Apache Spark pooler i din arbets yta [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

![Välj övervaka hubb](./media/common/left-nav.png)

Välj **Apache Spark pooler** om du vill visa listan över Apache Spark pooler.

 ![Välj Apache Spark pooler](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrera dina Spark-pooler

Du kan filtrera listan över Spark-pooler till dem som intresserar dig. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se de Spark-pooler som innehåller namnet "nu":

![Exempel filter](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Visa information om en angiven Spark-pool

Om du vill visa information om en av dina Spark-pooler väljer du Spark-poolen för att visa information.

![Information om Apache Spark pool](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelines körs i Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Mer information om övervakning av Apache Spark program finns i artikeln [övervaka Apache Spark program i Synapse Studio](how-to-monitor-spark-applications.md) .
