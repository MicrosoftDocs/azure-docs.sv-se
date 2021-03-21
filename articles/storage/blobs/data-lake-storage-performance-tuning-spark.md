---
title: 'Justera prestanda: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Förstå rikt linjerna för att justera prestandan för Spark med Azure HDInsight och Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: c99d57ddd86ecff71c35ad6c0f2c2561e279b4b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912815"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: Spark, HDInsight & Azure Data Lake Storage Gen2

När du justerar prestanda för Spark måste du fundera över antalet appar som ska köras i klustret.  Som standard kan du köra 4 appar samtidigt på ditt HDI-kluster (Obs! standardinställningen kan ändras).  Du kan välja att använda färre appar så att du kan åsidosätta standardinställningarna och använda mer av klustret för dessa appar.  

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2 konto**. Anvisningar om hur du skapar ett finns i [snabb start: skapa ett Azure Data Lake Storage Gen2 lagrings konto](../common/storage-account-create.md).
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Se till att aktivera fjärr skrivbord för klustret.
* **Kör Spark-kluster på data Lake Storage Gen2**.  Mer information finns i [använda HDInsight Spark-kluster för att analysera data i data Lake Storage Gen2](../../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Rikt linjer för prestanda justering på data Lake Storage Gen2**.  Allmänna prestanda koncept finns i [vägledning för data Lake Storage Gen2 prestanda justering](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametrar

När du kör Spark-jobb är följande de viktigaste inställningarna som kan justeras för att öka prestandan på Data Lake Storage Gen2:

* Antal **körningar** – antalet samtidiga aktiviteter som kan utföras.

* **Utförar – minne** – mängden minne som allokerats till varje utförar.

* **Utförar-kärnor** – antalet kärnor som allokerats till varje utförar.                     

**Antal körningar** Antal körningar anger det maximala antalet uppgifter som kan köras parallellt.  Det faktiska antalet aktiviteter som kan köras parallellt begränsas av de minnes-och processor resurser som är tillgängliga i klustret.

**Utförar-minne** Detta är mängden minne som allokeras till varje utförar.  Det minne som krävs för varje utförar är beroende av jobbet.  För komplexa åtgärder måste minnet vara högre.  För enkla åtgärder som Läs-och skriv åtgärder är minnes kraven lägre.  Mängden minne för varje utförar kan visas i Ambari.  I Ambari navigerar du till Spark och visar fliken configs.  

**Utförar – kärnor** Detta anger antalet kärnor som används per utförar, vilket avgör antalet parallella trådar som kan köras per utförar.  Om t. ex. utförar-cores = 2 kan varje utförar köra 2 parallella uppgifter i utförar.  De utförar-kärnor som behövs är beroende av jobbet.  I/O-tungt arbete kräver inte en stor mängd minne per aktivitet så att varje utförar kan hantera mer parallella uppgifter.

Som standard definieras två virtuella garn kärnor för varje fysisk kärna när Spark körs på HDInsight.  Det här talet ger en bättre balans mellan samtidighet och mängd kontext växling från flera trådar.  

## <a name="guidance"></a>Vägledning

När du kör Spark-analytiska arbets belastningar för att arbeta med data i Data Lake Storage Gen2 rekommenderar vi att du använder den senaste HDInsight-versionen för att få bästa möjliga prestanda med Data Lake Storage Gen2. När jobbet är mer I/O-intensiv kan vissa parametrar konfigureras för att förbättra prestanda.  Data Lake Storage Gen2 är en mycket skalbar lagrings plattform som kan hantera stora data flöden.  Om jobbet huvudsakligen består av Läs-eller Skriv åtgärder kan du öka prestandan genom att öka samtidigheten för I/O till och från Data Lake Storage Gen2.

Det finns några allmänna sätt att öka samtidigheten för I/O-intensiva jobb.

**Steg 1: Bestäm hur många appar som körs i klustret** – du bör veta hur många appar som körs på klustret inklusive det aktuella.  Standardvärdena för varje spark-inställning förutsätter att det finns 4 appar som körs samtidigt.  Därför kommer du bara att ha 25% av klustret tillgängligt för varje app.  För att få bättre prestanda kan du åsidosätta standardvärdena genom att ändra antalet körningar.  

**Steg 2: Ange utförar-Memory** – det första du ställer in är utförar-minnet.  Minnet kommer att vara beroende av det jobb som du ska köra.  Du kan öka samtidigheten genom att allokera mindre minne per utförar.  Om du ser slut på minnes undantag när du kör jobbet bör du öka värdet för den här parametern.  Ett alternativ är att få mer minne genom att använda ett kluster som har större minnes mängder eller ökar storleken på klustret.  Mer minne gör att fler körningar kan användas, vilket innebär mer samtidighet.

**Steg 3: Ange utförar-kärnor** – för I/O-intensiva arbets belastningar som inte har avancerade åtgärder, är det bäst att börja med ett stort antal utförar-kärnor för att öka antalet parallella aktiviteter per utförar.  Att ställa in utförar-kärnor på 4 är en korrekt start.   

utförar-kärnor = 4

Om du ökar antalet utförar-kärnor får du mer parallellt så att du kan experimentera med olika utförar-kärnor.  För jobb som har mer komplexa åtgärder bör du minska antalet kärnor per utförar.  Om utförar-kärnor är högre än 4 kan skräp insamling bli ineffektiv och försämra prestanda.

**Steg 4: Bestäm mängden garn minne i kluster** – den här informationen finns i Ambari.  Navigera till garn och Visa fliken configs.  GARN minnet visas i det här fönstret.  
Observera att när du är i fönstret kan du också se standard storleken för garn behållare.  GARN behållarens storlek är samma som minne per utförar-parameter.

Totalt garn minne = noder * garn minne per nod

**Steg 5: beräkna antal körningar**

**Beräkna minnes begränsning** -parametern för antal körningar är begränsad antingen av minne eller av CPU.  Minnes begränsningen bestäms av mängden tillgängligt garn minne för ditt program.  Du bör ta totalt garn minne och dividera det med utförar-Memory.  Begränsningen måste Avskalas för antalet appar så att vi delar upp det med antalet appar.

Minnes begränsning = (totalt garn minne/utförar-minne)/antal appar

**Beräkna processor begränsning** – processor begränsningen beräknas som de totala virtuella kärnorna dividerat med antalet kärnor per utförar.  Det finns två virtuella kärnor för varje fysisk kärna.  På liknande sätt som minnes begränsningen måste vi dividera med antalet appar.

- virtuella kärnor = (noder i klustret * antal fysiska kärnor i nod * 2)
- PROCESSOR begränsning = (totalt antal virtuella kärnor/antal kärnor per utförar)/antal appar

**Ange antal körningar** – parametern för antalet körningar bestäms genom att ta minsta minnes begränsning och processor begränsning. 

NUM-exekverare = min (totalt antal virtuella kärnor/antal kärnor per utförar, tillgängligt garn minne/utförar-minne)

Att ange ett högre antal fler körningar ökar inte nödvändigt vis prestanda.  Du bör överväga att lägga till fler körningar för att lägga till extra kostnader för varje ytterligare utförar, vilket kan försämra prestanda.  Antal körningar begränsas av kluster resurserna.    

## <a name="example-calculation"></a>Exempel beräkning

Anta att du för närvarande har ett kluster bestående av 8 D4v2-noder som kör 2 appar, inklusive den som du kommer att köra.  

**Steg 1: ta reda på hur många appar som körs i klustret** – du vet att du har två appar i klustret, inklusive det som du ska köra.  

**Steg 2: Ange utförar-Memory** – i det här exemplet fastställer vi att 6 GB för utförar-minne är tillräckligt för i/O-intensiva jobb.  

utförar-Memory = 6 GB

**Steg 3: Ange utförar-kärnor** – eftersom det här är ett I/O-intensivt jobb kan vi ange antalet kärnor för varje utförar till 4.  Att ställa in kärnor per utförar till större än 4 kan orsaka problem med skräp insamling.  

utförar-kärnor = 4

**Steg 4: Bestäm mängden garn minne i kluster** – vi navigerar till Ambari för att ta reda på att varje D4V2 har 25 GB garn minne.  Eftersom det finns 8 noder multipliceras det tillgängliga garn minnet med 8.

- Totalt garn minne = noder * garn minne * per nod
- Totalt garn minne = 8 noder * 25 GB = 200 GB

**Steg 5: beräkna antal körningar** – parametern för antalet körningar bestäms genom att minimi kraven för minnes begränsningen och CPU-begränsningen divideras med antalet appar som körs på Spark.    

**Beräkna minnes begränsning** – minnes begränsningen beräknas som det totala garn minnet delat med minnet per utförar.

- Minnes begränsning = (totalt garn minne/utförar-minne)/antal appar
- Minnes begränsning = (200 GB/6 GB)/2
- Minnes begränsning = 16 (avrundat)

**Beräkna processor begränsning** – processor begränsningen beräknas som det totala antalet garn kärnor dividerat med antalet kärnor per utförar.

- GARN kärnor = noder i klustret * antal kärnor per nod * 2
- GARN kärnor = 8 noder * 8 kärnor per D14 * 2 = 128
- PROCESSOR begränsning = (totalt garn kärnor/antal kärnor per utförar)/antal appar
- PROCESSOR begränsning = (128/4)/2
- PROCESSOR begränsning = 16

**Ange antal körningar**

- antal körningar = min (minnes begränsning, processor begränsning)
- antal körningar = min (16, 16)
- antal körningar = 16