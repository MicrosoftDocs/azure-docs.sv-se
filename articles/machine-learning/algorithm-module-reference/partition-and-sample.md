---
title: 'Partition och exempel: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder partition och exempel modul i Azure Machine Learning för att utföra sampling på en data uppsättning eller skapa partitioner från din data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898487"
---
# <a name="partition-and-sample-module"></a>Partition och exempel modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen partition och exempel för att utföra sampling på en data uppsättning eller skapa partitioner från din data uppsättning.

Sampling är ett viktigt verktyg i Machine Learning eftersom det gör att du kan minska storleken på en data uppsättning samtidigt som du behåller samma förhållande mellan värden. Den här modulen stöder flera relaterade uppgifter som är viktiga i Machine Learning: 

- Dela upp data i flera underavsnitt med samma storlek. 

  Du kan använda partitionerna för kors validering eller tilldela ärenden till slumpmässiga grupper.

- Separera data i grupper och arbeta med data från en speciell grupp. 

  När du slumpmässigt tilldelar ärenden till olika grupper kan du behöva ändra de funktioner som är associerade med endast en grupp.

- Ta. 

  Du kan extrahera en procent andel av data, tillämpa slumpmässig sampling eller välja en kolumn som ska användas för att balansera data uppsättningen och utföra Stratified-sampling på dess värden.

- Skapa en mindre data uppsättning för testning. 

  Om du har många data kanske du bara vill använda de första *n* raderna när du konfigurerar pipelinen och sedan växlar till att använda den fullständiga data uppsättningen när du skapar din modell. Du kan också använda sampling för att skapa en mindre data uppsättning som kan användas under utveckling.

## <a name="configure-the-module"></a>Konfigurera modulen

Den här modulen stöder följande metoder för att dela upp data i partitioner eller för att sampla. Välj metoden först och ange sedan ytterligare alternativ som krävs för metoden.

- Head
- Samling
- Tilldela till vikning
- Välj vikning

### <a name="get-top-n-rows-from-a-dataset"></a>Hämta de översta N raderna från en data uppsättning

Använd det här läget för att bara hämta de första *n* raderna. Det här alternativet är användbart om du vill testa en pipeline på ett litet antal rader och du inte behöver de data som ska bal anse ras eller samplas på något sätt.

1. Lägg till **partition och exempel** modul i din pipeline i gränssnittet och Anslut data uppsättningen.  

1. **Partition eller exempel läge**: Ange det här alternativet till **Head**.

1. **Antal rader att välja**: Ange antalet rader som ska returneras.

   Antalet rader måste vara ett heltal som inte är negativt. Om antalet markerade rader är större än antalet rader i data uppsättningen returneras hela data uppsättningen.

1. Skicka pipelinen.

Modulen matar ut en enda data uppsättning som bara innehåller det angivna antalet rader. Raderna läses alltid in från data uppsättningens överkant.

### <a name="create-a-sample-of-data"></a>Skapa ett exempel på data

Det här alternativet stöder enkel slumpmässig provtagning eller Stratified slumpmässig sampling. Det är användbart om du vill skapa en mindre representativ exempel data uppsättning för testning.

1. Lägg till **partition och exempel** modul i din pipeline och Anslut data uppsättningen.

1. **Partition eller exempel läge**: Ange det här alternativet för att  **Sampla**.

1. **Samplings frekvens**: Ange ett värde mellan 0 och 1. Det här värdet anger procent andelen av rader från käll data uppsättningen som ska inkluderas i data uppsättningen för utdata.

   Om du till exempel bara vill ha hälften av den ursprungliga data uppsättningen anger `0.5` du för att indikera att samplings frekvensen ska vara 50 procent.

   Raderna i data uppsättningen för indata är blandade och placeras selektivt i den utgående data uppsättningen enligt det angivna förhållandet.

1. **Slumpmässigt utsäde för sampling: om** du vill kan du ange ett heltal som ska användas som ett Seed-värde.

   Det här alternativet är viktigt om du vill att raderna ska delas upp på samma sätt varje gång. Standardvärdet är **0**, vilket innebär att ett startutsäde genereras baserat på system klockan. Det här värdet kan leda till något annorlunda resultat varje gången du kör pipelinen.

1. **Stratified Split för sampling**: Välj det här alternativet om det är viktigt att raderna i data uppsättningen delas jämnt av en nyckel kolumn före sampling.

   För **Stratification nyckel kolumn för sampling** väljer du en enda *Strata-kolumn* som ska användas vid division av data uppsättningen. Raderna i data uppsättningen delas sedan upp på följande sätt:

   1. Alla ingående rader grupperas (Stratified) med värdena i den angivna Strata-kolumnen.

   1. Rader flyttas i varje grupp.

   1. Varje grupp läggs selektivt till i data uppsättningen för utdata för att uppfylla det angivna förhållandet.


