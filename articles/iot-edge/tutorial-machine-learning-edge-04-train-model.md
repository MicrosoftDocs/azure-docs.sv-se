---
title: 'Självstudie: träna och distribuera en modell Machine Learning på Azure IoT Edge'
description: I den här självstudien kommer du att träna en maskin inlärnings modell med Azure Machine Learning och sedan paketera modellen som en behållar avbildning som kan distribueras som en Azure IoT Edge modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 757e34fd45b7d3d9703aa09daa7f040c5f605637
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932395"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Självstudie: träna och distribuera en Azure Machine Learning modell

I den här artikeln utför vi följande uppgifter:

* Använd Azure Notebooks för att träna en maskin inlärnings modell.
* Paketera den tränade modellen som en behållar avbildning.
* Distribuera behållar avbildningen som en Azure IoT Edge modul.

Azure Notebooks dra nytta av en Azure Machine Learning arbets yta, ett grundläggande block som används för att experimentera, träna och distribuera maskin inlärnings modeller.

Stegen i den här artikeln kan vanligt vis utföras av data experter.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Skapa ett Azure Notebooks-projekt för att träna en maskin inlärnings modell.
> * Använd den tränade Machine Learning-modellen.
> * Skapa en Azure IoT Edge-modul från maskin inlärnings modellen för behållare.

## <a name="prerequisites"></a>Krav

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du gå till den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="set-up-azure-notebooks"></a>Konfigurera Azure Notebooks

Vi använder Azure Notebooks för att vara värd för de två Jupyter-Anteckningsbokarna och de filer som stöds. Här skapar och konfigurerar vi ett Azure Notebooks-projekt. Om du inte har använt Jupyter och/eller Azure Notebooks, är här några introduktions dokument:

