---
title: Träna Vowpal Wabbit-modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen träna Vowpal Wabbit Model för att skapa en maskin inlärnings modell med hjälp av en instans av Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657662"
---
# <a name="train-vowpal-wabbit-model"></a>Träna Vowpal Wabbit-modell
Den här artikeln beskriver hur du använder modulen **träna Vowpal Wabbit modell** i Azure Machine Learning designer för att skapa en maskin inlärnings modell med Vowpal Wabbit.  

Om du vill använda Vowpal Wabbit för maskin inlärning formaterar du indata enligt Vowpal Wabbit-krav och förbereder data i det format som krävs. Använd den här modulen för att ange kommando rads argument för Vowpal-Wabbit. 

När pipelinen körs läses en instans av Vowpal Wabbit in i experimentets körnings tid, tillsammans med de angivna data. När inlärningen är klar serialiseras modellen tillbaka till arbets ytan. Du kan använda modellen direkt för att räkna data. 

För att stegvis träna en befintlig modell på nya data, ansluter du en sparad modell till den **förtränade Vowpal Wabbit Model** -port för **Vowpal Wabbit** och lägger till nya data till den andra Indataporten.  

## <a name="what-is-vowpal-wabbit"></a>Vad är Vowpal Wabbit?  

Vowpal Wabbit (VW) är ett snabbt parallellt ramverk för maskin inlärning som har utvecklats för distribuerad data behandling av Yahoo! Söker. Senare hamnade det till Windows och anpassat av John Langford (Microsoft Research) för avancerad data behandling i parallell arkitekturer.  

Funktioner i Vowpal-Wabbit som är viktiga för Machine Learning är kontinuerlig inlärning (online Learning), Dimensional reducering och interaktiv inlärning. Vowpal Wabbit är också en lösning för problem när du inte kan anpassa modell data i minnet.  

De primära användarna av Vowpal Wabbit är data tekniker som tidigare har använt ramverket för Machine Learning-uppgifter som klassificering, regression, ämnes modellering eller matris factorization. Azure-omslutningen för Vowpal Wabbit har mycket liknande prestanda egenskaper för den lokala versionen, så du kan använda de kraftfulla funktionerna och de ursprungliga prestandan hos Vowpal-Wabbit och enkelt publicera den utbildade modellen som en drifts tjänst.  

Modulen för [funktions-hash](feature-hashing.md) omfattar även funktioner som tillhandahålls av Vowpal Wabbit, som gör att du kan omvandla text data uppsättningar till binära funktioner med en hash-algoritm.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Så här konfigurerar du Vowpal Wabbit-modellen  

I det här avsnittet beskrivs hur du tränar en ny modell och hur du lägger till nya data i en befintlig modell.

Till skillnad från andra moduler i designern, anger den här modulen både modulens parametrar och tågen modellen. Om du har en befintlig modell kan du lägga till den som en valfri ingång för att stegvis träna modellen.

