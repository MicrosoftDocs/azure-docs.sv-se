---
title: 'ML Studio (klassisk): Optimera algoritmer – Azure'
description: Förklarar hur du väljer den optimala parameter uppsättningen för en algoritm i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: a216dc7cb17b9e35e412c6bebe34c0cccfb732e4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519311"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Välj parametrar för att optimera algoritmerna i Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

I det här avsnittet beskrivs hur du väljer rätt aktiv parameter för en algoritm i Azure Machine Learning Studio (klassisk). De flesta Machine Learning-algoritmer har parametrar att ställa in. När du tränar en modell måste du ange värden för dessa parametrar. Den tränade modellens effektivitet beror på de modell parametrar som du väljer. Processen för att hitta den optimala uppsättningen parametrar kallas *modell val*.



Det finns olika sätt att göra modell val. I Machine Learning är kors validering en av de mest använda metoderna för modell val och det är standard mekanismen för modell val i Azure Machine Learning Studio (klassisk). Eftersom Azure Machine Learning Studio (klassisk) stöder både R och python kan du alltid implementera egna metoder för modell val genom att använda R eller python.

Det finns fyra steg i processen att hitta den bästa parameter uppsättningen:

1. **Definiera parameter utrymmet**: för algoritmen ska du först bestämma de exakta parameter värden som du vill ta hänsyn till.
2. **Definiera kors validerings inställningarna**: Bestäm hur du vill välja kors validerings vikning för data uppsättningen.
3. **Definiera måttet**: Bestäm vilka mått som ska användas för att fastställa den bästa uppsättningen parametrar, till exempel precision, Rotors medelvärde, precision, återkallande eller f-poäng.
4. **Träna, utvärdera och jämför**: för varje unik kombination av parameter värden utförs kors validering av och baserat på det fel mått som du definierar. Efter utvärdering och jämförelse kan du välja den bästa modellen.

Följande bild illustrerar hur detta kan uppnås i Azure Machine Learning Studio (klassisk).

![Hitta den bästa parameter uppsättningen](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiera parameter utrymmet
Du kan definiera parametern som anges i steget modell initiering. Parameter fönstret för alla Machine Learning-algoritmer har två utbildare lägen: *enskild parameter* och *parameter intervall*. Välj parameter intervall läge. I parameter intervall läge kan du ange flera värden för varje parameter. Du kan ange kommaavgränsade värden i text rutan.

![Besluts träd med två klassers ökning, enkel parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativt kan du definiera de maximala och minsta punkterna i rutnätet och det totala antalet punkter som ska genereras med **use Range Builder**. Som standard genereras parameter värden på en linjär skala. Men om **logg skalan** är markerad genereras värdena i logg skalan (det vill säga förhållandet mellan intilliggande punkter är konstant i stället för deras skillnader). För heltals parametrar kan du definiera ett intervall med hjälp av ett bindestreck. Till exempel innebär "1-10" att alla heltal mellan 1 och 10 (båda) utgör parameter uppsättningen. Ett blandat läge stöds också. Parametern "1-10, 20, 50" skulle till exempel omfatta heltal som är 1-10, 20 och 50.

![Besluts träd med två klasser, parameter intervall](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiera kors validerings vikning
Modulen [partition och exempel][partition-and-sample] kan användas för att slumpmässigt tilldela vik data. I följande exempel konfiguration för modulen definierar vi fem vikningar och lägger slumpmässigt till ett vikt nummer i exempel instanserna.

![Partition och exempel](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiera måttet
Modulen för att [finjustera modellens standardparametrar][tune-model-hyperparameters] ger stöd för att välja den bästa uppsättningen parametrar för en specifik algoritm och data uppsättning. Förutom annan information om inlärning av modellen, innehåller **egenskaps** rutan för den här modulen måttet för att fastställa den bästa parameter uppsättningen. Den har två olika nedrullningsbara List rutor för klassificerings-och Regressions algoritmer. Om algoritmen som beaktas är en klassificerings algoritm ignoreras Regressions måttet och vice versa. I det här exemplet är måttet **noggrannhet**.   

![Svep parametrar](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Träna, utvärdera och jämför
Samma parameter för att [finjustera modellets][tune-model-hyperparameters] standardvärden är tågen alla modeller som motsvarar parameter uppsättningen, utvärderar olika mått och skapar sedan den bästa tränade modellen baserat på det mått som du väljer. Den här modulen har två obligatoriska indata:

* Den intränade eleven
* Data uppsättningen

Modulen har också en valfri data uppsättnings indata. Anslut data uppsättningen med viknings information till den obligatoriska data uppsättnings indata. Om data uppsättningen inte tilldelas någon vikt information körs en 10-vik kors validering automatiskt som standard. Om viknings tilldelningen inte görs och en verifierings data uppsättning tillhandahålls på den valfria data uppsättnings porten, väljs ett träna-test-läge och den första data uppsättningen används för att träna modellen för varje parameter kombination.

![Ökad klassificerare för besluts träd](./media/algorithm-parameters-optimize/fig6a.png)

Modellen utvärderas sedan i validerings data uppsättningen. Den vänstra utdataporten i modulen visar olika mått som funktioner för parameter värden. Den högra utdataporten ger den tränade modellen som motsvarar bästa möjliga modell enligt valt mått (**noggrannhet** i det här fallet).  

![Verifierings data uppsättning](./media/algorithm-parameters-optimize/fig6b.png)

Du kan se de exakta parametrarna som valts genom att visualisera rätt utgående port. Den här modellen kan användas i en bedömning av en test uppsättning eller i en fungerande webb tjänst efter att ha sparats som en utbildad modell.

<!-- Module References -->
[partition-and-sample]: /azure/machine-learning/studio-module-reference/partition-and-sample
[tune-model-hyperparameters]: /azure/machine-learning/studio-module-reference/tune-model-hyperparameters