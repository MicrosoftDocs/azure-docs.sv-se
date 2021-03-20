---
title: Introduktion till ML-tjänster på Azure HDInsight
description: Lär dig hur du använder ML-tjänster i HDInsight för att skapa program för stor data analys.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943924"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Vad är ML-tjänster i Azure HDInsight

Microsoft Machine Learning Server är tillgängligt som ett distributions alternativ när du skapar HDInsight-kluster i Azure. Kluster typen som innehåller det här alternativet kallas **ml-tjänster**. Den här funktionen ger åtkomst på begäran till anpassningsbara, distribuerade analys metoder på HDInsight.

ML-tjänster i HDInsight tillhandahåller de senaste funktionerna för R-baserade analyser på data uppsättningar i praktiskt taget vilken storlek som helst. Data uppsättningarna kan läsas in till antingen Azure Blob eller Data Lake Storage. Dina R-baserade program kan använda R-paket med 8000 + öppen källkod. Rutinerna i scaler, Microsofts Big data Analytics-paket är också tillgängliga.

Edge-noden är en praktisk plats för att ansluta till klustret och köra R-skript. Edge-noden gör det möjligt att köra skalnings parallellt distribuerade funktioner över serverns kärnor. Du kan också köra dem över noderna i klustret med hjälp av skalnings enhetens Hadoop-mappning minskar. Du kan också använda Apache Spark Compute-kontexter.

De modeller eller förutsägelser som orsakas av analyser kan laddas ned för lokal användning. De kan också vara `operationalized` någon annan stans i Azure. I synnerhet genom [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net)och [webb tjänsten](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Kom igång med ML-tjänster i HDInsight

Om du vill skapa ett ML Services-kluster i HDInsight väljer du kluster typen **ml-tjänster** . Kluster typen ML-tjänster innehåller ML Server på datanoderna och Edge-noden. Edge-noden fungerar som en landnings zon för ML service-baserad analys. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) för en genom gång av hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför bör man välja ML-tjänster i HDInsight?

