---
title: Använda AutoML för att skapa modeller & distribuera
titleSuffix: Azure Machine Learning
description: Skapa, granska och distribuera automatiserade maskininlärningsmodeller med Azure Machine Learning-studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 5718e0e3732f57b46500f9d2cdb1165e883ca44f
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575589"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Skapa, granska och distribuera automatiserade maskininlärningsmodeller med Azure Machine Learning


I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskininlärningsmodeller utan en enda kodrad i Azure Machine Learning-studio.

Automatiserad maskininlärning är en process där den bästa maskininlärningsalgoritmen att använda för dina specifika data väljs åt dig. Med den här processen kan du snabbt generera maskininlärningsmodeller. [Läs mer om automatiserad maskininlärning.](concept-automated-ml.md)
 
För ett exempel från slut till slut kan du prova självstudien för att [skapa en klassificeringsmodell Azure Machine Learning det automatiserade ML-gränssnittet](tutorial-first-experiment-automated-ml.md). 

För en Python-kodbaserad upplevelse [konfigurerar du dina automatiserade maskininlärningsexperiment](how-to-configure-auto-train.md) med Azure Machine Learning SDK.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning-arbetsyta. Se [Skapa en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md) 

## <a name="get-started"></a>Kom igång

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com). 

1. Välj din prenumeration och arbetsyta. 

1. Navigera till den vänstra rutan. Välj **Automatiserad ML** under **avsnittet** Författare.

