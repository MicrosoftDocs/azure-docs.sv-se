---
title: 'PCA-Based avvikelse identifiering: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen PCA-Based avvikelse identifiering för att skapa en avvikelse identifierings modell baserad på huvud komponent analys (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898466"
---
# <a name="pca-based-anomaly-detection-module"></a>Modul för PCA-Based avvikelse identifiering

Den här artikeln beskriver hur du använder modulen PCA-Based avvikelse identifiering i Azure Machine Learning designer för att skapa en modell för avvikelse identifiering baserat på Principal Component Analysis (PCA).

Med den här modulen kan du skapa en modell i scenarier där det är enkelt att hämta tränings data från en klass, till exempel giltiga transaktioner, men svårt att få tillräckligt med exempel på mål avvikelser. 

För att till exempel identifiera bedrägliga transaktioner har du ofta inte tillräckligt många exempel på bedrägerier att träna på. Men du kan ha många exempel på användbara transaktioner. Modulen PCA-Based avvikelse identifiering löser problemet genom att analysera tillgängliga funktioner för att fastställa vad som utgör en "normal"-klass. Modulen använder sedan avstånds mått för att identifiera fall som representerar avvikelser. Med den här metoden kan du träna en modell med hjälp av befintliga obalanserade data.

## <a name="more-about-principal-component-analysis"></a>Mer om analys av huvud objekts komponenter

PCA är en etablerad teknik i Machine Learning. Det används ofta i analys av analys av data, eftersom det visar den inre strukturen för data och förklarar var Ian sen i data.

PCA fungerar genom att analysera data som innehåller flera variabler. Det söker efter korrelationer mellan variablerna och avgör den kombination av värden som bäst samlar in skillnader i resultat. Dessa kombinerade funktions värden används för att skapa ett mer kompakt funktions utrymme som kallas *huvud komponenter*.

Vid avvikelse identifiering analyseras varje ny Indatatyp. Algoritmen för avvikelse identifiering beräknar projektionen på eigenvectors, tillsammans med ett normaliserat återuppbyggnads fel. Det normaliserade felet används som avvikelse poäng. Ju högre fel, desto mer avvikande är instansen.

Mer information om hur PCA fungerar och om implementeringen av avvikelse identifiering finns i följande dokument:

- [En slumpmässig algoritm för huvud komponent analys](https://arxiv.org/abs/0809.2274), av Rokhlin, Szlan och Tygert

- [Hitta strukturen med slumpmässig het: Probabilistic-algoritmer för att konstruera ungefärlig mat ris dekompositioner](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF-nedladdning), av Halko, Martinsson och Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Så här konfigurerar du PCA-Based avvikelse identifiering

1. Lägg till modulen **PCA-baserad avvikelse identifiering** i din pipeline i designern. Du hittar den här modulen i kategorin för **avvikelse identifiering** .

2. I den högra panelen i modulen väljer du alternativet **tränings läge** . Ange om du vill träna modellen med hjälp av en speciell uppsättning parametrar eller Använd en parameter svep för att hitta de bästa parametrarna.

    Om du vet hur du vill konfigurera modellen väljer du alternativet **enskild parameter** och anger en viss uppsättning värden som argument.

3. För **antalet komponenter som ska användas i PCA** anger du antalet utgångs funktioner eller komponenter som du vill använda.

    Beslutet om hur många komponenter som ska ingå är en viktig del av experiment design som använder PCA. Allmän vägledning är att du inte bör inkludera samma antal PCA-komponenter eftersom det finns variabler. I stället bör du börja med ett mindre antal komponenter och öka antalet komponenter tills villkoret är uppfyllt.

    Det bästa resultatet erhålls när antalet utgående komponenter är *mindre än* antalet funktions kolumner som är tillgängliga i data uppsättningen.

4. Ange mängden översampling som ska utföras under slumpmässig PCA-utbildning. Vid problem med avvikelse identifiering gör disbalanserade data det svårt att tillämpa standard PCA-tekniker. Genom att ange en mängd översampling kan du öka antalet mål instanser.

    Om du anger **1** utförs ingen översampling. Om du anger ett värde som är högre än **1** genereras ytterligare exempel för användning i träna modellen.

    Det finns två alternativ, beroende på om du använder en parameter Svep eller inte:

    - **Översamplings parameter för slumpmässig PCA**: Skriv ett enda heltal som representerar förhållandet för översampling av klassen minoritet över klassen normal. (Det här alternativet är tillgängligt när du använder övnings metoden för **en parameter** .)

    > [!NOTE]
    > Du kan inte visa den översamplade data uppsättningen. Mer information om hur översampling används med PCA finns i [teknisk information](#technical-notes).

5. Välj alternativet **Aktivera indatamängds funktion medelvärde** för att normalisera alla inmatade funktioner till ett medelvärde på noll. Normalisering eller skalning till noll rekommenderas vanligt vis för PCA, eftersom syftet med PCA är att maximera variansen mellan variabler.

    Det här alternativet är markerat som standard. Avmarkera det om värdena redan har normaliserats genom en annan metod eller skala.

6. Anslut en data uppsättning för Taggad utbildning och en av inlärnings modulerna.

   Om du ställer in alternativet **skapa utbildare läge** för **en enskild parameter** använder du modulen [träna avvikelse identifiering modell](train-anomaly-detection-model.md) .

7. Skicka pipelinen.

## <a name="results"></a>Resultat

När inlärningen är klar kan du spara den tränade modellen. Eller så kan du ansluta den till modulen [Poäng modell](score-model.md) för att förutsäga avvikande poäng.

Utvärdera resultatet av en modell för avvikelse identifiering:

1. Se till att en poäng kolumn är tillgänglig i båda data uppsättningarna.

    Om du försöker utvärdera en modell för avvikelse identifiering och får felet "det finns ingen Poäng kolumn i den returnerade data uppsättningen som ska jämföras", använder du en typisk utvärderings uppsättning som innehåller en etikett kolumn men inga sannolikhets poäng. Välj en data mängd som matchar schemats utdata för avvikelse identifierings modeller, som innehåller **Poäng etiketter** och returnerade **sannolikhets** kolumner.

2. Se till att etikett kolumner är markerade.

    Ibland tas de metadata som är kopplade till etikett-kolumnen bort i pipeline-diagrammet. Om det händer kan du, när du använder modulen [utvärdera modell](evaluate-model.md) för att jämföra resultaten av två avvikelse identifierings modeller, få felet "det finns ingen etikett kolumn i den returnerade data uppsättningen". Eller så kanske du får felet "det finns ingen etikett kolumn i den returnerade data uppsättningen som ska jämföras".

    Du kan undvika de här felen genom att lägga till modulen [Redigera metadata](edit-metadata.md) innan du kan [utvärdera modell](evaluate-model.md) -modulen. Använd kolumn väljaren för att välja klass kolumnen och välj sedan **etikett** i listan **fält** .

3. Använd modulen [Kör Python-skript](execute-python-script.md) för att justera etikett kolumn kategorier som **1 (positiv, normal)** och **0 (negativt, onormalt)**.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Tekniska anteckningar

Den här algoritmen använder PCA för att approximera det under utrymme som innehåller den normala klassen. Under utrymmet sträcks av eigenvectors som är associerat med den översta Eigenvalues i matrisen för data kovarians. 

För varje ny indata beräknar avvikelse detektorn först sin projektion i eigenvectors och beräknar sedan det normaliserade återuppbyggnads felet. Det här felet är avvikelse poängen. Ju högre fel desto mer avviker instansen. Mer information om hur det normala utrymmet beräknas finns i Wikipedia: [huvud komponent analys](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

Se [undantag och felkoder för designern](designer-error-codes.md) för en lista med fel som är speciella för designer-modulerna.