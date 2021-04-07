---
title: Använd den tränade & djup rekommenderar-modulen
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen träna wide & djup rekommendation i Azure Machine Learning designer för att träna en rekommendations modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655384"
---
# <a name="train-wide--deep-recommender"></a>Träna bred & djup rekommendation
Den här artikeln beskriver hur du använder modulen **träna Wide & djup rekommenderar** i Azure Machine Learning designer för att träna en rekommendations modell. Den här modulen baseras på bred & djup inlärning, som föreslås av Google.

Den **tränade & djup rekommenderar** -modulen läser en data uppsättning med tre olika användar objekt – omdömen och, om det finns några funktioner för användare och objekt. Den returnerar en utbildad bred & djup rekommendation.  Du kan sedan använda den tränade modellen för att generera bedömnings förutsägelser eller rekommendationer med hjälp av modulen [Poäng och djup rekommenderar](score-wide-and-deep-recommender.md) .  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Mer om rekommendations modeller och bred & djup rekommendation  

Det huvudsakliga syftet med ett rekommendations system är att rekommendera ett eller flera *objekt* till *användare* i systemet. Exempel på ett objekt kan vara en film, restaurang, bok eller låt. En användare kan vara en person, en grupp av personer eller en annan entitet med objekt inställningar.  

Det finns två huvudsakliga metoder för rekommenderade system. 

+ Den första är den **innehållsbaserade** metoden, som använder funktioner för både användare och objekt. Användare kan beskrivas av egenskaper som ålder och kön, och objekt kan beskrivas av egenskaper som författare och tillverkare. Vanliga exempel på innehållsbaserade rekommendations system finns på sociala matchmaking-webbplatser. 
+ Den andra metoden är samordnad **filtrering**, som endast använder identifierare för användarna och objekten och hämtar implicit information om dessa entiteter från en (sparse) mat ris av klassificeringar som ges av användarna till objekten. Vi kan lära dig om en användare från de objekt som de har klassificerat och från andra användare som har betygsatt samma objekt.  

Den breda & djup rekommenderarare kombinerar dessa metoder med hjälp av samarbetes filtrering med en innehålls baserad metod. Det anses därför vara en **hybrid-rekommendation**. 

Så här fungerar detta: när en användare är relativt ny till systemet förbättras förutsägelserna genom att använda funktions information om användaren, vilket innebär att den välkända "kall start"-problemet besvaras. När du har samlat in ett tillräckligt antal klassificeringar från en viss användare är det dock möjligt att göra helt anpassade förutsägelser för dem baserat på deras specifika klassificering snarare än enbart på deras funktioner. Därför finns det en smidig över gång från innehållsbaserade rekommendationer till rekommendationer baserat på samarbetes filtrering. Även om användar-eller objekt funktioner inte är tillgängliga fungerar bred & djupgående rekommenderar fortfarande i sitt samarbets filtrerings läge.  