+ [Förbered indata i något av de format som krävs](#prepare-the-input-data)
+ [Träna en ny modell](#create-and-train-a-vowpal-wabbit-model)
+ [Träna en befintlig modell stegvis](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Förbereda indata

För att träna en modell med hjälp av den här modulen måste indata-datamängden bestå av en enda text kolumn i något av de två stödda formaten: **SVMLight** eller **VW**. Detta innebär inte att Vowpal-Wabbit analyserar endast text data, bara att funktionerna och värdena måste förberedas i det obligatoriska text fil formatet.  

Data kan läsas från två typer av data uppsättningar, fil data uppsättning eller tabell data uppsättning. Båda dessa data uppsättningar måste antingen i SVMLight-eller VW-format. Vowpal Wabbit-dataformatet har fördelen att det inte kräver något kolumn format, vilket sparar utrymme vid hantering av sparse-data. Mer information om det här formatet finns på [wiki-sidan för Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Skapa och träna en Vowpal Wabbit-modell

1. Lägg till modulen **träna Vowpal Wabbit Model** i experimentet. 
  
2. Lägg till inlärnings data uppsättningen och Anslut den till **tränings data**. Om en data uppsättning för utbildning är en katalog som innehåller övnings data filen, anger du namnet på övnings data filen med **namnet på övnings data filen**. Om inlärnings data uppsättningen är en enskild fil lämnar du **namnet på tränings data filen** till tom.

3. I text rutan **VW-argument** skriver du kommando rads argument för den körbara filen Vowpal Wabbit.

     Du kan till exempel lägga till *`–l`* om du vill ange inlärnings frekvensen eller ange *`-b`* antalet hash-bitar.  

     Mer information finns i avsnittet [Vowpal Wabbit Parameters](#supported-and-unsupported-parameters) .  

4. **Namnet på tränings data filen**: Skriv namnet på filen som innehåller indata. Det här argumentet används bara när träning-datauppsättningen är en katalog.

5. **Ange filtyp**: Ange vilket format dina utbildnings data använder. Vowpal Wabbit stöder dessa två indatafiler:  

    - **VW** representerar det interna formatet som används av Vowpal-Wabbit. Mer information finns på [Vowpal Wabbit wiki-sidan](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - **SVMLight** är ett format som används av vissa andra Machine Learning-verktyg. 

6. **Läsbar modell fil för utdata**: Välj alternativet om du vill att modulen ska spara den läsbara modellen i körnings posterna. Det här argumentet motsvarar `--readable_model` parametern i VW kommando rad.  

7. **Utdata inverterad hash-fil**: Välj alternativet om du vill att modulen ska spara den inverterade hash-funktionen till en fil i körnings posterna. Det här argumentet motsvarar `--invert_hash` parametern i VW kommando rad.  

8. Skicka pipelinen.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Träna en befintlig Vowpal Wabbit-modell

Vowpal Wabbit har stöd för stegvis utbildning genom att lägga till nya data i en befintlig modell. Det finns två sätt att hämta en befintlig modell för omskolning:

+ Använd utdata från en annan **träna Vowpal Wabbit Model** -modul i samma pipeline.  
  
+ Leta upp en sparad modell i kategorin **data uppsättningar** i designerns vänstra navigerings fönster och dra den till din pipeline.  

1. Lägg till modulen **träna Vowpal Wabbit modell** i din pipeline.  
2. Anslut den tidigare tränade modellen till den **förtränade Vowpal-Wabbit modellens** dataport.
3. Anslut de nya **tränings data till Indataporten** för modulen.
4. I fönstret parametrar i **träna Vowpal Wabbit-modellen** anger du formatet för nya utbildnings data, och även namnet på tränings data filen om indata-datauppsättningen är en katalog.
5. Välj den **utskrivbara modell filen** och **utdata inverterade** alternativ för hash-filen om motsvarande filer måste sparas i körnings posterna.

6. Skicka pipelinen.  
7. Välj modulen och välj **registrera data uppsättning** under **utdata + fliken loggar** i den högra rutan för att bevara den uppdaterade modellen i Azure Machine Learning-arbetsytan.  Om du inte anger ett nytt namn skriver den uppdaterade modellen över den befintliga sparade modellen.

## <a name="results"></a>Resultat

+ Om du vill generera Poäng från modellen använder du [score Vowpal Wabbit modell](score-vowpal-wabbit-model.md).

> [!NOTE]
> Om du behöver distribuera den tränade modellen i designern ser du till att [score Vowpal Wabbit-modellen](score-vowpal-wabbit-model.md) i stället för **Poäng modellen** är ansluten till indata för [webb tjänstens output-modul](web-service-input-output.md) i härlednings pipelinen.

## <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="advantages-of-vowpal-wabbit"></a>Fördelar med Vowpal-Wabbit

Vowpal Wabbit ger mycket snabb inlärning över icke-linjära funktioner som n-gram.  

Vowpal Wabbit använder *utbildnings* tekniker som Stochastic gradient BRANTASTE (SGD) för att passa en modell en post i taget. Det upprepas därför mycket snabbt över rå data och kan utveckla en bra förväntare snabbare än de flesta andra modeller. Den här metoden undviker också att behöva läsa alla utbildnings data i minnet.  

Vowpal Wabbit konverterar alla data till hash-värden, inte bara text data utan andra kategoriska-variabler. Med hjälp av hash-värden blir ökningen av Regressions vikterna mer effektiv, vilket är viktigt för effektiv Stochastic gradient-brantaste.  

###  <a name="supported-and-unsupported-parameters"></a>Parametrar som stöds och som inte stöds 

I det här avsnittet beskrivs stöd för Vowpal Wabbit-kommando rads parametrar i Azure Machine Learning designer. 

I allmänhet stöds alla utom en begränsad uppsättning argument. Använd [wiki-sidan Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)för en fullständig lista över argument.    

Följande parametrar stöds inte:

-   De indata/utdata-alternativ som anges i [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     De här egenskaperna har redan kon figurer ATS automatiskt av modulen.  
  
-   Dessutom tillåts alla alternativ som genererar flera utmatningar eller tar flera indata. Dessa inkluderar *`--cbt`* , *`--lda`* och *`--wap`* .  
  
-   Endast övervakade Learning-algoritmer stöds. Dessa alternativ stöds därför inte: *`–active`* , `--rank` *`--search`* osv. 

### <a name="restrictions"></a>Begränsningar

Eftersom målet för tjänsten är att stödja erfarna användare av Vowpal-Wabbit, måste indata förvaras i förväg i förväg med hjälp av Vowpal wabbits interna text format, i stället för data uppsättnings formatet som används av andra moduler.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
