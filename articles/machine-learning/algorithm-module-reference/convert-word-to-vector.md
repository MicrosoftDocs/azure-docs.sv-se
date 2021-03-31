---
title: 'Konvertera ord till Vector: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder tre tillhandahållna Word2Vec-modeller för att extrahera en vokabulär och dess motsvarande Word-inbäddningar från en sökkorpus text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91536740"
---
# <a name="convert-word-to-vector-module"></a>Konvertera Word till Vector-modul

Den här artikeln beskriver hur du använder modulen konvertera Word till Vector i Azure Machine Learning designer för att utföra dessa uppgifter:

- Använd olika Word2Vec-modeller (Word2Vec, FastText, assisterad förtränad modell) på sökkorpus för text som du har angett som indatamängd.
- Skapa en ord lista med Word-inbäddningar.

I den här modulen används Gensim-biblioteket. Mer information om Gensim finns på den [officiella webbplatsen](https://radimrehurek.com/gensim/apiref.html), som innehåller självstudier och en förklaring av algoritmer.

### <a name="more-about-converting-words-to-vectors"></a>Mer om att konvertera ord till vektorer

Att konvertera ord till vektorer eller Word-vectorization är en process för naturlig språk bearbetning (NLP). Processen använder språk modeller för att mappa ord till vektor utrymme. Ett vektor utrymme representerar varje ord med en Vector med reella tal. Det innebär också att ord med liknande innebörd har liknande representationer.

Använd Word-inbäddningar som inledande indatamängd för NLP efterföljande uppgifter, till exempel text klassificering och sentiment analys.

I den här modulen implementerade vi tre vanliga metoder mellan olika ord inbäddnings tekniker. Två, Word2Vec och FastText är online-tränings modeller. Den andra är en förtränad modell, assisterad-wiki-gigaword-100. 

Online-tränings modeller är utbildade för indata. Förtränade modeller är intränat offline på en större text sökkorpus (till exempel Wikipedia, Google News) som vanligt vis innehåller cirka 100 000 000 000 ord. Word-inbäddningen förblir konstant under Word-vectorization. Förtränade Word-modeller ger fördelar som minskad inlärnings tid, bättre Word-vektorer kodade och förbättrade övergripande prestanda.

Här är lite information om metoderna:

+ Word2Vec är en av de mest populära teknikerna för att lära sig Word-inbäddningar med hjälp av ett ytligt neurala nätverk. Teorien beskrivs i det här dokumentet, som är tillgängligt som en PDF-nedladdning: [effektiv uppskattning av ord representationer i vektor utrymme](https://arxiv.org/pdf/1301.3781.pdf). Implementeringen i den här modulen baseras på [Gensim-biblioteket för Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ FastText teori förklaras i det här dokumentet, som är tillgängligt som en PDF-nedladdning: att [utöka ord vektorer med ord information](https://arxiv.org/pdf/1607.04606.pdf). Implementeringen i den här modulen baseras på [Gensim-biblioteket för fasttext](https://radimrehurek.com/gensim/models/fasttext.html).

+ Den förtränade assisterad-modellen är assisterad-wiki-gigaword-100. Det är en samling av förtränade vektorer som baseras på en Wikipedia-text sökkorpus, som innehåller 5 600 000 000 tokens och 400 000 för ord som inte är specialfall. En PDF-nedladdning är tillgänglig: [assisterad: globala vektorer för Word-representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Så här konfigurerar du konvertera ord till Vector

Den här modulen kräver en data uppsättning som innehåller en kolumn med text. Förbearbetad text är bättre.

1. Lägg till kommandot **konvertera ord till Vector** i din pipeline.

2. Som indata för modulen anger du en data uppsättning som innehåller en eller flera text kolumner.

3. För **mål kolumn** väljer du bara en kolumn som innehåller text att bearbeta.

    Eftersom den här modulen skapar en vokabulär från text, är innehållet i kolumner olika, vilket leder till olika vokabulär innehåll. Därför accepterar modulen bara en mål kolumn.

4. För  **Word2Vec-strategin** väljer du **från assisterad för förtränad engelsk modell**, **Gensim Word2Vec** och **Gensim Fasttext**.

5. Om **Word2Vec-strategin** är **Gensim Word2Vec** eller **Gensim Fasttext**:

    + För **Word2Vec**, väljer du från **Skip_gram** och **CBOW**. Skillnaden presenteras i [original dokumentet (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        Standard metoden är **Skip_gram**.

    + Ange dimensionalitet för ord vektorer för ord **inbäddnings längden**. Den här inställningen motsvarar `size` parametern i Gensim.

        Standard inbäddnings storleken är 100.

    + För **Sammanhangs fönster storlek** anger du det maximala avståndet mellan ordet som förutsägs och det aktuella ordet. Den här inställningen motsvarar `window` parametern i Gensim.

        Standard fönster storleken är 5.

    + För **antal epoker** anger du antalet epoker (iterationer) över sökkorpus. Motsvarar `iter` parametern i Gensim.

        Standardvärdet för epok är 5.

6. För **maximal vokabulär storlek** anger du det maximala antalet ord i den genererade ord listan.

    Om det finns mer unika ord än den maximala storleken kan du rensa de ovanliga.

    Standard storleken för ord listan är 10 000.

7. Ange ett minsta antal ord för **minsta antal ord**. Modulen ignorerar alla ord som har en lägre frekvens än det här värdet.

    Standardvärdet är 5.

8. Skicka pipelinen.

## <a name="examples"></a>Exempel

Modulen har ett resultat:

+ **Vokabulär med inbäddningar**: innehåller den genererade ord listan, tillsammans med varje ord inbäddning. En dimension upptar en kolumn.

I följande exempel visas hur konvertera ord till Vector-modulen fungerar. Den använder Convert Word to Vector med standardinställningar till den förbearbetade Wikipedia SP 500-datauppsättningen.

### <a name="source-dataset"></a>Källdatauppsättning

Data uppsättningen innehåller en kategori kolumn, tillsammans med den fullständiga texten som hämtats från wikipedia. I följande tabell visas några representativa exempel.

|Text|
|----------|
|Nasdaq 100 Component s p 500 komponent Foundation grundare location ort Apple Campus 1 oändlig loop, gata oändlig loop Cupertino California Cupertino California ort land, USA...|
|br Nasdaq 100 Nasdaq 100 Component br s p 500 s p 500 komponent bransch Computer Software Foundation br grundare Charles Geschke br John Warnock location Adobe Systems...|
|s p 500 s p 500 komponent bransch bil bransch autofordons-föregångare allmänt motorer företag 1908 2009 efterföljande...|
|s p 500 s p 500-komponent bransch konglomerat företag konglomerat Foundation grundare plats stad Fairfield Connecticut Fairfield Connecticut location Country USA...|
|br s p 500 s p 500 komponent Foundation 1903 grundare William s Harley br Arthur Davidson Harley Davidson grundare Arthur Davidson br Walter Davidson br William en Davidson plats...|

### <a name="output-vocabulary-with-embeddings"></a>Utmatnings ord lista med inbäddningar

Följande tabell innehåller utdata från den här modulen, med Wikipedia SP 500-datauppsättningen som indata. Kolumnen längst till vänster visar vokabulären. Dess inbäddnings vektor representeras av värden för återstående kolumner på samma rad.

|Vokabulär|Bädda in dim 0|Bädda in dim 1|Bädda in dim 2|Bädda in dim 3|Bädda in dim 4|Bäddar in dim 5|...|Bäddar in dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|– 0,375865|0,609234|0,812797|– 0,002236|0,319071|– 0,591986|...|0,364276
|-komponent|0,081302|0,40001|0,121803|0,108181|0,043651|– 0,091452|...|0,636587
|s|– 0,34355|– 0,037092|– 0,012167|0,151542|0,601019|0,084501|...|0,149419
|P|– 0,133407|0,073244|0,170396|0,326706|0,213463|– 0,700355|...|0,530901
Foundation|– 0,166819|0,10883|– 0,07933|– 0,073753|0,262137|0,045725|...|0,27487
grundare|– 0,297408|0,493067|0,316709|– 0,031651|0,455416|– 0,284208|...|0,22798
location|– 0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|– 0,460828|0,505516|– 0,074294|– 0,00639|0,116545|0,494368|...|– 0,2403
Apples|0,05779|0,672657|0,597267|– 0,898889|0,099901|0,11833|...|0,4636
kontor|– 0,281835|0,29312|0,106966|– 0,031385|0,100777|– 0,061452|...|0,05978
oändliga|– 0,263074|0,245753|0,07058|– 0,164666|0,162857|– 0,027345|...|– 0,0525
Peter|– 0,391421|0,52366|0,141503|– 0,105423|0,084503|– 0,018424|...|– 0,0521

I det här exemplet använde vi standard **Gensim-Word2Vec** för **Word2Vec-strategin**, och **algoritmen för inlärning** är **Skip-gram**. **Längden på ord inbäddning** är 100, så vi har 100 inbäddning av kolumner.

## <a name="technical-notes"></a>Tekniska anteckningar

I det här avsnittet finns tips och svar på vanliga frågor.

+ Skillnad mellan onlineutbildning och förtränad modell:

    I den här konverterade Word till Vector-modulen tillhandahöll vi tre olika strategier: två utbildnings modeller och en förtränad modell. Modellerna Online-Training använder din indata-datauppsättning som tränings data och genererar vokabulär och Word-vektorer under utbildningen. Den förtränade modellen har redan tränats av en mycket större text sökkorpus, till exempel Wikipedia eller Twitter-text. Den förtränade modellen är i själva verket en samling av ord/inbäddnings par.  

    Assisterad för förtränad modell sammanfattar en vokabulär från indata-datauppsättningen och genererar en inbäddnings vektor för varje ord från den förtränade modellen. Utan onlineutbildning, kan användningen av en förtränad modell Spara tid för utbildning. Den har bättre prestanda, särskilt när storleken på indata-datauppsättningen är relativt liten.

+ Inbäddnings storlek:

    Som standard är längden på ord inbäddning inställd på några hundra. Till exempel 100, 200, 300. En liten inbäddnings storlek innebär ett litet vektor utrymme, vilket kan orsaka att Word bäddar in kollisioner.  

    Längden på ord inbäddningar är fast för förtränade modeller. I det här exemplet är inbäddnings storleken för assisterad-wiki-gigaword-100 100.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

En lista med fel som är speciella för designer-modulerna finns i [Machine Learning felkoder](designer-error-codes.md).