Mer information om den breda & djup rekommendationen och dess underliggande Probabilistic-algoritm finns i relevant forsknings blad: [bred & djup inlärning för rekommenderade system](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Så här konfigurerar du träna wide & djup rekommendation  

+ [Förbered tränings data](#prepare-data)
+ [Träna modellen](#train-the-model)

### <a name="prepare-data"></a>Förbereda data

Innan du försöker använda modulen är det viktigt att dina data har det format som förväntas av rekommendations modellen. Det krävs en tränings data uppsättning för **användar objekts bedömning av tredubbel** information, men du kan även inkludera användar funktioner och objekt funktioner (om de är tillgängliga) i separata data uppsättningar.

#### <a name="required-dataset-of-user-item-ratings"></a>Obligatorisk data uppsättning för användar objekt – klassificeringar

De indata som används för träning måste innehålla rätt typ av data i rätt format: 

+ Den första kolumnen måste innehålla användar identifierare.
+ Den andra kolumnen måste innehålla objekt identifierare.
+ Den tredje kolumnen innehåller omdömet för paret User-Item. Klassificerings värden måste vara en numerisk typ. 

Till exempel kan en typisk uppsättning User-item-ratings se ut så här:

|UserId|MovieId|Omdöme|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Data uppsättning för användar funktioner (valfritt)

Data uppsättningen för **användar funktioner** måste innehålla identifierare för användare och använda samma identifierare som angavs i den första kolumnen i data uppsättningen Users-Items-Ratings. De återstående kolumnerna kan innehålla valfritt antal funktioner som beskriver användarna.  

En typisk uppsättning användar funktioner kan till exempel se ut så här: 

|UserId|Ålder|Kön|Intressanta|Location|
|------------|--------------|-----------------------|---------------|------------|
|1|25|man| Drama    |Europa|
|223|40|kvinna|Madrid|Asien|

#### <a name="item-features-dataset-optional"></a>Data uppsättning för objekt funktioner (valfritt)

Data uppsättningen för objekt funktioner måste innehålla objekt identifierare i den första kolumnen. De återstående kolumnerna kan innehålla valfritt antal beskrivande funktioner för objekten.  

Ett exempel på en typisk uppsättning objekt funktioner kan se ut så här:  

|MovieId|Rubrik|Ursprungligt språk|Genrer|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Godfather|Engelska|Drama|1972|
|31381|Borta med vridningen|Engelska|Historik|1939|

### <a name="train-the-model"></a>Träna modellen

1.  Lägg till modulen **träna bred och djup rekommenderarare** i designern och Anslut den till inlärnings data uppsättningen.  
  
2. Om du har en separat data uppsättning för antingen användar funktioner och/eller objekt funktioner, kan du ansluta dem till modulen **träna bred och djup rekommenderar** .  
  
    - **Data uppsättning för användar funktioner**: Anslut den data uppsättning som beskriver användare till den andra indatan.
    - **Data uppsättning för objekt funktioner**: Anslut data uppsättningen som beskriver objekt till den tredje indatan.  
    
3.  **Epoker**: Ange hur många gånger algoritmen ska bearbeta hela tränings data. 

    Ju högre siffra, desto mer adekvat träning. men utbildningar kostar mer tid och kan leda till överanpassning.

4. **Batchstorlek**: Ange antalet inlärnings exempel som används i ett övnings steg. 

     Den här parametern kan påverka inlärnings hastigheten. En högre batch-storlek leder till en mindre kostnads period, men kan öka konvergens tiden. Om batch är för stor för att rymma GPU/CPU kan ett minnes fel uppstå.

5.  **Bred del optimering**: Välj en optimering för att applicera övertoningar på den breda delen av modellen.

6.  **Bred optimerings inlärnings takt**: Ange ett tal mellan 0,0 och 2,0 som definierar inlärnings graden för bred dels optimering.

    Den här parametern avgör steg storleken i varje övnings steg samtidigt som den flyttas till en minimum av förlust funktionen. En för stor inlärnings takt kan orsaka att inlärningen hoppar över minimi, medan en för liten inlärnings takt kan orsaka konvergens problem.

7.  **Korsad funktions dimension**: Ange dimensionen genom att ange önskade användar-ID och objekt-ID-funktioner. 

    Den breda & djup rekommenderarre utför kors produkt Transformation över användar-ID och objekt-ID-funktioner som standard. Det korsade resultatet kommer att hashas enligt detta nummer för att säkerställa att dimensionen.

8.  **Djup del optimering**: Välj en optimering för att applicera övertoningar på den djupgående delen av modellen.

9.  **Inlärnings takt för djup optimering**: Ange ett tal mellan 0,0 och 2,0 som definierar inlärnings graden för optimering av djup del.

10.  **Användar inbäddnings dimension**: Ange ett heltal för att ange dimension för inbäddning av användar-ID.

     Den breda & djup rekommenderarare skapar inbäddningar av delade användar-ID och objekt-ID för både bred och djup del.

11.  **Objekt inbäddnings dimension**: Ange ett heltal för att ange dimension för objekt-ID-inbäddning.

12.  **Kategoriska features inbäddnings dimension**: Ange ett heltal för att ange dimensionerna för kategoriska funktions inbäddningar.

     I en djup komponent i wide & djupgående rekommenderar vi att du har en inbäddad Vector för varje kategoriska-funktion. Och dessa inbäddnings vektorer delar samma dimension.

13.  **Dolda enheter**: Ange antalet dolda noder i djup komponenten. Nodernas nummer i varje skikt avgränsas med kommatecken. Genom att skriva "1 000 500 100" kan du till exempel ange att djup komponenten har tre lager, med det första skiktet, de sista har 1000 noder, 500 noder och 100 noder.

14.  **Aktiverings funktion**: Välj en aktiverings funktion som tillämpas på varje skikt. standardvärdet är ReLU.

15.  **Dropout**: Ange ett tal mellan 0,0 och 1,0 för att fastställa sannolikheten för att utdata ska släppas i varje lager under träningen.

     Dropout är en regulariseringshastigheten-metod för att förhindra att neurala-nätverk översätts. Ett vanligt beslut för det här värdet är att börja med 0,5, vilket förefaller vara nära optimalt för ett brett utbud av nätverk och uppgifter.

16.  **Batch-normalisering**: Välj det här alternativet om du vill använda batch-normalisering efter varje dolt lager i djup komponenten.

     Batch-normalisering är en teknik för att bekämpa internt covariate-problem under nätverkets utbildning. I allmänhet kan den hjälpa till att förbättra nätverkets hastighet, prestanda och stabilitet. 

17.  Köra en pipeline.

## <a name="results"></a>Resultat

När pipeline-körningen har slutförts ska du använda modellen för att göra en bedömning av den [tränade och djupgående rekommendationen](train-wide-and-deep-recommender.md) för att få en [bred och djup rekommendation](score-wide-and-deep-recommender.md), för att förutsäga värden för nya exempel på ingångar.

##  <a name="technical-notes"></a>Tekniska anteckningar

Det breda & djupet driftskompatibla modeller i hela världen och djup neurala-nätverk som kombinerar styrkan för att komma ihåg och generalisera. Den breda komponenten accepterar en uppsättning RAW-funktioner och funktions omvandlingar för att komma ihåg funktions interaktioner. Och med mindre funktions teknik kan djup komponenten generaliseras för att osett funktions kombinationer med hjälp av undimensionella funktions inbäddningar. 

Vid implementering av bred & djup rekommendation använder modulen en standard modell struktur. Den breda komponenten tar användar inbäddningar, objekt inbäddningar och kors produkts omvandling av användar-ID: n och objekt-ID: n som indata. För en djup del av modellen är en inbäddnings vektor att lära sig för varje kategoriska-funktioner. Tillsammans med andra numeriska funktions vektorer matas dessa vektorer in i neurala-nätverket för djup flödet. Den breda delen och djupgående delen kombineras genom att summera den slutliga utgående loggen strider som förutsägelse, som slutligen går till en gemensam förlust funktion för gemensam utbildning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
