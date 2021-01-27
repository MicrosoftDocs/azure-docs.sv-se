---
title: 'Självstudie: prognoser för efter frågan & AutoML'
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en prognos modell för efter frågan med automatiserad maskin inlärning i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879367"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Självstudie: prognostisera efter frågan med automatiserad maskin inlärning


I den här självstudien använder du Automatisk maskin inlärning eller automatiserad ML i Azure Machine Learning Studio för att skapa en prognos modell för tids serier som förutsäger efter frågan för en cykel delnings tjänst.

Exempel på en klassificerings modell finns i [Självstudier: skapa en klassificerings modell med automatiserad ml i Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa och läsa in en data uppsättning.
> * Konfigurera och kör ett automatiserat ML experiment.
> * Ange prognos inställningar.
> * Utforska experiment resultatet.
> * Distribuera den bästa modellen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md). 

* Hämta [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) data filen

## <a name="get-started-in-azure-machine-learning-studio"></a>Kom igång i Azure Machine Learning Studio

I den här självstudien får du skapa ett automatiserat ML experiment i Azure Machine Learning Studio, ett konsoliderat webb gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för de olika kunskaps nivåerna för data vetenskap. Studio stöds inte i Internet Explorer-webbläsare.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **Kom igång**.

1. I det vänstra fönstret väljer du **Automatisk ml** under avsnittet **författare** .

1. Välj **+ ny automatiserad ml-körning**. 

## <a name="create-and-load-dataset"></a>Skapa och läsa in data uppsättning

Innan du konfigurerar experimentet laddar du upp data filen till din arbets yta i form av en Azure Machine Learning data uppsättning. På så sätt kan du se till att dina data formateras korrekt för experimentet.

