---
title: 'Självstudie: skapa en förutsägelse modell genom att använda automatisk ML (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar automatiserade maskin inlärnings modeller så att du kan använda den bästa modellen för att förutsäga resultat i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814813"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Självstudie: Power BI integration – skapa en förutsägelse modell med hjälp av automatisk maskin inlärning (del 1 av 2)

I del 1 av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell. Du använder automatisk Machine Learning (ML) i Azure Machine Learning Studio.  I del 2 använder du modellen för bästa prestanda för att förutsäga resultat i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa ett Azure Machine Learning beräknings kluster.
> * Skapa en data uppsättning.
> * Skapa en automatiserad Machine Learning-körning.
> * Distribuera den bästa modellen till en slut punkt för real tids resultat.


Det finns tre sätt att skapa och distribuera den modell du använder i Power BI.  Den här artikeln beskriver "alternativ C: träna och distribuera modeller med hjälp av automatisk maskin inlärning i Studio".  Det här alternativet är en redigerings upplevelse utan kod. Den automatiserar data förberedelse-och modell träningen fullständigt. 

Men du kan i stället använda något av de andra alternativen:

* [Alternativ A: träna och distribuera modeller med hjälp av Jupyter Notebooks](tutorial-power-bi-custom-model.md). Den här koden – första redigerings upplevelsen använder Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio.
* [Alternativ B: träna och distribuera modeller med hjälp av Azure Machine Learning designer](tutorial-power-bi-designer-model.md). Den här redigerings upplevelsen med låg kod använder ett användar gränssnitt med dra och släpp-funktionen.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte redan har en prenumeration kan du använda en [kostnads fri utvärderings version](https://aka.ms/AMLFree). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, se [skapa och hantera Azure Machine Learning arbets ytor](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Skapa ett beräkningskluster

Automatiserade maskin inlärnings tåg många Machine Learning-modeller för att hitta den bästa algoritmen och parametrarna. Azure Machine Learning parallelizes för att köra modell utbildningen över ett beräknings kluster.

Börja i [Azure Machine Learning Studio](https://ml.azure.com)i menyn till vänster och välj **Compute**. Öppna fliken **beräknings kluster** . Välj sedan **nytt**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Skärm bild som visar hur du skapar ett beräknings kluster.":::

På sidan **skapa beräknings kluster** :

1. Välj en VM-storlek. I den här självstudien är en **Standard_D11_v2** dator bra.
1. Välj **Nästa**.
1. Ange ett giltigt beräknings namn.
1. Behåll det **lägsta antalet noder** på `0` .
1. Ändra **maximalt antal noder** till `4` .
1. Välj **Skapa**.

Status för klustret ändras till att **skapas**.

>[!NOTE]
> Det nya klustret har 0 noder, så inga beräknings kostnader uppkommer. Du debiteras bara kostnader när det automatiserade Machine Learning-jobbet körs. Klustret skalas tillbaka till 0 automatiskt efter 120 sekunders vänte tid.


## <a name="create-a-dataset"></a>Skapa en datauppsättning

I den här självstudien använder du [diabetes-datauppsättningen](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Den här data uppsättningen är tillgänglig i [Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/).

Om du vill skapa data uppsättningen går du till menyn till vänster och väljer **data uppsättningar**. Välj sedan **skapa data uppsättning**. Du ser följande alternativ:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Skärm bild som visar hur du skapar en ny data uppsättning.":::

Välj **från öppna data uppsättningar**. På sidan **skapa data uppsättning från öppna data uppsättningar** :

1. Använd Sök fältet för att hitta *diabetes*.
1. Välj **exempel: diabetes**.
1. Välj **Nästa**.
1. Namnge din data uppsättnings *diabetes*.
1. Välj **Skapa**.

Om du vill utforska data väljer du data uppsättningen och väljer sedan **utforska**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Skärm bild som visar hur du utforskar en data uppsättning.":::

Data har 10 bas linje inmatnings variabler, till exempel ålder, kön, body mass index, Genomsnittligt blod tryck och sex blod serum mätningar. Den har också en Target-variabel med namnet **Y**. Den här mål variabeln är ett kvantitativt mått på diabetes förlopp ett år efter bas linjen.

## <a name="create-an-automated-machine-learning-run"></a>Skapa en automatiserad Machine Learning-körning

I [Azure Machine Learning Studio](https://ml.azure.com)väljer du **automatiserad ml** i menyn till vänster. Välj sedan **ny automatiserad ml-körning**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Skärm bild som visar hur du skapar en ny automatiserad Machine Learning-körning.":::

Välj sedan den **diabetes** -datauppsättning som du skapade tidigare. Välj sedan **Nästa**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Skärm bild som visar hur du väljer en data uppsättning.":::
 
På sidan **Konfigurera körning** :

1. Under **experimentets namn** väljer du **Skapa nytt**.
1. Ge experimentet ett namn.
1. I fältet **mål kolumn** väljer du **Y**.
1. I fältet **Välj beräknings kluster** väljer du det beräknings kluster som du skapade tidigare. 

Ditt färdiga formulär bör se ut så här:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Skärm bild som visar hur du konfigurerar Automatisk maskin inlärning.":::

Välj slutligen en maskin inlärnings uppgift. I det här fallet är uppgiften **regression**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Skärm bild som visar hur du konfigurerar en aktivitet.":::

Välj **Slutför**.

> [!IMPORTANT]
> Automatisk maskin inlärning tar cirka 30 minuter att slutföra utbildningen av 100-modeller.

## <a name="deploy-the-best-model"></a>Distribuera den bästa modellen

När automatisk maskin inlärning slutförs kan du se alla Machine Learning-modeller som har provats genom att välja fliken **modeller** . Modellerna sorteras efter prestanda. modellen för bästa prestanda visas först. När du har valt den bästa modellen är knappen **distribuera** aktive rad:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Skärm bild som visar listan över modeller.":::

Välj **distribuera** för att öppna ett fönster för att **distribuera en modell** :

1. Namnge din modell tjänst *diabetes-Model*.
1. Välj **Azure Container Service**.
1. Välj **Distribuera**.

Ett meddelande visas som anger att modellen har distribuerats.

## <a name="next-steps"></a>Nästa steg

I den här självstudien såg du hur du tränar och distribuerar en maskin inlärnings modell med hjälp av automatisk maskin inlärning. I nästa självstudie får du lära dig hur du använder (score) den här modellen i Power BI.

> [!div class="nextstepaction"]
> [Självstudie: använda en modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
