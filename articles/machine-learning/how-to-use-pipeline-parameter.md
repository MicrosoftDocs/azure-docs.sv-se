---
title: Använd pipeline-parametrar i designern för att bygga mångsidiga pipelines
titleSuffix: Azure Machine Learning
description: Använda pipeline-parametrar i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/19/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 09eabffb0e01ee6c5ea6b541378773a7d60397a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080455"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Använd pipeline-parametrar i designern för att bygga mångsidiga pipelines

Använd pipeline-parametrar för att bygga flexibla pipeliner i designern. Med pipeline-parametrar kan du dynamiskt ange värden vid körning för att kapsla in pipeline-logik och återanvända till gångar.

Pipeline-parametrar är särskilt användbara när du skickar en pipeline-körning, [omtränings modeller](how-to-retrain-designer.md)eller [utför batch-förutsägelser](how-to-run-batch-predictions-designer.md).

I den här artikeln får du lära dig hur du gör följande:

> [!div class="checklist"]
> * Skapa pipeline-parametrar
> * Ta bort och hantera pipeline-parametrar
> * Utlös ande pipelines körs när du justerar pipeline-parametrar

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* Om du vill ha en guidad introduktion till designern, fyller du i [design guiden](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Skapa pipeline-parameter

Det finns tre sätt att skapa en pipeline-parameter i designern:
- Skapa en pipeline-parameter i panelen Inställningar och bind den till en modul.
- Flytta upp en modul-parameter till en pipeline-parameter.
- Befordra en data uppsättning till en pipeline-parameter

> [!NOTE]
> Pipeline-parametrar stöder bara grundläggande data typer som `int` , `float` och `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Alternativ 1: skapa en pipeline-parameter i panelen Inställningar

I det här avsnittet skapar du en pipeline-parameter i panelen Inställningar.

I det här exemplet skapar du en pipeline-parameter som definierar hur en pipeline fyller i data som saknas med hjälp av modulen **Rensa data som saknas** .

1. Bredvid namnet på ditt pipeline-utkast väljer du **kugg hjuls ikonen** för att öppna panelen **Inställningar** .

1. I avsnittet **pipeline-parametrar** väljer du **+** ikonen.

1.  Ange ett namn för parametern och ett standardvärde. 

    Ange till exempel `replace-missing-value` parameter namn och `0` som standardvärde.

   ![Skärm bild som visar hur du skapar en pipeline-parameter](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


När du har skapat en pipeline-parameter måste du [koppla den till den modul-parameter](#attach-module-parameter-to-pipeline-parameter) som du vill ange dynamiskt.

### <a name="option-2-promote-a-module-parameter"></a>Alternativ 2: Höj en modul-parameter

Det enklaste sättet att skapa en pipeline-parameter för ett modul-värde är att flytta en modul-parameter. Använd följande steg för att uppgradera en modul-parameter till en pipeline-parameter:

1. Välj den modul som du vill koppla en pipeline-parameter till.
1. Mouseover den parameter som du vill ange i informations fönstret modul.
1. Välj de ellipser (**...**) som visas.
1. Välj **Lägg till i pipeline-parameter**.

    ![Skärm bild som visar hur du höjer en modul-parameter till pipelinen Parameter1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Ange ett parameter namn och standardvärde.
1. Välj **Spara**

Nu kan du ange nya värden för den här parametern när du skickar den här pipelinen.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Alternativ 3: befordra en data uppsättning till en pipeline-parameter

Om du vill skicka in din pipeline med variabla data uppsättningar måste du befordra din data uppsättning till en pipeline-parameter:

1. Välj den data uppsättning som du vill omvandla till en pipeline-parameter.

1. I informations panelen i data uppsättningen markerar du **Ange som pipeline-parameter**.

   ![Skärm bild som visar hur du anger data uppsättning som pipeline-parameter](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Nu kan du ange en annan data uppsättning genom att använda pipeline-parametern nästa gång du kör pipelinen.

## <a name="attach-module-parameter-to-pipeline-parameter"></a>Koppla modul-parameter till pipeline-parameter 

I det här avsnittet får du lära dig hur du kopplar en modul parameter till en pipeline-parameter.

Du kan koppla samma modul parametrar för duplicerade moduler till samma pipeline-parameter om du vill ändra värdet vid en tidpunkt när du aktiverar pipelinen.

I följande exempel finns en dubblett av en modul som **saknar rensade data** . För varje **rensad data** -modul som saknas, bifoga **ersättnings värde** till pipeline **-parameter Ersätt-saknas-värde**:

1. Välj modulen **Rensa data som saknas** .

1. I informations fönstret modul, till höger om arbets ytan, ställer du in **rengörings läget** på "anpassat ersättnings värde".

1. Mouseover fältet **ersättnings värde** .

1. Välj de ellipser (**...**) som visas.

1. Välj pipeline-parameter `replace-missing-value` .

   ![Skärm bild som visar hur du kopplar en pipeline-parameter](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Du har kopplat fältet **ersättnings värde** till din pipeline-parameter. **Ersättnings värdet** i modulerna är inte åtgärds bara.

 ![Skärm bild som visar att det inte går att utföra åtgärder efter koppling till pipeline-parametern](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)


## <a name="update-and-delete-pipeline-parameters"></a>Uppdatera och ta bort pipeline-parametrar

I det här avsnittet får du lära dig hur du uppdaterar och tar bort pipeline-parametrar.

### <a name="update-pipeline-parameters"></a>Uppdatera pipeline-parametrar

Använd följande steg för att uppdatera en modul pipeline-parameter:

1. Välj kugg hjuls ikonen längst upp på arbets ytan.
1. I avsnittet **pipeline-parametrar** kan du Visa och uppdatera namnet och standardvärdet för alla din pipeline-parameter.

### <a name="delete-a-dataset-pipeline-parameter"></a>Ta bort en pipeline-parameter för data uppsättning

Använd följande steg för att koppla från en pipeline-parameter för data uppsättning:

1. Välj data uppsättnings modulen.
1. Avmarkera alternativ **uppsättningen som pipeline-parameter**.


### <a name="delete-module-pipeline-parameters"></a>Ta bort pipeline-parametrar

Använd följande steg för att ta bort en modul pipeline-parameter:

1. Välj kugg hjuls ikonen längst upp på arbets ytan.

1. Välj ellipserna (**...**) bredvid pipeline-parametern.

    I den här vyn visas vilka moduler som pipeline-parametern är kopplad till. Om du vill ta bort en pipeline-parameter måste du först koppla från den från alla modul parametrar.

    ![Skärm bild som visar aktuell pipeline-parameter som tillämpas på en modul](media/how-to-use-pipeline-parameter/current-pipeline-parameter.png)

1. På arbets ytan väljer du en modul som pipelin parametern fortfarande är kopplad till.
1. I fönstret modul egenskaper till höger letar du reda på det fält som pipeline-parametern är kopplad till.
1. Mouseover det bifogade fältet. Välj sedan de ellipser (**...**) som visas.
1. Välj **Koppla från pipeline-parameter**

    ![Skärm bild som visar frånkopplade från pipeline-parametrar](media/how-to-use-pipeline-parameter/detach-from-pipeline-parameter.png)

1. Upprepa föregående steg tills du har kopplat bort pipeline-parametern från alla fält.
1. Välj ellipserna (**...**) bredvid pipeline-parametern.
1. Välj **ta bort parameter** för att ta bort pipeline-parametern.

    ![Skärm bild som visar borttagning av pipeline-parametrar](media/how-to-use-pipeline-parameter/delete-pipeline-parameter.png)

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Utlös en pipeline-körning med pipeline-parametrar 

I det här avsnittet får du lära dig hur du skickar en pipeline-körning samtidigt som du ställer in pipeline-parametrar.

### <a name="resubmit-a-pipeline-run"></a>Skicka en pipeline-körning på nytt

När du har skickat en pipeline med pipeline-parametrar kan du skicka en pipeline-körning på nytt med olika parametrar:

1. Gå till sidan pipeline-information. I fönstret **pipeline körnings översikt** kan du kontrol lera aktuella pipeline-parametrar och-värden.

1. Välj **Skicka igen**.
1. I **installations förloppet kör** anger du dina nya pipeline-parametrar. 

![Skärm bild som visar överförings pipelinen med pipeline-parametrar](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Använda publicerade pipelines

Du kan också publicera en pipeline för att använda dess pipeline-parametrar. En **publicerad pipeline** är en pipeline som har distribuerats till en beräknings resurs, som klient program kan anropa via en REST-slutpunkt.

Publicerade slut punkter är särskilt användbara för omskolnings-och batch förutsägelse-scenarier. Mer information finns i [så här tränar du om modeller i designern](how-to-retrain-designer.md) eller [kör batch-förutsägelser i designern](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar pipeline-parametrar i designern. Sedan kan du se hur du kan använda pipeline-parametrar för att [omträna modeller](how-to-retrain-designer.md) eller utföra [batch-förutsägelser](how-to-run-batch-predictions-designer.md).

Du kan också lära dig hur du [använder pipelines program mässigt med SDK: n](how-to-deploy-pipelines.md).
