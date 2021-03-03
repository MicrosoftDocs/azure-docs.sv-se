---
title: 'Självstudie: Distribuera ML-modeller med designern'
titleSuffix: Azure Machine Learning
description: Bygg en förutsägelse analys lösning i Azure Machine Learning designer. Träna, score och distribuera en maskin inlärnings modell med hjälp av dra-och-släpp-moduler.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: ec563371ab505113117707f56c31f506f7fdf377
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659519"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Självstudie: Distribuera en maskin inlärnings modell med designern


Du kan distribuera den förutsägande modellen som utvecklats i [del ett av självstudien](tutorial-designer-automobile-price-train-score.md) för att ge andra möjlighet att använda den. I del ett tränar du din modell. Nu är det dags att generera förutsägelser baserat på användarindata. I den här delen av självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en pipeline för real tids härledning.
> * Skapa ett inferencing-kluster.
> * Distribuera real tids slut punkten.
> * Testa real tids slut punkten.

## <a name="prerequisites"></a>Förutsättningar

Slutför [del ett av självstudien](tutorial-designer-automobile-price-train-score.md) och lär dig hur du tränar och poängs ätter en maskin inlärnings modell i designern.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Skapa en pipeline för real tids härledning

Om du vill distribuera din pipeline måste du först konvertera inlärnings pipelinen till en pipeline för real tids härledning. Den här processen tar bort moduler för utbildning och lägger till webb tjänst indata och utdata för att hantera begär Anden.

### <a name="create-a-real-time-inference-pipeline"></a>Skapa en pipeline för real tids härledning

