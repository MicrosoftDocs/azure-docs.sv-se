---
title: Skapa automatiserade ML-klassificerings modeller
titleSuffix: Azure Machine Learning
description: Träna & att distribuera klassificerings modeller utan att skriva kod med hjälp av Azure Machine Learning s automatiserade maskin inlärnings gränssnitt (automatiserad ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: d0e236891e48a20adf1901d2f95a90ae25969c49
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210854"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Självstudie: skapa en klassificerings modell med automatiserad ML i Azure Machine Learning

Lär dig hur du skapar en [enkel klassificerings modell](concept-automated-ml.md#classification) utan att skriva en enda rad med kod med hjälp av automatisk maskin inlärning i Azure Machine Learning Studio. Den här klassificerings modellen förutsäger om en klient kommer att prenumerera på en fast term insättning med ett finansiellt institut.

Med automatisk maskin inlärning kan du automatisera tids krävande uppgifter. Automatisk maskin inlärning itererar snabbt över många kombinationer av algoritmer och egenskaper för att hjälpa dig att hitta den bästa modellen utifrån ett lyckat mått på hur du väljer.

Du kommer inte att skriva någon kod i den här självstudien. du använder Studio-gränssnittet för att utföra utbildning.  Du får lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-arbetsyta.
> * Köra ett automatiserat maskininlärningsexperiment.
> * Visa experiment information.
> * Distribuera modellen.

Prova också automatisk maskin inlärning för dessa andra modell typer:

* Ett icke-kod exempel på Prognosticering finns i [Självstudier: prognoser för efter frågan & AutoML](tutorial-automated-ml-forecast.md).
* En kod som är ett första exempel på en Regressions modell finns i [självstudien: Använd automatisk maskin inlärning för att förutse taxi-priser](tutorial-auto-train-models.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree).

* Hämta [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) data filen. **Y** -kolumnen visar om en kund prenumererar på en fast term insättning, som senare identifieras som mål kolumn för förutsägelser i den här självstudien. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Det finns många [sätt att skapa en arbets yta](how-to-manage-workspace.md). I den här självstudien skapar du en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="sign-in-to-the-studio"></a>Logga in på Studio

Du har slutfört följande experiment med att ställa in och köra steg via Azure Machine Learning Studio på https://ml.azure.com , ett konsoliderat webb gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildning av data vetenskap för alla färdighets nivåer. Studio stöds inte i Internet Explorer-webbläsare.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **Kom igång**.

1. I det vänstra fönstret väljer du **Automatisk ml** under avsnittet **författare** .

   Eftersom det här är ditt första automatiserade ML-experiment ser du en tom lista och länkar till dokumentationen.

   ![Sidan Kom igång](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Välj **+ ny automatiserad ml-körning**. 

## <a name="create-and-load-dataset"></a>Skapa och läsa in data uppsättning

Innan du konfigurerar experimentet laddar du upp data filen till din arbets yta i form av en Azure Machine Learning data uppsättning. På så sätt kan du se till att dina data formateras korrekt för experimentet.

1. Skapa en ny data uppsättning genom att välja **från lokala filer** från List rutan  **+ skapa data uppsättning** . 

    1. Ge din data uppsättning ett namn i formuläret **grundläggande information** och ange en valfri beskrivning. Det automatiserade ML-gränssnittet stöder för närvarande endast TabularDatasets, så data uppsättnings typen ska vara standard i *tabell*.

    1. Välj **Nästa** längst ned till vänster

    1. I formuläret **data lager och fil markering** väljer du det standard data lager som konfigurerades automatiskt när arbets ytan skapades, **workspaceblobstore (Azure Blob Storage)**. Det är här du överför data filen för att göra den tillgänglig för din arbets yta.

    1. Välj **Bläddra**.
    
    1. Välj **bankmarketing_train.csv** -filen på den lokala datorn. Det här är den fil som du laddade ned som en [förutsättning](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** längst ned till vänster för att överföra den till standard behållaren som konfigurerades automatiskt när arbets ytan skapades.  
    
       När uppladdningen är klar fylls inställningarna och förhands gransknings formuläret i förväg baserat på filtypen. 
       
    1. Kontrol lera att **inställningarna och förhands gransknings** formuläret är ifyllt enligt följande och välj **Nästa**.
        
        Fält|Beskrivning| Värde för självstudier
        ---|---|---
        Filformat|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att ange avgränsningen mellan &nbsp; separata, oberoende regioner i oformaterad text eller andra data strömmar. |Komma
        Kodning|Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.| UTF-8
        Kolumnrubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.| Alla filer har samma rubriker
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.| Inget

    1. Med hjälp av **schema** formuläret kan du ytterligare konfigurera dina data för det här experimentet. I det här exemplet väljer du växlings växeln för **day_of_week**, så att du inte inkluderar den. Välj **Nästa**.
         ![Schema formulär](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. I formuläret **bekräfta information** kontrollerar du att informationen stämmer överens med den **grundläggande informationen, data lagret och fil val** och **Inställningar och för hands** formulär.
    
    1. Klicka på **skapa** för att slutföra skapandet av din data uppsättning.
    
    1. Välj din data uppsättning när den visas i listan.
    
    1. Granska **data förhands granskningen**  för att se till att du inte har inkluderat **Day_of_week** Välj sedan **Stäng**.

    1. Välj  **Nästa**.

## <a name="configure-run"></a>Konfigurera körning

När du har läst in och konfigurerat dina data kan du konfigurera experimentet. Den här installationen innehåller experiment design uppgifter, till exempel, hur du väljer storlek på din beräknings miljö och hur du anger vilken kolumn som du vill förutsäga. 

1. Välj alternativ knappen **Skapa ny** .

1. Fyll i formuläret **Konfigurera körning** enligt följande:
    1. Ange följande experiment namn: `my-1st-automl-experiment`

    1. Välj **y** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.
    
    1. Välj **+ skapa en ny beräkning** och konfigurera beräknings målet. Ett beräknings mål är en lokal eller molnbaserad resurs miljö som används för att köra ditt utbildnings skript eller som värd för tjänst distributionen. För det här experimentet använder vi en molnbaserad beräkning. 
        1. Fyll i formuläret för **virtuell dator** för att konfigurera din beräkning.

            Fält | Beskrivning | Värde för självstudier
            ----|---|---
            Prioritet för virtuell &nbsp; dator &nbsp; |Välj vilken prioritet ditt experiment ska ha| Dedikerad
            Typ av virtuell &nbsp; dator &nbsp;| Välj typ av virtuell dator för din beräkning.|PROCESSOR (Central bearbetnings enhet)
            &nbsp; &nbsp; Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning. En lista med rekommenderade storlekar tillhandahålls baserat på dina data och experiment typ. |Standard_DS12_V2
        
        1. Välj **Nästa** för att fylla i **formuläret Konfigurera inställningar**.
        
            Fält | Beskrivning | Värde för självstudier
            ----|---|---
            Namn på beräkning |  Ett unikt namn som identifierar din beräknings kontext. | automl – beräkning
            Min/högsta antal noder| Du måste ange 1 eller fler noder för att kunna profilera data.|Minsta antal noder: 1<br>Max noder: 6
            Inaktiva sekunder innan skalning | Inaktivitetstid innan klustret skalas automatiskt ned till lägsta antal noder.|120 (standard)
            Avancerade inställningar | Inställningar för att konfigurera och auktorisera ett virtuellt nätverk för experimentet.| Inget               

        1. Välj **skapa** för att skapa beräknings målet. 

            **Det tar några minuter att slutföra.** 

             ![Sidan Inställningar](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. När du har skapat väljer du det nya beräknings målet i den nedrullningsbara listan.

    1. Välj **Nästa**.

1. I formuläret **uppgifts typ och inställningar** slutför du installationen för ditt AUTOMATISERAde ml-experiment genom att ange aktivitets typ och konfigurations inställningar för Machine Learning.
    
    1.  Välj **klassificering** som typ av Machine Learning-aktivitet.

    1. Välj **Visa ytterligare konfigurations inställningar** och fyll i fälten enligt följande. De här inställningarna är för att bättre styra utbildnings jobbet. Annars tillämpas standardvärdena utifrån experiment val och data.

        Ytterligare &nbsp; konfigurationer|Beskrivning|Värde &nbsp; för &nbsp; självstudier
        ------|---------|---
        Primärt mått| Bedömnings mått som ska mätas av Machine Learning-algoritmen.|AUC_weighted
        Förklara bästa modell| Visar automatiskt förklaringar för den bästa modellen som skapats av automatisk ML.| Aktivera
        Blockerade algoritmer | Algoritmer som du vill undanta från utbildnings jobbet| Inget
        Avslutnings kriterium| Om ett villkor uppfylls stoppas utbildnings jobbet. |Utbildnings &nbsp; jobb &nbsp; tid (timmar): 1 <br> Mått &nbsp; poängs &nbsp; tröskel: ingen
        Validering | Välj en kors validerings typ och antalet tester.|Validerings typ:<br>&nbsp;k-vikning &nbsp; kors validering <br> <br> Antal valideringar: 2
        Samtidighet| Maximalt antal parallella iterationer som utförs per iteration| Max &nbsp; . antal samtidiga &nbsp; iterationer: 5
        
        Välj **Spara**.
    
1. Klicka på **Slutför** om du vill köra experimentet. Skärmen **körnings information**  öppnas med **körnings status** överst när experiment förberedelsen börjar. Den här statusen uppdateras när experimentet fortskrider. Meddelanden visas också i det övre högra hörnet i Studio för att informera dig om status för experimentet.

>[!IMPORTANT]
> Förberedelserna tar **10-15 minuter** för att förbereda experiment körningen.
> När du har kört det tar det **2-3 minuter för varje iteration**.  <br> <br>
> I produktion skulle du förmodligen gå undan för en bit. I den här självstudien rekommenderar vi att du börjar utforska de testade algoritmerna på fliken **modeller** när de är klara medan de andra fortfarande körs. 

##  <a name="explore-models"></a>Utforska modeller

Gå till fliken **modeller** om du vill se vilka algoritmer (modeller) som har testats. Som standard sorteras modellerna efter mått poäng när de är klara. I den här självstudien visas modellen som visar den högsta baserat på det valda **AUC_weighted** måttet överst i listan.

Medan du väntar på att alla experiment modeller ska slutföras väljer du **algoritmens namn** för en slutförd modell för att utforska dess prestanda information. 

Följande navigerar via flikarna **information** och **mått** för att visa den valda modellens egenskaper, mått och prestanda diagram. 

![Kör upprepnings information](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>Modell förklaringar

Medan du väntar på att modeller ska slutföras, kan du också ta en titt på modell förklaringar och se vilka data funktioner (RAW eller tekniker) som påverkas av en viss modells förutsägelser. 

Dessa modell förklaringar kan genereras på begäran och sammanfattas i modellen förklarings instrument panel som är en del av fliken **förklaringar (för hands version)** .

För att generera modell förklaringar, 
 
1. Välj **Kör 1** längst upp för att gå tillbaka till **modell** skärmen. 
1. Välj fliken **modeller** .
1. I den här självstudien väljer du den första **MaxAbsScaler, LightGBM-** modellen.
1. Välj knappen **förklara modell** överst. Till höger visas fönstret **förklarings modell** . 
1. Välj den **automl-beräkning** som du skapade tidigare. Detta beräknings kluster initierar en underordnad körning för att generera modell förklaringar.
1. Välj **skapa** längst ned. Ett grönt meddelande visas längst upp på skärmen. 
    >[!NOTE]
    > Välklarande körningen tar cirka 2-5 minuter att slutföra.
1. Välj **förklarings knappen (förhands granskning)** . Den här fliken fylls när förklaringen är klar.
1. Expandera fönstret till vänster och välj den rad som står för **rå** under **funktioner**. 
1. Välj fliken **mängd funktions prioritet** till höger. Det här diagrammet visar vilka data funktioner som påverkas av förutsägelserna för den valda modellen. 

    I det här exemplet verkar *varaktigheten* ha störst inverkan på förutsägelserna för den här modellen.
    
    ![Instrument panel för modell förklaring](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>Distribuera den bästa modellen

Med det automatiserade Machine Learning-gränssnittet kan du distribuera den bästa modellen som en webb tjänst med några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella områden i affärs möjligheten. 

För det här experimentet innebär distribution till en webb tjänst att finans Institutet nu har en iterativ och skalbar webb lösning för att identifiera potentiella fasta, långsiktiga kunder. 

Kontrol lera om experiment körningen är klar. Det gör du genom att gå tillbaka till den överordnade körnings sidan genom att välja **Kör 1** överst på skärmen. En **slutförd** status visas längst upp till vänster på skärmen. 

När experimentet har slutförts fylls sidan **information** i med ett avsnitt för **bästa modell Sammanfattning** . I den här experiment kontexten betraktas **VotingEnsemble** som den bästa modellen, baserat på **AUC_weighted** mått.  

Vi distribuerar den här modellen, men vi rekommenderar att distributionen tar ungefär 20 minuter att slutföra. Distributions processen innehåller flera steg som att registrera modellen, generera resurser och konfigurera dem för webb tjänsten.

1. Välj **VotingEnsemble** för att öppna den aktuella sidan.

1. Välj knappen **distribuera** längst upp till vänster.

1. Fyll i fönstret **distribuera en modell** enligt följande:

    Fält| Värde
    ----|----
    Distributions namn| My-automl – Deploy
    Distributions Beskrivning| Min första automatiserade test distribution av Machine Learning
    Typ av beräkning | Välj Azure Compute Instance (ACI)
    Aktivera autentisering| Inaktivera. 
    Använd anpassade distributioner| Inaktivera. Tillåter att standard driv rutins filen (bedömnings skriptet) och miljö filen genereras automatiskt. 
    
    I det här exemplet använder vi de standardvärden som anges i menyn *Avancerat* . 

1. Välj **Distribuera**.  

    Ett grönt meddelande visas längst upp på skärmen **Kör** och i fönstret **modell Sammanfattning** visas ett status meddelande under **distributions status**. Välj **Uppdatera** regelbundet för att kontrol lera distributions statusen.
    
Nu har du en fungerande webb tjänst för att generera förutsägelser. 

Fortsätt till [**Nästa steg**](#next-steps) om du vill lära dig mer om hur du använder din nya webb tjänst och testa dina förutsägelser med hjälp av Power BI har inbyggd Azure Machine Learning support.

## <a name="clean-up-resources"></a>Rensa resurser

Distributions filer är större än data-och experiment-filer, så att de kostar mer att lagra. Ta bara bort distributions filerna för att minimera kostnaderna till ditt konto, eller om du vill behålla arbets ytan och experimentet. Annars tar du bort hela resurs gruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributions instansen

Ta bara bort distributions instansen från Azure Machine Learning på https: \/ /ml.Azure.com/om du vill behålla resurs gruppen och arbets ytan för andra självstudier och utforskningar. 

1. Gå till [Azure Machine Learning](https://ml.azure.com/). Gå till arbets ytan och välj **slut punkter** i fönstret **till** vänster. 

1. Välj den distribution som du vill ta bort och välj **ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiska självstudien om Machine Learning använde du Azure Machine Learning s automatiserade ML-gränssnitt för att skapa och distribuera en klassificerings modell. Se de här artiklarna för mer information och nästa steg:

> [!div class="nextstepaction"]
> [Använda en webbtjänst](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Lär dig mer om [Automatisk maskin inlärning](concept-automated-ml.md).
+ Mer information om klassificerings mått och diagram finns i artikeln [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md) .
+ Läs mer om [funktionalisering](how-to-configure-auto-features.md#featurization).
+ Läs mer om [data profilering](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Den här data uppsättningen för bank marknadsföring görs tillgänglig i [CCO-licensen för Creative Commons (: offentlig domän)](https://creativecommons.org/publicdomain/zero/1.0/). Alla rättigheter i det enskilda innehållet i databasen licensieras enligt [licensen för databas innehåll](https://creativecommons.org/publicdomain/zero/1.0/) och är tillgängliga på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här data uppsättningen var ursprungligen tillgänglig i den [Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez och P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22–31, juni 2014.
