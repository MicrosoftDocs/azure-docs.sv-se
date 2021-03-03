---
title: Kom igång med Azure percept Advanced Development lokalt
description: Kom igång med den lokala Machine Learning-utvecklingen med VS Code + Jupyter notebooks på AzureML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665112"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Komma igång med Machine Learning-utveckling med VS Code + Jupyter notebooks på AzureML

Den här artikeln vägleder dig genom processen för att skapa en Azure Machine Learning arbets yta, skapa en beräknings instans, konfigurera en utvecklings miljö för Visual Studio-kod på din lokala dator och köra cellerna i en förkonfigurerad exempel Jupyter Notebook i VS Code.

[Antecknings boken](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) utför överförings inlärning med en förtränad TensorFlow-modell (MobileNetSSDV2Lite) på azureml i python med en anpassad data uppsättning för att identifiera bowls.

Antecknings boken visar hur du startar från [Coco-datauppsättningen](https://cocodataset.org/#home), filtrerar den nedåt till endast klassen av intresse (Bowls) och konverterar den sedan till lämpligt format. Du kan också använda [VoTT 2](https://github.com/microsoft/VoTT) med öppen källkod för att skapa och märka avgränsnings rutor i formatet Pascal VOC.

När du har omtränat modellen på den anpassade data uppsättningen kan modellen distribueras till din Azure percept DK med hjälp av modulens dubbla uppdaterings metod. Sedan kan du kontrol lera modell inferencing genom att Visa direkt RTSP-strömmen från Azure percept-insikten som. Både modell omskolning och distribution utförs i den bärbara datorn via fjärrinstansen.

## <a name="prerequisites"></a>Förutsättningar

- [Azure Machine Learning prenumeration](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure percept DK med Azure percept vision som ansluten](./overview-azure-percept-dk.md)
- [Inspelade Azure PERCEPT DK-funktioner](./quickstart-percept-dk-set-up.md) slutförda

## <a name="download-azure-percept-github-repository"></a>Hämta Azure percept GitHub-lagringsplatsen

1. Gå till [Azure PERCEPT DK GitHub-lagringsplatsen](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Klona lagrings platsen eller ladda ned ZIP-filen. Längst upp på skärmen klickar du på **kod**  ->  **Hämta zip**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub nedladdnings skärm.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Skapa en Azure Machine Learning arbets yta och fjärrinstanser av beräkningen

1. Gå till [Azure Machine Learning Portal](https://ml.azure.com).
1. Välj din katalog, Azure-prenumeration och Machine Learning arbets yta i list menyerna och klicka på **Kom igång**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Skärmen Azure ML kom igång.":::

    Om du inte har en Azure Machine Learning arbets yta ännu klickar du på **skapa en ny arbets yta**. Gör följande på fliken ny webbläsare:

    1. Välj din Azure-prenumeration.
    1. Välj din resursgrupp.
    1. Ange ett namn för din arbets yta.
    1. Välj din region.
    1. Välj din arbets ytas version.
    1. Klicka på **Granska och skapa**.
    1. På nästa sida granskar du dina val och klickar på **skapa**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Skärmen skapa arbets yta i Azure ML.":::

    Vänta några minuter för att skapa arbets ytor. När arbets ytan har skapats visas gröna kryss rutor bredvid resurserna och **distributionen är klar** överst på sidan Machine Learning Services översikt.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Bekräfta skapande av arbets yta." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    När arbets ytan har skapats går du tillbaka till fliken Machine Learning Portal och klickar på **Kom igång**.

1. På Start sidan för Machine Learning-arbetsytan klickar du på **Beräkna** i rutan till vänster.

1. Om det inte finns någon befintlig beräknings instans skapar du en ny processor-eller GPU-beräkning genom att klicka på **ny**. I fönstret **ny beräknings instans** anger du ett **beräknings namn**, väljer en **typ av virtuell dator** och väljer en **storlek på virtuell dator**. Klicka på **Skapa**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Sidan ny instans skapande av beräknings instans.":::

    När du klickar på **skapa** tar det några minuter att skapa beräknings instansen. Din **beräknings** status visar en grön cirkel och **\<your compute name> -körs** när beräknings skapandet har slutförts. Den här beräknings instansen kör Jupyter-servern och kommer att utnyttjas för den här självstudien.

## <a name="visual-studio-code-development-environment-setup"></a>Installation av Visual Studio Code Development Environment

1. Installera de verktyg som krävs.

    1. Alternativ 1:

        Använd [installations programmet för dev-verktyg](./dev-tools-installer.md) för att installera följande paket:

        - Visuell Studio-kod
        - Python 3,5, 3,6 eller 3,7
        - Miniconda3
        - Intel openprodukts Toolkit 2020,2

        Obs! Intel openprodukts Toolkit visas som ett valfritt paket i installations programmet för dev-paket, men det krävs för att arbeta med Azure percept-visionen som i Azure percept DK Development Kit.

    1. Alternativ 2:

        Om du föredrar att inte använda installations programmet för dev-paketet installerar du följande paket manuellt genom att klicka på länkarna nedan och följa de angivna rikt linjerna för att hämta och installera:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 eller 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel openprodukts Toolkit 2020,2](https://docs.openvinotoolkit.org/)

    Obs! Om du redan har den fullständiga Anaconda-distributionen installerad behöver du inte installera Miniconda. Alternativt, om du hellre inte vill använda Anaconda eller Miniconda, kan du skapa en virtuell python-miljö och installera de paket som behövs för att köra din AI-modells utveckling med pip.

1. Starta Visual Studio Code.
1. Konfigurera en data vetenskaps miljö:

    - Alternativ 1 – Anslut till en befintlig eller ny Machine Learning Remote Compute-instans som redan har de granskade ML-paketen. **Det här är det alternativ vi kommer att använda i den här självstudien**.

    - Alternativ 2 – Konfigurera en Conda-miljö på en lokal dator.
        1. Öppna en Anaconda-eller Miniconda-kommando tolk och kör följande kommando för att skapa en miljö med namnet **myenv** med de angivna paketen:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Mer information om hur du skapar och hanterar Anaconda-miljöer finns i [Anaconda-dokumentationen](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Skapa en VS Code-arbets yta:

    1. Skapa en mapp på en lämplig plats som fungerar som din Visual Studio Code-arbetsyta. Namnge min **arbets yta**.
    1. Öppna min **arbets yta** i Visual Studio Code genom att klicka på **Arkiv**  >  **Öppna mapp**  >  **Välj mapp**.
    1. I Utforskaren navigerar du till och väljer [filen Transferlearningusing_SSDLiteV2 Model. ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) från din lokala kopia av Azure percept DK GitHub-lagringsplatsen. Kopiera den här Notebook-filen till mappen min arbets yta.

## <a name="azure-integration-with-visual-studio-code"></a>Azure-integrering med Visual Studio Code

1. Om du inte redan gjort det kan du registrera dig för den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

1. Installera följande Azure-tillägg för VS Code:
    - [Azure Machine Learning-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Tillägget python Insiders. I vs Code går du till **Visa**  ->  **kommando palett**. I paletten kommando anger och väljer **du python: växla till Insiders dagliga kanaler**.
    - [Tillägg för Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Läs in ditt fönster i VS Code när du uppmanas till det.
    - [Tillägget Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Logga in på ditt Azure-konto i Visual Studio Code för att etablera resurser och köra arbets belastningar på Azure.
    1. Öppna paletten kommando i Visual Studio Code genom att välja **Visa**  >  **kommando-palett** på Meny raden.
    1. Ange **Azure: Logga** in på kommando paletten för att starta inloggnings processen.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure-inloggnings skärm." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Aktivera python-tillägget och Azure-kontot genom att klicka på Azure-ikonen till vänster om VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Azure-ikon i VS Code.":::

    1. Öppna den Transferlearningusing_SSDLiteV2-anteckningsboken Model_VSCode. ipynb från mappen min **arbets yta** .
    1. Öppna kommando paletten. Ange och välj **python: Ange lokal eller fjärran sluten Jupyter Server för anslutningar**.
    1. Du bör se en lista över anslutnings alternativ att välja mellan. Välj **Azure ml-beräkning-instanser**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Azure ML Compute instance-alternativ i VS Code.":::

    1. Följ de interaktiva anvisningarna för att välja en prenumeration, en arbets yta och en fjärrinstans av beräkningen. Välj arbets ytan och fjärrberäknings instansen som skapades tidigare i den här självstudien. Du kan också välja att skapa en ny beräknings instans.
    1. När du har valt en beräknings instans uppmanas du att läsa in VS Code-fönstret igen. När du har läst in igen väljer du den **Python 3,6-azureml** kernel. Nu kan du köra någon av cellerna i din antecknings bok. Genom att köra en Notebook-cell instansieras anslutningen mellan din bärbara dator och din beräknings instans. Verktygsfältet för notebook kommer att uppdateras för att återspegla den beräknings instans du arbetar med.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Val av kernel i VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Arbeta med antecknings boken

Du är nu redo att köra antecknings boken för att träna din anpassade Bowl-detektor i VS Code och distribuera den till din devkit. Se till att köra varje cell i antecknings boken individuellt eftersom några av cellerna kräver indataparametrar innan skriptet körs. Celler som kräver indataparametrar kan redige ras direkt i antecknings boken. Om du vill köra en cell klickar du på ikonen spela upp till vänster i cellen:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Cell ikonen för notebook-markering.":::

## <a name="next-steps"></a>Nästa steg

Om du vill ha fler Azure Machine Learning service exempel på bärbara datorer går du till den här [lagrings platsen](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
