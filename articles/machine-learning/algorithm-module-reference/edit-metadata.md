---
title: 'Redigera metadata: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen redigera metadata i Azure Machine Learning för att ändra metadata som är associerade med kolumner i en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908042"
---
# <a name="edit-metadata-module"></a>Redigera metadata-modul

I den här artikeln beskrivs en modul som ingår i Azure Machine Learning designer.

Använd modulen redigera metadata för att ändra metadata som är associerade med kolumner i en data uppsättning. Data uppsättningens värde och datatyp ändras efter användningen av modulen redigera metadata.

Typiska metadata-ändringar kan innehålla:
  
+ Behandlar booleska eller numeriska kolumner som kategoriska-värden.
  
+ Anger vilken kolumn som innehåller **klass** etiketten eller innehåller de värden som du vill kategorisera eller förutsäga.
  
+ Markerar kolumner som funktioner.
  
+ Ändra datum-/tids värden till numeriska värden eller vice versa.
  
+ Byta namn på kolumner.
  
 Använd redigera metadata när som helst behöver du ändra definitionen för en kolumn, vanligt vis för att uppfylla kraven för en underordnad modul. Till exempel fungerar vissa moduler bara med vissa data typer eller kräver flaggor på kolumnerna, till exempel `IsFeature` eller `IsCategorical` .  
  
 När du har utfört den begärda åtgärden kan du återställa metadata till dess ursprungliga tillstånd.
  
## <a name="configure-edit-metadata"></a>Konfigurera redigera metadata
  
1. I Azure Machine Learning designer lägger du till modulen redigera metadata i din pipeline och ansluter den data uppsättning som du vill uppdatera. Du hittar modulen i kategorin **data omvandling** .
  
1. Klicka på **Redigera kolumn** i den högra panelen i modulen och välj den kolumn eller uppsättning kolumner som du vill arbeta med. Du kan välja kolumner individuellt efter namn eller index, eller så kan du välja en grupp med kolumner efter typ.  
  
1. Välj alternativet **datatyp** om du behöver tilldela en annan datatyp till de markerade kolumnerna. Du kan behöva ändra data typen för vissa åtgärder. Om din käll data uppsättning exempelvis har tal som hanteras som text, måste du ändra dem till en numerisk datatyp innan du använder matematiska åtgärder.

    + De data typer som stöds är **sträng**, **heltal**, **Double**, **Boolean** och **datetime**.

    + Om du väljer flera kolumner måste du tillämpa metadata-ändringarna på *alla* valda kolumner. Anta till exempel att du väljer två eller tre numeriska kolumner. Du kan ändra dem till en sträng data typ och byta namn på dem i en enda åtgärd. Du kan dock inte ändra en kolumn till en sträng data typ och en annan kolumn från ett flyttal till ett heltal.
  
    + Om du inte anger någon ny datatyp, ändras inte kolumnens metadata.

    + Kolumn typen och värdena kommer att ändras när du har utfört åtgärden redigera metadata. Du kan återställa den ursprungliga data typen när som helst genom att använda redigera metadata för att återställa kolumn data typen.  

    > [!NOTE]
    > **Datetime-formatet** följer [python-inbyggt datetime-format](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior).  
    > Om du ändrar någon typ av tal till **datetime** -typen lämnar du fältet **datetime-format** tomt. Det går för närvarande inte att ange mål data formatet.

1. Välj alternativet **kategoriska** för att ange att värdena i de markerade kolumnerna ska behandlas som kategorier.

    Du kan till exempel ha en kolumn som innehåller siffrorna 0, 1 och 2, men vet att siffrorna faktiskt betyder "Röke", "icke-Röke" och "okänd". I så fall kan du genom att flagga kolumnen som kategoriska se till att värdena endast används för att gruppera data och inte i numeriska beräkningar.
  
1. Använd alternativet **fält** om du vill ändra hur Azure Machine Learning använder data i en modell.

    + **Funktion**: Använd det här alternativet för att flagga en kolumn som en funktion i moduler som endast fungerar på funktions kolumner. Som standard behandlas alla kolumner som inlednings vis som-funktioner.  
  
    + **Etikett**: Använd det här alternativet för att markera etiketten, som även kallas för det förutsägbara attributet eller mål variabeln. Många moduler kräver att exakt en etikett kolumn finns i data uppsättningen.

        I många fall kan Azure Machine Learning härleda att en kolumn innehåller en klass etikett. Genom att ange dessa metadata kan du se till att kolumnen identifieras korrekt. Om du väljer det här alternativet ändras inte data värden. Den ändrar bara det sätt på vilket vissa algoritmer för maskin inlärning hanterar data.
  
    > [!TIP]
    > Har du data som inte passar in i dessa kategorier? Din data uppsättning kan till exempel innehålla värden som unika identifierare som inte är användbara som variabler. Ibland kan sådana ID: n orsaka problem när de används i en modell.
    >
    > Lyckligt vis behåller Azure Machine Learning alla dina data så att du inte behöver ta bort sådana kolumner från data uppsättningen. När du behöver utföra åtgärder på vissa särskilda kolumner tar du bara bort alla andra kolumner med hjälp av modulen [Välj kolumner i data uppsättning](select-columns-in-dataset.md) . Senare kan du sammanfoga kolumnerna till data uppsättningen igen med hjälp av modulen [Lägg till kolumner](add-columns.md) .  
  
1. Använd följande alternativ för att rensa tidigare val och återställa metadata till standardvärdena.  
  
    + **Rensa funktion**: Använd det här alternativet om du vill ta bort funktions flaggan.  
  
         Alla kolumner behandlas från början som funktioner. För moduler som utför matematiska åtgärder kan du behöva använda det här alternativet för att förhindra att numeriska kolumner behandlas som variabler.
  
    + **Rensa etikett**: Använd det här alternativet om du vill ta bort **etikettens** metadata från den angivna kolumnen.  
  
    + **Rensa Poäng**: Använd det här alternativet om du vill ta bort **score** metadata från den angivna kolumnen.  
  
         Du kan för närvarande inte uttryckligen Markera en kolumn som en poäng i Azure Machine Learning. Vissa åtgärder resulterar dock i en kolumn som flaggas som ett resultat internt. Dessutom kan en anpassad R-modul returnera Poäng värden.

1. För **nya kolumn namn** anger du det nya namnet på den markerade kolumnen eller kolumnerna.  
  
    + Kolumn namn får endast använda tecken som stöds av UTF-8-kodning. Tomma strängar, null-värden eller namn som består av enbart blank steg är inte tillåtna.  
  
    + Om du vill byta namn på flera kolumner anger du namnen som en kommaavgränsad lista i ordningen för kolumn index.  
  
    + Alla markerade kolumner måste byta namn. Du kan inte utelämna eller hoppa över kolumner.  
  
1. Skicka pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.
