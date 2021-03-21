---
title: 'Dela data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen dela data i Azure Machine Learning för att dela upp en data uppsättning i två distinkta mängder.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907826"
---
# <a name="split-data-module"></a>Modulen dela data

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen dela data för att dela upp en data uppsättning i två distinkta mängder.

Den här modulen är användbar när du behöver separera data till inlärnings-och test uppsättningar. Du kan också anpassa hur data delas. Vissa alternativ stöder slumpmässig data. Andra skräddarsys för en viss datatyp eller modell typ.

## <a name="configure-the-module"></a>Konfigurera modulen

> [!TIP]
> Innan du väljer delnings läget kan du läsa alla alternativ för att avgöra vilken typ av delning du behöver.
> Om du ändrar delnings läget kan alla andra alternativ återställas.

1. Lägg till modulen **dela data** i din pipeline i designern. Du hittar den här modulen under **data omvandling**, i kategorin **exempel och Split** .

1. **Delnings läge**: Välj något av följande lägen, beroende på vilken typ av data du har och hur du vill dela upp det. Varje delnings läge har olika alternativ.

   - **Dela rader**: Använd det här alternativet om du bara vill dela upp data i två delar. Du kan ange den procent andel av data som ska beställas i varje delning. Som standard är data delade 50/50.

     Du kan också göra en slumpmässig markering av rader i varje grupp och använda Stratified-sampling. I Stratified-sampling måste du välja en enskild kolumn med data för vilka du vill att värden ska fördelas lika mellan de två resultat data uppsättningarna.  

   - **Delning av reguljära uttryck**: Välj det här alternativet om du vill dela upp data uppsättningen genom att testa en enskild kolumn för ett värde.

     Om du till exempel analyserar sentiment kan du söka efter ett visst produkt namn i ett textfält. Du kan sedan dela upp data uppsättningen i rader med mål produktens namn och rader utan mål produkt namnet.

   - **Delning av relativt uttryck**: Använd det här alternativet när du vill tillämpa ett villkor för en tal kolumn. Talet kan vara ett datum/tid-fält, en kolumn som innehåller ålders-eller dollar belopp eller till och med en procent andel. Du kanske t. ex. vill dela upp din data uppsättning baserat på kostnaden för objekten, gruppera personer efter ålders intervall eller separera data efter kalender datum.

### <a name="split-rows"></a>Dela rader

1. Lägg till modulen [dela data](./split-data.md) i din pipeline i designern och Anslut den data uppsättning som du vill dela.
  
1. För **delnings läge** väljer du **dela upp rader**. 

1. **Bråk del av rader i den första data uppsättningen**: Använd det här alternativet för att avgöra hur många rader som ska gå till den första (vänstra sidan) utdata. Alla andra rader hamnar i den andra (högra sidan) utdata.

   Förhållandet representerar procent andelen rader som har skickats till den första data uppsättningen, så du måste ange ett decimal tal mellan 0 och 1.
     
   Om du till exempel anger **0,75** som värde, kommer data uppsättningen att delas upp 75/25. I den här delningen skickas 75 procent av raderna till den första utdata-datauppsättningen. Återstående 25 procent skickas till den andra data uppsättningen för utdata.
  
1. Välj det **slumpmässiga delnings** alternativet om du vill slumpmässig data urvalet i de två grupperna. Det här är det bästa alternativet när du skapar utbildning och test data uppsättningar.

1. **Slumpmässigt utsäde**: Ange ett icke-negativt heltal för att starta pseudorandom-sekvensen av instanser som ska användas. Standardvärdet för start används i alla moduler som genererar slumpmässiga tal. 

   Genom att ange ett dirigering får resultatet att bli reproducerbart. Om du behöver upprepa resultatet av en delad åtgärd bör du ange ett start värde för slump tals generatorn. Annars anges det slumpmässiga startvärdet som standard till **0**, vilket innebär att det första startvärdet hämtas från system klockan. Därför kan data fördelningen vara något annorlunda varje gången du utför en delning. 

