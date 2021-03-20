---
title: Förstärka inlärning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Personanpassaren använder information om åtgärder och aktuell kontext för att göra bättre rangordnings förslag. Informationen om dessa åtgärder och kontext är attribut eller egenskaper som kallas funktioner.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132746"
---
# <a name="what-is-reinforcement-learning"></a>Vad är förstärknings inlärning?

Förstärknings inlärning är en metod för maskin inlärning som lär sig beteenden genom att få feedback från användningen.
 
Förstärka inlärningen fungerar med:

* Tillhandahålla en möjlighet eller frihets grader för att införa ett beteende som fattar beslut eller val.
* Tillhandahålla sammanhangsbaserad information om miljön och val.
* Ge feedback om hur väl beteendet uppnår ett visst mål.

Även om det finns många under typer och stilar för förstärknings inlärning, så är det här hur konceptet fungerar i en Personanpassare:

* Ditt program ger möjlighet att visa en innehålls del från en lista med alternativ.
* Programmet innehåller information om varje alternativ och användarens kontext.
* Ditt program beräknar en _belönings Poäng_.

Till skillnad från vissa metoder för att förbättra inlärningen kräver inte Personanpassan att en simulering fungerar i. Dess Learning-algoritmer är utformade för att reagera på en extern värld (och kontrol lera den) och lära sig från varje data punkt med en förståelse för att det är en unik affärs möjlighet som kostar tid och pengar att skapa, och att det finns en icke-noll-ångra (förlust av eventuell belöning) om det uppstår sämre prestanda.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Vilken typ av förstärka inlärnings algoritmer använder sig av Personanpassare?

Den aktuella versionen av Personanpassare använder **sammanhangsbaserade Bandits**, en metod för att förstärka inlärningen som är inramad av beslut eller val mellan diskreta åtgärder, i en viss kontext.

_Besluts minnet_, den modell som har tränats att samla in det bästa möjliga beslutet, baserat på ett sammanhang, använder en uppsättning linjära modeller. Detta har flera gånger visat affärs resultat och är en beprövad metod, delvis på grund av att de kan lära sig från den verkliga världen snabbt utan att behöva multi-pass-utbildning, och delvis eftersom de kan komplettera övervakade inlärnings modeller och djup neurala nätverks modeller.

Den utforska/utnyttja trafik tilldelningen görs slumpmässigt efter procent andelen för utforskning och standardalgoritmen för utforskning är Epsilon-girig.

### <a name="history-of-contextual-bandits"></a>Historik för sammanhangsbaserade Bandits

John Langford myntade namnet kontextuell Bandits (Langford och Zhang [2007]) för att beskriva en dragbar del av förstärknings inlärningen och har arbetat på ett halv dussin dokument som förbättrar vår förståelse av hur du kan lära dig i det här paradigmet:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer och Langford [2009]
* Li et al. [2010]

John har också fått flera självstudier tidigare i ämnen som gemensam förutsägelse (ICML 2015), kontextuell bandit teori (NIPS 2013), aktiv inlärning (ICML 2009) och exempel på komplexitets gränser (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Vilka ramverk för Machine Learning används?

En personanpassare använder för närvarande [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) som grund för Machine Learning. Det här ramverket tillåter maximalt data flöde och lägsta latens när du gör anpassnings rankning och tränar modellen med alla händelser.

## <a name="references"></a>Referenser

* [Fatta sammanhangsbaserade beslut med låg teknisk skuld](https://arxiv.org/abs/1606.03966)
* [En minsknings metod för god klassificering](https://arxiv.org/abs/1803.02453)
* [Effektiv sammanhangsbaserad Bandits i icke-stationiga världar](https://arxiv.org/abs/1708.01799)
* [Rest förlust förutsägelse: inlärning med ingen stegvis feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Mappnings instruktioner och visuella observationer till åtgärder med förstärkt inlärning](https://arxiv.org/abs/1704.08795)
* [Lär dig att söka bättre än din lärare](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Nästa steg

[Offlineutvärdering](concepts-offline-evaluation.md) 