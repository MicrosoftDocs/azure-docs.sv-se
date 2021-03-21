---
title: Prestanda justering – Hive på Azure Data Lake Storage Gen1
description: Lär dig mer om prestanda justering för Hive i HdInsight och Azure Data Lake Storage Gen1. För I/O-intensiva frågor kan du justera Hive för att få bättre prestanda.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723789"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Vägledning för prestanda justering för Hive i HDInsight och Azure Data Lake Storage Gen1

Standardinställningarna har ställts in för att ge bästa prestanda i många olika användnings fall.  För I/O-intensiva frågor kan Hive justeras för att få bättre prestanda med Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage gen1-konto. Se [skapa ett HDInsight-kluster med data Lake Storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att aktivera fjärr skrivbord för klustret.
* **Kör Hive i HDInsight**.  Information om hur du kör Hive-jobb i HDInsight finns i [använda Hive i HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Rikt linjer för prestanda justering på data Lake Storage gen1**.  Allmänna prestanda koncept finns i [vägledning för data Lake Storage gen1 prestanda justering](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna för att justera för förbättrade Data Lake Storage Gen1 prestanda:

* **Hive. Tez. container. size** – mängden minne som används av varje aktivitet

* **Tez. Grouping. min-size** – minsta storlek för varje mapper

* **Tez. Grouping. Max-size** – maximal storlek för varje mappning

* **hive.exec. reduce. byte. per.** dereducerare – storlek på varje minsknings man

**Hive. Tez. container. size** -container-storlek anger hur mycket minne som är tillgängligt för varje aktivitet.  Detta är den viktigaste indatan för styrning av samtidigheten i Hive.  

**Tez. Grouping. min-size** – med den här parametern kan du ange den minsta storleken för varje mapp.  Om antalet mappningar som Tez väljer är mindre än värdet för den här parametern kommer Tez att använda värdet som anges här.

**Tez. Grouping. Max-size** – parametern låter dig ange max storleken för varje mapp.  Om antalet mappningar som Tez väljer är större än värdet för den här parametern kommer Tez att använda värdet som anges här.

**hive.exec. Reducer. bytes. per. reduce** – den här parametern anger storleken på varje minskning.  Som standard är varje minskning 256 MB.  

## <a name="guidance"></a>Vägledning

**Ange hive.exec. dereducerare. bytes. per. reduce** – standardvärdet fungerar bra när data är okomprimerade.  För data som är komprimerade bör du minska storleken på minskningen.  

**Ange Hive. Tez. container. size** – i varje nod anges minnet av garn. nodemanager. Resource. Memory-MB och bör anges korrekt i HDI-kluster som standard.  Mer information om hur du ställer in rätt minne i garn finns i det här [inlägget](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

I/O-intensiva arbets belastningar kan dra nytta av mer parallellitet genom att minska storleken på Tez-behållare. Detta ger användaren fler behållare vilket ökar samtidigheten.  Vissa Hive-frågor kräver dock en stor mängd minne (t. ex. MapJoin).  Om det inte finns tillräckligt med minne på den här aktiviteten får du ett slut på minnes undantag under körningen.  Om du får slut på minnes undantag bör du öka minnet.   

Antalet samtidiga aktiviteter som körs eller parallellitet kommer att begränsas av det totala garn minnet.  Antalet garn behållare kommer att diktera hur många samtidiga aktiviteter som kan köras.  För att hitta garn minnet per nod kan du gå till Ambari.  Navigera till garn och Visa fliken configs.  GARN minnet visas i det här fönstret.  

> Totalt garn minne = noder * garn minne per nod antal garn behållare = total garn minne/Tez container storlek

Nyckeln för att förbättra prestanda med hjälp av Data Lake Storage Gen1 är att öka samtidigheten så mycket som möjligt.  Tez beräknar automatiskt antalet uppgifter som ska skapas så att du inte behöver ange den.   

## <a name="example-calculation"></a>Exempel beräkning

Anta att du har ett D14-kluster med 8 noder.  

> Totalt garn minne = noder * garn minne per nod totalt garn minne = 8 noder * 96GB = 768GB antal garn behållare = 768GB/3072MB = 256

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning** 

Om du når gränserna för bandbredden som tillhandahålls av Data Lake Storage Gen1 skulle du kunna se aktivitets felen. Detta kan identifieras genom att identifiera begränsnings fel i aktivitets loggarna.  Du kan minska parallellitet genom att öka storleken på Tez-behållare.  Om du behöver mer samtidighet för ditt jobb kan du kontakta oss.

Om du vill kontrol lera om du får en begränsning måste du aktivera fel söknings loggning på klient sidan. Så här kan du göra:

1. Lägg till följande egenskap i egenskaperna log4j i Hive config. Detta kan göras från Ambari View: log4j. loggfil. com. Microsoft. Azure. datalake. Store = DEBUG starta om alla noder/tjänster för att konfigurationen ska börja gälla.

2. Om du får en begränsning visas HTTP 429-felkoden i Hive-loggfilen. Hive-logg filen finns i/tmp/ &lt; User &gt; /Hive.log

## <a name="further-information-on-hive-tuning"></a>Mer information om Hive-justering

Här följer några Bloggar som kan hjälpa dig att justera dina Hive-frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Koda Hive-mallfilen i Azure HDInsight](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Tala om optimering av Hive i HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)