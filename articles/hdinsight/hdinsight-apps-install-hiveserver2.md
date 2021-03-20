---
title: Skala HiveServer2 på Azure HDInsight
description: Skala HiveServer2 vågrätt på Azure HDInsight-kluster med hjälp av Edge-noder för att öka fel tolerans och tillgänglighet.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88227402"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Skala HiveServer2 på Azure HDInsight-kluster för hög tillgänglighet

Lär dig hur du distribuerar ytterligare HiveServer2 till klustret för att öka tillgänglighet och belastnings fördelning. När du ökar huvudnoden storlek kan du också använda Edge-noder för att distribuera HiveServer2. 

> [!NOTE]
> Beroende på din användning kan det öka antalet anslutningar till Hive-metaarkiv om du ökar antalet HiveServer2. Se också till att din Azure SQL-databas är rätt inställd.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här guiden måste du förstå följande artikel:
- [Använd tomma Edge-noder på Apache Hadoop kluster i HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Installera HiveServer2

I det här avsnittet ska du installera ytterligare en HiveServer2 på dina mål värdar.

1. Öppna Ambari i webbläsaren och klicka på mål värden.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Menyn för värdarna i Ambari.":::

2. Klicka på knappen Lägg till och klicka på HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Lägg till HiveServer2 panel med värd.":::

3. Bekräfta och processen kommer att köras. Upprepa 1-3 för alla önskade värdar.

4. När du är färdig med installationen startar du om alla tjänster med inaktuella konfigurationer och startar HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Starta HiveServer2-panelen.":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du installerar HiveServer2 på klustret. Mer information om Edge-noder och program finns i följande artiklar:

* [Installera Edge Node](hdinsight-apps-use-edge-node.md): Lär dig hur du installerar en Edge-nod på ditt HDInsight-kluster.
* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Azure SQL DTU-anslutnings gränser](../azure-sql/database/resource-limits-dtu-single-databases.md): Lär dig mer om Azure SQL Database-gränser med DTU.
* [Anslutnings gränser för Azure SQL-vCore](../azure-sql/database/resource-limits-vcore-elastic-pools.md): Lär dig mer om Azure SQL Database-gränser med hjälp av virtuella kärnor.
