---
title: 'Justera prestanda: storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Förstå rikt linjerna för att justera prestanda för en Azure Storm-topologi i ett Azure HDInsight-kluster och Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912798"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: storm, HDInsight & Azure Data Lake Storage Gen2

Förstå faktorerna som bör övervägas när du justerar prestandan för en Azure Storm-topologi. Det är till exempel viktigt att förstå egenskaperna hos det arbete som utförs av kanaler och bultarna (oavsett om arbetet är I/O eller minnes krävande). Den här artikeln beskriver ett antal rikt linjer för prestanda justering, inklusive fel sökning av vanliga problem.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2 konto**. Anvisningar om hur du skapar ett finns i [snabb start: skapa ett lagrings konto för analys](../common/storage-account-create.md).
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Se till att aktivera fjärr skrivbord för klustret.
* **Köra ett Storm-kluster på data Lake Storage Gen2**. Mer information finns i [storm på HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Rikt linjer för prestanda justering på data Lake Storage Gen2**.  Allmänna prestanda koncept finns i [rikt linjer för data Lake Storage Gen2 prestanda justering](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Justera topologins parallellitet

Du kanske kan förbättra prestandan genom att öka samtidigheten för I/O till och från Data Lake Storage Gen2. En Storm-topologi har en uppsättning konfigurationer som fastställer parallellitet:
* Antal arbets processer (arbets tagarna är jämnt fördelade på de virtuella datorerna).
* Antal kanalen utförar-instanser.
* Antal bult utförar-instanser.
* Antal kanalen-uppgifter.
* Antal bult-aktiviteter.

Till exempel, i ett kluster med 4 virtuella datorer och 4 arbets processer, 32 kanalen-körningar och 32 kanalen-uppgifter, samt 256 bult-körningar och 512 bult-uppgifter, bör du tänka på följande:

Varje ansvarig, som är en arbetsnod, har en enda Worker Java Virtual Machine (JVM)-process. Den här JVM-processen hanterar 4 kanalen-trådar och 64 bult-trådar. I varje tråd körs aktiviteter sekventiellt. Med föregående konfiguration har varje kanalen-tråd 1 uppgift och varje bult-tråd har två uppgifter.

I storm är här de olika komponenterna som berörs och hur de påverkar graden av parallellitet som du har:
* Head-noden (kallas Nimbus i Storm) används för att skicka och hantera jobb. Dessa noder påverkar inte graden av parallellitet.
* De överordnade noderna. I HDInsight motsvarar detta en arbetsnods Azure VM.
* Arbets uppgifterna är Storm-processer som körs i de virtuella datorerna. Varje arbets uppgift motsvarar en JVM-instans. Storm distribuerar det antal arbets processer som du anger till arbetsnoderna så jämnt som möjligt.
* Kanalen-och bult utförar-instanser. Varje utförar-instans motsvarar en tråd som körs i arbets tagarna (JVMs).
* Storm-aktiviteter. Detta är logiska uppgifter som var och en av dessa trådar körs. Detta påverkar inte graden av parallellitet, så du bör utvärdera om du behöver flera aktiviteter per utförar eller inte.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Få bästa möjliga prestanda från Data Lake Storage Gen2

När du arbetar med Data Lake Storage Gen2 får du bästa möjliga prestanda om du gör följande:
* Slå samman dina små tillägg i större storlekar.
* Gör så många samtidiga förfrågningar som du kan. Eftersom varje bult-tråd blockerar läsningar, vill du ha någonstans i intervallet 8-12 trådar per kärna. Detta skyddar NÄTVERKSKORTet och den processor som används mycket. En större virtuell dator möjliggör fler samtidiga begär Anden.  

### <a name="example-topology"></a>Exempel sto pol Ogin

Vi antar att du har ett 8 Work Node-kluster med en D13v2 Azure VM. Den här virtuella datorn har 8 kärnor, så mellan de 8 arbetsnoderna har du 64 total kärnor.

Låt oss säga att vi har åtta bult-trådar per kärna. Med 64 kärnor innebär det att vi vill ha 512 totalt utförar-instanser för bult (det vill säga trådar). I det här fallet antar vi att vi börjar med en JVM per virtuell dator och använder i huvudsak tråd samtidigheten i JVM för att uppnå samtidighet. Det innebär att vi behöver 8 arbets uppgifter (en per virtuell Azure-dator) och 512 bult-körningar. Med hänsyn till den här konfigurationen försöker Storm att distribuera arbets tagarna jämnt över arbetsnoderna (även kallade tillsynsmyndigheter), vilket ger varje arbetsnod 1-JVM. Storm försöker nu att distribuera körningarna jämnt mellan supervisor, vilket ger varje ansvarig (det vill säga JVM) 8 trådar var som helst.

## <a name="tune-additional-parameters"></a>Justera ytterligare parametrar
När du har en grundläggande topologi kan du fundera över om du vill ändra någon av parametrarna:
* **Antal JVMs per arbets nod.** Om du har en stor data struktur (till exempel en uppslags tabell) som du är värd för i minnet, kräver varje JVM en separat kopia. Du kan också använda data strukturen för många trådar om du har färre JVMs. För bultens I/O gör antalet JVMs inte lika mycket av skillnaden som antalet trådar som har lagts till i JVMs. För enkelhetens skull är det en bra idé att ha en JVM per arbets tagare. Du kan behöva ändra det här värdet, beroende på vad din bult gör eller vilken program bearbetning du behöver.
* **Antal kanalen-körningar.** Eftersom föregående exempel använder bultar för att skriva till Data Lake Storage Gen2, är antalet kanaler inte direkt relevant för bultens prestanda. Beroende på mängden bearbetning eller I/O-åtgärder i kanalen är det dock en bra idé att justera kanaler för bästa prestanda. Se till att du har tillräckligt många kanaler för att kunna hålla bultarna upptagna. De utgående frekvenserna för kanaler bör matcha bultens data flöde. Den faktiska konfigurationen beror på kanalen.
* **Antal aktiviteter.** Varje bult körs som en enda tråd. Ytterligare aktiviteter per bult ger ingen ytterligare samtidighet. Den enda tiden de är av förmånen är om din process att bekräfta att tuppeln tar en stor del av din bult-körnings tid. Det är en bra idé att gruppera många tupler i ett större tillägg innan du skickar en bekräftelse från bulten. I de flesta fall ger flera aktiviteter ingen ytterligare förmån.
* **Lokal eller blandad gruppering.** När den här inställningen är aktive rad skickas tupler till bultar inom samma arbets process. Detta minskar kommunikation mellan processer och nätverks anrop. Detta rekommenderas för de flesta topologier.

Det här grundläggande scenariot är en lämplig start punkt. Testa med dina egna data för att justera föregående parametrar för att uppnå optimala prestanda.

## <a name="tune-the-spout"></a>Finjustera kanalen

Du kan ändra följande inställningar för att finjustera kanalen.

- **Tupel-timeout: topologi. Message. timeout. sekunder**. Den här inställningen avgör hur lång tid ett meddelande tar att slutföra och får bekräftelse innan det betraktas som misslyckat.

- **Högsta mängd minne per arbets process: Worker. childopts**. Med den här inställningen kan du ange ytterligare kommando rads parametrar för Java-arbetarna. Den vanligaste inställningen här är XmX, som avgör den maximala mängd minne som allokerats till en JVM-heap.

- **Max kanalen väntar: topologi. max. kanalen. väntar**. Den här inställningen avgör antalet tupler som kan vara flyg (ännu inte accepterade på alla noder i topologin) per kanalen-tråd när som helst.

  En lämplig beräkning är att beräkna storleken på varje tupel. Räkna sedan upp hur mycket minne en kanalen tråd har. Det totala minne som allokerats till en tråd, dividerat med det här värdet, ska ge dig den övre bindningen för den maximala kanalen-parametern som väntar.

Standard Data Lake Storage Gen2 Storm bult har en princip parameter för fileBufferSize (size) som kan användas för att finjustera den här parametern.

I i/O-intensiva topologier är det en bra idé att låta varje bult-tråd skriva till sin egen fil och att ange en fil rotations princip (fileRotationSize). När filen når en viss storlek, töms strömmen automatiskt och en ny fil skrivs till. Den rekommenderade fil storleken för rotation är 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Övervaka din topologi i storm  
När topologin körs kan du övervaka den i storm-användargränssnittet. Följande är de viktigaste parametrarna för att titta på:

* **Total fördröjning för process körning.** Detta är den genomsnittliga tiden som en tupel tar att genereras av kanalen, bearbetas av blixten och bekräftas.

* **Total blixt process svars tid.** Detta är den genomsnittliga tiden som tuppeln tar i bulten tills den får en bekräftelse.

* **Körnings fördröjning för total bult.** Detta är den genomsnittliga tiden som bulten har använt i Execute-metoden.

* **Antal felaktiga försök.** Detta avser antalet tupler som inte kunde bearbetas fullständigt innan tids gränsen uppnåddes.

* **Kapaciteten.** Detta är ett mått på hur upptagen systemet är. Om det här värdet är 1 fungerar bultarna så snabbt som de kan. Om det är mindre än 1 ökar du parallellt. Om det är större än 1 minskar du parallellt.

## <a name="troubleshoot-common-problems"></a>Felsök vanliga problem
Här följer några vanliga fel söknings scenarier.
* **Tids gränsen har nåtts för många tupler.** Ta reda på var Flask halsen är genom att titta på varje nod i topologin. Den vanligaste orsaken till detta är att bultarna inte kan hålla sig med kanaler. Detta leder till tupler clogging de interna buffertarna medan de väntar på att bearbetas. Överväg att öka timeout-värdet eller minska det högsta antalet kanalen som väntar.

* **Det finns en hög total process körnings fördröjning, men en svars tid för låg bult.** I det här fallet är det möjligt att tuplerna inte bekräftas tillräckligt snabbt. Kontrol lera att det finns tillräckligt många bekräftelser. En annan möjlighet är att de väntar i kön för länge innan bultarna börjar bearbeta dem. Minska Max kanalen.

* **Det finns en hög bult-körnings fördröjning.** Det innebär att metoden Execute () för din bult tar för lång tid. Optimera koden eller titta på Skriv storlek och tömnings beteende.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 begränsning
Om du når gränserna för bandbredden som tillhandahålls av Data Lake Storage Gen2 kan du se aktivitets felen. Kontrol lera aktivitets loggarna för att begränsa fel. Du kan minska parallellheten genom att öka behållarens storlek.    

Om du vill kontrol lera om du får en begränsning aktiverar du fel söknings loggning på klient sidan:

1. I **Ambari**  >  **Storm**  >  **config**  >  **Advanced Storm-Work-log4j**, ändra **&lt; rot nivå = "info" &gt;** till **&lt; rotnivån = "debug" &gt;**. Starta om alla noder/tjänster för att konfigurationen ska börja gälla.
2. Övervaka loggfilerna för Storm-topologin på arbetsnoder (under/var/log/Storm/Worker-Artifacts/ &lt; TopologyName &gt; / &lt; port &gt; /Worker.log) för data Lake Storage Gen2 begränsnings undantag.

## <a name="next-steps"></a>Nästa steg
Ytterligare prestanda justering för Storm kan refereras till i [den här bloggen](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

Ytterligare ett exempel på hur du kör finns i [den här GitHub](https://github.com/hdinsight/storm-performance-automation).