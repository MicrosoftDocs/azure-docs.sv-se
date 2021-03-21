---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning-Rebuild-webbtjänst'
description: Återskapa Studio (klassiska) webb tjänster som pipeline-slutpunkter i Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565504"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Återskapa en Studio-webbtjänst (klassisk) i Azure Machine Learning

I den här artikeln får du lära dig att återskapa en Studio-webbtjänst (klassisk) som en **slut punkt** i Azure Machine Learning.

Använd Azure Machine Learning pipelines slut punkter för att göra förutsägelser, träna modeller eller köra en allmän pipeline. Med REST-slutpunkten kan du köra pipeliner från vilken plattform som helst. 

Den här artikeln är en del av Studio (klassisk) för att Azure Machine Learning migrations serien. Mer information om hur du migrerar till Azure Machine Learning finns i [översikts artikeln om migrering](migrate-overview.md).

> [!NOTE]
> Den här migrations serien fokuserar på Dra-och-släpp-designer. Mer information om hur du distribuerar modeller program mässigt finns i [distribuera Machine Learning-modeller i Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Azure Machine Learning-arbetsyta. [Skapa en Azure Machine Learning-arbetsyta](../how-to-manage-workspace.md#create-a-workspace).
- En pipeline för Azure Machine Learning utbildning. Mer information finns i avsnittet [återskapa ett Studio-experiment (klassisk) i Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Slut punkt för real tids vs vs pipelines

Studio (klassiska) webb tjänster har ersatts av **slut punkter** i Azure Machine Learning. Använd följande tabell för att välja vilken slut punkts typ som ska användas:

|Webb tjänsten Studio (klassisk)| Azure Machine Learning ersättning
|---|---|
|Begäran/svar-webb tjänst (real tids förutsägelse)|Slut punkt i real tid|
|Batch-webbtjänst (batch-förutsägelse)|Pipeline-slutpunkt|
|Utbilda webb tjänst (omträning)|Pipeline-slutpunkt| 


## <a name="deploy-a-real-time-endpoint"></a>Distribuera en slut punkt i real tid

I Studio (klassisk) använde du en **begäran/svar-webb tjänst** för att distribuera en modell för real tids förutsägelser. I Azure Machine Learning använder du en **slut punkt i real tid**.

Det finns flera sätt att distribuera en modell i Azure Machine Learning. Ett av de enklaste sätten är att använda designern för att automatisera distributions processen. Använd följande steg för att distribuera en modell som en slut punkt för Real tid:

1. Kör den slutförda utbildnings pipelinen minst en gång.
1. När körningen är klar väljer du **skapa en härlednings** pipeline för  >  **real tids** pipelinen längst upp på arbets ytan.

    ![Skapa pipeline för real tids härledning](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Designern konverterar inlärnings pipelinen till en härlednings pipeline i real tid. En liknande konvertering sker också i Studio (klassisk).

    I designern registrerar konverterings steget även [den tränade modellen till din Azure Machine Learning-arbetsyta](../how-to-deploy-and-where.md#registermodel).

1. Välj **Skicka** för att köra real tids härlednings pipelinen och kontrol lera att den körs utan problem.

1. När du har verifierat en härlednings pipeline väljer du **distribuera**.

1. Ange ett namn för din slut punkt och en beräknings typ.

    I följande tabell beskrivs dina beräknings alternativ för distributionen i designern:

    | Beräkningsmål | Används för | Beskrivning | Skapa |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Real tids härledning|Storskaliga produktions distributioner. Snabb svars tid och automatisk skalning av tjänster.| Skapad av användare. Mer information finns i [skapa beräknings mål](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Testning eller utveckling | Småskaliga, CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne.| Skapas automatiskt av Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testa real tids slut punkten

När distributionen är klar kan du se mer information och testa slut punkten:

1. Gå till fliken **slut punkter** .
1. Välj slut punkt.
1. Välj fliken **Test**.
    
    ![Skärm bild som visar fliken slut punkter med knappen Testa slut punkten](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publicera en pipeline-slutpunkt för batch-förutsägelse eller omträning

Du kan också använda din utbildnings pipeline för att skapa en **pipeline-slutpunkt** i stället för en slut punkt i real tid. Använd **pipeline-slutpunkter** för att utföra antingen batch-förutsägelse eller omträning.

Pipeline-slutpunkter ersätter Studio (klassisk) **batch-körningens slut punkter**  och **återtränar webb tjänster**.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publicera en pipeline-slutpunkt för batch förutsägelse

Att publicera en batch förutsägelse-slutpunkt liknar slut punkten i real tid.

Använd följande steg för att publicera en pipeline-slutpunkt för batch förutsägelse:

1. Kör den slutförda utbildnings pipelinen minst en gång.

1. När körningen är klar väljer du skapa en pipeline pipeline för att **skapa en härlednings pipeline**  >  .

    ![Skärm bild som visar knappen Skapa härlednings nivå för en utbildnings pipeline](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Designern konverterar inlärnings pipelinen till en pipeline för batch-härledning. En liknande konvertering sker också i Studio (klassisk).

    I designern registrerar det här steget även [den tränade modellen till din Azure Machine Learning-arbetsyta](../how-to-deploy-and-where.md#registermodel).

1. Välj **Skicka** för att köra pipeline för batch-härledning och kontrol lera att den har slutförts.

1. När du har verifierat en härlednings pipeline väljer du **publicera**.
 
1. Skapa en ny pipeline-slutpunkt eller Välj en befintlig.
    
    En ny pipeline-slutpunkt skapar en ny REST-slutpunkt för din pipeline. 

    Om du väljer en befintlig pipeline-slutpunkt skriver du inte över den befintliga pipelinen. I stället Azure Machine Learning versioner varje pipeline i slut punkten. Du kan ange vilken version som ska köras i REST-anropet. Du måste också ange en standard pipeline om REST-anropet inte anger en version.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publicera en pipeline-slutpunkt för omskolning

Om du vill publicera en pipeline-slutpunkt för omskolning måste du redan ha ett pipeline-utkast som tränar en modell. Mer information om hur du skapar en utbildnings pipelin finns i [återskapa ett Studio-experiment (klassisk)](migrate-rebuild-experiment.md).

Om du vill återanvända din pipeline-slutpunkt för omträning måste du skapa en **pipeline-parameter** för din data uppsättning. På så sätt kan du konfigurera din utbildnings data uppsättning dynamiskt, så att du kan träna modellen igen.

Använd följande steg för att publicera pipeline-slut punkt:

1. Kör din utbildnings pipeline minst en gång.
1. När körningen är klar väljer du modulen data uppsättning.
1. I informations fönstret modul väljer du **Ange som pipeline-parameter**.
1. Ange ett beskrivande namn som "InputDataset".    

    ![Skärm bild som visar hur du skapar en pipeline-parameter](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Detta skapar en pipeline-parameter för indata-datauppsättningen. När du anropar din pipeline-slutpunkt för utbildning kan du ange en ny data uppsättning för att träna om modellen.

1. Välj **Publicera**.

    ![Skärm bild som markerar knappen publicera i en utbildnings pipeline](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Anropa din pipeline-slutpunkt från Studio

När du har skapat en batch-härledning eller omutbildning av pipelinen för pipelinen kan du anropa slut punkten direkt från webbläsaren.

1. Gå till fliken **pipelines** och välj pipeline- **slutpunkter**.
1. Välj den pipeline-slutpunkt som du vill köra.
1. Välj **Skicka**.

    Du kan ange eventuella pipeline-parametrar när du har valt **Skicka**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du återskapar en Studio-webbtjänst (klassisk) i Azure Machine Learning. Nästa steg är att [integrera din webb tjänst med-klient program](migrate-rebuild-integrate-with-client-app.md).


Se de andra artiklarna i serien Studio (klassisk) migrering:

1. [Översikt över migrering](migrate-overview.md).
1. [Migrera data uppsättning](migrate-register-dataset.md).
1. [Återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).
1. **Återskapa en Studio-webbtjänst (klassisk)**.
1. [Integrera en Azure Machine Learning-webbtjänst med klient program](migrate-rebuild-integrate-with-client-app.md).
1. [Migrera kör R-skript](migrate-execute-r-script.md).
