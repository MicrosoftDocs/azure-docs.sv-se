---
title: Optimera Azure Data Lake Storage Gen2 för prestanda | Microsoft Docs
description: Lär dig hur du optimerar Azure Data Lake Storage Gen2 för prestanda. Mata in data, strukturera din data uppsättning med mera.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f0f64d910d03e42008c5fe6fef28a5b9c0917abd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814473"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optimera Azure Data Lake Storage Gen2 för prestanda

Azure Data Lake Storage Gen2 stöder stora data flöden för I/O-intensiva analyser och data förflyttning.  I Data Lake Storage Gen2 använder alla tillgängliga data flöde – mängden data som kan läsas eller skrivas per sekund – är viktigt för att få bästa möjliga prestanda.  Detta uppnås genom att utföra så många läsningar och skrivningar parallellt som möjligt.

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 kan skalas för att tillhandahålla nödvändigt data flöde för alla analys scenarier. Som standard ger ett Data Lake Storage Gen2-konto tillräckligt med data flöde i standard konfigurationen för att uppfylla behoven hos en bred kategori av användnings fall. För de fall där kunderna får en standard gräns kan Data Lake Storage Gen2 kontot konfigureras för att tillhandahålla mer data flöde genom att kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Datainhämtning

När data matas in från ett käll system till Data Lake Storage Gen2, är det viktigt att tänka på att käll maskin varan, käll nätverks maskin varan eller nätverks anslutningen till Data Lake Storage Gen2 kan vara Flask hals.  

![Diagram som visar de faktorer som du bör tänka på när du matar in data från ett käll system till Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Det är viktigt att se till att data flytten inte påverkas av dessa faktorer.

### <a name="source-hardware"></a>Käll maskin vara

Oavsett om du använder lokala datorer eller virtuella datorer i Azure bör du noga välja lämplig maskin vara. För käll disk maskin vara föredrar du SSD till hård diskar och väljer disk maskin vara med snabbare spindeler. Använd de snabbaste nätverkskorten som är möjliga för käll nätverkets maskin vara.  På Azure rekommenderar vi virtuella Azure D14-datorer som har lämpligt kraftfulla disk-och nätverks maskin vara.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Nätverks anslutning till Data Lake Storage Gen2

Nätverks anslutningen mellan dina källdata och Data Lake Storage Gen2 kan ibland vara Flask hals. När dina källdata är lokala bör du överväga att använda en särskild länk med [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om dina källdata finns i Azure blir prestanda bäst när data finns i samma Azure-region som Data Lake Storage Gen2-kontot.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurera data inmatnings verktyg för maximalt parallellisering

När du har adresserat käll maskin varan och nätverks anslutningens Flask halsar ovan är du redo att konfigurera dina inmatnings verktyg. I följande tabell sammanfattas viktiga inställningar för flera populära inmatnings verktyg och ger detaljerade prestanda justerings artiklar för dem.  Om du vill veta mer om vilket verktyg som ska användas för ditt scenario kan du gå till den här [artikeln](data-lake-storage-data-scenarios.md).

| Verktyg               | Inställningar | Mer information                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Länk](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Länk](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. block. size,-m (mapper)    |   [Länk](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Strukturera din data uppsättning

När data lagras i Data Lake Storage Gen2 kan fil storleken, antalet filer och mappstrukturen påverka prestandan.  I följande avsnitt beskrivs bästa praxis i dessa områden.  

### <a name="file-size"></a>Filstorlek

Vanligt vis har analys motorer som HDInsight och Azure Data Lake Analytics en per fil-omkostnader per fil. Om du lagrar dina data som många små filer kan detta påverka prestanda negativt. I allmänhet ordnar du dina data i större filer för att få bättre prestanda (256 MB till 100 GB i storlek). Vissa motorer och program kan ha problem effektivt att bearbeta filer som är större än 100 GB.

Ibland har data pipelines begränsad kontroll över rå data som har många små filer. I allmänhet rekommenderar vi att systemet har en viss typ av process för att sammanställa små filer i större mängder för användning av underordnade program.

### <a name="organizing-time-series-data-in-folders"></a>Organisera Time Series-data i mappar

Vid Hive-arbetsbelastningar kan partitions rensning av Time Series-data hjälpa vissa frågor att läsa endast en delmängd av de data som förbättrar prestandan.    

De pipelines som inhämtar tids serie data, placerar ofta sina filer med ett strukturerat namn för filer och mappar. Nedan visas ett väldigt vanligt exempel som vi ser för data som är strukturerade efter datum:

*\DataSet\YYYY\MM\DD\ datafile_YYYY_MM_DD. tsv*

Observera att datetime-informationen visas både som mappar och i fil namnet.

För datum och tid är följande ett vanligt mönster

*\DataSet\YYYY\MM\DD\HH\mm\ datafile_YYYY_MM_DD_HH_mm. tsv*

Det val du gör med mappen och fil organisationen bör optimeras för större fil storlekar och ett rimligt antal filer i varje mapp.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimera I/O-intensiva jobb på Hadoop-och Spark-arbetsbelastningar på HDInsight

Jobben omfattas av någon av följande tre kategorier:

* **PROCESSOR intensiv.**  Dessa jobb har långa beräknings tider med minimala I/O-tider.  Exempel på detta är maskin inlärning och jobb för naturlig bearbetning av språk.  
* **Minnes krävande.**  Dessa jobb använder mycket minne.  Exempel är PageRank och analys jobb i real tid.  
* **I/O-intensiv.**  Dessa jobb ägnar det mesta av tiden att göra I/O.  Ett vanligt exempel är ett kopierings jobb som bara har Läs-och skriv åtgärder.  Andra exempel är data förberedelse jobb som läser mycket data, utför en del datatransformering och skriver sedan tillbaka data till butiken.  

Följande rikt linjer gäller endast för I/O-intensiva jobb.

## <a name="general-considerations"></a>Generella saker att tänka på

Du kan ha ett jobb som läser eller skriver så mycket som 100 MB i en enda åtgärd, men en buffert av den storleken kan påverka prestandan.
Optimera prestanda genom att försöka bevara storleken på en I/O-åtgärd mellan 4 MB och 16 MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allmänna överväganden för ett HDInsight-kluster

* **HDInsight-versioner.** Använd den senaste versionen av HDInsight för bästa prestanda.
* **Områdena.** Placera Data Lake Storage Gen2-kontot i samma region som HDInsight-klustret.  

An-HDInsight kluster består av två huvudnoder och vissa arbetsnoder. Varje arbetsnod tillhandahåller ett särskilt antal kärnor och minne, vilket bestäms av VM-typen.  När du kör ett jobb är garn det resurs Negotiator som allokerar tillgängligt minne och kärnor för att skapa behållare.  Varje behållare kör de uppgifter som krävs för att slutföra jobbet.  Behållare körs parallellt för att snabbt bearbeta uppgifter. Prestanda förbättras därför genom att köra så många parallella behållare som möjligt.

Det finns tre skikt i ett HDInsight-kluster som kan justeras för att öka antalet behållare och använda alla tillgängliga data flöden.  

* **Fysiskt lager**
* **GARN lager**
* **Arbets belastnings lager**

### <a name="physical-layer"></a>Fysiskt lager

**Kör kluster med fler noder och/eller större virtuella datorer.**  Ett större kluster gör att du kan köra fler garn behållare som visas på bilden nedan.

![Diagram som visar hur ett större kluster gör att du kan köra fler garn behållare.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Använd virtuella datorer med mer nätverks bandbredd.**  Mängden nätverks bandbredd kan vara en Flask hals om det finns mindre nätverks bandbredd än Data Lake Storage Gen2 data flöde.  Olika virtuella datorer har varierande storlek på nätverks bandbredd.  Välj en VM-typ som har största möjliga nätverks bandbredd.

### <a name="yarn-layer"></a>GARN lager

**Använd mindre garn behållare.**  Minska storleken på varje garn behållare för att skapa fler behållare med samma mängd resurser.

![Diagram som visar resultatet när du minskar storleken på varje garn behållare för att skapa fler behållare.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Beroende på din arbets belastning är det alltid en minsta storlek för garn behållare som behövs. Om du väljer för litet en behållare kommer jobben att köras i minnes brist. Vanligt vis får inte garn behållare vara mindre än 1 GB. Det är vanligt att se 3 GB garn behållare. För vissa arbets belastningar kan du behöva större garn behållare.  

**Öka kärnor per garn behållare.**  Öka antalet kärnor som tilldelas varje behållare för att öka antalet parallella aktiviteter som körs i varje behållare.  Detta fungerar för program som Spark som kör flera uppgifter per behållare.  För program som Hive som kör en enda tråd i varje behållare är det bättre att ha fler behållare snarare än fler kärnor per behållare.

### <a name="workload-layer"></a>Arbets belastnings lager

**Använd alla tillgängliga behållare.**  Ange antalet aktiviteter som ska vara lika med eller större än antalet tillgängliga behållare så att alla resurser används.

![Diagram som visar användningen av alla behållare.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Misslyckade uppgifter är kostsame.** Om varje aktivitet har en stor mängd data som ska bearbetas resulterar det i ett dyrt försök att utföra en aktivitet.  Därför är det bättre att skapa fler uppgifter, som var och en bearbetar en liten mängd data.

Utöver de allmänna rikt linjerna ovan har varje program olika parametrar som är tillgängliga för att justera för det specifika programmet. I tabellen nedan visas några av parametrarna och länkarna för att komma igång med prestanda justering för varje program.

| Arbetsbelastning | Parameter för att ange aktiviteter |
|----------|------------------------|
| [Spark på HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Antal körningar</li><li>Utförar-minne</li><li>Utförar – kärnor</li></ul> |
| [Hive i HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>Hive. Tez. container. size</li></ul> |
| [MapReduce på HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>MapReduce. map. minne</li><li>MapReduce. job. Maps</li><li>MapReduce. minska. minne</li><li>MapReduce. job. minskar</li></ul> |
| [Storm på HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Antal arbets processer</li><li>Antal kanalen utförar-instanser</li><li>Antal utförar-instanser för bult </li><li>Antal kanalen-uppgifter</li><li>Antal uppgifter i bult</li></ul>|

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
