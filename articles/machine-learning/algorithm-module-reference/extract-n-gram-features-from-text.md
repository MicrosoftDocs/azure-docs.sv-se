---
title: Extrahera N-gram-funktioner från text module-referens
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen extrahera N-g i Azure Machine Learning designer för att funktionalisera text data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861468"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrahera N-gram-funktioner från text module-referens

I den här artikeln beskrivs en modul i Azure Machine Learning designer. Använd extrahera N-g-funktioner från text-modulen för att *funktionalisera* ostrukturerade text data. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfiguration av extrahera N-gram-funktioner från textmodul

Modulen stöder följande scenarier för användning av en n-gram-ordlista:

* [Skapa en ny n-gram-ordlista](#create-a-new-n-gram-dictionary) från en kolumn med fritext.

* [Använd en befintlig uppsättning text funktioner](#use-an-existing-n-gram-dictionary) för att funktionalisera en kostnads fri text kolumn.

* [Poäng eller distribuera en modell](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) som använder n-gram.

### <a name="create-a-new-n-gram-dictionary"></a>Skapa en ny ord lista för n-gram

1.  Lägg till funktionerna extrahera N-g från text-modulen i din pipeline och Anslut den data uppsättning som innehåller den text som du vill bearbeta.

1.  Använd **text kolumn** för att välja en kolumn med sträng typ som innehåller den text som du vill extrahera. Eftersom resultaten är utförliga kan du bara bearbeta en kolumn i taget.

1. Ange det **vokabulär läge** som ska **skapas** för att visa att du skapar en ny lista över n-gram-funktioner. 

1. Ange **n-gram-storlek** för att ange den *maximala* storleken på N-g som ska extraheras och lagras. 

    Om du till exempel anger 3, unigrams, 2 g och trigrams kommer att skapas.

1. **Viktnings funktionen** anger hur du skapar dokument funktions vektorn och hur du extraherar vokabulär från dokument.

    * **Binär vikt**: tilldelar ett binärt närvaro värde till den extraherade n-gram. Värdet för varje n-gram är 1 när det finns i dokumentet och 0 annars.

    * **TF vikt**: tilldelar en term frekvens (tf) poäng till den extraherade n-gram. Värdet för varje n-gram är dess förekomst frekvens i dokumentet.

    * **IDF-vikt**: tilldelar ett IDF-värde (inverterad dokument frekvens) till den extraherade n-gram. Värdet för varje n-gram är loggen för sökkorpus storlek dividerat med dess förekomst frekvens i hela sökkorpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF – IDF-vikt**: tilldelar en term frekvens/Inverse Document frekvens (tf/IDF) poäng till den extraherade n-gram. Värdet för varje n-gram är dess TF-poäng multiplicerat med dess IDF-poäng.

1. Ange **minsta längd på ord** till det minsta antal bokstäver som kan användas i ett *enskilt ord* i en n-gram.

1. Använd **maximal ord längd** för att ange det maximala antalet bokstäver som kan användas i ett *enskilt ord* i en n-gram.

    Som standard är upp till 25 tecken per ord eller token tillåtna.

1. Använd **minsta frekvens för n-gram-dokument** för att ange de minsta förekomster som krävs för att n-g ska ingå i ord listan för n-gram. 

    Om du till exempel använder standardvärdet 5 måste alla n-gram visas minst fem gånger i sökkorpus för att inkluderas i n-gram-ordlistan. 

1.  Ange **högsta dokument kvot för n-gram** till det maximala förhållandet för antalet rader som innehåller en viss n-gram, över antalet rader i den övergripande sökkorpus.

    Till exempel betyder förhållandet 1 att även om det finns ett särskilt n-gram på varje rad, kan du lägga till n-gram i ord listan n-gram. Vanligt vis anses ett ord som förekommer i varje rad vara ett brus ord och tas bort. Om du vill filtrera bort domän beroende skräpord kan du försöka minska förhållandet.

    > [!IMPORTANT]
    > Antalet förekomster av vissa ord är inte enhetligt. Det varierar från dokument till dokument. Om du till exempel analyserar kund kommentarer om en specifik produkt kan produkt namnet vara mycket högt och nära ett brus ord, men det är en viktig term i andra sammanhang.

1. Välj alternativet **normalisera n-gram-funktions vektorer** för att normalisera funktions vektorerna. Om det här alternativet är aktiverat divideras varje n-gram-funktions vektor med dess L2-norm.

1. Skicka pipelinen.

### <a name="use-an-existing-n-gram-dictionary"></a>Använd en befintlig n-gram-ordlista

1.  Lägg till funktionerna extrahera N-g från text-modulen i din pipeline och Anslut den data uppsättning som innehåller den text som du vill bearbeta till **data uppsättnings** porten.

1.  Använd **text kolumnen** för att välja den text kolumn som innehåller den text som du vill funktionalisera. Som standard markeras alla kolumner av typen **sträng** i modulen. För bästa resultat ska du bearbeta en enskild kolumn i taget.

1. Lägg till den sparade data uppsättningen som innehåller en tidigare skapad n-gram-ordlista och Anslut den till porten för **indata-ordlista** . Du kan också ansluta **resultatet** av en överordnad instans av en överordnad instans av funktionerna extrahera N-g från text-modulen.

1. För **ord listans läge** väljer du alternativet **skrivskyddad** uppdatering i list rutan.

   Alternativet **ReadOnly** representerar sökkorpus för indatamängden. I stället för att använda term frekvenser från den nya text data uppsättningen (till vänster), tillämpas vikterna i n-gram från indata-vokabuläret som de är.

   > [!TIP]
   > Använd det här alternativet när du vill värdera en text klassificerare.

1.  För alla andra alternativ, se egenskaps beskrivningarna i [föregående avsnitt](#create-a-new-n-gram-dictionary).

1.  Skicka pipelinen.

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>Bygg en härlednings pipeline som använder n-g för att distribuera en slut punkt i real tid

En utbildnings pipeline som innehåller **funktionen extrahera N-g från text** och **Poäng modell** för att göra förutsägelser på test data uppsättning, är byggd i följande struktur:

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="Extrahera N-gram utbildnings pipeline exempel" border="true":::

**Ord listans läge** för **funktionen extractal Extract N-g från text** module är **create**, och **vokabulär läge** för modulen som ansluter till **Scores Model** -modulen är **skrivskyddad**.

När du har skickat in utbildnings pipelinen ovan kan du registrera utdata från den inringade modulen som data uppsättning.

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="registrera data uppsättning" border="true":::

Sedan kan du skapa en pipeline för real tids härledning. När du har skapat en härlednings pipeline måste du justera din härlednings pipeline manuellt, till exempel följande:

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="härlednings pipeline" border="true":::

Skicka sedan härlednings pipelinen och distribuera en slut punkt i real tid.

## <a name="results"></a>Resultat

Utdraget av N-gram-funktioner från en text-modul skapar två typer av utdata: 

* **Resultat data uppsättning**: det här resultatet är en sammanfattning av den analyserade texten kombinerat med det n-gram som extraherades. Kolumner som du inte har valt i **text kolumns** alternativet skickas till utdata. För varje kolumn med text som du analyserar genererar modulen dessa kolumner:

  * **Matris med n-gram-förekomster**: modulen genererar en kolumn för varje n-gram som påträffas i den totala sökkorpus och lägger till en poäng i varje kolumn för att ange vikten för n-gram för raden. 

* **Resultat vokabulär**: ord listan innehåller den faktiska n-gram-ordlistan, tillsammans med villkors poängen som genereras som en del av analysen. Du kan spara data uppsättningen för åter användning med en annan uppsättning indata eller för en senare uppdatering. Du kan också återanvända ord listan för modellering och bedömning.

### <a name="result-vocabulary"></a>Resultat vokabulär

Ord listan innehåller en n-gram-ordlista med termen frekvens Poäng som genereras som en del av analysen. DF-och IDF-poängen genereras oavsett andra alternativ.

+ **ID**: en identifierare som genereras för varje unik n-gram.
+ **NGram**: n-gram. Blank steg eller andra ord separatorer ersätts av under streck.
+ **DF**: term frekvens poängen för n-gram i den ursprungliga sökkorpus.
+ **IDF**: resultatet av den inverterade dokument frekvensen för n-gram i den ursprungliga sökkorpus.

Du kan uppdatera den här data uppsättningen manuellt, men du kan införa fel. Exempel:

* Ett fel uppstår om modulen hittar dubblettrader med samma nyckel i den angivna ord listan. Se till att inga två rader i ord listan har samma ord.
* Schemat för vokabulärens data uppsättningar måste matcha exakt, inklusive kolumn namn och kolumn typer. 
* Kolumnen **ID** och **DF** måste vara av heltals typ. 
* **IDF** -kolumnen måste vara av typen float.

> [!Note]
> Anslut inte data utdata till modulen träna modell direkt. Du bör ta bort tomma text kolumner innan de matas in i träna-modellen. Annars behandlas fritext kolumnerna som kategoriska-funktioner.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.
