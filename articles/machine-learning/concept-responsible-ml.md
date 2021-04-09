---
title: Vad är ansvarig för maskin inlärning (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig vad ansvarig maskin inlärning är och hur du använder det med Azure Machine Learning för att förstå modeller, skydda data och kontrol lera modell livs cykeln.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: f849968c16d2c3d8940a71da0e0cda536882d9c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101692070"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Vad är ansvaret för maskin inlärning? (förhandsversion)

I den här artikeln lär du dig vad ansvarig Machine Learning (ML) är och hur du kan lägga det i praxis med Azure Machine Learning.

## <a name="responsible-machine-learning-principles"></a>Ansvariga Machine Learning-principer

I utvecklingen och användningen av AI-system måste förtroendet ligga på kärnan. Förtroende i plattform, process och modeller. På Microsoft omfattar ansvarig maskin inlärning följande värden och principer:

- Förstå Machine Learning-modeller
  - Tolka och förklara modell beteende
  - Utvärdera och minimera modellens marknadsmässighet
- Skydda personer och deras data
  - Förhindra data exponering med differentiell sekretess
  - Arbeta med krypterade data med homomorphic-kryptering
- Styr processen från slut punkt till slut punkt för Machine Learning
  - Dokumentera livs cykeln för Machine Learning med datablad

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Ansvariga ML-pelare – tolkning, differentiell sekretess, homomorphic kryptering, gransknings historik-Azure Machine Learning":::

Eftersom artificiell intelligens och autonoma system integrerar mer i samhället i samhället är det viktigt att proaktivt göra en satsning på att förutse och minimera oönskade konsekvenser av dessa tekniker.

## <a name="interpret-and-explain-model-behavior"></a>Tolka och förklara modell beteende

Svårt att förklara eller täckande system kan vara problematiska eftersom det gör det svårt för intressenter som systemutvecklare, tillsynsmyndigheter, användare och affärs besluts fattare att förstå varför system gör vissa beslut. Vissa AI-system är mer förklarare än andra och det finns ibland en kompromiss mellan ett system med högre precision och ett som är mer förklarat.

Om du vill skapa tolknings bara AI-system använder du [InterpretML](https://github.com/interpretml/interpret), ett paket med öppen källkod som skapats av Microsoft. InterpretML-paketet har stöd för en mängd olika tolknings tekniker, till exempel SHapley additiva förklaringar (SHAP), efterlikna förklaringar och permutations funktions prioritet (PFI).  [InterpretML kan användas i Azure Machine Learning](how-to-machine-learning-interpretability.md) för att [tolka och förklara dina Machine Learning-modeller](how-to-machine-learning-interpretability-aml.md), inklusive [automatiserade maskin inlärnings modeller](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Minimera skälighet i Machine Learning-modeller

Eftersom AI-system blir mer inblandade i den dagliga besluts fattandet av samhället är det mycket viktigt att dessa system fungerar bra med att tillhandahålla verkliga resultat för alla.

Att AI-system är rättvist kan resultera i följande oönskade konsekvenser:

- Käll möjligheter, resurser eller information från individer.
- Förstärka bias och stereotyper.

Många aspekter av skälighet kan inte fångas eller representeras av mått. Det finns verktyg och metoder som kan förbättra skälighet i utformningen och utvecklingen av AI-system.

Det finns två viktiga steg för att minska den verkliga risken i AI-system för utvärdering och skydd. Vi rekommenderar [FairLearn](https://github.com/fairlearn/fairlearn), ett paket med öppen källkod som kan bedöma och minimera risken för AI-system. Läs mer om skälighet och FairLearn-paketet i [artikeln skälighet i ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Förhindra data exponering med differentiell sekretess

När data används för analys är det viktigt att data förblir privata och konfidentiella under hela användningen. Differentiell sekretess är en uppsättning system och metoder som hjälper till att skydda enskilda personers och privata data.

I traditionella scenarier lagras rå data i filer och databaser. När användarna analyserar data använder de vanligt vis rå data. Detta är ett problem eftersom det kan orsaka intrång i en individs integritet. Differentiell sekretess försöker åtgärda problemet genom att lägga till "brus" eller slumpmässig het för data så att användarna inte kan identifiera enskilda data punkter.

Att implementera Differentiellt privat system är svårt. [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) är ett projekt med öppen källkod som innehåller olika komponenter för att skapa globala, Differentiellt privata system. Mer information om differentiell sekretess och SmartNoise-projektet finns i avsnittet [bevara data sekretess med hjälp av differentiella sekretess-och SmartNoise](./concept-differential-privacy.md) -artiklar.

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Arbeta med krypterade data med homomorphic-kryptering

I traditionella moln lagrings-och beräknings lösningar måste molnet ha okrypterad åtkomst till kunddata för att kunna beräkna den. Den här åtkomsten visar data till moln operatörer. Data sekretess är beroende av åtkomst kontroll principer som implementerats av molnet och är betrott av kunden.

Homomorphic-kryptering gör det möjligt att utföra beräkningar på krypterade data utan att behöva åtkomst till en hemlig nyckel (dekryptering). Resultatet av beräkningarna krypteras och kan bara visas av ägaren av den hemliga nyckeln. Med homomorphic-kryptering kommer moln operatörer aldrig att ha okrypterad åtkomst till de data som de lagrar och använder. Beräkningar utförs direkt på krypterade data. Data sekretess är beroende av den senaste krypteringen och data ägaren styr alla informations utgåvor. Mer information om homomorphic-kryptering på Microsoft finns i [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Kom igång med homomorphic-kryptering i Azure Machine Learning genom att använda python-bindningar med [krypterad härledning](https://pypi.org/project/encrypted-inference/) för [Microsoft Seal](https://github.com/microsoft/SEAL). Microsoft SEAL är ett homomorphic krypterings bibliotek med öppen källkod som gör att tillägg och multiplicitet kan utföras på krypterade heltal eller reella tal. Mer information om Microsoft SEAL finns på sidan [Azure Architecture Center](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) eller [Microsoft Research Project](https://www.microsoft.com/research/project/microsoft-seal/).

Se följande exempel för att lära dig [hur du distribuerar en krypterad inferencing-webb tjänst i Azure Machine Learning](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Dokumentera livs cykeln för Machine Learning med datablad

Det är viktigt att dokumentera rätt information i Machine Learning-processen för att fatta ansvariga beslut i varje steg. Data blad är ett sätt att dokumentera maskin inlärnings resurser som används och skapas som en del av Machine Learning-livscykeln.

Modeller brukar betraktas som "täckande rutor" och det finns ofta lite information om dem. Eftersom Machine Learning-system blir mer genomgripande och används för besluts fattande, är det ett steg att utveckla fler ansvariga Machine Learning-system.

Viss modell information som du kanske vill dokumentera som en del av ett datablad:

- Avsedd användning
- Modell arkitektur
- Tränings data som används
- Använda utvärderings data
- Prestanda mått för tränings modell
- Skälighet-information.

I följande exempel kan du lära dig hur du använder Azure Machine Learning SDK för att implementera [data blad för modeller](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information finns i [ansvariga Innovations verktyg](/azure/architecture/guide/responsible-innovation/) för att lära dig mer om bästa praxis.
- Lär dig [mer om de här rikt](https://www.partnershiponai.org/about-ml/) linjerna för Machine Learning-system.