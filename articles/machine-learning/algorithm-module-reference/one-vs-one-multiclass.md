---
title: En-vs-en multiklass
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen en-vs-en multiklass i Azure Machine Learning för att skapa en klassificerings modell med flera klasser från en ensemble av binära klassificerings modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592914"
---
# <a name="one-vs-one-multiclass"></a>En-vs-en multiklass

Den här artikeln beskriver hur du använder modulen en-vs-en multiklass i Azure Machine Learning designer. Målet är att skapa en klassificerings modell som kan förutsäga flera klasser genom att använda *en-till-en-* metod.

Den här modulen är användbar för att skapa modeller som förutsäger tre eller fler möjliga resultat, när resultatet beror på kontinuerliga eller kategoriska för förutsägande variabler. Med den här metoden kan du också använda binära klassificerings metoder för problem som kräver flera utgående klasser.

### <a name="more-about-one-versus-one-models"></a>Mer om en-kontra-en-modell

Vissa klassificerings algoritmer tillåter användning av fler än två klasser efter design. Andra begränsar möjliga resultat till ett av två värden (en binär eller en modell med två klasser). Men även binära klassificerings algoritmer kan anpassas för klassificerings uppgifter med flera klasser via en mängd olika strategier. 

Den här modulen implementerar en-till-en-metod där en binär modell skapas per klass par. Vid förutsägelsen väljs klassen som tog emot de mest rösterna. Eftersom den kräver för att passa `n_classes * (n_classes - 1) / 2` klassificerare är den här metoden vanligt vis långsammare än en-till-alla, på grund av dess O (n_classes ^ 2) komplexitet. Den här metoden kan dock vara fördelaktig för algoritmer som kernel-algoritmer som inte skalar bra med `n_samples` . Detta beror på att varje enskilt inlärnings problem bara omfattar en liten delmängd av data, med en-och-alla, används gånger för hela data uppsättningen `n_classes` .

I grunden skapar modulen en ensemble med enskilda modeller och sammanfogar sedan resultaten för att skapa en enskild modell som förutsäger alla klasser. En binär klassificerare kan användas som grund för en en-till-en-modell.  

Anta till exempel att du konfigurerar en [dubbelriktad Vector Machine](two-class-support-vector-machine.md) -modell och anger den som indatamängd i modulen en-vs-en multiklass. Modulen skapar dubbelriktade stöd för vektor dator modeller för alla medlemmar i klassen output. Den använder sedan en-och-en-metod för att kombinera resultaten för alla klasser.  

Modulen använder OneVsOneClassifier av sklearn och du kan lära dig mer om det [här](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Så här konfigurerar du en-vs-en multiklass-klassificerare  

Den här modulen skapar en ensemble med binära klassificerings modeller för att analysera flera klasser. Om du vill använda den här modulen måste du först konfigurera och träna en *binär klassificerings* modell. 

Du ansluter den binära modellen till en modul med flera klasser. Sedan tränas ensemblen för modeller med hjälp av [träna modell](train-model.md) med en etikettad inlärnings data uppsättning.

När du kombinerar modeller skapar en-vs-en-multiklass flera binära klassificerings modeller, optimerar algoritmen för varje klass och sammanfogar sedan modellerna. Modulen utför dessa uppgifter även om träning-datauppsättningen kan ha flera klass värden.

1. Lägg till modulen en-vs-en multiklass i din pipeline i designern. Du hittar den här modulen under **Machine Learning-Initialize** i **klassificerings** kategorin.

   En-vs-en-klassificeraren i flera klasser har inga parametrar som kan konfigureras. Alla anpassningar måste göras i den binära klassificerings modellen som anges som indata.

2. Lägg till en binär klassificerings modell i pipelinen och konfigurera modellen. Du kan till exempel använda [två klass stöd för en Vector-dator](two-class-support-vector-machine.md) eller ett [utökat besluts träd med två klasser](two-class-boosted-decision-tree.md).

3. Lägg till modulen [träna modell](train-model.md) i din pipeline. Anslut den ej tränade klassificeraren som är resultatet av en-vs-en multiklass.

4. På den andra ingången av [träna modell](train-model.md)ansluter du en etikettad tränings uppsättning som har flera klass värden.

5. Skicka pipelinen.

## <a name="results"></a>Resultat

När utbildningen är klar kan du använda modellen för att göra förutsägelser i multiklass.

Alternativt kan du skicka den ej tränade klassificeraren till [kors](cross-validate-model.md) validerings modellen för kors validering mot en etikettad verifierings data uppsättning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
