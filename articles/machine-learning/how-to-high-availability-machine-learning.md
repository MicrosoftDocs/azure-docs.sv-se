---
title: Återhämtnings & hög tillgänglighet
titleSuffix: Azure Machine Learning
description: Lär dig hur du gör dina Azure Machine Learning resurser mer elastiska för avbrott genom att använda en konfiguration med hög tillgänglighet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325482"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Öka Azure Machine Learning återhämtning



I den här artikeln får du lära dig hur du gör Microsoft Azure Machine Learning resurser mer elastiska med hjälp av konfigurationer med hög tillgänglighet. Du kan konfigurera de Azure-tjänster som Azure Machine Learning är beroende av för hög tillgänglighet. Den här artikeln identifierar de tjänster som du kan konfigurera för hög tillgänglighet och länkar till ytterligare information om hur du konfigurerar resurserna.

> [!NOTE]
> Azure Machine Learning kan inte erbjuda ett alternativ för haveri beredskap.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Förstå Azure-tjänster för Azure Machine Learning

Azure Machine Learning är beroende av flera olika Azure-tjänster och har flera lager. Några av dessa tjänster är etablerade i din (kund) prenumeration. Du ansvarar för den hög tillgängliga konfigurationen av dessa tjänster. Andra tjänster skapas i en Microsoft-prenumeration och hanteras av Microsoft. 

Azure-tjänster inkluderar:

* **Azure Machine Learning infrastruktur**: en Microsoft-hanterad miljö för arbets ytan Azure Machine Learning.

* **Associerade resurser**: resurser etablerade i prenumerationen under Azure Machine Learning skapa arbets yta. Dessa resurser omfattar Azure Storage, Azure Key Vault, Azure Container Registry och Application Insights. Du är ansvarig för att konfigurera inställningar för hög tillgänglighet för dessa resurser.
  * Standard lagringen har data, till exempel modell, tränings loggar och data uppsättningar.
  * Key Vault har autentiseringsuppgifter för Azure Storage, Container Registry och data lager.
  * Container Registry har en Docker-avbildning för utbildnings-och inferencing miljöer.
  * Application Insights är för övervakning Azure Machine Learning.

* **Beräknings resurser**: resurser som du skapar efter distribution av arbets ytan. Du kan till exempel skapa en beräknings instans eller ett beräknings kluster för att träna en Machine Learning modell.
  * Beräknings instans och beräknings kluster: Microsoft-hanterade modell utvecklings miljöer.
  * Andra resurser: resurser för Microsoft-datorer som du kan ansluta till Azure Machine Learning, till exempel Azure Kubernetes service (AKS), Azure Databricks, Azure Container Instances och Azure HDInsight. Du är ansvarig för att konfigurera inställningar för hög tillgänglighet för dessa resurser.

* **Ytterligare data lager**: Azure Machine Learning kan montera ytterligare data lager, till exempel Azure Storage, Azure Data Lake Storage och Azure SQL Database för tränings data.  Dessa data lager tillhandahålls i din prenumeration. Du är ansvarig för att konfigurera inställningar för hög tillgänglighet.

I följande tabell visas vilka Azure-tjänster som hanteras av Microsoft, som hanteras av dig och som är hög tillgängliga som standard.

| Tjänst | Hanteras av | Hög tillgänglighet som standard |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktur** | Microsoft | |
| **Associerade resurser** |
| Azure Storage | Du | |
| Key Vault | Du | ✓ |
| Container Registry | Du | |
| Application Insights | Du | NA |
| **Beräknings resurser** |
| Beräkninsinstans | Microsoft |  |
| Beräkningskluster | Microsoft |  |
| Andra beräknings resurser som AKS, <br>Azure Databricks, Container Instances, HDInsight | Du |  |
| **Ytterligare data lager** , till exempel Azure Storage, SQL Database,<br> Azure Database for PostgreSQL Azure Database for MySQL, <br>Azure Databricks fil system | Du | |

Resten av den här artikeln beskriver de åtgärder som du behöver vidta för att göra var och en av dessa tjänster hög tillgängliga.

## <a name="associated-resources"></a>Associerade resurser

> [!IMPORTANT]
> Azure Machine Learning stöder inte standard lagrings konto redundans med Geo-redundant lagring (GRS), Geo-redundant lagring (GZRS), Geo-redundant lagring med Läs behörighet (RA-GRS) eller Read-Access geo-Zone-redundant lagring (RA-GZRS).

Se till att konfigurera inställningar för hög tillgänglighet för varje resurs genom att referera till följande dokumentation:

* **Azure Storage**: om du vill konfigurera inställningar för hög tillgänglighet, se [Azure Storage redundans](../storage/common/storage-redundancy.md).
* **Key Vault**: Key Vault ger hög tillgänglighet som standard och kräver ingen användar åtgärd.  Se [Azure Key Vault tillgänglighet och redundans](../key-vault/general/disaster-recovery-guidance.md).
* **Container Registry**: Välj alternativet Premium Registry för geo-replikering. Se [geo-replikering i Azure Container Registry](../container-registry/container-registry-geo-replication.md).
* **Application Insights**: Application Insights ger inte inställningar för hög tillgänglighet. Information om hur du justerar data kvarhållning och information finns i [data insamling, kvarhållning och lagring i Application Insights](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Beräknings resurser

Se till att konfigurera inställningar för hög tillgänglighet för varje resurs genom att referera till följande dokumentation:

* **Azure Kubernetes-tjänst**: se [metod tips för verksamhets kontinuitet och haveri beredskap i Azure KUBERNETES service (AKS)](../aks/operator-best-practices-multi-region.md) och [skapa ett Azure KUBERNETES service-kluster (AKS) som använder tillgänglighets zoner](../aks/availability-zones.md). Om AKS-klustret har skapats med hjälp av Azure Machine Learning Studio, SDK eller CLI stöds inte hög tillgänglighet mellan regioner.
* **Azure Databricks**: se [regional haveri beredskap för Azure Databricks-kluster](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Container instances**: en Orchestrator är ansvarig för redundans. Se [Azure Container instances-och behållar dirigeringar](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight**: se [tjänster med hög tillgänglighet som stöds av Azure HDInsight](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>Ytterligare data lager

Se till att konfigurera inställningar för hög tillgänglighet för varje resurs genom att referera till följande dokumentation:

* **Azure Blob container/Azure Files/data Lake Storage Gen2**: samma som standard lagring.
* **Data Lake Storage gen1**: se [rikt linjer för hög tillgänglighet och katastrof återställning för data Lake Storage gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **SQL Database**: se [hög tillgänglighet för Azure SQL Database och SQL-hanterad instans](../azure-sql/database/high-availability-sla.md).
* **Azure Database for PostgreSQL**: se [koncept med hög tillgänglighet i Azure Database for PostgreSQL-enskild server](../postgresql/concepts-high-availability.md).
* **Azure Database for MySQL**: se [förstå affärs kontinuitet i Azure Database for MySQL](../mysql/concepts-business-continuity.md).
* **Azure Databricks fil system**: se [regional haveri beredskap för Azure Databricks kluster](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Om du anger en egen kundhanterad nyckel för att distribuera en Azure Machine Learning arbets yta, tillhandahålls Azure Cosmos DB också i din prenumeration. I så fall är du ansvarig för att konfigurera inställningarna för hög tillgänglighet. Se [hög tillgänglighet med Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera Azure Machine Learning med associerade resurser med dina inställningar för hög tillgänglighet, använder du en [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).