ML-tjänster i HDInsight ger följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovation från Microsoft och öppen källkod

  ML-tjänster innehåller en mycket anpassningsbar, distribuerad uppsättning algoritmer som [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)och [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Dessa algoritmer kan arbeta med data storlekar som är större än storleken på det fysiska minnet. De körs också på en mängd olika plattformar på ett distribuerat sätt. Läs mer om samlingen av Microsofts anpassade R- [paket](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [python-paket](/machine-learning-server/python-reference/introducing-python-package-reference) som ingår i produkten.
  
  ML-tjänster förenar dessa Microsoft-innovationer och bidrag från communityn för öppen källkod (R, python och AI). Allt ovanpå en enda plattform i företags klass. Ett Machine Learning-paket med R eller python kan fungera sida vid sida med en egen innovation från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och driftsättning och administration med hög skala

  Företag som förlitar sig på traditionella paradigm och miljöer investerar mycket tid och ansträngning mot driftsättning. Den här åtgärden resulterar i fasta kostnader och fördröjningar, inklusive översättnings tiden för: modeller, iterationer för att hålla dem giltiga och aktuella, regler för godkännande och hantering av behörigheter.

  ML-tjänster erbjuder [driftsättning](/machine-learning-server/what-is-operationalization)i företags klass. När en maskin inlärnings modell har slutförts tar det bara några klick att generera API: er för webb tjänster. Dessa [webb tjänster](/machine-learning-server/operationalize/concept-what-are-web-services) finns i ett Server rutnät i molnet och kan integreras med branschspecifika program. Möjligheten att distribuera till ett elastiskt rutnät gör att du kan skala sömlöst med företagets behov, både för batch-och real tids resultat. Instruktioner finns i [OPERATIONALISERA ml-tjänster i HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Kluster typen ML-tjänster i HDInsight stöds endast på HDInsight 3,6. HDInsight 3,6 har schemalagts för att dra tillbaka den 31 december 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Viktiga funktioner i ML-tjänster i HDInsight

Följande funktioner ingår i ML-tjänster i HDInsight.

| Funktions kategori | Beskrivning |
|------------------|-------------|
| R-aktiverad | [R-paket](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) för lösningar som är skrivna i R, med en distribution med öppen källkod av R och körnings infrastruktur för skript körning. |
| Python-aktiverat | [Python-moduler](/machine-learning-server/python-reference/introducing-python-package-reference) för lösningar som skrivits i python, med en distribution med öppen källkod av python och körnings infrastruktur för skript körning.
| [Förtränade modeller](/machine-learning-server/install/microsoftml-install-pretrained-models) | För analys av visuella analyser och text sentiment är det dags att visa data som du anger. |
| [Distribuera och förbruka](r-server-operationalize.md) | `Operationalize` servern och distribuerar lösningar som en webb tjänst. |
| [Fjärrkörning](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starta fjärrsessioner på ML Services-kluster i nätverket från din klient dator. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Alternativ för data lagring för ML-tjänster i HDInsight

Standard lagring för HDFS-filsystemet kan vara ett Azure Storage konto eller Azure Data Lake Storage. Data som överförs till kluster lagring under analysen görs permanenta. Data är tillgängliga även efter att klustret har tagits bort. Olika verktyg kan hantera data överföringen till lagringen. Verktygen omfattar den portalbaserade uppladdnings funktionen för lagrings kontot och AzCopy-verktyget.

Du kan ge åtkomst till ytterligare blob-och data Lake-butiker när klustret skapas. Du är inte begränsad till det primära lagrings alternativet som används.  Se [Azure Storage alternativ för ml-tjänster i HDInsight](./r-server-storage.md) -artikeln för att lära dig mer om hur du använder flera lagrings konton.

Du kan också använda Azure Files som ett lagrings alternativ för att använda på Edge-noden. Azure Files aktiverar fil resurser som skapats i Azure Storage till Linux-filsystemet. Mer information finns i [Azure Storage alternativ för ml-tjänster i HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Edge-noden Access ML Services

Du kan ansluta till Microsoft ML Server på Edge-noden med hjälp av en webbläsare, eller SSH/SparaTillFil. R-konsolen installeras som standard när klustret skapas.  

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

Dina R-skript kan använda alla R-paket med öppen källkod. Du kan också använda de parallella och distribuerade rutinerna från skalnings biblioteket. Skript som körs på Edge-noden körs i R-tolken på den noden. Förutom steg som anropar skalnings funktioner med beräknings kontexten Map reduce (RxHadoopMR) eller Spark (Rxspark beräkningskontexten). Funktionerna körs i en distribuerad miljö över datanoderna som är associerade med data. Mer information om kontext alternativ finns i [alternativ för beräknings kontext för ml-tjänster i HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` en modell

När data modelleringen är klar kan `operationalize` modellen göra förutsägelser för nya data antingen från Azure eller lokalt. Den här processen kallas för poängsättning. Du kan göra en beräkning i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight

Skriv en R-funktion för att skapa Poäng i HDInsight. Funktionen anropar modellen för att göra förutsägelser för en ny datafil som du har läst in till ditt lagrings konto. Spara sedan förutsägelserna på lagrings kontot. Du kan köra den här rutinen på begäran på noden Edge i klustret eller genom att använda ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Machine Learning (AML)

Om du vill använda Azure Machine Learning använder du det Azure Machine Learning R-paketet med öppen källkod som heter [azureml](https://cran.r-project.org/src/contrib/Archive/AzureML/) för att publicera din modell som en Azure-webbtjänst. För enkelhetens skull är det här paketet förinstallerat på Edge-noden. Använd sedan funktionerna i Azure Machine Learning för att skapa ett användar gränssnitt för webb tjänsten och anropa sedan webb tjänsten efter behov för att gå vidare. Konvertera sedan skalnings modell objekt till motsvarande modell objekt med öppen källkod som ska användas med webb tjänsten. Använd funktioner för skalnings tvång, till exempel `as.randomForest()` för Ensemble-baserade modeller, för den här konverteringen.

### <a name="score-on-premises"></a>Poängen lokalt

Om du vill göra lokala efter att du har skapat din modell: serialisera modellen i R, ladda ned den, deserialisera den och Använd den för att bedöma nya data. Du kan räkna med nya data med hjälp av metoden som beskrivits tidigare i poäng i HDInsight eller med hjälp av [webb tjänster](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Underhålla klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta R-paket som du använder krävs på Edge-noden eftersom de flesta stegen i dina R-skript körs där. Om du vill installera ytterligare R-paket på Edge-noden kan du använda- `install.packages()` metoden i R.

Om du bara använder rutiner för skalnings bibliotek behöver du vanligt vis inte ytterligare R-paket. Du kan behöva ytterligare paket för **rxExec** -eller **RxDataStep** -körning på datanoderna.

Du kan installera ytterligare paket med en skript åtgärd när du har skapat klustret. Mer information finns i [Hantera ml-tjänster i HDInsight-kluster](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändra Apache Hadoop MapReduce minnes inställningar

Tillgängligt minne till ML-tjänster kan ändras när ett MapReduce-jobb körs. Om du vill ändra ett kluster använder du Apache Ambari-ANVÄNDARGRÄNSSNITTET för klustret. Ambari UI-instruktioner finns i [Hantera HDInsight-kluster med hjälp av Ambari-WEBBgränssnittet](../hdinsight-hadoop-manage-ambari.md).

Tillgängligt minne till ML-tjänster kan ändras med hjälp av Hadoop-växlar i anropet till **RxHadoopMR**:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Skala klustret

Ett befintligt ML-kluster i HDInsight kan skalas upp eller ned via portalen. Genom att skala upp får du ytterligare kapacitet för större bearbetnings uppgifter. Du kan skala upp ett kluster när det är inaktivt. Instruktioner för hur du skalar ett kluster finns i [Hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhålla systemet

OS-underhåll görs på de underliggande virtuella Linux-datorerna i ett HDInsight-kluster under andra tider. Normalt görs underhållet vid 3:30 AM (VM: s lokala tid) varje måndag och torsdag. Uppdateringar påverkar inte mer än en fjärdedel av klustret i taget.

Pågående jobb kan bli långsamma under underhållet. De bör dock fortfarande köras till slut för ande. All anpassad program vara eller lokala data som du har bevaras över dessa underhålls händelser om inte ett oåterkalleligt fel uppstår som kräver ett kluster återuppbyggnad.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster i HDInsight

Linux Edge-noden i ett HDInsight-kluster är landnings zon för R-baserad analys. De senaste versionerna av HDInsight tillhandahåller en webbläsarbaserad IDE av RStudio-server på Edge-noden. RStudio-servern är mer produktiv än R-konsolen för utveckling och körning.

En Desktop IDE kan komma åt klustret via en fjärran sluten MapReduce eller Spark Compute-kontext. Alternativen är: Microsofts [R Tools för Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio och Walwares Sol förmörkelse-baserade tillstånd.

Öppna R-konsolen på Edge-noden genom att skriva **R** i kommando tolken. När du använder-konsol gränssnittet är det praktiskt att utveckla R-skript i en text redigerare. Klipp ut och klistra in avsnitt i skriptet i R-konsolen efter behov.

## <a name="pricing"></a>Priser

Priserna som är associerade med ett ML-kluster med ML-tjänster struktureras på samma sätt som andra typer av HDInsight-kluster. De baseras på storleken på de underliggande virtuella datorerna över namn-, data-och Edge-noderna. Core – timme lyfter också. Mer information finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster i HDInsight-kluster finns i följande artiklar:

* [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio-Server](machine-learning-services-quickstart-job-rstudio.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Lagrings alternativ för ML Services-kluster i HDInsight](r-server-storage.md)