---
title: 'Träna modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen **träna modell** i Azure Machine Learning för att träna en klassificerings-eller Regressions modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 41b327e793c6b35a2b2aeae825c493a484d84fb4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562774"
---
# <a name="train-model-module"></a>Träna modell modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att träna en klassificerings-eller Regressions modell. Träningen sker efter att du har definierat en modell och angett dess parametrar och kräver taggade data. Du kan också använda **träna modell** för att omträna en befintlig modell med nya data. 

## <a name="how-the-training-process-works"></a>Så här fungerar inlärnings processen

I Azure Machine Learning är det vanligt vis en tre stegs process att skapa och använda en maskin inlärnings modell. 

1. Du konfigurerar en modell genom att välja en viss typ av algoritm och definiera dess parametrar eller dess egenskaper. Välj någon av följande modell typer: 

    + **Klassificerings** modeller, baserade på neurala nätverk, besluts träd och besluts skogar och andra algoritmer.
    + **Regressions** modeller, som kan innehålla standard linjär regression eller som använder andra algoritmer, inklusive neurala-nätverk och Bayesian regression.  

2. Ange en data mängd som är märkt och har data som är kompatibla med algoritmen. Anslut både data och modellen för att **träna modellen**.

    Vilken utbildning som skapas är ett speciellt binärformat, iLearner, som kapslar in de statistiska mönster som har lärts från data. Du kan inte ändra eller läsa det här formatet direkt. andra moduler kan dock använda den här utbildade modellen. 
    
    Du kan också Visa modellens egenskaper. Mer information finns i avsnittet resultat.

3. När utbildningen har slutförts använder du den tränade modellen med en av [poängsättnings-modulerna](./score-model.md)för att göra förutsägelser på nya data.

## <a name="how-to-use-train-model"></a>Använda träna modell 
    
1. Lägg till modulen **träna modell** i pipelinen.  Du hittar den här modulen under kategorin **Machine Learning** . Expandera **träna** och dra modulen **träna modell** till din pipeline.
  
1.  Koppla det nedtränade läget till vänster. Koppla data uppsättningen utbildning till den högra indatan för **träna modell**.

    Data uppsättningen för träning måste innehålla en etikett kolumn. Alla rader utan etiketter ignoreras.
  
1.  För **kolumnen etikett** klickar du på **Redigera kolumn** i den högra panelen i modulen och väljer en enda kolumn som innehåller resultat som modellen kan använda för utbildning.
  
    - För klassificerings problem måste etikett kolumnen innehålla antingen **kategoriska** -värden eller **diskreta** värden. Några exempel kan vara ja/nej-klassificering, en klassificerings kod, ett namn på en sjukdom eller en inkomst grupp.  Om du väljer en noncategorical-kolumn kommer modulen att returnera ett fel under träningen.
  
    -   För Regressions problem måste etikett kolumnen innehålla **numeriska** data som representerar variabeln Response. Det bästa är att de numeriska data representerar en kontinuerlig skala. 
    
    Exempel kan vara ett kredit risk poäng, planerat tid för att Miss lyckas med en hård disk eller det prognostiserade antalet anrop till ett Call Center på en bestämd dag eller tid.  Om du inte väljer en numerisk kolumn kan du få ett fel meddelande.
  
    -   Om du inte anger vilken etikett kolumn som ska användas försöker Azure Machine Learning att härleda som är lämplig etikett kolumn med hjälp av data uppsättningens metadata. Om du väljer fel kolumn använder du kolumn väljaren för att korrigera den.
  
    > [!TIP] 
    > Om du har problem med att använda kolumn Väljaren kan du läsa mer i artikeln [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) . I den här artikeln beskrivs några vanliga scenarier och tips för att använda alternativen **med regler** och **efter namn** .
  
1.  Skicka pipelinen. Om du har stora mängder data kan det ta en stund.

    > [!IMPORTANT] 
    > Om du har en ID-kolumn som är ID för varje rad, eller en text kolumn som innehåller för många unika värden, kan **träna modell** få ett fel meddelande som "antalet unika värden i kolumnen: {column_name} är större än tillåtet.
    >
    > Detta beror på att kolumnen träffar tröskelvärdet för unika värden och kan orsaka slut på minne. Du kan använda [Redigera metadata](edit-metadata.md) för att markera kolumnen som **rensad funktion** och den används inte i utbildningen eller [extrahera N-gram-funktioner från text-modulen](extract-n-gram-features-from-text.md) för att Förbearbeta text kolumnen. Mer fel information finns i [fel koden för designer](././designer-error-codes.md) .

## <a name="model-interpretability"></a>Modell tolkning

Modell tolkningen ger möjlighet att förstå ML-modellen och presentera den underliggande grunden för besluts fattande på ett sätt som kan förstås av människa.

Modulen **modell modell** stöder för närvarande stöd [för att använda tolknings paket för att förklara ml-modeller](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs). Följande inbyggda algoritmer stöds:

- Linjär regression
- Regression för Neural Network
- Logistic Regression med två klasser
- Tvåklassig dator för vektorstöd
- Besluts skog med flera klasser

Om du vill generera modell förklaringar kan du välja **Sant** i list rutan med **modell förklaring** i träna modell-modul. Som standard är den inställd på falskt i modulen **träna modell** . Observera att generering av förklaringar kräver extra beräknings kostnader.

![Skärm bild som visar kryss rutan modell förklaring](./media/module/train-model-explanation-checkbox.png)

När pipeline-körningen är klar kan du besöka **förklaringar** i den högra rutan i modulen **träna modell** och utforska modell prestanda, data uppsättning och funktions betydelse.

![Skärm bild som visar modell förklarings diagram](./media/module/train-model-explanations-tab.gif)

Mer information om hur du använder modell förklaringar i Azure Machine Learning finns i instruktions artikeln om att [tolka ml-modeller](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Resultat

När modellen har tränats:


+ Om du vill använda modellen i andra pipeliner väljer du modulen och väljer ikonen **registrera data uppsättning** under fliken **utdata** i den högra panelen. Du kan komma åt sparade modeller i modulen modul under **data uppsättningar**.

+ Om du vill använda modellen för att förutsäga nya värden ansluter du den till modulen [Poäng modell](./score-model.md) tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.