[![Azure Machine Learning-studio navigeringsfönster](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Om det här är första gången du utför experiment visas en tom lista och länkar till dokumentationen. 

Annars visas en lista över dina senaste automatiserade maskininlärningsexperiment, inklusive de som skapats med SDK. 

## <a name="create-and-run-experiment"></a>Skapa och köra experiment

1. Välj **+ Ny automatiserad ML-körning** och fyll i formuläret.

1. Välj en datauppsättning från lagringscontainern eller skapa en ny datauppsättning. Datauppsättningar kan skapas från lokala filer, webb-URL:er, datalager eller öppna Azure-datauppsättningar. Läs mer om hur [du skapar datauppsättningar.](how-to-create-register-datasets.md)  

    >[!Important]
    > Krav på träningsdata:
    >* Data måste vara i tabellform.
    >* Det värde som du vill förutsäga (målkolumnen) måste finnas i data.

    1. Om du vill skapa en ny datauppsättning från en fil på din lokala dator väljer **du +Skapa datauppsättning** och sedan **Från lokal fil**. 

    1. I formuläret **Grundläggande information** ger du datauppsättningen ett unikt namn och anger en valfri beskrivning. 

    1. Välj **Nästa** för att öppna **formuläret Datalager och filval.** I det här formuläret väljer du var du vill ladda upp datauppsättningen. standardlagringscontainern som skapas automatiskt med din arbetsyta, eller välj en lagringscontainer som du vill använda för experimentet. 
    
        1. Om dina data finns bakom ett virtuellt nätverk måste du aktivera funktionen **hoppa** över valideringen för att säkerställa att arbetsytan kan komma åt dina data. Mer information finns i Använda [Azure Machine Learning-studio i ett virtuellt Azure-nätverk.](how-to-enable-studio-virtual-network.md) 
    
    1. Välj **Bläddra** för att ladda upp datafilen för datauppsättningen. 

    1. Granska formuläret **Inställningar och förhandsgranskning** för att se om det är korrekt. Formuläret fylls i intelligent baserat på filtypen. 

        Fält| Beskrivning
        ----|----
        Filformat| Definierar layouten och typen av data som lagras i en fil.
        Avgränsare| Ett eller flera tecken för att ange gränsen mellan separata, oberoende regioner i oformaterad text eller andra dataströmmar.
        Kodning| Identifierar vilken bit-till-tecken-schematabell som ska användas för att läsa datauppsättningen.
        Kolumnrubriker| Anger hur datauppsättningens huvuden, om några, kommer att behandlas.
        Hoppa över rader | Anger hur många, om några, rader som hoppas över i datauppsättningen.
    
        Välj **Nästa**.

    1. Formuläret **Schema** fylls i intelligent baserat på valen i formuläret **Inställningar och förhandsgranskning.** Här konfigurerar du datatypen för varje kolumn, granskar kolumnnamnen och väljer vilka kolumner som **inte ska ingå** i experimentet. 
            
        Välj **Nästa**.

    1. Formuläret **Bekräfta information** är en sammanfattning av den information som tidigare fyllts i i **formulären Grundläggande information** och Inställningar **och** förhandsgranskning. Du kan också skapa en dataprofil för datauppsättningen med hjälp av en profileringsaktiverad beräkning. Läs mer om [dataprofilering.](how-to-connect-data-ui.md#profile)

        Välj **Nästa**.
1. Välj din nyligen skapade datauppsättning när den visas. Du kan också visa en förhandsgranskning av datauppsättningen och exempelstatistik. 

1. I formuläret **Konfigurera körning** väljer du Skapa **ny och** anger **Tutorial-automl-deploy** som experimentnamn.

1. Välj en målkolumn. Det här är den kolumn som du vill göra förutsägelser på.

1. Välj en beräkning för dataprofilerings- och träningsjobbet. En lista över dina befintliga beräkningar finns i listrutan. Om du vill skapa en ny beräkning följer du anvisningarna i steg 7.

1. Välj **Skapa en ny beräkning för** att konfigurera beräkningskontexten för det här experimentet.

    Fält|Beskrivning
    ---|---
    Namn på beräkning| Ange ett unikt namn som identifierar din beräkningskontext.
    Prioritet för virtuell dator| Virtuella datorer med låg prioritet är billigare men garanterar inte beräkningsnoderna. 
    Typ av virtuell dator| Välj CPU eller GPU som typ av virtuell dator.
    Storlek för virtuell dator| Välj storlek på den virtuella datorn för beräkningen.
    Minsta/högsta antal noder| Om du vill profilera data måste du ange 1 eller flera noder. Ange det maximala antalet noder för beräkningen. Standardvärdet är 6 noder för en AML-beräkning.
    Avancerade inställningar | Med de här inställningarna kan du konfigurera ett användarkonto och ett befintligt virtuellt nätverk för experimentet. 
    
    Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

    >[!NOTE]
    > Ditt beräkningsnamn anger om den beräkning som du väljer/skapar *är profilering aktiverad.* (Mer information finns [i avsnittet om](how-to-connect-data-ui.md#profile) dataprofilering).

    Välj **Nästa**.

1. I formuläret **Aktivitetstyp och** inställningar väljer du uppgiftstyp: klassificering, regression eller prognostiserade. Mer [information finns i Uppgiftstyper](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) som stöds.

    1. För **klassificering** kan du även aktivera djupinlärning.
    
        Om djupinlärning är aktiverat är valideringen begränsad till _train_validation dela_. [Läs mer om verifieringsalternativ](how-to-configure-cross-validation-data-splits.md).


    1. För **prognostiserade** kan du 
    
        1. Aktivera djupinlärning.
    
        1. Välj *tidskolumn:* Den här kolumnen innehåller tidsdata som ska användas.

        1. Välj *prognosprognos:* Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen kommer att kunna förutsäga i framtiden. Ju mer modellen krävs för att förutsäga i framtiden, desto mindre exakt blir den. [Läs mer om prognostiserade prognoser och prognostiserade horisonter.](how-to-auto-train-forecast.md)

1. (Valfritt) Visa ytterligare konfigurationsinställningar: Ytterligare inställningar som du kan använda för att bättre styra träningsjobbet. Annars tillämpas standardvärden baserat på experimentval och data. 

    Ytterligare konfigurationer|Description
    ------|------
    Primärt mått| Huvudmått som används för bedömning av din modell. [Läs mer om modellmått.](how-to-configure-auto-train.md#primary-metric)
    Förklara den bästa modellen | Välj att aktivera eller inaktivera för att visa förklaringar till den rekommenderade bästa modellen. <br> Den här funktionen är för närvarande inte [tillgänglig för vissa prognosalgoritmer](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Blockerad algoritm| Välj algoritmer som du vill undanta från träningsjobbet. <br><br> Algoritmer som tillåts är bara tillgängliga för [SDK-experiment.](how-to-configure-auto-train.md#supported-models) <br> Se de [modeller som stöds för varje uppgiftstyp.](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels)
    Avslutningskriterium| När något av dessa villkor uppfylls stoppas träningsjobbet. <br> *Tid för träningsjobb (timmar)*: Hur länge träningsjobbet ska köras. <br> *Tröskelvärde för måttpoäng:* Minsta måttpoäng för alla pipelines. Detta säkerställer att du inte lägger mer tid på träningsjobbet än nödvändigt om du har ett definierat målmått som du vill nå.
    Validering| Välj ett av korsvalideringsalternativen som ska användas i träningsjobbet. <br> [Läs mer om korsvalidering.](how-to-configure-cross-validation-data-splits.md#prerequisites)<br> <br>Prognostiserade data stöder endast korsvalidering med k-fold.
    Samtidighet| *Maximalt antal samtidiga iterationer:* Maximalt antal pipelines (iterationer) som ska testas i träningsjobbet. Jobbet körs inte mer än det angivna antalet iterationer. Läs mer om hur automatiserad ML utför [flera underordnade körningar i kluster.](how-to-configure-auto-train.md#multiple-child-runs-on-clusters)

1. (Valfritt) Visa funktionersinställningar: Om du väljer att aktivera automatisk **funktionering** i formuläret Ytterligare **konfigurationsinställningar** tillämpas standardteknikerna för funktioner. I inställningarna **för visa featurization** kan du ändra dessa standardvärden och anpassa därefter. Lär dig hur [du anpassar featurizations](#customize-featurization). 

    ![Skärmbild som visar dialogrutan Välj aktivitetstyp med Visa featuriseringsinställningar markeras.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Anpassa featurisering

I formuläret **Funktioner kan** du aktivera/inaktivera automatisk funktionering och anpassa inställningarna för automatisk funktionering för experimentet. Om du vill öppna det här formuläret går du till steg 10 [i avsnittet Skapa och köra experiment.](#create-and-run-experiment) 

I följande tabell sammanfattas de anpassningar som för närvarande är tillgängliga via studio. 

Kolumn| Anpassning
---|---
Ingår | Anger vilka kolumner som ska inkluderas för träning.
Funktionstyp| Ändra värdetypen för den valda kolumnen.
Impute med| Välj vilket värde du vill imputera saknade värden med i dina data.

![Azure Machine Learning-studio anpassad featuralisering](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Köra experiment och visa resultat

Välj **Slutför** för att köra experimentet. Experimentförberedelserna kan ta upp till 10 minuter. För träningsjobb kan det ta ytterligare 2–3 minuter för varje pipeline att slutföra körningen.

> [!NOTE]
> Algoritmerna för automatiserad ML använder inbyggd slumpmässighet som kan orsaka viss variation i den rekommenderade modellens slutliga måttpoäng, till exempel noggrannhet. Automatiserad ML utför också åtgärder på data, till exempel uppdelning av träna-test, uppdelning av träna-validering eller korsvalidering vid behov. Så om du kör ett experiment med samma konfigurationsinställningar och primära mått flera gånger ser du förmodligen variation i varje experiments slutliga måttpoäng på grund av dessa faktorer. 

### <a name="view-experiment-details"></a>Visa experimentinformation

Skärmen **Körningsinformation** öppnas på **fliken** Information. Den här skärmen visar en sammanfattning av experimentkörningen, inklusive ett statusfält längst upp bredvid körningsnumret. 

Fliken **Modeller** innehåller en lista över de modeller som skapats ordnade efter måttpoängen. Som standard visas modellen med högst poäng utifrån det valda måttet överst i listan. När träningsjobbet testar fler modeller läggs de till i listan. Använd det här för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

![Körningsdetaljer](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Visa träningskörningsinformation

Öka detaljgranska någon av de slutförda modellerna för att  se **träningskörningsinformation,** till exempel en modellsammanfattning på fliken Modell eller prestandamåttdiagram på fliken Mått. Läs [mer om diagram.](how-to-understand-automated-ml.md)

[![Iterationsinformation](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations-preview"></a>Modellförklaringar (förhandsversion)

För att bättre förstå din modell kan du se vilka datafunktioner (rådata eller tekniker) som påverkar modellens förutsägelser med instrumentpanelen för modellförklaringar. 

Instrumentpanelen för modellförklaringar ger en övergripande analys av den tränade modellen tillsammans med dess förutsägelser och förklaringar. Du kan också visa mer information om en enskild datapunkt och dess enskilda egenskaps prioriteter. [Läs mer om förklaring av visualiseringar på instrumentpanelen.](how-to-machine-learning-interpretability-aml.md#visualizations)

Om du vill få förklaringar till en viss modell, 

1. På fliken **Modeller** väljer du den modell som du vill förstå. 
1. Välj knappen **Förklara modell** och ange en beräkning som kan användas för att generera förklaringarna.
1. Kontrollera **statusen på** fliken Underordnade körningar. 
1. När du är klar går du till **fliken Förklaringar (förhandsversion)** som innehåller instrumentpanelen med förklaringar. 

    ![Instrumentpanel för modellförklaring](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen tillhands är det dags att distribuera den som en webbtjänst för att göra förutsägelser på nya data.

>[!TIP]
> Om du vill distribuera en modell som genererades via paketet med `automl` Python SDK måste du registrera din [modell](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) på arbetsytan. 
>
> När du har registrerat modellen letar du reda på den i studio genom att **välja Modeller** i den vänstra rutan. När du har öppnat din  modell kan du välja knappen Distribuera längst upp på skärmen och sedan följa anvisningarna enligt beskrivningen i **steg 2** i **avsnittet Distribuera din** modell.

Automatiserad ML hjälper dig att distribuera modellen utan att skriva kod:

1. Du har ett par distributionsalternativ. 

    + Alternativ 1: Distribuera den bästa modellen enligt de måttkriterier som du har definierat. 
        1. När experimentet är klart går du till den överordnade körningssidan genom att välja Kör **1** överst på skärmen. 
        1.  Välj den modell som visas i **avsnittet Best model summary (Bästa modellsammanfattning).** 
        1. Välj **Distribuera** längst upp till vänster i fönstret. 

    + Alternativ 2: Distribuera en specifik modell iteration från det här experimentet.
        1. Välj den önskade modellen på fliken **Modeller**
        1. Välj **Distribuera** längst upp till vänster i fönstret.

1. Fyll i fönstret **Distribuera** modell.

    Fält| Värde
    ----|----
    Namn| Ange ett unikt namn för distributionen.
    Description| Ange en beskrivning för att bättre identifiera vad distributionen är till för.
    Typ av beräkning| Välj den typ av slutpunkt som du vill *distribuera: Azure Kubernetes Service (AKS)* eller *Azure Container Instance (ACI).*
    Namn på beräkning| *Gäller endast för AKS:* Välj namnet på det AKS-kluster som du vill distribuera till.
    Aktivera autentisering | Välj om du vill tillåta tokenbaserad eller nyckelbaserad autentisering.
    Använda anpassade distributionstillgångar| Aktivera den här funktionen om du vill ladda upp ett eget bedömningsskript och en egen miljöfil. [Läs mer om bedömningsskript](how-to-deploy-and-where.md).

    >[!Important]
    > Filnamn måste vara under 32 tecken och måste börja och sluta med alfanumeriska tecken. Får innehålla bindestreck, understreck, punkter och alfanumeriska tecken mellan. Blanksteg är inte tillåtna.

    På menyn *Avancerat* finns standardfunktioner för distribution som [datainsamling](how-to-enable-app-insights.md) och inställningar för resursutnyttjande. Om du vill åsidosätta dessa standardinställningar gör du det på den här menyn.

1. Välj **Distribuera**. Det kan ta ungefär 20 minuter att slutföra distributionen.
    När distributionen börjar visas fliken **Modellsammanfattning**. Distributionsförloppet visas i avsnittet **Distributionsstatus**. 

Nu har du ett fungerande webbtjänst för att generera förutsägelser! Du kan testa förutsägelserna genom att fråga tjänsten via [Power BI:s inbyggda Azure Machine Learning-stöd](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder en webbtjänst](how-to-consume-web-service.md).
* [Förstå resultat från automatiserad maskininlärning.](how-to-understand-automated-ml.md)
* [Läs mer om automatiserad maskininlärning](concept-automated-ml.md) och Azure Machine Learning.