1. Skicka pipelinen.

   Med det här alternativet matar modulen ut en enda data uppsättning som innehåller ett representativt urval av data. Den återstående, insamplade delen av data uppsättningen är inte utdata. 

## <a name="split-data-into-partitions"></a>Dela upp data i partitioner

Använd det här alternativet om du vill dela upp data uppsättningen i del mängder av data. Det här alternativet är också användbart när du vill skapa ett anpassat antal vikningar för kors validering eller dela rader i flera grupper.

1. Lägg till **partition och exempel** modul i din pipeline och Anslut data uppsättningen.

1. För **partition eller exempel läge** väljer **du tilldela till vikning**.

1. **Använd ersättnings i partitionering**: Välj det här alternativet om du vill att den exempel rad som ska läggas till i poolen med rader för eventuell åter användning. Därför kan samma rad tilldelas till flera vikningar.

   Om du inte använder Replacement (standard alternativet) tas inte den exempel raden tillbaka till poolen med rader för eventuell åter användning. Därför kan varje rad endast tilldelas till en vikning.

1. **Slumpmässig delning**: Välj det här alternativet om du vill att rader ska tilldelas slumpmässigt till vikning.

   Om du inte väljer det här alternativet tilldelas rader till vikning genom metoden Round-Robin.

1. **Slumpmässigt utsäde**: Alternativt kan du ange ett heltal som ska användas som startvärdet. Det här alternativet är viktigt om du vill att raderna ska delas upp på samma sätt varje gång. Annars innebär standardvärdet **0** att ett slumpmässigt start värde används.

1. **Ange partitionerings metod**: Ange hur du vill att data ska fördelas till varje partition med hjälp av följande alternativ:

   - **Jämn partition**: Använd det här alternativet om du vill placera ett lika antal rader i varje partition. Ange antalet utgående partitioner genom att ange ett heltal i rutan **Ange antalet kurvor som ska delas jämnt i** rutan.

   - **Partition med anpassade proportioner**: Använd det här alternativet för att ange storleken på varje partition som en kommaavgränsad lista.

     Anta till exempel att du vill skapa tre partitioner. Den första partitionen kommer att innehålla 50 procent av data. De återstående två partitionerna kommer att innehålla 25 procent av datan. I **listan med proportioner avgränsade med kommatecken** anger du följande nummer: **0,5,. 25,. 25**.

     Summan av alla partition storlekar måste vara upp till exakt 1.

     Om du anger siffror som lägger till upp till *mindre än 1*, skapas en extra partition som innehåller de återstående raderna. Om du till exempel anger värdena **2** och **.3** skapas en tredje partition som innehåller de återstående 50 procenten av alla rader.
     
     Om du anger siffror som lägger till upp till *fler än 1* uppstår ett fel när du kör pipelinen.

1. **Stratified Split**: Välj det här alternativet om du vill att raderna ska Stratified vid delning, och sedan välja _kolumnen Strata_.

1. Skicka pipelinen.

   Med det här alternativet kommer modulen att mata ut flera data uppsättningar. Data uppsättningarna partitioneras enligt de regler som du har angett.

### <a name="use-data-from-a-predefined-partition"></a>Använda data från en fördefinierad partition  

Använd det här alternativet när du har delat upp en data uppsättning i flera partitioner och nu vill läsa in varje partition i tur och svar för vidare analys eller bearbetning.

1. Lägg till **partitionen och modulen exempel** i pipelinen.

1. Anslut modulen till utdata från en föregående instans av **partition och exempel**. Den instansen måste ha använt alternativet **tilldela till vikning** för att generera ett antal partitioner.

1. **Partition eller exempel läge**: Välj **Välj vikning**.

1. **Ange vilken vikning som ska samplas från**: Välj en partition som ska användas genom att ange dess index. Partition index är 1-baserade. Om du till exempel har delat upp data uppsättningen i tre delar, skulle partitionerna ha index 1, 2 och 3.

   Om du anger ett ogiltigt index värde genereras ett design tids fel: "Error 0018: dataset innehåller ogiltiga data".

   Förutom att gruppera data uppsättningen efter vikning kan du separera data uppsättningen i två grupper: en mål vikning och allt annat. Det gör du genom att ange indexet för en enda vikning och sedan välja alternativet **Välj komplementet till den valda vikningen** för att hämta allt, men data i den angivna vikningen.

1. Om du arbetar med flera partitioner måste du lägga till fler instanser av **partition och exempel** -modulen för att hantera varje partition.

   Till exempel är **partition och exempel** modul på den andra raden inställd på **vikning** och modulen på den tredje raden är inställd på att **välja vikning**.   

   ![Partition och exempel](./media/module/partition-and-sample.png)

1. Skicka pipelinen.

   Med det här alternativet matar modulen ut en enda data uppsättning som bara innehåller de rader som tilldelats den vikningen.

> [!NOTE]
>  Du kan inte Visa viknings designerna direkt. De finns bara i metadata.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 