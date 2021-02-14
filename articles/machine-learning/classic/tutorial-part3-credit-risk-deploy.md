---
title: 'ML Studio (klassisk) självstudie 3: Distribuera kredit risk modeller – Azure'
description: En detaljerad självstudie som visar hur du skapar en förutsägelse analys för kredit riskbedömning i Azure Machine Learning Studio (klassisk). Självstudien är del tre i en självstudieserie i tre delar. Den visar hur du distribuerar en modell som en webbtjänst.
keywords: kreditrisk, lösning för förutsägelseanalys, riskbedömning, distribuera, webbtjänst
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 68a5f6773543b922d024336556e4de24dd96fd33
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517390"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Självstudie 3: Distribuera kredit risk modell – Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

I den här självstudien tittar vi närmare på hur du utvecklar en lösning för förutsägelseanalys. Du utvecklar en enkel modell i Machine Learning Studio (klassisk).  Därefter distribuerar du modellen som en Azure Machine Learning-webbtjänst.  Den här distribuerade modellen kan göra förutsägelser med nya data. Den här självstudien är **del tre i en själv studie serie i tre delar**.

Anta att du behöver förutsäga kreditrisken för en person baserat på den information som han eller hon fyller i på en kreditansökan.  

Kreditriskbedömning är ett komplext problem, men den här självstudien kommer att förenkla processen. Du kommer att använda det som ett exempel på hur du kan skapa en förutsägelse analys lösning med hjälp av Microsoft Azure Machine Learning Studio (klassisk). Du använder Azure Machine Learning Studio (klassisk) och en Machine Learning webb tjänst för den här lösningen. 

I den här självstudien i tre delar börjar du med offentligt tillgängliga kreditriskdata.  Därefter utvecklar du och tränar en förutsägelsemodell.  Slutligen distribuerar du modellen som en webbtjänst.

I [del ett av självstudien](tutorial-part1-credit-risk.md)har du skapat en Machine Learning Studio (klassisk)-arbets yta, överfört data och skapat ett experiment.

I [del två av självstudien](tutorial-part2-credit-risk-train.md) fick du träna och utvärdera modeller.

I den här delen av självstudien ska du:

> [!div class="checklist"]
> * Förbereda för distribution
> * Distribuera webbtjänsten
> * Testa webbtjänsten
> * Hantera webbtjänsten
> * Få åtkomst till webbtjänsten

## <a name="prerequisites"></a>Förutsättningar

Slutför [del två av självstudien](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution
För att ge andra chansen att använda den förutsägande modell som du utvecklade i självstudien, måste du distribuera den som en webbtjänst i Azure.

Hittills har du experimenterat med att träna vår modell. Men den distribuerade tjänsten kommer inte längre att tränas – den kommer att generera nya förutsägelser genom bedömning av användarindata baserat på vår modell. Vi kommer att göra en förberedelse för att konvertera det här experimentet från en *-**utbildning**, och experimentera till ett _ *_förutsägande_** experiment. 

Att förbereda distributionen är en process i tre steg:  

1. Ta bort en av modellerna
1. Konvertera det *träningsexperiment* som du skapade till ett *förutsägelseexperiment*
1. Distribuera förutsägelseexperimentet som en webbtjänst

### <a name="remove-one-of-the-models"></a>Ta bort en av modellerna

Först behöver du minska experimentet lite. du har just nu två olika modeller i experimentet, men du ska bara använda en modell när du distribuerar det som en webbtjänst.  

Vi antar att du har bestämt dig för att trädmodellen presterade bättre än SVM-modellen. Så det första du ska göra är att ta bort modulen [Tvåklassig dator för vektorstöd][two-class-support-vector-machine] och de moduler som användes för att träna den. Du kan göra en kopia av experimentet först genom att klicka på **Spara som** längst ned på arbetsytan för experimentet.

du måste ta bort följande moduler:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine] (Tvåklassig dator för vektorstöd)
* Modulerna [Train Model][train-model] (Träningsmodell) och [Score Model][score-model] (Poängmodell) som var anslutna till den
* [Normalisera data][normalize-data] (båda två)
* [Evaluate Model][evaluate-model] (Utvärdera modellen) (eftersom vi är klara med modellutvärderingen)

Markera varje modul och tryck på Delete-tangenten, eller högerklicka på modulen och välj **Ta bort**. 

