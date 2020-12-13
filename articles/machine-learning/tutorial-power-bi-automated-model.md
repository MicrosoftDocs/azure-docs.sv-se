---
title: 'Självstudie: skapa en förutsägelse modell med automatisk ML (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar automatiserade ML-modeller, så att du kan använda den bästa modellen för att förutsäga resultat i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370350"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Självstudie: Power BI integration – skapa en förutsägelse modell med automatisk maskin inlärning (del 1 av 2)

I den första delen av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell med hjälp av automatisk maskin inlärning i Azure Machine Learning Studio.  I del 2 använder du sedan den bästa presterande modellen för att förutsäga resultat i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa ett Azure Machine Learning beräknings kluster
> * Skapa en datauppsättning
> * Skapa en automatisk ML-körning
> * Distribuera den bästa modellen till en slut punkt för real tids Poäng


Det finns tre olika sätt att skapa och distribuera den modell du använder i Power BI.  Den här artikeln beskriver alternativ C: träna och distribuera modeller med automatiserad ML i Studio.  Det här alternativet visar en redigering utan kod som kompletterar data förberedelser och modell träning. 

I stället kan du använda:

* [Alternativ A: träna och distribuera modeller med hjälp av antecknings böcker](tutorial-power-bi-custom-model.md) – en kod för första redigering med Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio.
* [Alternativ B: träna och distribuera modeller med designern](tutorial-power-bi-designer-model.md)– en låg kod redigerings upplevelse med hjälp av designer (ett användar gränssnitt med dra och släpp).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration ([en kostnads fri utvärderings version finns tillgänglig](https://aka.ms/AMLFree)). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, följer du [hur du skapar en Azure Machine Learning-arbetsyta](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Skapa beräknings kluster

Automatiserad ML tränar automatiskt upp flera olika maskin inlärnings modeller för att hitta "bästa" algoritm och parametrar. Azure Machine Learning parallelizes för att köra modell utbildningen över ett beräknings kluster.

I [Azure Machine Learning Studio](https://ml.azure.com)väljer du **beräkning** i den vänstra menyn följt av fliken **Compute Clusters** . Välj **nytt**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Skärm bild som visar hur du skapar ett beräknings kluster":::

På skärmen **skapa beräknings kluster** :

1. Välj en VM-storlek (i den här självstudien `Standard_D11_v2` är en dator felfri).
1. Välj **Nästa**
1. Ange ett giltigt beräknings namn
1. Behåll **minsta antalet noder** vid 0
1. Ändra **maximalt antal noder** till 4
1. Välj **Skapa**

Du kan se att status för klustret har ändrats till att **skapas**.

>[!NOTE]
> När klustret skapas har det 0 noder, vilket innebär att inga beräknings kostnader uppkommer. Du debiteras bara kostnader när det automatiserade ML-jobbet körs. Klustret kommer att skalas tillbaka till 0 automatiskt efter 120 sekunders inaktivitet.


## <a name="create-dataset"></a>Skapa datauppsättning

I den här självstudien använder du [diabetes-datauppsättningen](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), som görs tillgänglig i [Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/).

Om du vill skapa data uppsättningen väljer du den vänstra menyn för **data uppsättningar** följt av **skapa data uppsättning** . följande alternativ visas:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Skärm bild som visar hur du skapar en ny data uppsättning":::

Välj **från öppna data uppsättningar** och sedan i **skapa data uppsättning från skärmen öppna data uppsättningar** :

1. Sök efter *diabetes* med hjälp av Sök fältet
1. Välj **exempel: diabetes**
1. Välj **Nästa**
1. Ange ett namn för din data uppsättning – *diabetes*
1. Välj **Skapa**

Du kan utforska data genom att välja data uppsättningen följt av **utforska**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Skärm bild som visar hur du utforskar data uppsättningen":::

Data har 10 bas linje inmatnings variabler (till exempel ålder, kön, body mass index, Genomsnittligt blod tryck och sex blod Serums mått) och en mål variabel med namnet **Y** (ett kvantitativt mått på diabetes förlopp ett år efter bas linjen).

## <a name="create-automated-ml-run"></a>Skapa automatisk ML-körning

I [Azure Machine Learning Studio](https://ml.azure.com) väljer du **automatiserad ml** i den vänstra menyn följt av **ny automatiserad ml-körning**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Skärm bild som visar hur du skapar en ny automatisk ML-körning":::

Välj sedan den **diabetes** -datauppsättning som du skapade tidigare och välj **Nästa**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Skärm bild som visar hur du väljer en data uppsättning":::
 
På skärmen **Konfigurera körning** :

1. Under **experimentets namn väljer du** **Skapa ny**
1. Ange ett experiment namn
1. I fältet mål kolumn väljer du **Y**
1. I fältet **Välj beräknings kluster** väljer du det beräknings kluster som du skapade tidigare. 

Ditt färdiga formulär bör se ut ungefär så här:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Skärm bild som visar hur du konfigurerar automatisk ML":::

Slutligen måste du välja den Machine Learning-uppgift som ska utföras, som är **regression**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Skärm bild som visar hur du konfigurerar aktivitet":::

Välj **Slutför**.

> [!IMPORTANT]
> Det tar cirka 30 minuter för automatisk ML att slutföra utbildningen på 100 olika modeller.

## <a name="deploy-the-best-model"></a>Distribuera den bästa modellen

När den automatiserade ML-körningen har slutförts kan du se en lista över alla olika Machine Learning-modeller som har provats genom att välja fliken **modeller** . Modellerna ordnas i prestanda ordning – den bästa modellen visas först. När du väljer den bästa modellen aktive ras knappen **distribuera** :

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Skärm bild som visar listan över modeller":::

Om du väljer **distribuera** visas en **distribuera en modell** skärm:

1. Ange ett namn för din modell tjänst – Använd **diabetes-Model**
1. Välj **Azure Container Service**
1. Välj **distribuera**

Du bör se ett meddelande om att modellen har distribuerats korrekt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien såg du hur du tränar och distribuerar en maskin inlärnings modell med automatisk ML. I nästa självstudie visas hur du förbrukar (score) den här modellen från Power BI.

> [!div class="nextstepaction"]
> [Självstudie: använda modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
