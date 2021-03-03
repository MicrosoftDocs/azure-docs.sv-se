---
title: 'Självstudie: förutsäga bil priser med designern'
titleSuffix: Azure Machine Learning
description: Träna maskin inlärnings modell för att förutsäga bil priser med linjär regression. Den här självstudien är del ett i en serie med två delar.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: 7f602f82063e2a8092eecf533a75449231c1e900
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660992"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Självstudie: Förutsäg ett bilpris med designern


I den här självstudien får du lära dig hur du använder Azure Machine Learning designer för att träna och distribuera en maskin inlärnings modell som förutsäger priset på en bil. Designern är ett dra-och-släpp-verktyg som gör att du kan skapa maskin inlärnings modeller utan en enda rad kod.

I del ett av självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny pipeline.
> * Importera data.
> * Förbered data.
> * Träna en maskin inlärnings modell.
> * Utvärdera en maskin inlärnings modell.

I [del två](tutorial-designer-automobile-price-deploy.md) av självstudien distribuerar du din modell som en inferencing-slutpunkt i real tid för att förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den. 

> [!NOTE]
>En slutförd version av den här självstudien är tillgänglig som en exempel pipeline.
>
>Du hittar det genom att gå till designern på arbets ytan. I det **nya pipeline** -avsnittet väljer du **exempel 1-regression: Automobile pris förutsägelse (grundläggande)**.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Skapa en ny pipeline

Azure Machine Learning pipelines ordnar flera maskin inlärnings-och data bearbetnings steg i en enda resurs. Pipelines gör det möjligt att organisera, hantera och återanvända komplexa Machine Learning-arbetsflöden mellan projekt och användare.

Om du vill skapa en Azure Machine Learning pipeline behöver du en Azure Machine Learning arbets yta. I det här avsnittet får du lära dig hur du skapar båda dessa resurser.

### <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

Du behöver en Azure Machine Learning-arbetsyta för att kunna använda designern. Arbets ytan är den översta resursen för Azure Machine Learning, den innehåller en central plats där du kan arbeta med alla artefakter som du skapar i Azure Machine Learning. Instruktioner om hur du skapar en arbets yta finns i [skapa och hantera Azure Machine Learning arbets ytor](how-to-manage-workspace.md).

> [!NOTE]
> Om din arbets yta använder ett virtuellt nätverk finns det ytterligare konfigurations steg som du måste använda för att använda designern. Mer information finns i [använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk](how-to-enable-studio-virtual-network.md)

### <a name="create-the-pipeline"></a>Skapa pipelinen

1. Logga in på <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>och välj den arbets yta som du vill arbeta med.

