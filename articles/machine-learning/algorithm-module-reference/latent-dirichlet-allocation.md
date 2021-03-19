---
title: 'Latend Dirichlet-allokering: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den latenta Dirichlet för att gruppera annan oklassificerad text i kategorier.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907853"
---
# <a name="latent-dirichlet-allocation-module"></a>Tilldelnings modul för latend Dirichlet

I den här artikeln beskrivs hur du använder den Latende Dirichlet i Azure Machine Learning designer för att gruppera annars oklassificerad text i kategorier. 

Latend Dirichlet Allocation (LDA) används ofta i naturlig språk bearbetning för att hitta texter som liknar varandra. En annan vanlig term är *ämnes modeller*.

Den här modulen tar en kolumn med text och genererar dessa utdata:

+ Käll texten, tillsammans med en poäng för varje kategori

+ En funktions mat ris som innehåller extraherade villkor och koefficienter för varje kategori

+ En omvandling, som du kan spara och återanvända för ny text som används som indata

I den här modulen används scikit – lär dig biblioteket. Mer information om scikit-information finns i GitHub- [lagringsplatsen](https://github.com/scikit-learn/scikit-learn), som innehåller självstudier och en förklaring av algoritmen.

## <a name="more-about-latent-dirichlet-allocation"></a>Mer om Latend Dirichlet-allokering

LDA är vanligt vis inte en metod för klassificering. Men det använder en fullständig metod, så du behöver inte ange kända klass etiketter och sedan härleda mönstren.  Algoritmen genererar i stället en Probabilistic modell som används för att identifiera grupper av ämnen. Du kan använda Probabilistic-modellen för att klassificera antingen befintliga utbildnings ärenden eller nya ärenden som du anger som inaktuella för modellen.

Du kanske föredrar en fullständig modell eftersom den inte gör starka antaganden om relationen mellan texten och kategorierna. Den använder bara distributionen av ord till matematiska modell ämnen.

Teorien beskrivs i det här dokumentet, som är tillgängligt som en PDF-nedladdning: [latend Dirichlet-allokering: Blei, naturgas och Jordanien](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

Implementeringen i den här modulen baseras på [scikit-biblioteket](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) för LDA.

Mer information finns i avsnittet [tekniska anteckningar](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Så här konfigurerar du latens Dirichlet-allokering

Den här modulen kräver en data uppsättning som innehåller en kolumn med text, antingen RAW eller Förbearbetad.

1. Lägg till **tilldelnings modulen för den latenta Dirichlet** i din pipeline.

2. Som indata för modulen anger du en data uppsättning som innehåller en eller flera text kolumner.

3. För **mål kolumner** väljer du en eller flera kolumner som innehåller text att analysera.

    Du kan välja flera kolumner, men de måste vara av data typen **String** .

    Eftersom LDA skapar en stor funktions mat ris från texten, analyseras vanligt vis en enskild text kolumn.

4. Ange ett heltal mellan 1 och 1000 som anger hur många kategorier eller ämnen som du vill härleda från inmatad text för  **antal ämnen i modell**.

    Som standard skapas 5 ämnen.

5. För **n-gram** anger du den maximala längden för n-g som genereras under hashing.

    Standardvärdet är 2, vilket innebär att både 2 gram och unigrams genereras.

6. Välj alternativet **normalisera** för att konvertera utdata till sannolikhets värde. 

    I stället för att representera transformerade värden som heltal omvandlas värdena i data uppsättningen för utdata och funktionen enligt följande:

    + Värden i data uppsättningen visas som en sannolikhet var `P(topic|document)` .

    + Värdena i matrisen för funktions ämnen visas som en sannolikhet var `P(word|topic)` .

    > [!NOTE] 
    > I Azure Machine Learning Designer stöder biblioteket inte längre scikit *doc_topic_distr* utdata från version 0,19. I den här modulen kan **normaliserings** parametern endast användas för *mat ris utdata i funktions ämnet* . *Transformerade data uppsättnings* utdata är alltid normaliserade.

7. Välj alternativet **Visa alla alternativ** och ställ sedan in det på **Sant** om du vill ange följande avancerade parametrar.

    Dessa parametrar är speciella för scikit-lär implementeringen av LDA. Det finns några bra självstudier om LDA i scikit-och det officiella [scikit-dokumentet](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho-parameter**. Ange en tidigare sannolikhet för de glesa ämnes distributionerna. Den här parametern motsvarar sklearn- `topic_word_prior` parametern. Använd värdet **1** om du förväntar dig att fördelningen av ord är platt; det vill säga att alla ord antas vara equiprobable. Om du tror att de flesta ord ser glest ut kan du ange det till ett lägre värde.

    + **Alpha-parameter**. Ange en tidigare sannolikhet för den gleshet av ämnes vikter per dokument. Den här parametern motsvarar sklearn- `doc_topic_prior` parametern.

    + **Uppskattat antal dokument**. Ange ett tal som representerar din bästa uppskattning av antalet dokument (rader) som ska bearbetas. Med den här parametern kan modulen allokera en hash-tabell av tillräckligt stor storlek. Det motsvarar `total_samples` parametern i scikit-lär.

    + **Storlek på batchen**. Ange ett tal som anger hur många rader som ska tas med i varje sats med text som skickas till LDA-modellen. Den här parametern motsvarar `batch_size` parametern i scikit-lär.

    + **Initialt värde för iteration som används i utbildnings uppdaterings schema**. Ange start värde som downweights inlärnings takten för tidiga iterationer i online Learning. Den här parametern motsvarar `learning_offset` parametern i scikit-lär.

    + **Effekt som tillämpas på iterationen under uppdateringar**. Ange den energi nivå som tillämpas på antalet iterationer för att kontrol lera inlärnings hastigheten under online-uppdateringar. Den här parametern motsvarar `learning_decay` parametern i scikit-lär.

    + **Antal pass över data**. Ange det maximala antalet gånger som algoritmen ska gå över data. Den här parametern motsvarar `max_iter` parametern i scikit-lär.

8. Välj alternativet **build-ordlista för ngrams** eller **build-ordlista för ngrams före LDA** om du vill skapa en n-gram-lista i ett första pass innan du klassificerar text.

    Om du skapar den första ord listan i förväg kan du senare använda ord listan när modellen granskas. Att kunna mappa resultat till text i stället för numeriska index är vanligt vis enklare för tolkning. Det tar dock längre tid att spara ord listan att använda ytterligare lagrings utrymme.

9. Ange det totala antalet rader som kan skapas i ord listan för n-gram för **maximal storlek för ngram-ordlista**.

    Det här alternativet är användbart för att kontrol lera storleken på ord listan. Men om antalet ngrams i indatamängden överskrider den här storleken kan kollisioner uppstå.

10. Skicka pipelinen. LDA-modulen använder Bayes satsen för att avgöra vilka ämnen som kan kopplas till enskilda ord. Ord är inte exklusivt kopplade till några ämnen eller grupper. I stället har varje n-gram en inlärd sannolikhet att associeras med någon av de identifierade klasserna.

## <a name="results"></a>Resultat

Modulen har två utdata:

+ **Transformerad data uppsättning**: utdata innehåller inmatnings text, ett angivet antal identifierade kategorier och poängen för varje text exempel för varje kategori.

+ **Mat ris för funktions ämnen**: kolumnen längst till vänster innehåller funktionen extraherad text. En kolumn för varje kategori innehåller poängen för den funktionen i den kategorin.


### <a name="lda-transformation"></a>LDA-transformering

I den här modulen matas även *LDA-omvandlingen* som använder LDA till data uppsättningen.

Du kan spara den här omvandlingen och återanvända den för andra data uppsättningar. Den här tekniken kan vara användbar om du har tränat på en stor sökkorpus och vill återanvända koefficienterna eller kategorierna.

Om du vill använda den här omvandlingen väljer du ikonen **registrera data uppsättning** i den högra panelen i den Latende Dirichlet för att behålla modulen under **data uppsättnings** kategorin i listan modul. Sedan kan du ansluta den här modulen till modulen [Använd omvandling](apply-transformation.md) för att återanvända denna omvandling.

### <a name="refining-an-lda-model-or-results"></a>Förfina en LDA modell eller resultat

Normalt kan du inte skapa en enda LDA-modell som uppfyller alla behov. Även en modell som är utformad för en aktivitet kan kräva många iterationer för att förbättra noggrannheten. Vi rekommenderar att du provar alla dessa metoder för att förbättra din modell:

+ Ändra modell parametrarna
+ Använda visualisering för att förstå resultaten
+ Få feedback om ämnes experter för att avgöra om de genererade ämnena är användbara

Kvalitativa mått kan också vara användbara för att utvärdera resultaten. Överväg följande om du vill utvärdera ämnes modell resultaten:

+ Beräkning. Liknar de liknande objekten?
+ Spridning. Kan modellen skilja mellan liknande objekt när det krävs för affärs problemet?
+ Skalbarhet. Fungerar det i en mängd olika text kategorier eller bara på en begränsad mål domän?

Du kan ofta förbättra precisionen för modeller som baseras på LDA med hjälp av naturlig språk bearbetning för att rensa, sammanfatta och förenkla eller kategorisera text. Följande tekniker, som stöds i Azure Machine Learning, kan förbättra klassificerings precisionen:

+ Stoppa borttagning av ord

+ Fall normalisering

+ Lemmatisering eller stamering

+ Igenkänning av namngiven enhet

Mer information finns i [Förbearbeta text](preprocess-text.md).

I designern kan du också använda R-eller Python-bibliotek för text bearbetning: [Kör R-skript](execute-r-script.md),  [Kör Python-skript](execute-python-script.md).



## <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="implementation-details"></a>Implementeringsdetaljer

Som standard normaliseras distributioner av utdata för en transformerad data uppsättning och funktions avsnitts mat ris som sannolikhet:

+ Den transformerade data uppsättningen normaliseras som den villkorliga sannolikheten för ämnen som har fått ett dokument. I det här fallet är summan av varje rad lika med 1.

+ Matrisen för funktions ämnen normaliseras som den villkorliga sannolikheten för ord som anges i ett ämne. I det här fallet är summan av varje kolumn lika med 1.

> [!TIP]
> Ibland kan modulen returnera ett tomt ämne. Oftast är orsaken-slumpmässig initiering av algoritmen. Om detta inträffar kan du prova att ändra relaterade parametrar. Du kan till exempel ändra Max storleken för N-gram-ordlistan eller antalet bitar som ska användas för hashing av funktioner.

### <a name="lda-and-topic-modeling"></a>LDA-och ämnes modeller

Latend Dirichlet-allokering används ofta för *innehållsbaserade ämnes modeller*, vilket i princip innebär inlärnings kategorier från oklassificerad text. I Content-based ämnes modellering är ett ämne en distribution över ord.

Anta till exempel att du har angett en sökkorpus av kund granskningar som innehåller många produkter. Texten för granskningar som har skickats av kunder över tid innehåller många villkor, varav vissa används i flera ämnen.

Ett *ämne* som LDA-processen identifierar kan representera recensioner för en enskild produkt, eller så kan den representera en grupp produkt granskningar. I LDA är själva avsnittet bara en sannolikhets fördelning över tid för en uppsättning ord.

Termerna är sällan exklusiva till en enda produkt. De kan referera till andra produkter eller vara allmänna villkor som gäller för allt ("fantastiska", "Awful"). Andra termer kan vara skräpord. LDA-metoden försöker dock inte fånga alla ord i universum eller förstå hur orden är relaterade, förutom sannolikheten för samförekomster. Det kan bara gruppera ord som används i mål domänen.

När term indexen har beräknats jämför ett avstånds beroende likhets mått enskilda rader med text för att avgöra om två text delar liknar varandra. Du kan till exempel se att produkten har flera namn som är starkt korrelerade. Eller så kanske du upptäcker att starkt negativa villkor vanligt vis är associerade med en viss produkt. Du kan använda likhets måttet för att identifiera relaterade villkor och skapa rekommendationer.

###  <a name="module-parameters"></a>Parametrar för modul

|Namn|Typ|Intervall|Valfritt|Standardvärde|Beskrivning|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Mål kolumn (er)|Kolumn val||Obligatorisk|StringFeature|Mål kolumn namn eller index.|  
|Antal ämnen som ska modelleras|Integer|[1; 1000]|Obligatorisk|5|Modellera dokument distributionen mot N ämnen.|  
|N-gram|Integer|[1; 10]|Obligatorisk|2|Ordningen för N-gram genereras under hashing.|  
|Normalisera|Boolesk|Sant eller falskt|Obligatorisk|true|Normalisera utdata till sannolikhet.  Den transformerade data uppsättningen kommer att vara P (ämne&#124;dokument) och matrisen för funktions ämnet är P (Word&#124;-avsnittet).|  
|Visa alla alternativ|Boolesk|Sant eller falskt|Obligatorisk|Falskt|Visar ytterligare parametrar som är speciella för scikit – lära online-LDA.|  
|Rho-parameter|Float|[0.00001; 1.0]|Gäller när kryss rutan **Visa alla alternativ** är markerad|0,01|Avsnittet Word föregående distribution.|  
|Alpha-parameter|Float|[0.00001; 1.0]|Gäller när kryss rutan **Visa alla alternativ** är markerad|0,01|Dokument ämnet tidigare distribution.|  
|Uppskattat antal dokument|Integer|[1; int. MaxValue|Gäller när kryss rutan **Visa alla alternativ** är markerad|1000|Uppskattat antal dokument. Motsvarar `total_samples` parametern.|  
|Storlek på batchen|Integer|[1; 1024]|Gäller när kryss rutan **Visa alla alternativ** är markerad|32|Storlek på batchen.|  
|Initialt värde för iteration som används i uppdaterings schema för inlärnings takt|Integer|[0; int. MaxValue|Gäller när kryss rutan **Visa alla alternativ** är markerad|0|Det första värdet som downweights inlärnings pris för tidiga iterationer. Motsvarar `learning_offset` parametern.|  
|Effekt som tillämpas på iterationen under uppdateringar|Float|[0.0; 1.0]|Gäller när kryss rutan **Visa alla alternativ** är markerad|0,5|Effekt som tillämpas på antalet iterationer för att kontrol lera inlärnings takten. Motsvarar `learning_decay` parametern. |  
|Antal upprepningar av utbildning|Integer|[1; 1024]|Gäller när kryss rutan **Visa alla alternativ** är markerad|25|Antal upprepningar av inlärning.|  
|Versions ord lista för ngrams|Boolesk|Sant eller falskt|Gäller när kryss rutan **Visa alla alternativ** *inte* är markerad|Sant|Skapar en ord lista med ngrams innan du beräknar LDA. Användbart för modell besiktning och tolkning.|  
|Maximal storlek för ngram-ordlista|Integer|[1; int. MaxValue|Gäller när alternativet **build-ordlistan för ngrams** är **Sant**|20000|Maximal storlek för ngrams-ordlistan. Om antalet tokens i indatamängden överskrider den här storleken kan kollisioner uppstå.|  
|Antal bitar som ska användas för hashing av funktioner.|Integer|[1; 31]|Gäller när kryss rutan **Visa alla alternativ** *inte* är markerad och **build-ordlistan för ngrams** är **falskt**|12|Antal bitar som ska användas för hashing av funktioner.| 
|Build-ordlista av ngrams före LDA|Boolesk|Sant eller falskt|Gäller när kryss rutan **Visa alla alternativ** är markerad|Sant|Skapar en ord lista med ngrams före LDA. Användbart för modell besiktning och tolkning.|  
|Maximalt antal ngrams i ord listan|Integer|[1; int. MaxValue|Gäller när kryss rutan **Visa alla alternativ** är markerad och alternativet build- **ordlista för ngrams** är **Sant**|20000|Maximal storlek för ord listan. Om antalet tokens i indatamängden överskrider den här storleken kan kollisioner uppstå.|  
|Antal hash-bitar|Integer|[1; 31]|Gäller när kryss rutan **Visa alla alternativ** är markerad och alternativet build- **ordlista för ngrams** är **falskt**|12|Antal bitar som ska användas vid hashing av funktioner.|   


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

En lista med fel som är speciella för modulerna finns i [undantag och felkoder för designern](designer-error-codes.md).
