---
title: Vad är en arbetsyta
titleSuffix: Azure Machine Learning
description: Arbetsytan är resursen på den översta nivån för Azure Machine Learning. Den sparar en historik över alla träningskörningar, med loggar, mått, utdata och en ögonblicksbild av dina skript.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719069"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Vad är en Azure Machine Learning-arbetsyta?

Arbetsytan är den översta resursen för Azure Machine Learning, vilket ger en central plats att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning.  Arbetsytan innehåller en historik över alla träningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av dina skript. Du använder den här informationen för att avgöra vilken träningskörning som skapar den bästa modellen.  

När du har en modell som du gillar registrerar du den med arbetsytan. Sedan använder du den registrerade modellen och bedömningsskripten för att distribuera till Azure Container Instances, Azure Kubernetes Service eller till en fält programmerbar grindmatris (FPGA) som en REST-baserad HTTP-slutpunkt. Du kan också distribuera modellen till en Azure IoT Edge enhet som en modul.

## <a name="taxonomy"></a>Taxonomi 

En taxonomi för arbetsytan visas i följande diagram:

[![Taxonomi för arbetsytor](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagrammet visar följande komponenter i en arbetsyta:

+ En arbetsyta kan [innehålla Azure Machine Learning beräkningsinstanser](concept-compute-instance.md), molnresurser som konfigurerats med den Python-miljö som krävs för att köra Azure Machine Learning.

+ [Med användarroller](how-to-assign-roles.md) kan du dela din arbetsyta med andra användare, team eller projekt.
+ [Beräkningsmål](concept-azure-machine-learning-architecture.md#compute-targets) används för att köra experiment.
+ När du skapar arbetsytan [skapas även associerade](#resources) resurser åt dig.
+ [Experiment](concept-azure-machine-learning-architecture.md#experiments) är träningskörningar som du använder för att skapa dina modeller.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) är återanvändbara arbetsflöden för träning och omträning av din modell.
+ [Datauppsättningar](concept-azure-machine-learning-architecture.md#datasets-and-datastores) hjälper till med hanteringen av de data som du använder för modellträning och pipelineskapande.
+ När du har en modell som du vill distribuera skapar du en registrerad modell.
+ Använd den registrerade modellen och ett bedömningsskript för att skapa en [distributionsslutpunkt.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Verktyg för interaktion med arbetsytor

Du kan interagera med din arbetsyta på följande sätt:

> [!IMPORTANT]
> Verktyg som är markerade (förhandsversion) nedan är för närvarande i offentlig förhandsversion.
> Förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ På webben:
    + [Azure Machine Learning-studio ](https://ml.azure.com) 
    + [Azure Machine Learning Designer](concept-designer.md) 
+ I alla Python-miljöer med [Azure Machine Learning SDK för Python](/python/api/overview/azure/ml/intro).
+ I alla R-miljöer med [Azure Machine Learning SDK för R (förhandsversion).](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ På kommandoraden med hjälp av Azure Machine Learning [CLI-tillägget](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS Code-tillägg](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Maskininlärning med en arbetsyta

Maskininlärningsuppgifter läser och/eller skriver artefakter till din arbetsyta.

+ Kör ett experiment för att träna en modell – skriver experimentkörningsresultat till arbetsytan.
+ Använd automatiserad ML för att träna en modell – skriver träningsresultat till arbetsytan.
+ Registrera en modell på arbetsytan.
+ Distribuera en modell – använder den registrerade modellen för att skapa en distribution.
+ Skapa och kör återanvändbara arbetsflöden.
+ Visa maskininlärningsartefakter som experiment, pipelines, modeller och distributioner.
+ Spåra och övervaka modeller.

## <a name="workspace-management"></a>Arbetsytehantering

Du kan också utföra följande arbetsytehanteringsuppgifter:

| Arbetsytehanteringsaktivitet   | Portalen              | Studio | Python SDK/R SDK       | CLI        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Skapa en arbetsyta        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Hantera arbetsyteåtkomst    | **&check;**   || |  **&check;**    ||
| Skapa och hantera beräkningsresurser    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Skapa en virtuell notebook-dator |   | **&check;** | |     ||

> [!WARNING]
> Det finns inte Azure Machine Learning att flytta din arbetsyta till en annan prenumeration eller att flytta den egna prenumerationen till en ny klientorganisation. Detta kan orsaka fel.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Skapa en arbetsyta

Det finns flera sätt att skapa en arbetsyta:  

* Använd [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) för ett punkt-och-klicka-gränssnitt för att gå igenom varje steg.
* Använd Azure Machine Learning [SDK för Python för att](how-to-manage-workspace.md?tabs=python#create-a-workspace) skapa en arbetsyta direkt från Python-skript eller Jupyter Notebooks
* Använd en [Azure Resource Manager mall](how-to-create-workspace-template.md) eller [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) när du behöver automatisera eller anpassa skapandet med företagets säkerhetsstandarder.
* Om du arbetar i Visual Studio Code använder du [VS Code-tillägget](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> Arbetsytans namn är inte case-okänsligt.

## <a name="associated-resources"></a><a name="resources"></a> Associerade resurser

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

+ [Azure Storage:](https://azure.microsoft.com/services/storage/)Används som standarddatalager för arbetsytan.  Jupyter Notebooks som används med dina Azure Machine Learning-beräkningsinstanser lagras även här. 
  
  > [!IMPORTANT]
  > Som standard är lagringskontot ett v1-konto för generell användning. Du kan [uppgradera detta till generell användning v2](../storage/common/storage-account-upgrade.md) när arbetsytan har skapats. Aktivera inte hierarkisk namnrymd på lagringskontot efter uppgraderingen till generell användning v2.

  Om du vill använda ett Azure Storage-konto kan det inte vara av typen BlobStorage eller ett Premium-konto (Premium_LRS och Premium_GRS). Den kan inte heller ha ett hierarkiskt namnområde (används med Azure Data Lake Storage Gen2). Varken Premium Storage eller hierarkiska namnrymder stöds med _arbetsytans_ standardlagringskonto. Du kan använda premiumlagring eller hierarkisk namnrymd med _lagringskonton som inte_ är standard.
  
+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)Registrerar dockercontainrar som du använder under träning och när du distribuerar en modell. För att minimera kostnaderna är ACR **inläst tills** distributionsavbildningar har skapats.

+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)Lagrar övervakningsinformation om dina modeller.

+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)Lagrar hemligheter som används av beräkningsmål och annan känslig information som behövs för arbetsytan.

> [!NOTE]
> Du kan i stället använda befintliga Azure-resursinstanser när du skapar arbetsytan med [Python SDK,](how-to-manage-workspace.md?tabs=python#create-a-workspace) [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)eller Azure Machine Learning CLI med hjälp av [en ARM-mall.](how-to-create-workspace-template.md)

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Vad hände med Enterprise Edition?

Från och med september 2020 är alla funktioner som var tillgängliga i Enterprise Edition-arbetsytor nu också tillgängliga i Basic Edition-arbetsytor. Det går inte längre att skapa nya Enterprise-arbetsytor.  Alla SDK-, CLI Azure Resource Manager- eller Azure Resource Manager-anrop som använder parametern fortsätter att fungera, men `sku` en Basic-arbetsyta etableras.

Från och med 21 december Enterprise Edition alla arbetsytor automatiskt till Basic Edition, som har samma funktioner. Inga driftstopp inträffar under den här processen. Den 1 januari 2021 kommer Enterprise Edition att dras tillbaka formellt. 

I båda versionerna ansvarar kunderna för kostnaderna för förbrukade Azure-resurser och behöver inte betala några ytterligare avgifter för Azure Machine Learning. Mer information finns [Azure Machine Learning sidan](https://azure.microsoft.com/pricing/details/machine-learning/) med prisinformation.

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Machine Learning i:

+ [Azure Machine Learning översikt](overview-what-is-azure-ml.md)
+ [Skapa och hantera en arbetsyta](how-to-manage-workspace.md)
+ [Självstudie: Komma igång Azure Machine Learning i din utvecklingsmiljö](tutorial-1st-experiment-sdk-setup-local.md)
+ [Självstudie: Kom igång med att skapa ditt första ML-experiment på en beräkningsinstans](tutorial-1st-experiment-sdk-setup.md)
+ [Självstudie: Skapa din första klassificeringsmodell med automatiserad maskininlärning](tutorial-first-experiment-automated-ml.md) 
+ [Självstudie: Förutsäg ett bilpris med designern](tutorial-designer-automobile-price-train-score.md)