1. Välj **Designer**.

    ![Skärm bild av den visuella arbets ytan som visar hur du får åtkomst till designern](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Välj **lättanvända inbyggda moduler**.

1. Längst upp på arbets ytan väljer du standard pipelinen för pipelinen **– skapad – på**. Byt namn på den till *bil förutsägelser*. Namnet behöver inte vara unikt.

## <a name="set-the-default-compute-target"></a>Ange standard beräknings mål

En pipeline körs på ett beräknings mål, som är en beräknings resurs som är kopplad till din arbets yta. När du har skapat ett beräknings mål kan du återanvända det för framtida körningar.

Du kan ange ett **standard beräknings mål** för hela pipelinen, vilket anger att varje modul ska använda samma beräknings mål som standard. Du kan dock ange beräknings mål per modul.

1. Bredvid pipelinens namn **väljer du** ![ skärm bild av kugg hjuls ikonen längst ](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) upp på arbets ytan för att öppna fönstret **Inställningar** .

1. I fönstret **Inställningar** till höger om arbets ytan väljer du **Välj Compute Target (Välj Compute Target**).

    Om du redan har ett tillgängligt beräknings mål kan du välja att köra denna pipeline.

    > [!NOTE]
    > Designern kan bara köra utbildnings experiment på Azure Machine Learning Compute men andra beräknings mål visas inte.

1. Ange ett namn för beräknings resursen.

1. Välj **Spara**.

    > [!NOTE]
    > Det tar cirka fem minuter att skapa en beräknings resurs. När resursen har skapats kan du återanvända den och hoppa över vänte tiden för framtida körningar.
    >
    > Beräknings resursen skalar automatiskt till noll noder när den är inaktiv för att spara pengar. När du använder den igen efter en fördröjning kan du uppleva ungefär fem minuters vänte tid medan den skalas upp.

## <a name="import-data"></a>Importera data

Det finns flera exempel data uppsättningar som ingår i designern som du kan experimentera med. I den här självstudien använder du **bil-pris data (RAW)**. 

1. Till vänster om arbets ytan för pipelinen är en palett med data uppsättningar och moduler. Välj **exempel data uppsättningar** för att visa tillgängliga exempel data uppsättningar.

1. Välj data uppsättningens **bil pris data (RAW)** och dra den till arbets ytan.

   ![Dra data till arbets yta](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualisera datan

Du kan visualisera data för att förstå den data uppsättning som du kommer att använda.

1. Högerklicka på **bil pris data (RAW)** och välj utdata för **visualisera**  >  **data uppsättning**.

1. Välj olika kolumner i data fönstret om du vill visa information om var och en.

    Varje rad representerar en bil och variablerna som är kopplade till varje bil visas som kolumner. Det finns 205 rader och 26 kolumner i den här data uppsättningen.

## <a name="prepare-data"></a>Förbereda data

Data uppsättningar kräver vanligt vis lite för bearbetning före analys. Du kanske har märkt vissa saknade värden när du kontrollerade data uppsättningen. De värden som saknas måste rengöras så att modellen kan analysera data korrekt.

### <a name="remove-a-column"></a>Ta bort en kolumn

När du tränar en modell måste du göra något om de data som saknas. I den här data uppsättningen saknar kolumnen **normaliserade förluster** många värden, så du kommer att utesluta den kolumnen från modellen helt och hållet.

1. I paletten modul till vänster om arbets ytan, expanderar du avsnittet **datatransformering** och letar upp modulen **Välj kolumner i data uppsättning** .

1. Dra modulen **Välj kolumner i data uppsättning** till arbets ytan. Släpp modulen under data uppsättnings modulen.

1. Anslut data uppsättningen för **Automobil pris data (RAW)** till modulen **Välj kolumner i data uppsättning** . Dra från data uppsättningens utgående port, som är den lilla cirkeln längst ned i data uppsättningen på arbets ytan, till Indataporten för **Select-kolumner i data uppsättningen**, som är den lilla cirkeln överst i modulen.

    > [!TIP]
    > Du skapar ett data flöde via din pipeline när du ansluter utdataporten för en modul till en annan indataport.
    >

    ![Anslut moduler](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Välj modulen **Välj kolumner i data uppsättning** .

1. I informations fönstret för moduler till höger om arbets ytan väljer du **Redigera kolumn**.

1. Expandera List rutan med **kolumn namn** bredvid **Inkludera** och markera  **alla kolumner**.

1. Välj **+** för att lägga till en ny regel.

1. I list menyerna väljer du **Uteslut** och **kolumn namn**.
    
1. Ange *normaliserade förluster* i text rutan.

1. I det nedre högra hörnet väljer du **Spara** för att stänga kolumn väljaren.

    ![Undanta en kolumn](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Välj modulen **Välj kolumner i data uppsättning** . 

1. I informations fönstret för moduler till höger om arbets ytan markerar du text rutan **kommentar** och anger *exkludera normaliserade förluster*.

    Kommentarerna visas i grafen för att hjälpa dig att organisera din pipeline.

### <a name="clean-missing-data"></a>Rensa saknade data

Din data uppsättning har fortfarande värden som saknas efter att du tagit bort kolumnen **normaliserade förluster** . Du kan ta bort återstående data som saknas med hjälp av modulen **Rensa data som saknas** .

> [!TIP]
> Att rensa saknade värden från indata är ett krav för att använda de flesta moduler i designern.

1. I paletten modul till vänster om arbets ytan, expanderar du avsnittet **data omvandling** och letar reda på modulen **Rensa data som saknas** .

1. Dra modulen **Rensa data som saknas** till pipeline-arbetsytan. Anslut den till modulen **Välj kolumner i data uppsättning** . 

1. Välj modulen **Rensa data som saknas** .

1. I informations fönstret för moduler till höger om arbets ytan väljer du **Redigera kolumn**.

1. I fönstret **kolumner som ska rensas** visas expanderar du den nedrullningsbara menyn bredvid **Inkludera**. Markera, **alla kolumner**

1. Välj **Spara**

1. I informations fönstret för moduler till höger om arbets ytan väljer du **ta bort hela raden** under **rensnings läge**.

1. I informations fönstret för moduler till höger om arbets ytan markerar du rutan **kommentar** och anger *ta bort saknade värde rader*. 

    Din pipeline bör nu se ut ungefär så här:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png" alt-text="Select-Column":::

## <a name="train-a-machine-learning-model"></a>Träna en maskininlärningsmodell

Nu när du har modulerna på plats för att bearbeta data kan du ställa in träna moduler.

Eftersom du vill förutsäga pris, vilket är ett tal, kan du använda en Regressions algoritm. I det här exemplet använder du en linjär Regressions modell.

### <a name="split-the-data"></a>Dela upp data

Att dela data är en vanlig uppgift i Machine Learning. Du kommer att dela upp dina data i två separata data uppsättningar. En data uppsättning kommer att träna modellen och den andra kommer att testa hur väl modellen utförs.

1. I modulen modul expanderar du avsnittet **data omvandling** och letar reda på modulen **dela data** .

1. Dra modulen **dela data** till pipeline-arbetsytan.

1. Anslut den vänstra porten för modulen **Rensa data som saknas** till modulen **dela data** .

    > [!IMPORTANT]
    > Se till att de vänstra Utdataportarna av **rensa saknade data** ansluter till **delade data**. Den vänstra porten innehåller de rensade data. Rätt port innehåller discarted-data.

1. Välj modulen **dela data** .

1. I informations fönstret för moduler till höger om arbets ytan, ställer du in **del av raderna i den första data uppsättningen för utdata** till 0,7.

    Det här alternativet delar upp 70 procent av data för att träna modellen och 30 procent för att testa den. Data uppsättningen 70 procent kan nås via den vänstra utdataporten. Återstående data kommer att vara tillgängliga via den högra utdataporten.

1. I informations fönstret för moduler till höger om arbets ytan markerar du rutan **kommentar** och anger *dela in data uppsättningen i Training set (0,7) och test uppsättning (0,3)*.

### <a name="train-the-model"></a>Träna modellen

Träna modellen genom att ge den en data uppsättning som inkluderar priset. Algoritmen skapar en modell som förklarar förhållandet mellan funktionerna och priset som presenteras av tränings data.

1. I paletten modul expanderar du **Machine Learning algoritmer**.
    
    Med det här alternativet visas flera kategorier av moduler som du kan använda för att initiera Learning-algoritmer.

1. Välj **regression**  >  **linjär regression** och dra den till pipeline-arbetsytan.

1. I modulen modul expanderar du **träna modul-modul** och drar modulen **träna modell** till arbets ytan.

1. Anslut utdata från modulen **linjär regression** till vänster indata för modulen **träna modell** .

1. Anslut övnings data utmatningen (den vänstra porten) för modulen **dela data** till rätt indata för modulen **träna modell** .
    
    > [!IMPORTANT]
    > Se till att de vänstra Utdataportarna för **delade data** ansluter till **träna modell**. Den vänstra porten innehåller inlärnings uppsättningen. Rätt port innehåller test uppsättningen.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png" alt-text="Skärm bild som visar korrekt konfiguration av modulen träna modell. Modulen linjär regression ansluter till den vänstra porten för modulen träna modell och modulen dela data ansluts till rätt port för träna modell.":::

1. Välj **träningsmodellmodulen**.

1. I informations fönstret för moduler till höger om arbets ytan väljer du **Redigera kolumn** väljare.

1. I dialog rutan **etikett kolumn** expanderar du den nedrullningsbara menyn och väljer **kolumn namn**. 

1. I text rutan anger du *pris* för att ange det värde som din modell ska förutsäga.

    >[!IMPORTANT]
    > Se till att du anger kolumn namnet exakt. Lägg inte till versala **priser**. 

    Din pipeline bör se ut så här:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png" alt-text="Skärm bild som visar korrekt konfiguration av pipelinen efter att du lagt till modulen träna modell.":::

### <a name="add-the-score-model-module"></a>Lägg till modulen Poäng modell

När du har tränat din modell genom att använda 70 procent av data kan du använda den för att se hur väl modellen fungerar.

1. Ange *Poäng modell* i sökrutan för att hitta modulen **Poäng modell** . Dra modulen till pipeline-arbetsytan. 

1. Anslut utdataporten för modulen **Träna modell** till den vänstra indataporten för **Poängsätta modell**. Anslut utdataporten för testning (den högra porten) för modulen **Dela data** till den högra indataporten för **Poängsätta modell**.

### <a name="add-the-evaluate-model-module"></a>Lägga till modulen Utvärdera modell

Använd modulen **utvärdera modell** för att utvärdera hur bra din modell bevisar test data uppsättningen.

1. Välj *utvärdera* i sökrutan för att hitta modulen **utvärdera modell** . Dra modulen till pipeline-arbetsytan. 

1. Anslut utdata från modulen **Poäng modell** till den vänstra inmatningen av **utvärdera modell**. 

    Den sista pipelinen bör se ut ungefär så här:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png" alt-text="Skärm bild som visar korrekt konfiguration av pipelinen.":::

## <a name="submit-the-pipeline"></a>Skicka pipelinen

Nu när din pipeline är all konfiguration kan du skicka en pipeline-körning för att träna din Machine Learning-modell. Du kan skicka en giltig pipeline-körning när som helst, som kan användas för att granska ändringar i din pipeline under utvecklingen.

1. Välj **Skicka** på arbets ytans överkant.

1. I dialog rutan **Konfigurera pipeline-körning** väljer du **Skapa ny**.

    > [!NOTE]
    > Experiment grupp liknande pipeliner körs tillsammans. Om du kör en pipeline flera gånger kan du välja samma experiment för efterföljande körningar.

    1. För **nytt experiment namn** anger du **självstudie – CarPrices**.

    1. Välj **Skicka**.
    
    Du kan visa körnings status och information överst till höger på arbets ytan.
    
    Om det här är den första körningen kan det ta upp till 20 minuter innan din pipeline har slutförts. Standard beräknings inställningarna har en minsta Node-storlek på 0, vilket innebär att Designer måste allokera resurser efter inaktivitet. Upprepade pipelines körningar tar mindre tid eftersom beräknings resurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.

### <a name="view-scored-labels"></a>Visa Poäng etiketter

När körningen är klar kan du visa resultatet av pipeline-körningen. Börja med att titta på förutsägelserna som genereras av Regressions modellen.

1. Högerklicka på modulen **Poäng modell** och välj **visualisera**  >  **Poäng data uppsättning** för att visa dess utdata.

    Här kan du se de förutsagda priser och de faktiska priserna från test data.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png" alt-text="Skärm bild av utmatnings visualiseringen som markerar den markerade etikett kolumnen":::

### <a name="evaluate-models"></a>Utvärdera modeller

Använd **utvärdera modell** för att se hur väl den tränade modellen utförts på test data uppsättningen.

1. Högerklicka på modulen **utvärdera modell** och välj **visualisera**  >  **utvärderings resultat** för att visa dess utdata.

Följande statistik visas för din modell:

* **Medelvärde för absolut fel (Mae)**: medelvärdet av absoluta fel. Ett fel är skillnaden mellan det förväntade värdet och det faktiska värdet.
* **Rot genomsnitts fel (rmse)**: kvadratroten ur genomsnittet av de förutsägelser som gjorts på test data uppsättningen.
* **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Friktionskoefficienten**: även känt som R-kvadratvärdet anger det här statistik måttet hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelserna är närmare de faktiska värdena. För att fastställa koefficienten är det närmare värdet en (1,0), desto bättre förutsägelser.

## <a name="clean-up-resources"></a>Rensa resurser

Hoppa över det här avsnittet om du vill fortsätta med del 2 av självstudien, [Distribuera modeller](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I del två får du lära dig hur du distribuerar din modell som en slut punkt i real tid.

> [!div class="nextstepaction"]
> [Fortsätt till att distribuera modeller](tutorial-designer-automobile-price-deploy.md)