1. Ovanför pipeline-arbetsytan väljer du **skapa en härlednings** pipeline i  >  **real tid**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="Skärm bild som visar var du hittar knappen Skapa pipeline":::

    Din pipeline bör nu se ut så här: 

   ![Skärm bild som visar den förväntade konfigurationen för pipelinen när den har förberedats för distribution](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    När du väljer **skapa en härlednings pipeline** inträffar flera saker:
    
    * Den tränade modellen lagras som en **data uppsättnings** modul i modulens palett. Du kan hitta den under **mina data uppsättningar**.
    * Inlärnings moduler som **tränar modell** och **delade data** tas bort.
    * Den sparade utbildade modellen läggs tillbaka i pipelinen.
    * Moduler för **webb tjänst indata** och **webb tjänstens utdata** läggs till. De här modulerna visar var användar data går in i pipelinen och var data returneras.

    > [!NOTE]
    > Som standard förväntas samma data schema som de utbildnings data som används för att skapa den förutsägande pipeline i **webb tjänsten** . I det här scenariot ingår priset i schemat. Priset används dock inte som en faktor under förutsägelsen.
    >

1. Välj **Skicka** och Använd samma beräknings mål och experiment som du använde i del ett.

    Om det här är den första körningen kan det ta upp till 20 minuter innan din pipeline har slutförts. Standard beräknings inställningarna har en minsta Node-storlek på 0, vilket innebär att Designer måste allokera resurser efter inaktivitet. Upprepade pipelines körningar tar mindre tid eftersom beräknings resurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.

1. Välj **Distribuera**.

## <a name="create-an-inferencing-cluster"></a>Skapa ett inferencing-kluster

I dialog rutan som visas kan du välja från alla befintliga Azure Kubernetes service-kluster (AKS) för att distribuera din modell till. Om du inte har något AKS-kluster kan du använda följande steg för att skapa ett.

1. Välj **beräkning** i dialog rutan som visas för att gå till **beräknings** sidan.

1. I menyfliksområdet navigering väljer du **härlednings kluster**  >  **+ ny**.

    ![Skärm bild som visar hur du kommer till fönstret nytt fönster för utgångs kluster](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Konfigurera en ny Kubernetes-tjänst i fönstret fönster för utlåsnings kluster.

1. Ange *AKS-Compute* för **beräknings namnet**.
    
1. Välj en närliggande region som är tillgänglig för **regionen**.

1. Välj **Skapa**.

    > [!NOTE]
    > Det tar cirka 15 minuter att skapa en ny AKS-tjänst. Du kan kontrol lera etablerings statusen på sidan för att lösa **kluster** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Distribuera real tids slut punkten

När din AKS-tjänst har slutfört etableringen återgår du till inferencing-pipeline i real tid för att slutföra distributionen.

1. Välj **distribuera** ovanför arbets ytan.

1. Välj **distribuera ny slut punkt i real tid**. 

1. Välj det AKS-kluster som du skapade.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="Skärm bild som visar hur du konfigurerar en ny slut punkt för Real tid":::

    Du kan också ändra den **avancerade** inställningen för slut punkten i real tid.
    
    |Avancerad inställning|Beskrivning|
    |---|---|
    |Aktivera Application Insights diagnostik och data insamling| Om Azure Application insikter ska aktive ras för insamling av data från de distribuerade slut punkterna. </br> Som standard: falskt |
    |Poängsättnings-timeout| En timeout i millisekunder för att tvinga fram Poäng anrop till webb tjänsten.</br>Som standard: 60000|
    |Automatisk skalning aktive rad|   Om autoskalning ska aktive ras för webb tjänsten.</br>Som standard: sant|
    |Minsta antal repliker| Det minsta antal behållare som ska användas när den här webb tjänsten autoskalas.</br>Som standard: 1|
    |Maximalt antal repliker| Det maximala antal behållare som ska användas när den här webb tjänsten autoskalas.</br> Som standard: 10|
    |Mål användning|Mål användningen (i procent av 100) som autoskalning ska försöka underhålla för den här webb tjänsten.</br> Som standard: 70|
    |Uppdaterings period|Hur ofta (i sekunder) som autoskalning försöker skala webb tjänsten.</br> Som standard: 1|
    |PROCESSOR reserv kapacitet|Antalet processor kärnor som ska allokeras för den här webb tjänsten.</br> Som standard: 0,1|
    |Minnes reserv kapacitet|Mängden minne (i GB) som ska allokeras för den här webb tjänsten.</br> Som standard: 0,5|
        

1. Välj **Distribuera**. 

    Ett meddelande visas ovanför arbets ytan när distributionen är klar. Det kan ta några minuter.

> [!TIP]
> Du kan också distribuera till **Azure Container instance** (ACI) om du väljer **Azure Container instance** för **Compute-typ** i inställnings rutan för slut punkt i real tid.
> Azure Container instance används för testning eller utveckling. Använd ACI för processorbaserade CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne.

## <a name="test-the-real-time-endpoint"></a>Testa real tids slut punkten

När distributionen är klar kan du Visa real tids slut punkten genom att gå till sidan **slut punkter** .

1. På sidan **slut punkter** väljer du den slut punkt som du har distribuerat.

    På fliken **information** kan du se mer information, till exempel rest-URI, Swagger-definition, status och taggar.

    På fliken **förbrukare** kan du hitta exempel på förbruknings kod, säkerhets nycklar och ange autentiseringsmetoder.

    På fliken **distributions loggar** hittar du detaljerade distributions loggar för din real tids slut punkt.

1. Testa slut punkten genom att gå till fliken **test** . Härifrån kan du ange test data och välja **testa** för att verifiera slut punktens utdata.

Mer information om hur du konsumerar din webb tjänst finns i [använda en modell som distribueras som en webb tjänst](how-to-consume-web-service.md)

## <a name="limitations"></a>Begränsningar

Om du gör några ändringar i din utbildnings pipeline bör du skicka in utbildnings pipelinen igen, **Uppdatera** härlednings pipelinen och köra härlednings pipelinen igen.

Observera att endast utbildade modeller kommer att uppdateras i härlednings pipelinen, medan Datatransformeringen inte kommer att uppdateras.

Om du vill använda den uppdaterade omvandlingen i en härlednings pipeline måste du registrera Transformations resultatet för Transformations modulen som data uppsättning.

![Skärm bild som visar hur du registrerar omvandlings data uppsättning](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

Ersätt sedan den **TD-** modulen i härlednings pipelinen manuellt med den registrerade data uppsättningen.

![Skärm bild som visar hur du ersätter omvandlings modulen](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

Sedan kan du skicka en härlednings pipeline till den uppdaterade modellen och omvandlingen, och distribuera.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig viktiga steg i hur du skapar, distribuerar och använder en maskin inlärnings modell i designern. Mer information om hur du kan använda designern finns i följande länkar:

+ [Design exempel](samples-designer.md): Lär dig hur du använder designern för att lösa andra typer av problem.
+ [Använd Azure Machine Learning Studio i ett virtuellt Azure-nätverk](how-to-enable-studio-virtual-network.md).