![Markerar vilka moduler som ska tas bort för att ta bort support Vector Machine Model](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Nu bör modellen se ut ungefär så här:

![Resulterande experiment när dator modellen för support Vector tas bort](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nu är vi redo att distribuera modellen med hjälp av [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Tvåklassigt förbättrat beslutsträd).

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägelseexperiment

För att förbereda den här modellen för distribution, måste du konvertera träningsexperimentet till ett förutsägelseexperiment. Detta omfattar tre steg:

1. Spara modellen som du har tränat och ersätt sedan träningsmodulerna
1. Minska storleken på experimentet genom att ta bort moduler som endast behövdes till träningen
1. Definiera var webbtjänsten ska ta emot indata och var den genererar utdata

du kan göra detta manuellt om du vill, men som tur är kan alla tre stegen utföras genom att klicka på **Set Up Web Service** (Konfigurera webbtjänst) längst ned på arbetsytan för experimentet (och välja alternativet **Predictive Web Service** (Förutsägelsewebbtjänst)).

> [!TIP]
> Om du vill ha mer information om vad som händer när du konverterar ett utbildnings experiment till ett förutsägelse experiment, se så [här förbereder du modellen för distribution i Azure Machine Learning Studio (klassisk)](deploy-a-machine-learning-web-service.md).

När du klickar på **Set Up Web Service** (Konfigurera webbtjänst) händer flera saker:

* Den tränade modellen konverteras till den enda modulen för **Trained Model** (Tränad modell) och lagras i modulpaletten till vänster om arbetsytan för experimentet (du hittar den under **Trained Models** (Tränade modeller))
* Moduler som har använts för träning tas bort, exempelvis:
  * [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree]
  * [Träningsmodell][train-model]
  * [Dela data][split]
  * den andra modulen [Kör R-skript][execute-r-script] som användes för testdata
* Den sparade tränade modellen har lagts tillbaka i experimentet
* Modulerna **Web service input** (Webbtjänstens indata) och **Web service output** (Webbtjänstens utdata) har lagts till (de identifierar var användarens data kommer in i modellen, vilka data som returneras och när webbtjänsten används)

> [!NOTE]
> Du kan se att experimentet sparas i två delar under flikar som lagts till överst i arbetsytan för experimentet. Det ursprungliga träningsexperimentet finns under fliken **Träningsexperiment** och det nyligen skapade förutsägelseexperiment finns i **Förutsägelseexperiment**. Du ska distribuera förutsägelseexperimentet som en webbtjänst.

du måste vidta ytterligare en åtgärd i det här experimentet.
du har lagt till två moduler för [Kör R-skript][execute-r-script] för att få en viktad funktion till datan. Det var bara något som användes i din träning och testning, så du kan ta bort dessa moduler i den slutliga modellen.
Machine Learning Studio (klassisk) tog bort en [execute R-skript][execute-r-script] -modul [när den togs][split] bort. Nu kan du ta bort den andra och ansluta [Metadata Editor][metadata-editor] direkt till [Score Model][score-model] (Poängmodell).    

Vårt experiment bör nu se ut så här:  

![Bedömning av den tränade modellen](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Du kanske undrar varför du lämnade kvar datamängden UCI German Credit Card Data i förutsägelseexperimentet. Tjänsten kommer att bedöma användarens data, inte den ursprungliga datauppsättningen, så varför ska vi ha kvar den ursprungliga datauppsättningen i modellen?
> 
> Det stämmer att tjänsten inte behöver den ursprungliga kreditkortsinformationen. Men den behöver schemat för dessa data, där det exempelvis finns information om hur många kolumner som finns och vilka kolumner som är numeriska. Den här schemainformationen är nödvändig för att vi ska kunna tolka användarens data. du kan låta dessa komponenter vara anslutna så att bedömningsmodulen har datamängdsschemat när tjänsten körs. Datan används inte, bara schemat.  
> 
>Observera att om din ursprungliga datamängd innehöll etiketten, kommer det förväntade schemat från webbindatan också förväntas innehålla en kolumn med etiketten! Du kan undvika problemet genom att ta bort etiketten och andra data som finns i datamängden för träning men som inte ska finnas i webbindatan, innan du kopplar webbindatan och datamängden för träning till en gemensam modul. 
> 

Kör experimentet en sista gången (klicka på **Kör**.) Om du vill kontrol lera att modellen fortfarande fungerar klickar du på utdata från modulen [Poäng modell][score-model] och väljer **Visa resultat**. Du kan se att ursprungliga data visas tillsammans med kreditriskvärdet (”Scored Labels” (Poängsatta etiketter)) och bedömningens sannolikhetsvärde (”Scored Probabilities” (Poängsatt sannolikhet).) 

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera experimentet som antingen en klassisk webbtjänst eller som en ny webbtjänst som baseras på Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuera som en klassisk webbtjänst
Om du vill distribuera en klassisk webbtjänst som härletts från vårt experiment, klickar du på **Distribuera webbtjänst** under arbetsytan och väljer **Distribuera webbtjänst [klassisk]**. Machine Learning Studio (klassisk) distribuerar experimentet som en webb tjänst och tar dig till instrument panelen för webb tjänsten. Från den här sidan kan du återgå till experimentet (**Visa ögonblicksbild** eller **Visa senaste**) och köra ett enkelt test av webbtjänsten (finns i **Testa webbtjänsten** nedan). Det finns även information här för att skapa program som har åtkomst till webbtjänsten (mer information finns i nästa steg i den här självstudien).

![Instrumentpanel för webbtjänsten](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Du kan konfigurera tjänsten genom att klicka på fliken **konfiguration** . Här kan du ändra tjänst namnet (det får namnet på experimentet som standard) och ge det en beskrivning. Du kan också ange fler egna etiketter för inkommande och utgående data.  

![Konfigurera webbtjänsten](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Distribuera som en ny webbtjänst

> [!NOTE] 
> Du måste ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten till när du distribuerar en ny webbtjänst. Mer information finns i [Hantera en webbtjänst med hjälp av portalen för Azure Machine Learning-webbtjänster](manage-new-webservice.md). 

Distribuera en ny webbtjänst från vårt experiment:

1. Klicka på **Distribuera webbtjänst** under arbetsytan och välj **Distribuera webbtjänst [nytt]**. Machine Learning Studio (klassisk) överför du till experiment sidan för att **distribuera** Azure Machine Learning webb tjänster.

1. Ange ett namn på webbtjänsten. 

1. I **Prisplan** kan du välja en befintlig prisplan eller ”Skapa ny”, namnge den nya planen och välja alternativet för en månatlig plan. Planen får som standard nivåerna för planer i din standardregion och din webbtjänst distribueras till den regionen.

1. Klicka på **Distribuera**.

Efter några minuter öppnas sidan **Snabbstart** för din webbtjänst.

Du kan konfigurera tjänsten genom att klicka på fliken **Konfigurera** . Här kan du ändra tjänst titeln och ge den en beskrivning. 

Om du vill testa webbtjänsten klickar du på fliken **Testa** (se **Testa webbtjänsten** nedan). Om du vill ha mer information om hur du skapar program med åtkomst till webbtjänsten, klickar du på fliken **Förbruka** (nästa steg i den här självstudien beskriver detta mer detaljerat).

> [!TIP]
> Du kan uppdatera webbtjänsten efter att du har distribuerat den. Om du till exempel vill ändra modell kan du redigera träningsexperimentet, justera modellparametrarna och klicka på **Distribuera webbtjänst**, välja **Distribuera webbtjänst [klassisk]** eller **Distribuera webbtjänst [nytt]**. När du distribuerar experimentet igen ersätts webbtjänsten med den uppdaterade modellen.  
> 
> 

## <a name="test-the-web-service"></a>Testa webbtjänsten

När webbtjänsten används kommer användarens data in via modulen **Webbtjänstens indata** där den skickas till modulen [Poängmodell][score-model] och poängsätts. På det sätt som du har konfigurerat förutsägelseexperimentet förväntar sig modellen data i samma format som den ursprungliga kreditriskdatamängden.
Resultaten returneras till användaren från webbtjänsten via modulen **Webbtjänstens utdata**.

> [!TIP]
> På det sätt som förutsägelseexperimentet har konfigurerats, returneras hela resultatet från modulen [Poängmodell][score-model]. Detta inkluderar alla indata plus kreditriskvärdet och bedömningen av sannolikheten. Men du kan returnera något annat om du vill – du kan till exempel returnera enbart kreditriskvärdet. Det gör du genom att infoga en modul för [Val av kolumner][select-columns] mellan [Poäng modell][score-model] och **webb tjänstens utdata** för att ta bort kolumner som du inte vill att webb tjänsten ska returnera. 
> 
> 

Du kan testa en klassisk webb tjänst antingen i **Machine Learning Studio (klassisk)** eller i **Azure Machine Learning Web Services-** portalen.
Du kan bara testa en ny webb tjänst i **Machine Learning Web Services-** portalen.

> [!TIP]
> När du testar i Azure Machine Learning-portalen för webbtjänster kan du låta portalen skapa exempeldata som du kan använda för att testa tjänsten med begäran–svar. På sidan **Konfigurera** väljer du ”Ja” i **Sample Data Enabled** (Aktivera exempeldata?). När du öppnar fliken Begäran–svar på sidan **Test**, fyller portalen i exempeldata som hämtas från den ursprungliga kreditriskdatamängden.

### <a name="test-a-classic-web-service"></a>Testa en klassisk webbtjänst

Du kan testa en klassisk webb tjänst i Machine Learning Studio (klassisk) eller i Machine Learning Web Services-portalen. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testa i Machine Learning Studio (klassisk)

1. På sidan **INSTRUMENTPANEL** för webbtjänsten klickar du på knappen **Test** under **Standardslutpunkt**. En dialogruta visas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kreditriskdatamängden.  

1. Ange en uppsättning data och klicka sedan på **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testa i portalen för Machine Learning-webbtjänster

1. På sidan **INSTRUMENTPANEL** i webbtjänsten klickar du på länken **Testa förhandsversion** under **Standardslutpunkt**. Testsidan i portalen för Azure Machine Learning-webbtjänster för webbtjänstens slutpunkt öppnas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kreditriskdatamängden.

2. Klicka på **Testa begäran–svar**. 

### <a name="test-a-new-web-service"></a>Testa en ny webbtjänst

Du kan enbart testa en ny webbtjänst i portalen för Machine Learning-webbtjänster.

1. I portalen för [Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) klickar du på **Test** överst på sidan. Sidan **Test** öppnas och du kan ange data för tjänsten. Indatafälten som visas motsvarar de kolumner som visades i den ursprungliga kreditriskdatamängden. 

1. Ange en uppsättning med data och klicka sedan på **Testa begäran–svar**.

Resultatet av testet visas till höger på sidan i utdatakolumnen. 


## <a name="manage-the-web-service"></a>Hantera webbtjänsten

När du har distribuerat din webbtjänst (klassisk eller ny) kan du hantera den från portalen för [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart).

Övervaka webbtjänstens prestanda:

1. Logga in på portalen för [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart)
1. Klicka på **Webbtjänster**
1. Klicka på din webbtjänst
1. Klicka på **Instrumentpanel**

## <a name="access-the-web-service"></a>Få åtkomst till webbtjänsten

Du har distribuerat en webbtjänst som använder din kreditriskförutsägelsemodell i föregående steg i den här självstudien. Användarna kan nu skicka data till den och ta emot resultaten. 

Webbtjänsten är en Azure-webbtjänst som kan ta emot och returnera data med hjälp av REST API:er på ett av följande två sätt:  

* **Begäran/svar** – Användaren skickar en eller flera rader med kreditdata till tjänsten med hjälp av ett HTTP-protokoll och tjänsten svarar med en eller flera resultatuppsättningar.
* **Batchkörning** – Användaren lagrar en eller flera rader med kreditdata i en Azure-blob och skickar sedan blobplatsen till tjänsten. Tjänsten bedömer alla rader med data i indatabloben, lagrar resultatet i en annan blob och returnerar URL:en för containern. 

> [!NOTE]
> Funktions kolumn namn i Studio (klassisk) är **SKIFT läges känsliga**. Kontrol lera att indata för att anropa webb tjänsten har samma kolumn namn som i träning-datauppsättningen.

Mer information om hur du kommer åt och använder webb tjänsten finns i [använda en Azure Machine Learning-webbtjänst med en mall för webbappar](./consume-web-services.md).



## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört dessa steg:

> [!div class="checklist"]
> * Förbereda för distribution
> * Distribuera webbtjänsten
> * Testa webbtjänsten
> * Hantera webbtjänsten
> * Få åtkomst till webbtjänsten

Du kan också skapa ett anpassat program som får åtkomst till webbtjänsten med hjälp av startkod som finns i programmeringsspråken R, C# och Python.

> [!div class="nextstepaction"]
> [Använda en Azure Machine Learning-webbtjänst](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset