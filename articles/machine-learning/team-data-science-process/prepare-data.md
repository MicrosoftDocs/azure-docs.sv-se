---
title: Förbered data för ML Studio (klassisk) – team data science process
description: Förbearbeta och rensa data för att förbereda den för användning effektivt för maskin inlärning.
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
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012415"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Uppgifter för att förbereda data för förbättrad Machine Learning
För bearbetning och rensning av data är viktiga uppgifter som måste utföras innan en data uppsättning kan användas för modell träning. Rå data är ofta störningar och otillförlitliga och kan sakna värden. Att använda sådana data för modellering kan producera missvisande resultat. De här uppgifterna är en del av TDSP (Team data science process) och följer vanligt vis en inledande utforskning av en data uppsättning som används för att identifiera och planera för bearbetningen som krävs. Mer detaljerad information om TDSP-processen finns i stegen som beskrivs i [team data science-processen](overview.md).

För bearbetnings-och rengörings aktiviteter, t. ex. data utforsknings uppgift, kan utföras i en mängd olika miljöer, till exempel SQL eller Hive eller Azure Machine Learning Studio (klassisk) och med olika verktyg och språk, till exempel R eller python, beroende på var dina data lagras och hur de formateras. Eftersom TDSP är iterativt kan dessa uppgifter utföras i olika steg i arbets flödet för processen.

I den här artikeln beskrivs olika data bearbetnings koncept och uppgifter som kan utföras antingen före eller efter att data matas in i Azure Machine Learning Studio (klassisk).

Ett exempel på data utforskning och för bearbetning som utförs i Azure Machine Learning Studio (klassisk) finns i videon om [för bearbetning av data](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Varför i förväg bearbeta och rensa data?
Verkliga världs data samlas in från olika källor och processer och kan innehålla felaktigheter eller skadade data som äventyrar data uppsättningens kvalitet. Vanliga problem med data kvalitet som uppstår är:

* **Ofullständig**: data saknar attribut eller innehåller värden som saknas.
* **Brus**: data innehåller felaktiga poster eller avvikare.
* **Inkonsekvent**: data innehåller motstridiga poster eller avvikelser.

Kvalitets data är ett krav för kvalitets förutsägelse modeller. För att undvika att "skräp in, skräp ut" och förbättra data kvaliteten och därmed modell prestanda, är det absolut nödvändigt att utföra en data hälso kontroll för att upptäcka data problem tidigt och bestämma om motsvarande data bearbetnings-och rengörings steg.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Vad är några typiska data hälso skärmar som används?
Vi kan kontrol lera den allmänna kvaliteten på data genom att kontrol lera:

* Antalet **poster**.
* Antalet **attribut** (eller **funktioner**).
* Attributets **data typer** (nominellt, ordnings tal eller kontinuerliga).
* Antalet **saknade värden**.
* **Välformulerade** data.
  * Om data är i TSV eller CSV kontrollerar du att kolumn avgränsare och rad avgränsare alltid är korrekt åtskilda kolumner och rader.
  * Om data är i HTML-eller XML-format kontrollerar du om data är korrekt utformade baserat på deras respektive standarder.
  * Parsning kan också behövas för att extrahera strukturerad information från halv strukturerade eller ostrukturerade data.
* **Inkonsekventa data poster**. Kontrol lera att värde intervallet är tillåtet. Om t. ex. data innehåller student BETYGs klass (genomsnitt) kontrollerar du om GPA är inom det angivna intervallet, ungefär 0 ~ 4.

När du hittar problem med data är **bearbetnings stegen** nödvändiga, vilket ofta inbegriper rensning av saknade värden, data normalisering, diskretiseringsmetoden stämmer, text bearbetning för att ta bort och/eller ersätta inbäddade tecken som kan påverka data justeringen, blandade data typer i gemensamma fält och andra.

**Azure Machine Learning använder välformulerade tabell data**.  Om data redan finns i tabell form kan data för bearbetning utföras direkt med Azure Machine Learning Studio (klassisk) i Machine Learning.  Om data inte är i tabell format, antar vi att de är i XML-format, kan tolkas för att konvertera data till tabell format.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Vad är några av de viktigaste uppgifterna i för bearbetning av data?
* **Data rensning**: Fyll i saknade värden, identifiera och ta bort data och avvikande störningar.
* **Data omvandling**: normalisera data för att minska dimensioner och brus.
* **Data reducering**: exempel på data poster eller attribut för enklare data hantering.
* **Data diskretiseringsmetoden stämmer**: konvertera kontinuerliga attribut till kategoriska-attribut för enkel användning med vissa Machine Learning-metoder.
* **Text rensning**: ta bort inbäddade tecken som kan orsaka data fel justering, till exempel inbäddade flikar i en tabbavgränsade datafil, inbäddade nya rader som kan bryta poster, till exempel.

I avsnitten nedan beskrivs några av de här stegen för data bearbetning.

## <a name="how-to-deal-with-missing-values"></a>Hur hanterar jag saknade värden?
För att hantera saknade värden, är det bäst att först identifiera orsaken till att de saknade värdena för att hantera problemet bättre. Typiska metoder för värde hantering som saknas är:

* **Borttagning**: ta bort poster med saknade värden
* **Dummy-ersättning**: Ersätt saknade värden med ett dummy-värde: t. ex. *okänd* för kategoriska eller 0 för numeriska värden.
* **Medel ersättning**: om data som saknas är numeriska ersätter du värdena som saknas med medelvärdet.
* **Frekvent ersättning**: om data som saknas är kategoriska ersätter du de värden som saknas med det vanligaste objektet
* **Regressions ersättning**: Använd en Regressions metod för att ersätta saknade värden med försämrat-värden.  

## <a name="how-to-normalize-data"></a>Hur normaliserar jag data?
Data normalisering skalar om numeriska värden till ett visst intervall. Exempel på populära data normaliserings metoder:

* **Minsta-Max normalisering**: linjärt transformera data till ett intervall, till exempel mellan 0 och 1, där det lägsta värdet skalas till 0 och Max värdet till 1.
* **Z-score normalisering**: skala data baserat på medelvärde och standard avvikelse: dividera skillnaden mellan data och medelvärdet med standard avvikelsen.
* **Decimal skalning**: skala data genom att flytta attributvärdets decimal tecken.  

## <a name="how-to-discretize-data"></a>Hur användas vid diskretisering du data?
Det går att diskretiserade data genom att konvertera kontinuerliga värden till nominella attribut eller intervall. Några sätt att göra detta är:

* **Diskretisering med samma bredd**: dividera intervallet med alla möjliga värden för ett attribut till N grupper av samma storlek och tilldela de värden som infaller på en lager plats med lager plats numret.
* **Diskretisering med samma höjd**: dividera intervallet med alla möjliga värden för ett attribut till N grupper, där var och en innehåller samma antal instanser, och tilldela sedan de värden som infaller på en lager plats med lager plats numret.  

## <a name="how-to-reduce-data"></a>Hur minskar du data?
Det finns olika metoder för att minska data storleken för enklare data hantering. Följande metoder kan användas, beroende på data storlek och domän:

* **Post sampling**: exempel på data poster och välj den representativa del mängden från data.
* **Sampling av attribut**: Välj endast en delmängd av de viktigaste attributen från data.  
* **Aggregation**: dela in data i grupper och lagra numren för varje grupp. Till exempel kan de dagliga intäkterna från en restaurang kedja under de senaste 20 åren aggregeras per månads intäkt för att minska storleken på data.  

## <a name="how-to-clean-text-data"></a>Så här rensar du text data?
**Textfält i tabell data** kan innehålla tecken som påverkar justering av kolumner och/eller post gränser. Till exempel är inbäddade flikar i en tabbavgränsade fil orsak till kolumn fel justering och inbäddade nya rad tecken Bryt post rader. Felaktig text kodnings hantering vid skrivning eller läsning av text leder till informations förlust, oavsiktlig introduktion av oläsliga tecken (t. ex. nullvärden) och kan också påverka text tolkning. Noggrann parsning och redigering kan krävas för att rensa textfält för korrekt justering och/eller för att extrahera strukturerade data från ostrukturerade eller delvis strukturerade text data.

**Data utforskning** erbjuder en tidig vy över data. Ett antal data problem kan tas bort under det här steget och motsvarande metoder kan användas för att åtgärda dessa problem.  Det är viktigt att ställa frågor, till exempel vad som är orsaken till problemet och hur problemet kan ha införts. Den här processen hjälper dig också att bestämma de data bearbetnings steg som måste vidtas för att lösa dem. Att identifiera de slutliga användnings fallen och personer kan också användas för att prioritera data bearbetnings ansträngningen.

## <a name="references"></a>Referenser
> *Data utvinning: begrepp och tekniker*, tredje utgåvan, Morgan Kaufmann, 2011, Jiawei han, Micheline Kamber och Jian Pei
> 
> 