1. I formuläret **Välj data uppsättning** väljer du **från lokala filer** från List rutan  **+ skapa data uppsättning** . 

    1. Ge din data uppsättning ett namn i formuläret **grundläggande information** och ange en valfri beskrivning. Data uppsättnings typen ska vara standard i **tabell**, eftersom automatisk ML i Azure Machine Learning Studio endast stöder tabell data uppsättningar.
    
    1. Välj **Nästa** längst ned till vänster

    1. I formuläret **data lager och fil markering** väljer du det standard data lager som konfigurerades automatiskt när arbets ytan skapades, **workspaceblobstore (Azure Blob Storage)**. Det här är lagrings platsen där du överför data filen. 

    1. Välj **Bläddra**. 
    
    1. Välj **bike-no.csv** -filen på den lokala datorn. Det här är den fil som du laddade ned som en [förutsättning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Välj **Nästa**

       När uppladdningen är klar fylls inställningarna och förhands gransknings formuläret i förväg baserat på filtypen. 
       
    1. Kontrol lera att **inställningarna och förhands gransknings** formuläret är ifyllt enligt följande och välj **Nästa**.
        
        Fält|Beskrivning| Värde för självstudier
        ---|---|---
        Filformat|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att ange avgränsningen mellan &nbsp; separata, oberoende regioner i oformaterad text eller andra data strömmar. |Komma
        Kodning|Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.| UTF-8
        Kolumnrubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.| Använd huvuden från den första filen
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.| Inget

    1. Med hjälp av **schema** formuläret kan du ytterligare konfigurera dina data för det här experimentet. 
    
        1. I det här exemplet väljer du att ignorera de **vardagliga** och **registrerade** kolumnerna. De här kolumnerna är en uppdelning av kolumnen  **CNT** så därför inkluderar vi dem inte.

        1. I det här exemplet ska du också lämna standardvärdena för **egenskaperna** och **typen**. 
        
        1. Välj **Nästa**.

    1. I formuläret **bekräfta information** kontrollerar du att informationen stämmer överens med den **grundläggande informationen** och **inställningarna och för hands** formulären.

    1. Klicka på **skapa** för att slutföra skapandet av din data uppsättning.

    1. Välj din data uppsättning när den visas i listan.

    1. Välj  **Nästa**.

## <a name="configure-run"></a>Konfigurera körning

När du har läst in och konfigurerat dina data konfigurerar du ditt fjärrberäknings mål och väljer vilken kolumn i dina data du vill förutsäga.

1. Fyll i formuläret **Konfigurera körning** enligt följande:
    1. Ange ett experiment namn: `automl-bikeshare`

    1. Välj **CNT** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar antalet totala hyres hyror för cykel resurser.

    1. Välj **skapa en ny beräkning** och konfigurera beräknings målet. Automatisk ML stöder endast Azure Machine Learning beräkning. 

        1. Fyll i formuläret för **virtuell dator** för att konfigurera din beräkning.

            Fält | Beskrivning | Värde för självstudier
            ----|---|---
            Prioritet för virtuell &nbsp; dator &nbsp; |Välj vilken prioritet ditt experiment ska ha| Dedikerad
            Typ av virtuell &nbsp; dator &nbsp;| Välj typ av virtuell dator för din beräkning.|PROCESSOR (Central bearbetnings enhet)
            &nbsp; &nbsp; Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning. En lista med rekommenderade storlekar tillhandahålls baserat på dina data och experiment typ. |Standard_DS12_V2
        
        1. Välj **Nästa** för att fylla i **formuläret Konfigurera inställningar**.
        
             Fält | Beskrivning | Värde för självstudier
            ----|---|---
            Namn på beräkning |  Ett unikt namn som identifierar din beräknings kontext. | cykel – beräkning
            Min/högsta antal noder| Du måste ange 1 eller fler noder för att kunna profilera data.|Minsta antal noder: 1<br>Max noder: 6
            Inaktiva sekunder innan skalning | Inaktivitetstid innan klustret skalas automatiskt ned till lägsta antal noder.|120 (standard)
            Avancerade inställningar | Inställningar för att konfigurera och auktorisera ett virtuellt nätverk för experimentet.| Inget 
  
        1. Välj **skapa** för att hämta beräknings målet. 

            **Det tar några minuter att slutföra.** 

        1. När du har skapat väljer du det nya beräknings målet i den nedrullningsbara listan.

    1. Välj **Nästa**.

## <a name="select-forecast-settings"></a>Välj prognos inställningar

Slutför installationen av ditt automatiserade ML-experiment genom att ange aktivitets typ och konfigurations inställningar för Machine Learning.

1. I formuläret **uppgifts typ och inställningar** väljer du **prognos för tids serier** som typ av maskin inlärnings aktivitet.

1. Välj **datum** som **tids kolumn** och lämna **Time Series-identifierare** tomma. 

1. **Prognos horisonten** är tiden i framtiden som du vill förutsäga.  Avmarkera identifiera och skriv 14 i fältet. 

1. Välj **Visa ytterligare konfigurations inställningar** och fyll i fälten enligt följande. De här inställningarna är för att bättre styra utbildnings jobbet och ange inställningar för din prognos. Annars tillämpas standardvärdena utifrån experiment val och data.

    Ytterligare &nbsp; konfigurationer|Description|Värde &nbsp; för &nbsp; självstudier
    ------|---------|---
    Primärt mått| Bedömnings mått som ska mätas av Machine Learning-algoritmen.|Normaliserat rot genomsnitts fel
    Förklara bästa modell| Visar automatiskt förklaringar för den bästa modellen som skapats av automatisk ML.| Aktivera
    Blockerade algoritmer | Algoritmer som du vill undanta från utbildnings jobbet| Extrema slumpmässiga träd
    Ytterligare prognos inställningar| De här inställningarna hjälper dig att förbättra modellens precision. <br><br> _**Beräkna mål lags:**_ hur långt tillbaka du vill konstruera lags för mål variabeln <br> _**Mål riktnings fönster**_: anger storleken på det rullande fönster över vilka funktioner, till exempel *Max, min* och *Summa*, som ska genereras. | <br><br>Lags för prognos &nbsp; mål &nbsp; : ingen <br> &nbsp;Storlek för rullande fönster i mål &nbsp; &nbsp; : ingen
    Avslutnings kriterium| Om ett villkor uppfylls stoppas utbildnings jobbet. |Utbildnings &nbsp; jobb &nbsp; tid (timmar): 3 <br> Mått &nbsp; poängs &nbsp; tröskel: ingen
    Validering | Välj en kors validerings typ och antalet tester.|Validerings typ:<br>&nbsp;k-vikning &nbsp; kors validering <br> <br> Antal verifieringar: 5
    Samtidighet| Maximalt antal parallella iterationer som utförs per iteration| Max &nbsp; . antal samtidiga &nbsp; iterationer: 6
    
    Välj **Spara**.

## <a name="run-experiment"></a>Kör experiment

Om du vill köra experimentet väljer du **Slutför**. Skärmen **Kör information**  öppnas med status för **körning** högst upp bredvid körnings numret. Den här statusen uppdateras när experimentet fortskrider. Meddelanden visas också i det övre högra hörnet i Studio för att informera dig om status för experimentet.

>[!IMPORTANT]
> Förberedelserna tar **10-15 minuter** för att förbereda experiment körningen.
> När du har kört det tar det **2-3 minuter för varje iteration**.<br> <br>
> I produktion skulle du förmodligen gå undan för en bit eftersom den här processen tar tid. Medan du väntar rekommenderar vi att du börjar utforska de testade algoritmerna på fliken **modeller** när de är klara. 

##  <a name="explore-models"></a>Utforska modeller

Gå till fliken **modeller** om du vill se vilka algoritmer (modeller) som har testats. Som standard sorteras modellerna efter mått poäng när de är klara. I den här självstudien visas den modell som visar den högsta baserat på det valda **normaliserade rot medelvärdet** för ett kvadratvärde överst i listan.

Medan du väntar på att alla experiment modeller ska slutföras väljer du **algoritmens namn** för en slutförd modell för att utforska dess prestanda information. 

I följande exempel navigerar du till flikarna **information** och **mått** för att visa den valda modellens egenskaper, mått och prestanda diagram. 

![Körnings information](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>Distribuera modellen

Med automatisk maskin inlärning i Azure Machine Learning Studio kan du distribuera den bästa modellen som en webb tjänst med några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella områden i affärs möjligheten. 

För det här experimentet innebär distributionen till en webb tjänst att cykel resurs företaget nu har en iterativ och skalbar webb lösning för att bedöma behovet av att dela Hyr cykel. 

När körningen är klar går du tillbaka till den överordnade körnings sidan genom att välja **Kör 1** överst på skärmen.

I avsnittet **bästa modell Sammanfattning** anses **StackEnsemble** vara den bästa modellen i samband med det här experimentet, baserat på det **normaliserade rot medelvärdet för fel** måttet.  

Vi distribuerar den här modellen, men vi rekommenderar att distributionen tar ungefär 20 minuter att slutföra. Distributions processen innehåller flera steg som att registrera modellen, generera resurser och konfigurera dem för webb tjänsten.

1. Välj **StackEnsemble** för att öppna den aktuella sidan.

1. Välj knappen **distribuera** som finns i det övre vänstra hörnet på skärmen.

1. Fyll i fönstret **distribuera en modell** enligt följande:

    Fält| Värde
    ----|----
    Distributions namn| bikeshare – distribuera
    Distributions Beskrivning| cykel resurs-distribution efter behov
    Typ av beräkning | Välj Azure Compute Instance (ACI)
    Aktivera autentisering| Inaktivera. 
    Använda anpassade distributions till gångar| Inaktivera. Om du inaktiverar tillåter det att standard driv rutins filen (bedömnings skript) och miljö filen skapas automatiskt. 
    
    I det här exemplet använder vi de standardvärden som anges i menyn *Avancerat* . 

1. Välj **Distribuera**.  

    Ett grönt meddelande visas längst upp på skärmen **Kör** som talar om att distributionen har startats. Du hittar förloppet för distributionen i fönstret **modell Sammanfattning** under **distributions status**.
    
När distributionen har slutförts har du en fungerande webb tjänst för att generera förutsägelser. 

Fortsätt till [**Nästa steg**](#next-steps) om du vill lära dig mer om hur du använder din nya webb tjänst och testa dina förutsägelser med hjälp av Power BI har inbyggd Azure Machine Learning support.

## <a name="clean-up-resources"></a>Rensa resurser

Distributions filer är större än data-och experiment-filer, så att de kostar mer att lagra. Ta bara bort distributions filerna för att minimera kostnaderna till ditt konto, eller om du vill behålla arbets ytan och experimentet. Annars tar du bort hela resurs gruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributions instansen

Ta bara bort distributions instansen från Azure Machine Learning Studio om du vill behålla resurs gruppen och arbets ytan för andra självstudier och utforskningar. 

1. Gå till [Azure Machine Learning Studio](https://ml.azure.com/). Gå till arbets ytan och välj **slut punkter** i fönstret **till** vänster. 

1. Välj den distribution som du vill ta bort och välj **ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använt automatisk ML i Azure Machine Learning Studio för att skapa och distribuera en tids serie prognos modell som förutsäger behovet av cykel resurs uthyrning. 

I den här artikeln finns anvisningar om hur du skapar ett schema för Power BI som stöds för att under lätta användningen av den nyligen distribuerade webb tjänsten:

> [!div class="nextstepaction"]
> [Använda en webbtjänst](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Lär dig mer om [Automatisk maskin inlärning](concept-automated-ml.md).
+ Mer information om klassificerings mått och diagram finns i artikeln [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md) .
+ Läs mer om [funktionalisering](how-to-configure-auto-features.md#featurization).
+ Läs mer om [data profilering](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Den här data uppsättningen för cykel resursen har ändrats för den här självstudien. Den här data uppsättningen har gjorts tillgänglig som en del av en [Kaggle-tävling](https://www.kaggle.com/c/bike-sharing-demand/data) och var ursprungligen tillgänglig via [kapital Bikeshare](https://www.capitalbikeshare.com/system-data). Det kan också finnas i den [Machine Learning databasen med](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Källa: Fanaee-T, Hadi, och Gama, Joao, händelse etiketter som kombinerar Ensemble-detektorer och bakgrunds kunskap, förloppet i artificiell intelligens (2013): s. 1-15, springer Berlin Heidelberg.