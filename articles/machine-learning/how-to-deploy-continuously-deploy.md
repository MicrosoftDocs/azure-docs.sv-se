---
title: Distribuera Azure Machine Learning modeller kontinuerligt
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar modeller kontinuerligt med Azure Machine Learning DevOps-tillägget. Sök automatiskt efter och distribuera nya modell versioner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609421"
---
# <a name="continuously-deploy-models"></a>Distribuera modeller kontinuerligt

Den här artikeln visar hur du använder kontinuerlig distribution i Azure DevOps för att automatiskt söka efter nya versioner av registrerade modeller och skicka dessa nya modeller till produktion.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har registrerat en modell i din Azure Machine Learning-arbetsyta. I [den här självstudien](how-to-train-scikit-learn.md) får du ett exempel på hur du tränar och registrerar en scikit-modell.

## <a name="continuously-deploy-models"></a>Distribuera modeller kontinuerligt

Du kan distribuera modeller kontinuerligt med Machine Learning-tillägget för [Azure-DevOps](https://azure.microsoft.com/services/devops/). Du kan använda Machine Learning-tillägget för Azure-DevOps för att utlösa en distributions pipeline när en ny maskin inlärnings modell registreras i en Azure Machine Learning arbets yta.

1. Registrera dig för [Azure-pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up), som möjliggör kontinuerlig integrering och leverans av ditt program till vilken plattform eller moln som möjligt. (Observera att Azure-pipeliner inte är samma som [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Skapa ett Azure DevOps-projekt.](/azure/devops/organizations/projects/create-project)

1. Installera [Machine Learning-tillägget för Azure-pipeliner](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Använd tjänst anslutningar för att konfigurera en tjänstens huvud namns anslutning till din Azure Machine Learning-arbetsyta så att du kan komma åt dina artefakter. Gå till projekt inställningar, Välj **tjänst anslutningar** och välj sedan **Azure Resource Manager**:

    [![Välj Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. I listan **omfattnings nivå** väljer du **AzureMLWorkspace** och anger sedan resten av värdena:

    ![Välj AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Om du vill distribuera din Machine Learning-modell kontinuerligt med hjälp av Azure-pipelines väljer du **version** under pipelines. Lägg till en ny artefakt och välj sedan den **azureml modell** artefakt och den tjänst anslutning som du skapade tidigare. Välj modell och version för att utlösa en distribution:

    [![Välj AzureML-modell](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Aktivera modell utlösaren i modell artefakten. När du aktiverar utlösaren utlöses en pipeline för Azure DevOps release varje gång den angivna versionen (det vill säga den senaste versionen) av modellen registreras i din arbets yta.

    [![Aktivera modell utlösaren](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Nästa steg

Se nedanstående projekt på GitHub för fler exempel på kontinuerlig distribution för ML-modeller.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)