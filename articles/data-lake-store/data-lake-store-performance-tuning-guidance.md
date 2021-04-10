---
title: Azure Data Lake Storage Gen1 prestanda justering
description: Lär dig hur du använder all tillgänglig data flöde i Azure Data Lake Storage Gen1 är viktigt för att få bästa möjliga prestanda genom att göra så många läsningar och skrivningar parallellt som möjligt.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: twooley
ms.openlocfilehash: c7f16dd9ea450185893164e10928c7022d6ab5a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724688"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Justera Azure Data Lake Storage Gen1 för prestanda

Data Lake Storage Gen1 stöder stora data flöden för I/O-intensiva analyser och data förflyttning. I Data Lake Storage Gen1 använder alla tillgängliga data flöde – mängden data som kan läsas eller skrivas per sekund – är viktigt för att få bästa möjliga prestanda. Detta uppnås genom att utföra så många läsningar och skrivningar parallellt som möjligt.

![Data Lake Storage Gen1 prestanda](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 kan skalas för att tillhandahålla nödvändigt data flöde för alla analys scenarier. Som standard ger ett Data Lake Storage Gen1-konto automatiskt tillräckligt med data flöde för att uppfylla behoven hos en bred kategori av användnings fall. För de fall där kunderna får en standard gräns kan Data Lake Storage Gen1 kontot konfigureras för att tillhandahålla mer data flöde genom att kontakta Microsoft support.

## <a name="data-ingestion"></a>Datainhämtning

När data matas in från ett käll system till Data Lake Storage Gen1 är det viktigt att tänka på att käll maskin varan, käll nätverks maskin vara och nätverks anslutning till Data Lake Storage Gen1 kan vara Flask halsen.

![Diagram som visar att käll maskin vara, käll nätverks maskin vara och nätverks anslutning till Data Lake Storage Gen1 kan vara Flask halsen.](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Det är viktigt att se till att data flytten inte påverkas av dessa faktorer.

### <a name="source-hardware"></a>Käll maskin vara

Oavsett om du använder lokala datorer eller virtuella datorer i Azure bör du noga välja lämplig maskin vara. För käll disk maskin vara föredrar du SSD till hård diskar och väljer disk maskin vara med snabbare spindeler. Använd de snabbaste nätverkskorten som är möjliga för käll nätverkets maskin vara. På Azure rekommenderar vi virtuella Azure D14-datorer som har den lämpligaste kraftfulla disk-och nätverks maskin varan.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Nätverks anslutning till Data Lake Storage Gen1

Nätverks anslutningen mellan dina källdata och Data Lake Storage Gen1 kan ibland vara Flask hals. När dina källdata är lokala bör du överväga att använda en särskild länk med [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Om dina källdata finns i Azure blir prestanda bäst när data finns i samma Azure-region som Data Lake Storage Gen1-kontot.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurera data inmatnings verktyg för maximalt parallellisering

När du har åtgärdat käll maskin varan och nätverks anslutningens Flask halsar är du redo att konfigurera dina inmatnings verktyg. I följande tabell sammanfattas viktiga inställningar för flera populära inmatnings verktyg och ger detaljerade prestanda justerings artiklar för dem. Om du vill veta mer om vilket verktyg som ska användas för ditt scenario kan du gå till den här [artikeln](./data-lake-store-data-scenarios.md).

| Verktyg          | Inställningar | Mer information                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Länk](./data-lake-store-get-started-powershell.md) |
| AdlCopy    | Azure Data Lake Analytics enheter | [Länk](./data-lake-store-copy-data-azure-storage-blob.md#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Länk](./data-lake-store-copy-data-wasb-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Länk](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. block. size,-m (mapper) | [Länk](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Strukturera din data uppsättning

När data lagras i Data Lake Storage Gen1 påverkar fil storleken, antalet filer och mappstrukturen prestanda. I följande avsnitt beskrivs bästa praxis i dessa områden.

### <a name="file-size"></a>Filstorlek

Vanligt vis har analys motorer som HDInsight och Azure Data Lake Analytics en per fil-omkostnader per fil. Om du lagrar dina data som många små filer kan detta påverka prestanda negativt.

I allmänhet ordnar du dina data i större filer för bättre prestanda. Som en tumregel kan du ordna data uppsättningar i filer om 256 MB eller större. I vissa fall, till exempel bilder och binära data, går det inte att bearbeta dem parallellt. I dessa fall rekommenderar vi att du behåller enskilda filer under 2 GB.

Ibland har data pipelines begränsad kontroll över rå data som har många små filer. Vi rekommenderar att du har en "matlagning"-process som genererar större filer som kan användas för underordnade program.

### <a name="organize-time-series-data-in-folders"></a>Organisera Time Series-data i mappar

För Hive-och ADLA-arbetsbelastningar, kan partitions rensning av Time Series-data hjälpa vissa frågor att läsa endast en delmängd av data, vilket förbättrar prestandan.

De pipelines som inhämtar tids serie data, placerar ofta sina filer med en strukturerad namngivning för filer och mappar. Följande är ett vanligt exempel som vi ser för data som är strukturerade efter datum: *\dataset\yyyy\mm\dd\ datafile_YYYY_MM_DD. tsv*.

Observera att datetime-informationen visas både som mappar och i fil namnet.

För datum och tid är följande ett vanligt mönster: *\dataset\yyyy\mm\dd\hh\mm\ datafile_YYYY_MM_DD_HH_mm. tsv*.

Det val du gör med mappen och fil organisationen bör optimeras för större fil storlekar och ett rimligt antal filer i varje mapp.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimera I/O-intensiva jobb på Hadoop-och Spark-arbetsbelastningar på HDInsight

Jobben omfattas av någon av följande tre kategorier:

* **PROCESSOR intensiv.** Dessa jobb har långa beräknings tider med minimala I/O-tider. Exempel på detta är maskin inlärning och jobb för naturlig bearbetning av språk.
* **Minnes krävande.** Dessa jobb använder mycket minne. Exempel är PageRank och analys jobb i real tid.
* **I/O-intensiv.** Dessa jobb ägnar det mesta av tiden att göra I/O. Ett vanligt exempel är ett kopierings jobb som bara har Läs-och skriv åtgärder. Andra exempel är data förberedelse jobb som läser flera data, utför en del datatransformering och skriver sedan tillbaka data till butiken.

Följande rikt linjer gäller endast för I/O-intensiva jobb.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allmänna överväganden för ett HDInsight-kluster

* **HDInsight-versioner.** Använd den senaste versionen av HDInsight för bästa prestanda.
* **Områdena.** Placera Data Lake Storage Gen1-kontot i samma region som HDInsight-klustret.

An-HDInsight kluster består av två huvudnoder och vissa arbetsnoder. Varje arbetsnod tillhandahåller ett särskilt antal kärnor och minne, vilket bestäms av VM-typen. När du kör ett jobb är garn det resurs Negotiator som allokerar tillgängligt minne och kärnor för att skapa behållare. Varje behållare kör de uppgifter som krävs för att slutföra jobbet. Behållare körs parallellt för att snabbt bearbeta uppgifter. Prestanda förbättras därför genom att köra så många parallella behållare som möjligt.

Det finns tre skikt i ett HDInsight-kluster som kan justeras för att öka antalet behållare och använda alla tillgängliga data flöden.

* **Fysiskt lager**
* **GARN lager**
* **Arbets belastnings lager**

### <a name="physical-layer"></a>Fysiskt lager

**Kör kluster med fler noder och/eller större virtuella datorer.** Ett större kluster gör att du kan köra fler garn behållare som visas på bilden nedan.

![Diagram som visar användningen av fler garn behållare.](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Använd virtuella datorer med mer nätverks bandbredd.** Mängden nätverks bandbredd kan vara en Flask hals om det finns mindre nätverks bandbredd än Data Lake Storage Gen1 data flöde. Olika virtuella datorer har varierande storlek på nätverks bandbredd. Välj en VM-typ som har största möjliga nätverks bandbredd.

### <a name="yarn-layer"></a>GARN lager

**Använd mindre garn behållare.** Minska storleken på varje garn behållare för att skapa fler behållare med samma mängd resurser.

![Diagram som visar användningen av mindre garn behållare.](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Beroende på din arbets belastning är det alltid en minsta storlek för garn behållare som behövs. Om du väljer för litet en behållare kommer jobben att köras i minnes brist. Vanligt vis får inte garn behållare vara mindre än 1 GB. Det är vanligt att se 3 GB garn behållare. För vissa arbets belastningar kan du behöva större garn behållare.

**Öka kärnor per garn behållare.** Öka antalet kärnor som tilldelas varje behållare för att öka antalet parallella aktiviteter som körs i varje behållare. Detta fungerar för program som Spark, som kör flera uppgifter per behållare. För program som Hive som kör en enda tråd i varje behållare är det bättre att ha fler behållare snarare än fler kärnor per behållare.

### <a name="workload-layer"></a>Arbets belastnings lager

**Använd alla tillgängliga behållare.** Ange antalet aktiviteter som ska vara lika med eller större än antalet tillgängliga behållare så att alla resurser används.

![Diagram som visar användningen av alla tillgängliga behållare.](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Misslyckade uppgifter är kostsame.** Om varje aktivitet har en stor mängd data som ska bearbetas resulterar det i ett dyrt försök att utföra en aktivitet. Därför är det bättre att skapa fler uppgifter, som var och en bearbetar en liten mängd data.

Utöver de allmänna rikt linjerna ovan har varje program olika parametrar som är tillgängliga för att justera för det specifika programmet. I tabellen nedan visas några av parametrarna och länkarna för att komma igång med prestanda justering för varje program.

| Arbetsbelastning               | Parameter för att ange aktiviteter                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark på HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Antal körningar</li><li>Utförar-minne</li><li>Utförar – kärnor</li></ul> |
| [Hive i HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive. Tez. container. size</li></ul>         |
| [MapReduce på HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. minne</li><li>MapReduce. job. Maps</li><li>MapReduce. minska. minne</li><li>MapReduce. job. minskar</li></ul> |
| [Storm på HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Antal arbets processer</li><li>Antal kanalen utförar-instanser</li><li>Antal utförar-instanser för bult </li><li>Antal kanalen-uppgifter</li><li>Antal uppgifter i bult</li></ul>|

## <a name="see-also"></a>Se även

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)