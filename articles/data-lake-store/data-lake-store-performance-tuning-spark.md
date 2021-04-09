---
title: Prestanda justering – Spark med Azure Data Lake Storage Gen1
description: Lär dig mer om prestanda justerings rikt linjer för Spark på Azure HDInsight och Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722512"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Prestanda justerings vägledning för Spark på HDInsight och Azure Data Lake Storage Gen1

När du justerar prestanda för Spark måste du fundera över antalet appar som ska köras i klustret. Som standard kan du köra fyra appar samtidigt på ditt HDI-kluster (Obs! standardinställningen kan ändras). Du kan välja att använda färre appar så att du kan åsidosätta standardinställningarna och använda mer av klustret för dessa appar.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage gen1-konto. Se [skapa ett HDInsight-kluster med data Lake Storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att aktivera fjärr skrivbord för klustret.
* **Kör Spark-kluster på data Lake Storage gen1**. Mer information finns i [använda HDInsight Spark-kluster för att analysera data i data Lake Storage gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Rikt linjer för prestanda justering på data Lake Storage gen1**. Allmänna prestanda koncept finns i [vägledning för data Lake Storage gen1 prestanda justering](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametrar

När du kör Spark-jobb är följande de viktigaste inställningarna som kan justeras för att öka prestandan på Data Lake Storage Gen1:

* Antal **körningar** – antalet samtidiga aktiviteter som kan utföras.

* **Utförar – minne** – mängden minne som allokerats till varje utförar.

* **Utförar-kärnor** – antalet kärnor som allokerats till varje utförar.

**Antal körningar** Antal körningar anger det maximala antalet uppgifter som kan köras parallellt. Det faktiska antalet aktiviteter som kan köras parallellt begränsas av de minnes-och processor resurser som är tillgängliga i klustret.

**Utförar-minne** Detta är mängden minne som allokeras till varje utförar. Det minne som krävs för varje utförar är beroende av jobbet. För komplexa åtgärder måste minnet vara högre. För enkla åtgärder som Läs-och skriv åtgärder är minnes kraven lägre. Mängden minne för varje utförar kan visas i Ambari. I Ambari navigerar du till Spark och visar fliken **configs** .

**Utförar – kärnor** Detta anger mängden kärnor som används per utförar, vilket avgör antalet parallella trådar som kan köras per utförar. Om t. ex. utförar-cores = 2 kan varje utförar köra 2 parallella uppgifter i utförar. De utförar-kärnor som behövs är beroende av jobbet. I/O-tungt arbete kräver inte en stor mängd minne per aktivitet så att varje utförar kan hantera mer parallella uppgifter.

Som standard definieras två virtuella garn kärnor för varje fysisk kärna när Spark körs på HDInsight. Det här talet ger en bättre balans mellan samtidighet och mängd kontext växling från flera trådar.

## <a name="guidance"></a>Vägledning

När du kör Spark-analytiska arbets belastningar för att arbeta med data i Data Lake Storage Gen1 rekommenderar vi att du använder den senaste HDInsight-versionen för att få bästa möjliga prestanda med Data Lake Storage Gen1. När jobbet är mer I/O-intensiv kan vissa parametrar konfigureras för att förbättra prestanda. Data Lake Storage Gen1 är en mycket skalbar lagrings plattform som kan hantera stora data flöden. Om jobbet huvudsakligen består av Läs-eller Skriv åtgärder kan du öka prestandan genom att öka samtidigheten för I/O till och från Data Lake Storage Gen1.

Det finns några allmänna sätt att öka samtidigheten för I/O-intensiva jobb.

**Steg 1: Bestäm hur många appar som körs i klustret** – du bör veta hur många appar som körs på klustret inklusive det aktuella. Standardvärdena för varje spark-inställning förutsätter att det finns 4 appar som körs samtidigt. Därför kommer du bara att ha 25% av klustret tillgängligt för varje app. För att få bättre prestanda kan du åsidosätta standardvärdena genom att ändra antalet körningar.

**Steg 2: Ange utförar-Memory** – det första du ställer in är utförar-minnet. Minnet kommer att vara beroende av det jobb som du ska köra. Du kan öka samtidigheten genom att allokera mindre minne per utförar. Om du ser slut på minnes undantag när du kör jobbet bör du öka värdet för den här parametern. Ett alternativ är att få mer minne genom att använda ett kluster som har större minnes mängder eller ökar storleken på klustret. Mer minne gör att fler körningar kan användas, vilket innebär mer samtidighet.

**Steg 3: Ange utförar-kärnor** – för I/O-intensiva arbets belastningar som inte har avancerade åtgärder, är det bäst att börja med ett stort antal utförar-kärnor för att öka antalet parallella aktiviteter per utförar. Att ställa in utförar-kärnor på 4 är en korrekt start.

```console
executor-cores = 4
```

Om du ökar antalet utförar-kärnor får du mer parallellt så att du kan experimentera med olika utförar-kärnor. För jobb som har mer komplexa åtgärder bör du minska antalet kärnor per utförar. Om utförar-kärnor är högre än 4 kan skräp insamling bli ineffektiv och försämra prestanda.

**Steg 4: Bestäm mängden garn minne i kluster** – den här informationen finns i Ambari. Navigera till garn och Visa fliken Contigs. GARN minnet visas i det här fönstret.
Observera att när du är i fönstret kan du också se standard storleken för garn behållare. GARN behållarens storlek är samma som minne per utförar-parameter.

Totalt garn minne = noder * garn minne per nod

**Steg 5: beräkna antal körningar**

**Beräkna minnes begränsning** -parametern för antal körningar är begränsad antingen av minne eller av CPU. Minnes begränsningen bestäms av mängden tillgängligt garn minne för ditt program. Ta det totala garn minnet och dividera det med utförar-Memory. Begränsningen måste Avskalas för antalet appar så att vi delar upp det med antalet appar.

Minnes begränsning = (totalt garn minne/utförar-minne)/antal appar

**Beräkna processor begränsning** – processor begränsningen beräknas som de totala virtuella kärnorna dividerat med antalet kärnor per utförar. Det finns två virtuella kärnor för varje fysisk kärna. På liknande sätt som minnes begränsningen har vi dividerat med antalet appar.

virtuella kärnor = (noder i klustret * antal fysiska kärnor i Node * 2) processor begränsning = (totalt antal virtuella kärnor/antal kärnor per utförar)/antal appar

**Ange antal körningar** – parametern för antalet körningar bestäms genom att ta minsta minnes begränsning och processor begränsning. 

antal körningar = min (totalt antal virtuella kärnor/antal kärnor per utförar, tillgängligt garn minne/utförar-minne) en högre mängd antal körningar ökar inte nödvändigt vis prestanda. Du bör överväga att lägga till fler körningar för att lägga till extra kostnader för varje ytterligare utförar, vilket kan försämra prestanda. Antal körningar begränsas av kluster resurserna.

## <a name="example-calculation"></a>Exempel beräkning

Anta att du för närvarande har ett kluster bestående av 8 D4v2-noder som kör två appar, inklusive den som du kommer att köra.

**Steg 1: ta reda på hur många appar som körs i klustret** – du vet att du har två appar i klustret, inklusive det som du ska köra.

**Steg 2: Ange utförar-Memory** – i det här exemplet fastställer vi att 6 GB för utförar-minne är tillräckligt för i/O-intensiva jobb.

```console
executor-memory = 6GB
```

**Steg 3: Ange utförar-kärnor** – eftersom det här är ett I/O-intensivt jobb kan vi ange antalet kärnor för varje utförar till fyra. Att ställa in kärnor per utförar till större än fyra kan orsaka problem med skräp insamling.

```console
executor-cores = 4
```

**Steg 4: Bestäm mängden garn minne i kluster** – vi navigerar till Ambari för att ta reda på att varje D4V2 har 25 GB garn minne. Eftersom det finns 8 noder multipliceras det tillgängliga garn minnet med 8.

Totalt garn minne = noder * garn minne * per nod totalt garn minne = 8 noder * 25 GB = 200 GB

**Steg 5: beräkna antal körningar** – parametern för antalet körningar bestäms genom att minimi kraven för minnes begränsningen och CPU-begränsningen divideras med antalet appar som körs på Spark.

**Beräkna minnes begränsning** – minnes begränsningen beräknas som det totala garn minnet delat med minnet per utförar.

Minnes begränsning = (totalt garn minne/utförar-minne)/antal appar minnes begränsning = (200 GB/6 GB)/2 minnes begränsning = 16 (avrundad) **beräkna processor begränsning** – processor begränsningen beräknas som den totala garn kärnan dividerat med antalet kärnor per utförar.

GARN kärnor = noder i kluster * antal kärnor per nod * 2 garn kärnor = 8 noder * 8 kärnor per D14 * 2 = 128 processor begränsning = (totalt garn kärnor/antal kärnor per utförar)/antal appar CPU-begränsning = (128/4)/2 processor begränsning = 16

**Ange antal körningar**

NUM-exekverare = min (minnes begränsning, processor begränsning) antal-körningar = min (16, 16) NUM-exekverare = 16