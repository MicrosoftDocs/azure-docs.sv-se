---
title: Machine Learning-algoritm lathund Sheet-designer
titleSuffix: Azure Machine Learning
description: Ett Lathundt ark för att skriva ut Machine Learning algorithm hjälper dig att välja rätt algoritm för förutsägande modellen i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367064"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Lathund-bladet Machine Learning algorithm för Azure Machine Learning designer

I **lathund-bladet Azure Machine Learning algorithm** kan du välja rätt algoritm från designern för en förutsägelse analys modell.

Azure Machine Learning har ett stort bibliotek med algoritmer från ***klassificering** _, _*_rekommenderade system_*_, _*_klustring_*_, _*_avvikelse identifiering_*_, _*_regression_*_ och _ *_text analys_** familjer. Var och en är utformad för att lösa en annan typ av Machine Learning-problem.

Ytterligare vägledning finns i [så här väljer du algoritmer](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Ladda ned: Machine Learning algorithm lathund blad

**Ladda ned lathund-bladet här: [Machine Learning algorithm lathund Sheet (11 x 17 tum)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Lathund-ark för Machine Learning algorithm: Lär dig hur du väljer en Machine Learning algoritm.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Hämta och skriv ut Machine Learning algorithm lathund-bladet i tabloid-storlek för att hålla det praktiskt och få hjälp att välja en algoritm.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Så här använder du lathund-bladet Machine Learning algorithm

Förslagen som erbjuds i den här algoritmen lathund-blad är ungefärliga regler. Vissa kan vara böjda och vissa kan vara flagrantly överträdda. Det här lathund-bladet är avsett att föreslå en start punkt. Var inte rädd att köra en tävlings huvud mellan flera algoritmer på dina data. Det finns bara ingen ersättning för att förstå principerna för varje algoritm och det system som genererade dina data.

Alla Machine Learning-algoritmer har en egen typ eller induktiv kompensation. För ett särskilt problem kan flera algoritmer vara lämpliga och en algoritm kan passa bättre än andra. Men det är inte alltid möjligt att lära sig i förväg, vilket är den bästa anpassningen. I sådana fall visas flera algoritmer tillsammans i lathund-bladet. En lämplig strategi är att testa en algoritm, och om resultatet inte ännu är tillfredsställande kan du prova med de andra. 

Om du vill veta mer om algoritmerna i Azure Machine Learning designer går du till [algoritm-och modulreferens](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Typer av maskin inlärning

Det finns tre huvudsakliga kategorier av Machine Learning: *övervakad utbildning*, *inövervakad inlärning* och *förstärknings inlärning*.

### <a name="supervised-learning"></a>Övervakad utbildning

I övervakad utbildning är varje data punkt märkt eller associerad med en kategori eller ett värde av intresse. Ett exempel på en kategoriska-etikett tilldelar en avbildning som antingen "katt" eller "hund". Ett exempel på en värde etikett är försäljnings priset som är kopplat till en Använd bil. Målet med övervakad inlärning är att studera många märkta exempel som dessa och sedan för att kunna göra förutsägelser om framtida data punkter. Du kan till exempel identifiera nya foton med rätt djur eller tilldela korrekta försäljnings priser till andra begagnade bilar. Det här är en populär och användbar typ av maskin inlärning.

### <a name="unsupervised-learning"></a>Ej övervakad utbildning

I en obevakad inlärning har data punkter inga etiketter kopplade till sig. Målet för en ej övervakad utbildningskurs är i stället att organisera data på ett visst sätt eller för att beskriva dess struktur. Oövervakade inlärnings grupps data i kluster, som K-betyder, eller hittar olika sätt att titta på komplexa data så att det ser enklare ut.

### <a name="reinforcement-learning"></a>Kunskapsförmedling

I förstärknings inlärningen hämtar algoritmen för att välja en åtgärd som svar på varje data punkt. Det är en vanlig metod i Robotics, där uppsättningen sensor läsningar vid en tidpunkt är en data punkt och algoritmen måste välja nästa åtgärd för roboten. Det är också en naturlig anpassning för Sakernas Internet program. Learning-algoritmen får också en belönings signal en kort tid senare, vilket anger hur väl beslutet var. Med hjälp av den här signalen ändrar algoritmen strategin för att uppnå den högsta belöningen. 

## <a name="next-steps"></a>Nästa steg

* Se ytterligare vägledning om [hur du väljer algoritmer](how-to-select-algorithms.md)

* [Lär dig mer om Studio i Azure Machine Learning och Azure Portal](overview-what-is-azure-ml.md).

* [Självstudie: Bygg en förutsägelse modell i Azure Machine Learning designer](tutorial-designer-automobile-price-train-score.md).

* [Lär dig mer om djup inlärning eller Machine Learning](concept-deep-learning-vs-machine-learning.md).
