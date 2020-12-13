---
title: 'Självstudie: Dra och släpp för att skapa en förutsägelse modell (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar en förutsägelse modell för Machine Learning med designer, så att du kan använda den för att förutsäga resultat i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370296"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Självstudie: Power BI integration – dra och släpp för att skapa en förutsägelse modell (del 1 av 2)

I den första delen av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell med hjälp av Azure Machine Learning designer – ett användar gränssnitt med låg kod för dra och släpp-funktionen. I del 2 använder du sedan modellen för att förutse resultatet i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning beräknings instans
> * Skapa ett Azure Machine Learning-härlednings kluster
> * Skapa en datauppsättning
> * Träna en regressionsmodell
> * Distribuera modellen till en slut punkt för real tids Poäng


Det finns tre olika sätt att skapa och distribuera den modell du använder i Power BI.  Den här artikeln beskriver alternativ B: träna och distribuera modeller med hjälp av designer.  Det här alternativet visar en låg kod redigerings upplevelse med hjälp av designer (ett användar gränssnitt med dra och släpp).  

I stället kan du använda:

* [Alternativ A: träna och distribuera modeller med hjälp av antecknings böcker](tutorial-power-bi-custom-model.md) – en kod för första redigering med Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio.
* [Alternativ C: träna och distribuera modeller med automatiserad ml](tutorial-power-bi-automated-model.md) – en redigering utan kod som fullständigt automatiserar data förberedelser och modell träning.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration ([en kostnads fri utvärderings version finns tillgänglig](https://aka.ms/AMLFree)). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, följer du [hur du skapar en Azure Machine Learning-arbetsyta](./how-to-manage-workspace.md#create-a-workspace).
- Introduktions kunskap om Machine Learning-arbetsflöden.


## <a name="create-compute-for-training-and-scoring"></a>Skapa beräkning för utbildning och Poäng

I det här avsnittet skapar du en *beräknings instans* som används för utbildning av Machine Learning-modeller. Du kan också skapa ett *härlednings kluster* som ska användas som värd för den distribuerade modellen för resultat i real tid.

Logga in på [Azure Machine Learning Studio](https://ml.azure.com) och välj **Beräkna** på menyn till vänster följt av **ny**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Skärm bild som visar hur man skapar en beräknings instans":::

På skärmen **skapa beräknings instans** som skapas väljer du en VM-storlek (för den här självstudien väljer du en `Standard_D11_v2` ) följt av **Nästa**. På sidan inställning anger du ett giltigt namn för din beräknings instans genom att välja **skapa**. 

>[!TIP]
> Compute-instansen kan också användas för att skapa och köra antecknings böcker.

Nu kan du se att din beräknings  instans status **skapas** – det tar cirka 4 minuter för datorn som ska tillhandahållas. När du väntar väljer du fliken för att konfigurera ett **kluster** på sidan beräkna och sedan **ny**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Skärm bild som visar hur du skapar ett härlednings kluster":::

På sidan Skapa ett utöknings Bart **kluster** väljer du en region följt av en VM-storlek (för den här självstudien väljer du en `Standard_D11_v2` ) och väljer sedan **Nästa**. På sidan **Konfigurera inställningar** :

1. Ange ett giltigt beräknings namn
1. Välj **dev-test** som kluster syfte (skapar en enskild nod som värd för den distribuerade modellen)
1. Välj **Skapa**

Nu kan du se att **statusen** för ditt härlednings kluster **skapas** – det tar cirka 4 minuter för ditt kluster för en nod att distribueras.

## <a name="create-a-dataset"></a>Skapa en datauppsättning

I den här självstudien använder du [diabetes-datauppsättningen](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), som görs tillgänglig i [Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/).

Om du vill skapa data uppsättningen går du till menyn till vänster och väljer **data uppsättningar** och sedan **skapa data uppsättning** . följande alternativ visas:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Skärm bild som visar hur du skapar en ny data uppsättning":::

Välj **från öppna data uppsättningar** och sedan i **skapa data uppsättning från skärmen öppna data uppsättningar** :

1. Sök efter *diabetes* med hjälp av Sök fältet
1. Välj **exempel: diabetes**
1. Välj **Nästa**
1. Ange ett namn för din data uppsättning – *diabetes*
1. Välj **Skapa**

Du kan utforska data genom att välja data uppsättningen följt av **utforska**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Skärm bild som visar hur data uppsättningen utforskas":::

Data har 10 bas linje inmatnings variabler (till exempel ålder, kön, body mass index, Genomsnittligt blod tryck och sex blod Serums mått) och en mål variabel med namnet **Y** (ett kvantitativt mått på diabetes förlopp ett år efter bas linjen).

## <a name="create-a-machine-learning-model-using-designer"></a>Skapa en Machine Learning modell med hjälp av designer

När du har skapat data bearbetningen och data uppsättningarna kan du gå vidare till skapa Machine Learning-modellen med hjälp av designer. I Azure Machine Learning Studio väljer du **Designer** följt av **ny pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Skärm bild som visar hur du skapar en ny pipeline":::

Du ser en tom *arbets yta* där du också kan se **menyn Inställningar**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Skärm bild som visar hur du väljer ett beräknings mål":::

På **menyn Inställningar** väljer du **Compute Target** och väljer sedan den beräknings instans som du skapade tidigare följt av **Spara**. Byt namn på **utkastet** till något mer minnes värt (till exempel *diabetes-Model*) och ange en beskrivning.

Därefter expanderar du **data uppsättningar** i listade till gångar och letar upp **diabetes** -datauppsättningen – dra och släpp den här modulen på arbets ytan:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Skärm bild som visar hur man drar en komponent på":::

Dra och släpp sedan följande komponenter på arbets ytan:

1. Linjär regression (finns i **Machine Learning algoritmer**)
1. Träna modell (finns i **modell utbildning**)

Din arbets yta bör se ut (Observera att komponenternas topp och nederkant har små cirklar som kallas portar – markerade i rött nedan):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Skärm bild som visar hur frånkopplade komponenter":::
 
Därefter måste du *ansluta dessa komponenter* till varandra. Välj porten längst ned i **diabetes** -datauppsättningen och dra den till den högra porten överst i **träna modell** -komponenten. Välj porten längst ned i den **linjära Regressions** komponenten och dra till den vänstra porten överst i **tågets modell** port.

Välj den kolumn i data uppsättningen som ska användas som variabel variabel att förutsäga. Välj komponenten **träna modell** följt av **Edit-kolumnen**. Välj **kolumnen ange kolumn namn** följt av **Y** i list rutan i dialog rutan:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Skärm bild Välj etikett kolumn":::

Välj **Spara**. *Arbets flödet* för Machine Learning bör se ut så här:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Skärm bild som visar hur anslutna komponenter":::

Välj **Skicka** och **skapa sedan nytt** under experimentet. Ange ett namn på experimentet följt av **Skicka**.

>[!NOTE]
> Det bör ta cirka 5 minuter innan experimentet är klart vid den första körningen. Efterföljande körningar är mycket snabbare cacheminnen som redan kör-komponenter för att minska svars tiden.

När experimentet är klart visas:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Skärm bild som visar slutförd körning":::

Du kan kontrol lera loggfilerna för experimentet genom att välja **träna modell** följt av **utdata + loggar**.

## <a name="deploy-the-model"></a>Distribuera modellen

Om du vill distribuera modellen väljer du **skapa härlednings pipeline** (finns överst på arbets ytan) följt av **pipelinen för real tids härledning**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Skärm bild som visar pipeline för real tids härledning":::
 
Pipelinen komprimerar bara de komponenter som krävs för att utföra modell poängen. När du utvärderar data kommer du inte att känna till målvärdena för värden, och därför kan vi ta bort **Y** från data uppsättningen. Ta bort genom att lägga till på arbets ytan a **Välj kolumner i data uppsättnings** komponenten. Tråd-till-komponenten så att diabetes-datauppsättningen är indata och resultatet blir utdata i **poängen modell** -komponenten:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Skärm bild som visar borttagning av en kolumn":::

Välj komponenten **Välj kolumner i data uppsättning** på arbets ytan följt av **Redigera kolumner**. I dialog rutan Välj kolumner väljer du **efter namn** och kontrollerar sedan att alla variabler är markerade men **inte** målet:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Skärm bild som visar borttagning av kolumn inställningar":::

Välj **Spara**. Välj slutligen komponenten **Poäng modell** och se till att kryss rutan **Lägg till Poäng kolumner till utdata** är avmarkerad (endast förutsägelserna skickas tillbaka, i stället för indata *och* förutsägelser, minska latens):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Skärm bild som visar komponent inställningar för Poäng modell":::

Välj **Skicka** överst på arbets ytan.

När du har kört en härlednings pipeline kan du distribuera modellen till ditt härlednings kluster. Välj **distribuera**, som visar dialog rutan för att **ställa in real tids slut punkt** . Välj **distribuera ny real tids slut punkt**, namnge slut punkten **My-diabetes-Model**, Välj den härledning som du skapade tidigare, Välj **distribuera**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Skärm bild som visar slut punkts inställningar i real tid":::
## <a name="next-steps"></a>Nästa steg

I den här självstudien såg du hur du tränar och distribuerar en designer modell. I nästa avsnitt får du lära dig hur du förbrukar (Poäng) den här modellen från Power BI.

> [!div class="nextstepaction"]
> [Självstudie: använda modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
