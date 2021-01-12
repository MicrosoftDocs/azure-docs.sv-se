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
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121162"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Självstudie: träna och distribuera en Azure Machine Learning modell

I den här artikeln utför vi följande uppgifter:

* Använd Azure Machine Learning Studio för att träna en maskin inlärnings modell.
* Paketera den tränade modellen som en behållar avbildning.
* Distribuera behållar avbildningen som en Azure IoT Edge modul.

Azure Machine Learning Studio är ett grundläggande block som används för att experimentera, träna och distribuera maskin inlärnings modeller.

Stegen i den här artikeln kan vanligt vis utföras av data experter.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa Jupyter-anteckningsböcker i Azure Machine Learning-arbetsyta för att träna en maskin inlärnings modell.
> * Använd den tränade Machine Learning-modellen.
> * Skapa en Azure IoT Edge-modul från maskin inlärnings modellen för behållare.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du gå till den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="set-up-azure-machine-learning"></a>Konfigurera Azure Machine Learning 

Vi använder Azure Machine Learning Studio för att vara värd för de två Jupyter-Anteckningsbokarna och de filer som stöds. Här skapar och konfigurerar vi ett Azure Machine Learning-projekt. Om du inte har använt Jupyter och/eller Azure Machine Learning Studio, är här några introduktions dokument:

* **Jupyter Notebook:** [arbeta med Jupyter-anteckningsböcker i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [kom igång med Azure Machine Learning i Jupyter Notebooks](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> När du har konfigurerat tjänsten kan du använda tjänsten Azure Machine Learning från vilken dator som helst. Under installationen bör du använda den virtuella utvecklings datorn som innehåller alla filer som du behöver.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Installera Azure Machine Learning Visual Studio Code-tillägg
VS Code på den virtuella utvecklings datorn ska ha det här tillägget installerat. Om du kör på en annan instans måste du installera om tillägget enligt beskrivningen [här.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Skapa ett Azure Machine Learning konto  
För att kunna etablera resurser och köra arbets belastningar på Azure måste du logga in med dina autentiseringsuppgifter för Azure-kontot.

1. Öppna paletten kommando i Visual Studio Code genom att välja **Visa**  >  **kommando palett** på Meny raden. 

1. `Azure: Sign In`Starta inloggnings processen genom att ange kommandot i kommando paletten. Följ anvisningarna för att slutföra inloggningen. 

1. Skapa en Azure ML-beräkning-instans för att köra arbets belastningen. Med kommandots lastpall anger du kommandot `Azure ML: Create Compute` . 
1. Välj din Azure-prenumeration
1. Välj **+ Skapa ny Azure ml-arbetsyta** och ange namn `turbofandemo` .
1. Välj den resurs grupp som du har använt för den här demon.
1. Du bör kunna se förloppet för att skapa arbets ytor i det nedre högra hörnet av ditt VS Code-fönster: **skapa arbets yta: turobofandemo** (det kan ta en minut eller två). 
1. Vänta tills arbets ytan har skapats. Det bör stå att **turbofandemo för Azure ml-arbetsytan skapas**.


### <a name="upload-jupyter-notebook-files"></a>Ladda upp Jupyter Notebook-filer

Vi kommer att ladda upp exempel på notebook-filer till en ny Azure ML-arbetsyta.

1. Gå till ml.azure.com och logga in.
1. Välj din Microsoft-katalog, Azure-prenumeration och den nyligen skapade Azure ML-arbetsytan.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Välj din Azure ML-arbetsyta." :::

1. När du har loggat in på Azure ML-arbetsytan navigerar du till avsnittet **antecknings böcker** med hjälp av den vänstra sidan.
1. Välj fliken **Mina filer** .

1. Välj **Ladda upp** (uppåtpilen) 


1. Navigera till **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Markera alla filer i listan och klicka på **Öppna**.

1. Markera kryss rutan **Jag litar på innehållet i dessa filer** .

1. Välj **Ladda upp** för att börja ladda upp och välj sedan **klart** när processen är klar.

### <a name="jupyter-notebook-files"></a>Jupyter Notebook filer

Vi går igenom filerna som du laddade upp i din Azure ML-arbetsyta. Aktiviteterna i den här delen av själv studie kursen sträcker sig över två notebook-filer, som använder några få stödfiler.

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

## <a name="run-jupyter-notebooks"></a>Kör Jupyter Notebooks

Nu när arbets ytan har skapats kan du köra antecknings böckerna. 

1. Från sidan **Mina filer** väljer du **01-turbofan \_ regression. ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Välj den första antecknings boken som ska köras. ":::

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

1. På fliken **Mina filer** på din Azure ml-anteckningsbok väljer du **Uppdatera**.

1. Kontrol lera att följande filer har skapats:

    | Fil | Description |
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
    | Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Felsökning

Du kan infoga python-instruktioner i antecknings boken för fel sökning, till exempel `print()` kommandot för att visa värden. Om du ser variabler eller objekt som inte har definierats kör du cellerna där de först deklareras eller instansieras.

Du kanske måste ta bort tidigare skapade filer och Azure-resurser om du behöver göra om antecknings böckerna.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi två Jupyter-anteckningsböcker som körs i Azure ML Studio för att använda data från turbofan-enheter för att träna en kvarvarande RUL-klassificerare () för att spara klassificeraren som en modell, för att skapa en behållar avbildning och för att distribuera och testa avbildningen som en webb tjänst.

Fortsätt till nästa artikel om du vill skapa en IoT Edge enhet.

> [!div class="nextstepaction"]
> [Konfigurera en IoT Edge-enhet](tutorial-machine-learning-edge-05-configure-edge-device.md)