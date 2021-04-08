---
title: Affärs förståelse i team data science process
description: Mål, uppgifter och slut produkter för affärs förståelses fasen för dina data vetenskaps projekt i team data science-processen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "76710328"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Affärs förståelses fasen i livs cykeln för team data science process

Den här artikeln beskriver de mål, uppgifter och slut produkter som är associerade med affärs förståelses fasen för team data science-processen (TDSP). Den här processen ger en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Ange de nyckelattribut som ska fungera som modell mål och vars relaterade mått används för att fastställa projektets framgångar.
* Identifiera relevanta data källor som företaget har åtkomst till eller behöver skaffa.

## <a name="how-to-do-it"></a>Gör så här
Det finns två huvudsakliga uppgifter i det här steget: 

   * **Definiera mål**: arbeta med din kund och andra intressenter för att förstå och identifiera affärs problem. Formulera frågor som definierar de affärs mål som teknikerna för data vetenskap kan rikta in sig på.
   * **Identifiera data källor**: hitta relevanta data som hjälper dig att besvara frågorna som definierar projektets mål.

### <a name="define-objectives"></a>Definiera mål
1. Ett centralt mål i det här steget är att identifiera de viktiga affärsvariabler som analysen behöver förutsäga. Vi refererar till dessa variabler som *modell mål*, och vi använder de mått som är kopplade till dem för att fastställa projektets framgångar. Två exempel på sådana mål är försäljnings prognoser eller sannolikheten för att en beställning tas till bedräglig.

2. Definiera projekt målen genom att fråga och förfina "skarpa" frågor som är relevanta, speciella och otvetydiga. Data science är en process som använder namn och siffror för att besvara sådana frågor. Du använder vanligt vis data vetenskap eller maskin inlärning för att besvara fem typer av frågor:
 
   * Hur mycket eller hur många? regression
   * Vilken kategori? klassning
   * Vilken grupp? redundanskluster
   * Är det här underligt? (avvikelse identifiering)
   * Vilket alternativ ska vidtas? rekommenderade

   Ta reda på vilka av dessa frågor som du frågar och hur du får svar på dina affärs mål.

3. Definiera projekt gruppen genom att ange roller och ansvars områden för dess medlemmar. Utveckla en mil stolpe plan på hög nivå som du itererar när du upptäcker mer information. 

4. Definiera måtten för framgång. Till exempel kanske du vill uppnå en förutsägelse av kund omsättning. Du behöver en noggrannhets grad på "x" procent i slutet av det här tre månaders projektet. Med dessa data kan du erbjuda kund kampanjer för att minska omsättningen. Måtten måste vara **smarta**: 

   * **S** pecifika 
   * **M** easurable
   * **En** chievable 
   * **R**-Elevant 
   * **T** IME-kopplad 

### <a name="identify-data-sources"></a>Identifiera datakällor
Identifiera data källor som innehåller kända exempel på svar på dina skarpa frågor. Leta efter följande data:

* Data som är relevanta för frågan. Har du mått på det mål och de funktioner som är relaterade till målet?
* Data som är ett korrekt mått för ditt modell mål och funktionerna i intresse.

Du kan till exempel se att de befintliga systemen behöver samla in och logga ytterligare typer av data för att lösa problemet och uppnå projekt målen. I så fall kanske du vill söka efter externa data källor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artifacts
Här är slut produkterna i det här skedet:

   * [Charter dokument](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): en standardmall finns i TDSP-projektets struktur definition. Stadgan-dokumentet är ett levande dokument. Du uppdaterar mallen i projektet när du gör nya identifieringar och när affärs behoven ändras. Nyckeln är att iterera till det här dokumentet, vilket innebär att du kan lägga till mer information när du går igenom identifierings processen. Låt kunden och andra intressenter delta i att göra ändringarna och på ett tydligt sätt meddela orsakerna till ändringarna.  
   * [Data källor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): avsnittet **rå data källor** i rapporten **data definitioner** som finns i mappen TDSP Project **data Report** innehåller data källorna. I det här avsnittet anges de ursprungliga och mål platserna för rå data. I senare steg kan du fylla i ytterligare information som skripten för att flytta data till din analys miljö.  
   * [Data ord listor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): det här dokumentet innehåller beskrivningar av de data som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (data typer och information om verifierings reglerna, om det finns några) och diagram över entitetsrelationer, om det är tillgängligt.

## <a name="next-steps"></a>Nästa steg

Här följer länkar till varje steg i livs cykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 