* **Snabb start:** [skapa och dela en bärbar dator](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Självstudie:** [skapa och kör en Jupyter-anteckningsbok med python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Att använda Azure Notebooks garanterar en konsekvent miljö för övningen.

> [!NOTE]
> När du har konfigurerat tjänsten kan du använda tjänsten Azure Notebooks från vilken dator som helst. Under installationen bör du använda den virtuella utvecklings datorn som innehåller alla filer som du behöver.

### <a name="create-an-azure-notebooks-account"></a>Skapa ett Azure Notebooks konto

Om du vill använda Azure Notebooks måste du skapa ett konto. Azure Notebook-konton är oberoende av Azure-prenumerationer.

1. Navigera till [Azure Notebooks](https://notebooks.azure.com).

1. Klicka på **Logga** in i det övre högra hörnet på sidan.

1. Logga in med ett arbets-eller skol konto (Azure Active Directory) eller ditt personliga konto (Microsoft-konto).

1. Om du inte har använt Azure Notebooks tidigare uppmanas du att bevilja åtkomst till Azure Notebooks-appen.

1. Skapa ett användar-ID för Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Ladda upp Jupyter notebook-filer

Vi kommer att ladda upp exempel på notebook-filer till ett nytt Azure Notebooks-projekt.

1. På sidan användare i ditt nya konto väljer du **Mina projekt** i den översta meny raden.

1. Lägg till ett nytt projekt genom att välja **+** knappen.

1. Ange ett **projekt namn** i dialog rutan **Skapa nytt projekt** . 

1. Lämna **offentligt** och **viktigt** omarkerat eftersom det inte behövs något behov av att projektet ska vara offentligt eller ha ett viktigt.

1. Välj **Skapa**.

1. Välj **Ladda upp** (uppåtpilen) och välj **från dator**.

1. Välj **Välj filer**.

1. Navigera till **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Markera alla filer i listan och klicka på **Öppna**.

1. Markera kryss rutan **Jag litar på innehållet i dessa filer** .

1. Välj **Ladda upp** för att börja ladda upp och välj sedan **klart** när processen är klar.

### <a name="azure-notebook-files"></a>Azure notebook-filer

Vi går igenom filerna som du laddade upp i Azure Notebooks-projektet. Aktiviteterna i den här delen av själv studie kursen sträcker sig över två notebook-filer, som använder några få stödfiler.

* **01-turbofan \_ regression. ipynb:** den här antecknings boken använder arbets ytan Machine Learning tjänst för att skapa och köra ett Machine Learning-experiment. I stort sett gör antecknings boken följande steg:

  1. Hämtar data från det Azure Storage konto som genererades av enhets nätet.
  1. Utforskar och förbereder data och använder sedan data för att träna klassificerings modellen.
  1. Utvärdera modellen från experimentet med hjälp av en test data uppsättning (test \_FD003.txt).
  1. Publicerar den bästa klassificerings modellen till arbets ytan för Machine Learnings tjänsten.

* **02-turbofan \_ distribuera \_ modell. ipynb:** den här Notebook tar modellen som skapats i den tidigare antecknings boken och använder den för att skapa en behållar avbildning som kan distribueras till en Azure IoT Edge enhet. Antecknings boken utför följande steg:

  1. Skapar ett bedömnings skript för modellen.
  1. Skapar en behållar avbildning med hjälp av klassificerings modellen som sparades i arbets ytan Machine Learning tjänst.
  1. Distribuerar avbildningen som en webb tjänst på Azure Container instance.
  1. Använder webb tjänsten för att verifiera modellen och avbildningen fungerar som förväntat. Den verifierade avbildningen distribueras till vår IoT Edge enhet i avsnittet [skapa och distribuera anpassade IoT Edge moduler](tutorial-machine-learning-edge-06-custom-modules.md) i den här självstudien.

* **Test \_FD003.txt:** den här filen innehåller de data som vi ska använda som vår test uppsättning när du verifierar vår utbildade klassificerare. Vi valde att använda test data, enligt vad som anges för den ursprungliga tävlingen, som vår test uppsättning för dess enkelhet.

* **RUL \_FD003.txt:** den här filen innehåller återstående livs längd (RUL) för den senaste cykeln för varje enhet i test \_FD003.txts filen. Se readme.txt och Modeling.pdf filer för skada i avsnittet C: \\ Source \\ IoTEdgeAndMlSample \\ data \\ turbofan för en detaljerad förklaring av data.

* **Utils.py:** Innehåller en uppsättning python-verktyg för att arbeta med data. Den första antecknings boken innehåller en detaljerad förklaring av funktionerna.

* **Readme.MD:** README som beskriver hur du använder antecknings böckerna.  

## <a name="run-azure-notebooks"></a>Kör Azure Notebooks

Nu när projektet har skapats kan du köra antecknings böckerna. 

1. Från projekt sidan väljer du **01-turbofan \_ regression. ipynb**.

    ![Välj den första notebook som ska köras](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Om antecknings boken **inte är betrodd** klickar du på widgeten **ej betrodd** överst till höger i antecknings boken. Välj **förtroende** när dialog rutan öppnas.

1. För bästa resultat bör du läsa dokumentationen för varje cell och köra den individuellt. Välj **Kör** i verktygsfältet. Senare är det viktigt att köra flera celler. Du kan ignorera uppgraderingar och utfasnings varningar.

    När en cell körs visas en asterisk mellan hakparenteserna ([ \* ]). När cellens åtgärd är klar ersätts asterisken med ett tal och relevanta utdata kan visas. Cellerna i en bärbar dator skapas i turordning och bara en kan köras i taget.

    Du kan också använda körnings alternativ från **cell** -menyn `Ctrl`  +  `Enter` för att köra en cell och `Shift`  +  `Enter` köra en cell och gå vidare till nästa cell.

    > [!TIP]
    > Undvik att köra samma bärbara dator från flera flikar i webbläsaren för konsekventa cell åtgärder.

1. I cellen som följer anvisningarna för att **ange globala egenskaper** skriver du in värdena för din Azure-prenumeration, dina inställningar och resurser. Kör sedan cellen.

    ![Ange globala egenskaper i antecknings boken](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. I cellen tidigare till **arbets ytans information**, efter att den har körts, letar du efter länken som uppmanar dig att logga in för att autentisera:

    ![Inloggnings varning för enhetsautentisering](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Öppna länken och ange den angivna koden. Den här inloggnings proceduren autentiserar Jupyter Notebook för att få åtkomst till Azure-resurser med hjälp av kommando rads gränssnittet Microsoft Azure plattforms oberoende plattform.  

    ![Bekräfta autentisering av program vid enhet](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopiera värdet från körnings-ID: t i cellen som föregår **resultaten** och klistra in det för körnings-ID: t i cellen som följer **rekonstruerar en körning**.

   ![Kopiera körnings-ID: t mellan celler](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Kör de återstående cellerna i antecknings boken.

1. Spara antecknings boken och gå tillbaka till projekt sidan.

1. Öppna **02-turbofan \_ distribuera \_ Model. ipynb** och kör varje cell. Du måste logga in för att autentisera i cellen som följer **Konfigurera arbets yta**.

1. Spara antecknings boken och gå tillbaka till projekt sidan.

### <a name="verify-success"></a>Verifieringen lyckades

Kontrol lera att antecknings böckerna har slutförts genom att kontrol lera att några objekt har skapats.

1. På sidan Azure Notebooks projekt väljer du **Visa dolda objekt** så att objekt namn som börjar med en punkt visas.

1. Kontrol lera att följande filer har skapats:

    | Fil | Beskrivning |
    | --- | --- |
    | ./aml_config/.azureml/config.jspå | Konfigurations fil som används för att skapa Azure Machine Learning-arbetsyta. |
    | ./aml_config/model_config.jspå | Konfigurations fil som vi behöver för att distribuera modellen i **turbofanDemo** Machine Learning-arbetsytan i Azure. |
    | myenv. yml| Innehåller information om beroenden för den distribuerade Machine Learnings modellen.|

1. Kontrol lera att följande Azure-resurser har skapats. Vissa resurs namn läggs till med slumpmässiga tecken.

    | Azure-resurs | Name |
    | --- | --- |
    | Machine Learning arbets yta | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Program insikter | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Lagring | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Felsökning

Du kan infoga python-instruktioner i antecknings boken för fel sökning, till exempel `print()` kommandot för att visa värden. Om du ser variabler eller objekt som inte har definierats kör du cellerna där de först deklareras eller instansieras.

Du kanske måste ta bort tidigare skapade filer och Azure-resurser om du behöver göra om antecknings böckerna.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi två Jupyter-anteckningsböcker som körs i Azure Notebooks för att använda data från turbofan-enheter för att träna en återstående RUL-klassificerare () för att spara klassificeraren som en modell, för att skapa en behållar avbildning och för att distribuera och testa avbildningen som en webb tjänst.

Fortsätt till nästa artikel om du vill skapa en IoT Edge enhet.

> [!div class="nextstepaction"]
> [Konfigurera en IoT Edge-enhet](tutorial-machine-learning-edge-05-configure-edge-device.md)