1. **Stratified Split**: Ange det här alternativet till **Sant** för att säkerställa att de två data uppsättningarna innehåller ett representativt sampel av värdena i kolumnen *Strata* eller *Stratification Key*. 

   Med Stratified-sampling delas data upp så att varje utdata-datauppsättning får ungefär samma procent andel av varje målvärde. Du kanske vill se till att dina utbildnings-och test uppsättningar är ungefär balanserade med avseende på resultatet eller någon annan kolumn (t. ex. kön).

1. Skicka pipelinen.


## <a name="select-a-regular-expression"></a>Välj ett reguljärt uttryck

1. Lägg till modulen [dela data](./split-data.md) i din pipeline och Anslut den som indata till den data uppsättning som du vill dela.  
  
1. För **delnings läge** väljer du **delad reguljära uttryck**.

1. I rutan **reguljära uttryck** anger du ett giltigt reguljärt uttryck. 
  
   Det reguljära uttrycket måste följa python-syntaxen för reguljära uttryck.

1. Skicka pipelinen.

   Baserat på det reguljära uttryck som du anger är data uppsättningen uppdelad i två uppsättningar med rader: rader med värden som matchar uttrycket och alla återstående rader. 

Följande exempel visar hur du delar upp en data uppsättning med hjälp av alternativet för **reguljära uttryck** . 

### <a name="single-whole-word"></a>Enstaka hela ord 

I det här exemplet placeras alla rader som innehåller texten i kolumnen i den första data uppsättningen `Gryphon` `Text` . Den placerar andra rader i det andra resultatet av **delade data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Under sträng

Det här exemplet söker efter den angivna strängen i valfri position i den andra kolumnen i data uppsättningen. Positionen betecknas här av indexvärdet 1. Matchningen är Skift läges känslig.

```text
(\1) ^[a-f]
```

Den första resultat data uppsättningen innehåller alla rader där index-kolumnen börjar med något av följande tecken: `a` ,,,, `b` `c` `d` `e` , `f` . Alla andra rader dirigeras till den andra utdata.

## <a name="select-a-relative-expression"></a>Välj ett relativt uttryck

1. Lägg till modulen [dela data](./split-data.md) i din pipeline och Anslut den som indata till den data uppsättning som du vill dela.
  
1. För **delnings läge** väljer du **relativt uttryck**.
  
1. I rutan **Relations uttryck** anger du ett uttryck som utför en jämförelse åtgärd på en enskild kolumn.

   För **numerisk kolumn**:
   - Kolumnen innehåller siffror av vilken numerisk datatyp som helst, inklusive datum-och tids data typer.
   - Uttrycket kan referera till högst ett kolumn namn.
   - Använd et-tecknet, `&` för åtgärden och. Använd pipe-tecknet `|` för åtgärden eller.
   - Följande operatorer stöds:,,,, `<` `>` `<=` `>=` `==` , `!=` .
   - Du kan inte gruppera åtgärder med hjälp av `(` och `)` .
   
   För **sträng kolumn**:
   - Följande operatorer stöds: `==` , `!=` .

1. Skicka pipelinen.

   Uttrycket delar in data uppsättningen i två uppsättningar med rader: rader med värden som uppfyller villkoret och alla återstående rader.

Följande exempel visar hur du delar upp en data uppsättning genom att använda alternativet för **relativa uttryck** i modulen **dela data** .  

### <a name="calendar-year"></a>Kalenderår

Ett vanligt scenario är att dela upp en data uppsättning per år. Följande uttryck väljer alla rader där värdena i kolumnen `Year` är större än `2010` .

```text
\"Year" > 2010
```

Datum uttrycket måste ha ett konto för alla datum delar som ingår i data kolumnen. Datum formatet i data kolumnen måste vara konsekvent. 

I en datum kolumn som använder formatet ska uttrycket till exempel `mmddyyyy` vara något som liknar detta:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Kolumn index

Följande uttryck visar hur du kan använda kolumn indexet för att markera alla rader i den första kolumnen i data uppsättningen som innehåller värden som är mindre än eller lika med 30, men inte lika med 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
