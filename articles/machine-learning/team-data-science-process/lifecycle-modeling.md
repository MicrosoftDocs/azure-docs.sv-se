---
title: Modellerings steg för processens livs cykel för team data vetenskap
description: Mål, uppgifter och slut produkter för modellerings fasen för dina data vetenskaps projekt
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4719bd21bc6299373f9dabfe4733541c6fa27b94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94740189"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modellerings steg för processens livs cykel för team data vetenskap

Den här artikeln beskriver de mål, uppgifter och slut produkter som är associerade med modell fasen för team data science-processen (TDSP). Den här processen ger en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livs cykeln:

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Fastställ optimala data funktioner för maskin inlärnings modellen.
* Skapa en informativ modell för maskin inlärning som förutsäger målet noggrant.
* Skapa en maskin inlärnings modell som är lämplig för produktion.

## <a name="how-to-do-it"></a>Gör så här
Det finns tre huvudsakliga uppgifter i det här steget:

  * **Funktions teknik**: skapa data funktioner från rå data för att under lätta modell träningen.
  * **Modell träning**: Leta upp den modell som svarar på frågan noggrant genom att jämföra deras framgång.
  * Ta reda på om din modell är **lämplig för produktion.**

### <a name="feature-engineering"></a>Funktionsframställning
Funktions teknik inbegriper inkludering, agg regering och omvandling av obehandlade variabler för att skapa de funktioner som används i analysen. Om du vill veta mer om vad som driver en modell måste du förstå hur funktionerna relaterar till varandra och hur algoritmerna för maskin inlärning är att använda dessa funktioner. 

Det här steget kräver en kreativ kombination av domän expert kunskaper och de insikter som hämtats från data utforsknings steget. Funktions teknik är en balanserings åtgärd för att hitta och inkludera informativa variabler, men samtidigt försöker du undvika för många orelaterade variabler. Informativa variabler ger bättre resultat. orelaterade variabler introducerar onödigt brus i modellen. Du måste också generera dessa funktioner för alla nya data som erhålls under poängsättningen. Därför kan generationens funktioner bara vara beroende av data som är tillgängliga vid tidpunkten för poängsättningen. 

Teknisk vägledning om funktions teknik när du använder olika Azure-datatekniker finns i [funktions teknik i processen för data vetenskap](create-features.md). 

### <a name="model-training"></a>Modellträning
Det finns många tillgängliga modellvariabler beroende på vilken typ av fråga du försöker svara på. Anvisningar om hur du väljer algoritmer finns i [så här väljer du algoritmer för Microsoft Azure Machine Learning](../how-to-select-algorithms.md). Även om den här artikeln använder Azure Machine Learning, är den vägledning som är användbar för alla Machine Learning-projekt. 

Processen för modell träning innehåller följande steg: 

   * **Dela indata-data** slumpmässigt för modellering i en tränings data uppsättning och en test data uppsättning.
   * **Skapa modeller** med hjälp av övnings data uppsättningen.
   * **Utvärdera** utbildningen och test data uppsättningen. Använd en serie av konkurrerande algoritmer för maskin inlärning tillsammans med de olika associerade justerings parametrarna (kallas även *parameter svep*) som är kopplade till att besvara frågan om intresse med aktuella data.
   * **Fastställ den "bästa" lösningen** för att besvara frågan genom att jämföra måtten för framgång mellan alternativa metoder.

> [!NOTE]
> **Undvik läckage**: du kan orsaka dataläckage om du inkluderar data utanför den tränings data uppsättning som tillåter en modell eller en algoritm för maskin inlärning för att göra realistiska förutsägelser bättre. Läckage är en vanlig orsak till att data experter får nerv information när de får förutsägelse resultat som verkar vara för lämpliga för att vara sanna. Dessa beroenden kan vara svåra att identifiera. För att undvika läckage krävs ofta att du skapar en analys data uppsättning, skapar en modell och utvärderar resultatet. 
> 
> 

## <a name="artifacts"></a>Artifacts
Artefakterna som produceras i det här steget är:

   * [Funktions uppsättningar](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): de funktioner som har utvecklats för modellering beskrivs i avsnittet **funktions uppsättningar** i rapporten **data definition** . Den innehåller pekare till koden för att generera funktioner och en beskrivning av hur funktionen genererades.
   * [Modell rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): för varje modell som provas skapas en standard-mall baserad rapport som innehåller information om varje experiment.
   * **Kontroll punkts beslut**: utvärdera om modellen fungerar tillräckligt för produktion. Några viktiga frågor att fråga om är:
     * Besvarar modellen frågan med tillräckligt förtroende för test data? 
     * Bör du prova några alternativa metoder? Ska du samla in ytterligare data, göra fler funktioner eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här följer länkar till varje steg i livs cykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för särskilda scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](./index.yml).
