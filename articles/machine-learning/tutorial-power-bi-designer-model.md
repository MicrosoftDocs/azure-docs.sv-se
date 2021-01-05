---
title: 'Självstudie: Dra och släpp för att skapa en förutsägelse modell (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar en förutsägelse modell för Machine Learning med hjälp av designern. Senare kan du använda det för att förutsäga resultat i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814796"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Självstudie: Power BI integration – dra och släpp för att skapa en förutsägelse modell (del 1 av 2)

I del 1 av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell med hjälp av Azure Machine Learning designer. Designern är ett användar gränssnitt med nedcode dra och släpp-gränssnittet. I del 2 använder du modellen för att förutsäga resultat i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning beräknings instans.
> * Skapa ett Azure Machine Learning-härlednings kluster.
> * Skapa en data uppsättning.
> * Träna en Regressions modell.
> * Distribuera modellen till en slut punkt för real tids resultat.


Det finns tre sätt att skapa och distribuera den modell du använder i Power BI.  Den här artikeln beskriver "alternativ B: träna och distribuera modeller med hjälp av designern".  Det här alternativet är en låg kod redigerings upplevelse som använder design gränssnittet.  

Men du kan i stället använda något av de andra alternativen:

* [Alternativ A: träna och distribuera modeller med hjälp av Jupyter Notebooks](tutorial-power-bi-custom-model.md). Den här koden – första redigerings upplevelsen använder Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio.
* [Alternativ C: träna och distribuera modeller med hjälp av automatisk maskin inlärning](tutorial-power-bi-automated-model.md). Den här redigeringen av den här processen ger helt automatiserad data förberedelse och modell träning.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte redan har en prenumeration kan du använda en [kostnads fri utvärderings version](https://aka.ms/AMLFree). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, se [skapa och hantera Azure Machine Learning arbets ytor](./how-to-manage-workspace.md#create-a-workspace).
- Introduktions kunskap om Machine Learning-arbetsflöden.


## <a name="create-compute-to-train-and-score"></a>Skapa beräkning för att träna och Poäng

I det här avsnittet skapar du en *beräknings instans*. Beräknings instanser används för att träna maskin inlärnings modeller. Du kan också skapa ett löftes *kluster* som värd för den distribuerade modellen för resultat i real tid.

Logga in på [Azure Machine Learning Studio](https://ml.azure.com). Välj **Compute** på menyn till vänster och sedan **nytt**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Skärm bild som visar hur du skapar en beräknings instans.":::

På sidan **skapa beräknings instans** väljer du en VM-storlek. I den här självstudien väljer du en **Standard_D11_v2** VM. Välj sedan **Nästa**. 

På sidan **Inställningar** namnger du beräknings instansen. Välj sedan **Skapa**. 

>[!TIP]
> Du kan också använda beräknings instansen för att skapa och köra antecknings böcker.

Din beräknings instans **status** **skapas** nu. Det tar ungefär 4 minuter att etablera datorn. 

När du väntar går du till sidan **Compute** och väljer fliken för dator **kluster** . Välj sedan **nytt**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Skärm bild som visar hur du skapar ett härlednings kluster.":::

På sidan **skapa** ett uppräknings kluster väljer du en region och en VM-storlek. I den här självstudien väljer du en **Standard_D11_v2** VM. Välj sedan **Nästa**. 

På sidan **Konfigurera inställningar** :

1. Ange ett giltigt beräknings namn.
1. Välj **dev-test** som kluster syfte. Med det här alternativet skapas en enskild nod som värd för den distribuerade modellen.
1. Välj **Skapa**.

**Statusen** för ditt härlednings kluster **skapas** nu. Ett kluster med en nod tar cirka 4 minuter att distribuera.

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

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Skapa en maskin inlärnings modell med hjälp av designern

När du har skapat data bearbetningen och data uppsättningarna kan du skapa Machine Learning-modellen med hjälp av designern. I Azure Machine Learning Studio väljer du **Designer** och sedan **ny pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Skärm bild som visar hur du skapar en ny pipeline.":::

Du ser en tom *arbets yta* och en meny för **Inställningar** :

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Skärm bild som visar hur du väljer ett beräknings mål.":::

På menyn **Inställningar** väljer du **Välj Compute Target (Välj Compute Target**). Välj den beräknings instans som du skapade tidigare och välj sedan **Spara**. Ändra **utkastets namn** till något mer minnes värt, till exempel *diabetes-Model*. Ange slutligen en beskrivning.

I listan över till gångar expanderar du **data uppsättningar** och letar reda på **diabetes** -datauppsättningen. Dra den här komponenten till arbets ytan:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Skärm bild som visar hur man drar en komponent till arbets ytan.":::

Dra sedan följande komponenter till arbets ytan:

1. **Linjär regression** (finns i **Machine Learning algoritmer**)
1. **Träna modell** (finns i **modell utbildning**)

Lägg märke till cirklarna längst upp och ned i komponenterna på arbets ytan. Dessa cirklar är portar.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Skärm bild som visar portarna på komponenter som inte är anslutna.":::
 
Nu *ska* du ansluta komponenterna till varandra. Välj porten längst ned i **diabetes** -datauppsättningen. Dra den till porten på den övre högra sidan av komponenten **träna modell** . Välj porten längst ned i den **linjära Regressions** komponenten. Dra den till porten på den övre vänstra sidan av komponenten **träna modell** .

Välj kolumnen data mängd som ska användas som variabel variabel att förutsäga. Välj komponenten **träna modell** och välj sedan **Redigera kolumn**. 

I dialog rutan väljer du **Ange kolumn namn**  >  **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Skärm bild som visar hur du väljer en etikett kolumn.":::

Välj **Spara**. *Arbets flödet* för Machine Learning bör se ut så här:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Skärm bild som visar anslutna komponenter.":::

Välj **Skicka**. Under **experiment** väljer du **Skapa nytt**. Ge experimentet ett namn och välj sedan **Skicka**.

>[!NOTE]
> Experimentets första körning bör ta cirka 5 minuter. Efterföljande körningar är mycket snabbare eftersom designern cachelagrar komponenter som har körts för att minska svars tiden.

När experimentet är klart visas den här vyn:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Skärm bild som visar en slutförd körning.":::

Om du vill kontrol lera experiment loggarna väljer du **träna modell** och väljer sedan **utdata + loggar**.

## <a name="deploy-the-model"></a>Distribuera modellen

Om du vill distribuera modellen går du till arbets ytans överkant och väljer Skapa härlednings **pipeline**  >  **real tids pipeline i real tid**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Skärm bild som visar var du kan välja en pipeline för real tids härledning.":::
 
Pipelinen komprimerar bara de komponenter som krävs för att Poäng modellen. När du utvärderar data känner du inte till mål variabel värden. Så du kan ta bort **Y** från data uppsättningen. 

Om du vill ta bort **Y** lägger du till en **Välj kolumner i data uppsättnings** komponenten till arbets ytan. Sätt i komponenten så att diabetes-datauppsättningen är indata. Resultatet blir utdata i **poängen modell** -komponenten:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Skärm bild som visar hur du tar bort en kolumn.":::

Välj komponenten **Välj kolumner i data uppsättning** på arbets ytan och välj sedan **Redigera kolumner**. 

I dialog rutan **Välj kolumner** väljer du **efter namn**. Kontrol lera att alla variabler är markerade men att målet *inte* är markerat:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Skärm bild som visar hur du tar bort kolumn inställningar.":::

Välj **Spara**. 

Välj slutligen komponenten **Poäng modell** och se till att kryss rutan **Lägg till Poäng kolumner till utdata** är avmarkerad. Förutsägelserna skickas tillbaka utan indata för att minska svars tiden.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Skärm bild som visar inställningar för poängen modell-komponenten.":::

Välj **Skicka** på arbets ytans överkant.

När du har kört en härlednings pipeline kan du distribuera modellen till ditt härlednings kluster. Välj **Distribuera**. 

I dialog rutan **Konfigurera real tids slut punkt** väljer du **distribuera ny real tids slut punkt**. Namnge slut punkten *My-diabetes-Model*. Välj den härledning som du skapade tidigare och välj sedan **distribuera**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Skärm bild som visar inställningar för slut punkt i real tid.":::
## <a name="next-steps"></a>Nästa steg

I den här självstudien såg du hur du tränar och distribuerar en designer modell. I nästa avsnitt får du lära dig hur du förbrukar (Poäng) den här modellen i Power BI.

> [!div class="nextstepaction"]
> [Självstudie: använda en modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
