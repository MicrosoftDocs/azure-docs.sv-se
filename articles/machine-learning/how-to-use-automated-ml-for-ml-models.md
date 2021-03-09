---
title: Använda AutoML för att skapa modeller & distribuera
titleSuffix: Azure Machine Learning
description: Skapa, granska och distribuera automatiserade maskin inlärnings modeller med Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: a91a4f3a2e686c8d502093dc41520df32b313209
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520649"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Skapa, granska och distribuera automatiserade maskin inlärnings modeller med Azure Machine Learning


I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskin inlärnings modeller utan en enda kodrad i Azure Machine Learning Studio.

Automatisk maskin inlärning är en process där den bästa Machine Learning-algoritmen som används för dina aktuella data väljs åt dig. Med den här processen kan du snabbt skapa maskin inlärnings modeller. [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md).
 
För ett slut punkt till slut punkts exempel kan du prova [självstudien för att skapa en klassificerings modell med Azure Machine Learning s automatiserade ml-gränssnitt](tutorial-first-experiment-automated-ml.md). 

[Konfigurera automatiserade maskin inlärnings experiment](how-to-configure-auto-train.md) med Azure Machine Learning SDK för en python-kod baserad upplevelse.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md). 

## <a name="get-started"></a>Kom igång

1. Logga in på Azure Machine Learning på https://ml.azure.com . 

1. Välj din prenumeration och arbets yta. 

1. Navigera till den vänstra rutan. Välj **Automatisk ml** under avsnittet **författare** .

