---
title: 'Förbearbeta text: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för Förbearbetad text i Azure Machine Learning designer för att rensa och förenkla text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659315"
---
# <a name="preprocess-text"></a>Förbearbeta text

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen för **Förbearbetad text** för att rensa och förenkla text. Det stöder följande vanliga text bearbetnings åtgärder:

* Borttagning av stopp-ord
* Använda reguljära uttryck för att söka efter och ersätta specifika mål strängar
* Lemmatisering, som konverterar flera relaterade ord till ett enda kanoniskt format
* Fall normalisering
* Borttagning av vissa klasser av tecken, t. ex. siffror, specialtecken och sekvenser av upprepade tecken, till exempel "AAAA"
* Identifiering och borttagning av e-post och webb adresser

Modulen för **Förbearbetad text** stöder för närvarande bara engelska.

## <a name="configure-text-preprocessing"></a>Konfigurera text förbehandling  

1.  Lägg till modulen för **Förbearbetad text** i din pipeline i Azure Machine Learning. Du kan hitta den här modulen under **textanalys**.

1. Anslut en data uppsättning som har minst en kolumn som innehåller text.

1. Välj språk i list rutan **språk** .

1. **Text kolumn att rensa**: Välj den kolumn som du vill Förbearbeta.

1. **Ta bort stopp ord**: Välj det här alternativet om du vill använda en fördefinierad stoppord-lista i text kolumnen. 

    Stoppord-listor är språk beroende och anpassningsbara.

1. **Lemmatisering**: Välj det här alternativet om du vill att ord ska visas i deras kanoniska form. Det här alternativet är användbart för att minska antalet unika förekomster av andra liknande tokens.

    Lemmatisering-processen är mycket språk beroende...

1. **Identifiera meningar**: Välj det här alternativet om du vill att modulen ska infoga en ord avgränsnings markering när du utför analysen.

    I den här modulen används en serie med tre pipe-tecken `|||` för att representera menings avslutning.

1. Utför valfria Sök-och-ersätt-åtgärder med hjälp av reguljära uttryck. Det reguljära uttrycket kommer att bearbetas först, före alla andra inbyggda alternativ.

    * **Anpassat reguljärt uttryck**: definiera texten som du söker efter.
    * **Anpassad ersättnings sträng**: definiera ett enda ersättnings värde.

1. **Normalisera versaler till gemener**: Välj det här alternativet om du vill konvertera ASCII-versaler till gemener.

    Om tecknen inte är normaliserade, betraktas samma ord i versaler och gemener som två olika ord.

1. Du kan också ta bort följande typer av tecken eller tecken serier från texten för bearbetade utdata:

    * **Ta bort tal**: Välj det här alternativet om du vill ta bort alla numeriska tecken för det angivna språket. Identifierings nummer är domän beroende och språk beroende. Om numeriska tecken är en viktig del av ett känt ord, kan det hända att antalet inte tas bort. Läs mer i [tekniska anteckningar](#technical-notes).
    
    * **Ta bort specialtecken**: Använd det här alternativet om du vill ta bort icke-alfanumeriska specialtecken.
    
    * **Ta bort duplicerade tecken**: Välj det här alternativet om du vill ta bort extra tecken i sekvenser som upprepas för fler än två gånger. En sekvens som "AAAAA" skulle till exempel minskas till "AA".
    
    * **Ta bort e-post adresser**: Välj det här alternativet om du vill ta bort en sekvens av formatet `<string>@<string>` .  
    * **Ta bort webb adresser**: Välj det här alternativet om du vill ta bort en sekvens som innehåller följande URL-prefix: `http` , `https` , `ftp` , `www`
    
1. **Expandera verb**: det här alternativet gäller endast för språk som använder verb-kontrakt. för närvarande endast på engelska. 

    Genom att välja det här alternativet kan du till exempel ersätta frasen *"Behåll inte där* *".*

1. **Normalisera omvända snedstreck till snedstreck**: Välj det här alternativet om du vill mappa alla instanser av `\\` till `/` .

1. **Dela tokens på specialtecken**: Välj det här alternativet om du vill dela upp ord på tecken som `&` , `-` och så vidare. Det här alternativet kan också minska de särskilda tecknen när de upprepas mer än två gånger. 

    Strängen skulle t. ex `MS---WORD` . vara indelad i tre tokens,, `MS` `-` och `WORD` .

1. Skicka pipelinen.

## <a name="technical-notes"></a>Tekniska anteckningar

Modulen för för **bearbetnings text** i Studio (klassisk) och designer använder olika språk modeller. Designern använder en tränad CNN-modell med flera aktiviteter från [avstånd](https://spacy.io/models/en). Olika modeller ger olika tokenizer-och tagger, vilket leder till olika resultat.

Här följer några exempel:

| Konfiguration | Resultat av utdata |
| --- | --- |
|Med alla alternativ markerade </br> Förklaring: </br> För de fall som "3test" i "WC-3 3test 4test" tar konstruktören bort hela ordet "3test", eftersom i den här kontexten anger för den här token "tagger" som siffror, och enligt den här delen tar modulen bort den.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Med alla alternativ markerade" border="True"::: |
|Med endast `Removing number` valt </br> Förklaring: </br> För de fall som "3test", "4-EG", har designern tokenizer-dosen inte delat dessa fall och behandlar dem som hela tokens. Det innebär att det inte tar bort talen i dessa ord.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Med endast &quot;ta bort nummer&quot; valt" border="True"::: |

Du kan också använda reguljärt uttryck för att generera anpassade resultat:

| Konfiguration | Resultat av utdata |
| --- | --- |
|Med alla alternativ markerade </br> Anpassat reguljärt uttryck: `(\s+)*(-|\d+)(\s+)*` </br> Anpassad ersättnings sträng: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Med alla markerade alternativ och ett reguljärt uttryck" border="True"::: |
|Med endast `Removing number` valt </br> Anpassat reguljärt uttryck: `(\s+)*(-|\d+)(\s+)*` </br> Anpassad ersättnings sträng: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Med att ta bort siffror markerat och reguljärt uttryck" border="True"::: |


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 