[![Navigerings fönstret i Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Om det här är första gången du gör ett experiment visas en tom lista och länkar till dokumentationen. 

Annars visas en lista över dina senaste automatiserade maskin inlärnings experiment, inklusive de som har skapats med SDK: n. 

## <a name="create-and-run-experiment"></a>Skapa och kör experiment

1. Välj **+ ny automatiserad ml-körning** och fyll i formuläret.

1. Välj en data uppsättning från din lagrings behållare eller skapa en ny data uppsättning. Data uppsättningar kan skapas från lokala filer, webb-URL: er, data lager eller Azure Open-datauppsättningar. Lär dig mer om att [skapa data uppsättning](how-to-create-register-datasets.md).  

    >[!Important]
    > Krav på träningsdata:
    >* Data måste vara i tabell form.
    >* Värdet som du vill förutse (mål kolumnen) måste finnas i data.

    1. Om du vill skapa en ny data uppsättning från en fil på den lokala datorn väljer du **+ skapa data uppsättning** och väljer sedan **från lokal fil**. 

    1. Ge din data uppsättning ett unikt namn och ge en valfri beskrivning i formuläret **grundläggande information** . 

    1. Välj **Nästa** för att öppna **formuläret data lager och fil markering**. I det här formuläret väljer du var du vill ladda upp din data uppsättning. standard lagrings behållare som skapas automatiskt med din arbets yta eller Välj en lagrings behållare som du vill använda för experimentet. 
    
        1. Om dina data ligger bakom ett virtuellt nätverk måste du aktivera funktionen **hoppa över verifiering** för att se till att arbets ytan kan komma åt dina data. Mer information finns i [använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk](how-to-enable-studio-virtual-network.md). 
    
    1. Välj **Bläddra** för att ladda upp data filen för din data uppsättning. 

    1. Granska **inställningarna och för hands versions** formuläret för noggrannhet. Formuläret fylls i intelligent baserat på filtypen. 

        Fält| Beskrivning
        ----|----
        Filformat| Definierar layout och typ av data som lagras i en fil.
        Avgränsare| Ett eller flera tecken för att ange avgränsningen mellan separata, oberoende regioner i oformaterad text eller andra data strömmar.
        Kodning| Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.
        Kolumnrubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.
    
        Välj **Nästa**.

    1. **Schema** formuläret fylls i intelligent utifrån valen i formuläret **Inställningar och för hands version** . Konfigurera data typen för varje kolumn, granska kolumn namnen och välj vilka kolumner som **inte ska ingå** i experimentet. 
            
        Välj **Nästa**.

    1. Formuläret **bekräfta information** är en sammanfattning av den information som tidigare har fyllts i i **grundläggande information** och **Inställningar och för hands** formulär. Du kan också välja att skapa en data profil för din data uppsättning med en profilerings aktive rad beräkning. Läs mer om [data profilering](how-to-connect-data-ui.md#profile).

        Välj **Nästa**.
1. Välj den nyligen skapade data uppsättningen när den visas. Du kan också visa en förhands granskning av data uppsättningen och exempel statistiken. 

1. I formuläret **Konfigurera körning** väljer du **Skapa ny** och anger **självstudie – automl – distribuera** för experimentets namn.

1. Välj en mål kolumn. Det här är den kolumn som du vill göra förutsägelser på.

1. Välj en beräkning för data profilering och utbildnings jobb. Det finns en lista över dina befintliga beräkningar i list rutan. Följ instruktionerna i steg 7 för att skapa en ny beräkning.

1. Välj **skapa en ny beräkning** för att konfigurera din beräknings kontext för det här experimentet.

    Fält|Beskrivning
    ---|---
    Namn på beräkning| Ange ett unikt namn som identifierar din beräknings kontext.
    Prioritet för virtuell dator| Virtuella datorer med låg prioritet är billigare men garanterar inte Compute-noderna. 
    Typ av virtuell dator| Välj CPU eller GPU för typ av virtuell dator.
    Storlek för virtuell dator| Välj storlek på den virtuella datorn för din beräkning.
    Min/högsta antal noder| Du måste ange 1 eller fler noder för att kunna profilera data. Ange det maximala antalet noder för din beräkning. Standardvärdet är 6 noder för en AML-beräkning.
    Avancerade inställningar | Med de här inställningarna kan du konfigurera ett användar konto och ett befintligt virtuellt nätverk för experimentet. 
    
    Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

    >[!NOTE]
    > Ditt beräknings namn anger om den beräkning som du väljer/skapar profilering är *aktive rad*. (Mer information finns i avsnittet om avsnitts [data profilering](how-to-connect-data-ui.md#profile) ).

    Välj **Nästa**.

1. I formuläret **uppgifts typ och inställningar** väljer du uppgifts typ: klassificering, regression eller Prognosticering. Mer information finns i [aktivitets typer som stöds](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) .

    1. För **klassificering** kan du också aktivera djup inlärning.
    
        Om djup inlärning är aktiverat begränsas verifieringen till _train_validation delning_. [Läs mer om verifierings alternativ](how-to-configure-cross-validation-data-splits.md).


    1. För **prognoser** kan du 
    
        1. Aktivera djup inlärning.
    
        1. Välj *tids kolumn*: den här kolumnen innehåller de tids data som ska användas.

        1. Välj *prognos Horisont*: Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen ska kunna förutsäga till framtiden. Den ytterligare modellen krävs för att förutsäga i framtiden. den mindre exakta den blir. [Lär dig mer om prognostisering av prognoser och prognoser](how-to-auto-train-forecast.md).

1. Valfritt Visa ytterligare konfigurations inställningar: ytterligare inställningar som du kan använda för att styra utbildnings jobbet bättre. Annars tillämpas standardvärdena utifrån experiment val och data. 

    Ytterligare konfigurationer|Beskrivning
    ------|------
    Primärt mått| Främsta mått som används för att värdera din modell. [Lär dig mer om modell mått](how-to-configure-auto-train.md#primary-metric).
    Förklara bästa modell | Välj om du vill aktivera eller inaktivera för att visa förklaringar för den rekommenderade bästa modellen. <br> Den här funktionen är för närvarande inte tillgänglig för [vissa algoritmer för Prognosticering](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Blockerad algoritm| Välj algoritmer som du vill undanta från utbildnings jobbet. <br><br> Att tillåta algoritmer är bara tillgängligt för [SDK-experiment](how-to-configure-auto-train.md#supported-models). <br> Se de [modeller som stöds för varje aktivitets typ](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Avslutnings kriterium| När något av dessa villkor uppfylls stoppas utbildnings jobbet. <br> *Utbildnings jobb tid (timmar)*: hur lång tid det tar att köra utbildnings jobbet. <br> *Mät* värdes tröskel: minsta mått Poäng för alla pipeliner. Detta säkerställer att om du har ett definierat målmått som du vill nå, ägnar du inte mer tid åt övnings jobbet än nödvändigt.
    Validering| Välj ett av de kors validerings alternativ som ska användas i övnings jobbet. <br> [Läs mer om kors validering](how-to-configure-cross-validation-data-splits.md#prerequisites).<br> <br>Prognosticering stöder endast mellanvalidering av n:te vikning.
    Samtidighet| *Max. antal samtidiga iterationer*: maximalt antal pipelines (iterationer) som ska testas i utbildnings jobbet. Jobbet kan inte köra fler än det angivna antalet iterationer. Läs mer om hur automatiserad ML utför [flera underordnade körningar i kluster](how-to-configure-auto-train.md#multiple-child-runs-on-clusters).

1. Valfritt Visa funktionalisering-inställningar: om du väljer att aktivera **Automatisk funktionalisering** i formuläret för **ytterligare konfigurations inställningar** tillämpas standard funktionalisering-teknikerna. I **Visa funktionalisering-inställningar** kan du ändra dessa standardvärden och anpassa dem efter behov. Lär dig hur du [anpassar featurizations](#customize-featurization). 

    ![Skärm bild som visar dialog rutan Välj typ av aktivitet med inställningarna för Visa funktionalisering som har anropats.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Anpassa funktionalisering

I formuläret **funktionalisering** kan du aktivera/inaktivera automatisk funktionalisering och anpassa inställningarna för automatisk funktionalisering för ditt experiment. För att öppna det här formuläret, se steg 10 i avsnittet [skapa och köra experiment](#create-and-run-experiment) . 

I följande tabell sammanfattas de anpassningar som för närvarande är tillgängliga via Studio. 

Kolumn| Anpassning
---|---
Ingår | Anger vilka kolumner som ska ingå i utbildningen.
Funktions typ| Ändra värde typen för den markerade kolumnen.
Räkna med| Välj det värde som du vill använda för att ange värden för saknade värden i dina data.

![Anpassad funktionalisering för Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Kör experimentet och visa resultaten

Klicka på **Slutför** för att köra experimentet. Experimentförberedelserna kan ta upp till 10 minuter. För träningsjobb kan det ta ytterligare 2–3 minuter för varje pipeline att slutföra körningen.

> [!NOTE]
> Algoritmerna för automatisk ML-användning har potentiell slumpmässig het som kan orsaka smärre variationer i en rekommenderad modells slutliga mått poäng, som noggrannhet. Med automatisk ML utförs även åtgärder för data som träna-test-delning, träna eller kors validering när det behövs. Så om du kör ett experiment med samma konfigurations inställningar och primär mått flera gånger, kommer du troligen att se variationen i varje experiment slutliga Mät resultat på grund av dessa faktorer. 

### <a name="view-experiment-details"></a>Visa experimentinformation

Skärmen **körnings information** öppnas på fliken **information** . Den här skärmen visar en sammanfattning av experiment körningen inklusive ett statusfält överst bredvid körnings numret. 

Fliken **Modeller** innehåller en lista över de modeller som skapats ordnade efter måttpoängen. Som standard visas modellen med högst poäng utifrån det valda måttet överst i listan. När träningsjobbet testar fler modeller läggs de till i listan. Använd det här för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

![Körnings information](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Visa information om tränings körningar

Öka detalj nivån för alla färdiga modeller för att se information om utbildning, t. ex. en modell sammanfattning på fliken **modell** eller diagram över prestanda mått på fliken **mått** . [Läs mer om diagram](how-to-understand-automated-ml.md).

[![Upprepnings information](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations"></a>Modell förklaringar

För att bättre förstå din modell, se vilka data funktioner (RAW eller som är tillverkade) som påverkat modellens förutsägelser med modellen förklarings instrument panel. 

Instrument panelen för modell förklaringar innehåller en övergripande analys av den tränade modellen tillsammans med dess förutsägelser och förklaringar. Du kan också öka detalj nivån i en enskild data punkt och dess enskilda funktioner. [Lär dig mer om förklaringens visualiseringar och vissa ritytor](how-to-machine-learning-interpretability-aml.md#visualizations).

För att få förklaringar för en viss modell, 

1. På fliken **modeller** väljer du den modell som du vill använda. 
1. Välj knappen **förklara modell** och ange en beräkning som kan användas för att generera förklaringar.
1. Kontrol lera status på fliken **underordnade körningar** . 
1. När du är klar navigerar du till fliken **förklaringar (förhands granskning)** som innehåller förklarings instrument panelen. 

    ![Instrument panel för modell förklaring](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen tillhands är det dags att distribuera den som en webbtjänst för att göra förutsägelser på nya data.

Automatiserad ML hjälper dig att distribuera modellen utan att skriva kod:

1. Du har ett par alternativ för distribution. 

    + Alternativ 1: distribuera den bästa modellen enligt de mått kriterier som du har definierat. 
        1. När experimentet är klart navigerar du till den överordnade körnings sidan genom att välja **Kör 1** överst på skärmen. 
        1.  Välj den modell som visas i avsnittet **bästa modell Sammanfattning** . 
        1. Välj **distribuera** längst upp till vänster i fönstret. 

    + Alternativ 2: om du vill distribuera en speciell modell iteration från det här experimentet.
        1. Välj den önskade modellen på fliken **Modeller**
        1. Välj **distribuera** längst upp till vänster i fönstret.

1. Fyll i fönstret **distribuera modell** .

    Fält| Värde
    ----|----
    Namn| Ange ett unikt namn för din distribution.
    Beskrivning| Ange en beskrivning för att bättre identifiera vad den här distributionen är för.
    Typ av beräkning| Välj den typ av slut punkt som du vill distribuera: *Azure Kubernetes service (AKS)* eller *Azure Container Instance (ACI)*.
    Namn på beräkning| *Gäller endast för AKS:* Välj namnet på det AKS-kluster som du vill distribuera till.
    Aktivera autentisering | Välj för att tillåta tokenbaserad eller nyckelbaserad autentisering.
    Använda anpassade distributions till gångar| Aktivera den här funktionen om du vill överföra ditt eget bedömnings skript och miljö fil. [Läs mer om bedömningsskript](how-to-deploy-and-where.md).

    >[!Important]
    > Fil namn måste vara under 32 tecken och måste börja och sluta med alfanumeriska tecken. Får innehålla bindestreck, understreck, punkter och alfanumeriska tecken mellan. Blanksteg är inte tillåtna.

    På menyn *Avancerat* finns standardfunktioner för distribution som [datainsamling](how-to-enable-app-insights.md) och inställningar för resursutnyttjande. Om du vill åsidosätta dessa standardinställningar gör du det på den här menyn.

1. Välj **Distribuera**. Det kan ta ungefär 20 minuter att slutföra distributionen.
    När distributionen börjar visas fliken **Modellsammanfattning**. Distributionsförloppet visas i avsnittet **Distributionsstatus**. 

Nu har du ett fungerande webbtjänst för att generera förutsägelser! Du kan testa förutsägelserna genom att fråga tjänsten via [Power BI:s inbyggda Azure Machine Learning-stöd](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder en webb tjänst](how-to-consume-web-service.md).
* [Förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).
* [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md) och Azure Machine Learning.