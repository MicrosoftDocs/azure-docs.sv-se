---
title: Kom igång med Azure percept Advanced Development i molnet
description: Kom igång med avancerad utveckling i molnet via Jupyter-anteckningsböcker och Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665055"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Komma igång med avancerad utveckling i molnet via Jupyter-anteckningsböcker och Azure Machine Learning

Den här artikeln vägleder dig genom processen för att skapa en Azure Machine Learning arbets yta, överföra ett förkonfigurerat exempel Jupyter Notebook till arbets ytan, skapa en beräknings instans och köra cellerna i antecknings boken i arbets ytan.

[Antecknings boken](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) utför överförings inlärning med en förtränad TensorFlow-modell (MobileNetSSDV2Lite) på azureml i python med en anpassad data uppsättning för att identifiera bowls.

Antecknings boken visar hur du startar från [Coco](https://cocodataset.org/#home), filtrerar den nedåt till endast klassen av intresse (Bowls) och konverterar den sedan till lämpligt format. Du kan också använda [VoTT 2](https://github.com/microsoft/VoTT) med öppen källkod för att skapa och märka avgränsnings rutor i formatet Pascal VOC.

Efter att ha tränat om modellen på den anpassade data uppsättningen kan modellen distribueras till din Azure percept DK med hjälp av modulens dubbla uppdaterings metod.

Sedan kan du kontrol lera modell inferencing genom att Visa direkt RTSP-strömmen från Azure percept-insikten som. Både modell omskolning och distribution utförs i den bärbara datorn i molnet.

## <a name="prerequisites"></a>Förutsättningar

- [Azure Machine Learning prenumeration](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure percept DK med Azure percept vision som ansluten](./overview-azure-percept-dk.md)
- [Inspelade Azure PERCEPT DK-funktioner](./quickstart-percept-dk-set-up.md) slutförda

## <a name="download-azure-percept-github-repository"></a>Hämta Azure percept GitHub-lagringsplatsen

1. Gå till [Azure percept GitHub-lagringsplatsen](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Klona lagrings platsen eller ladda ned ZIP-filen. Längst upp på skärmen klickar du på **kod**  ->  **Hämta zip**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub nedladdnings skärm.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Konfigurera Azure Machine Learning Portal och Notebook

1. Gå till [Azure Machine Learning Portal](https://ml.azure.com).

1. Välj din katalog, Azure-prenumeration och Machine Learning arbets yta i list menyerna och klicka på **Kom igång**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Skärmen Azure ML kom igång.":::

    Om du inte har en Azure Machine Learning arbets yta ännu klickar du på **skapa en ny arbets yta**. Gör följande på fliken ny webbläsare:

    1. Välj din Azure-prenumeration.
    1. Välj din resursgrupp.
    1. Ange ett namn för din arbets yta.
    1. Välj din region.
    1. Välj din arbets ytas version.
    1. Klicka på **Granska och skapa**.
    1. På nästa sida granskar du dina val och klickar på **skapa**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Skärmen skapa arbets yta i Azure ML.":::

    Vänta några minuter för att skapa arbets ytor. När arbets ytan har skapats visas gröna kryss rutor bredvid resurserna och **distributionen är klar** överst på sidan Machine Learning Services översikt.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Bekräfta skapande av arbets yta." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    När arbets ytan har skapats går du tillbaka till fliken Machine Learning Portal och klickar på **Kom igång**.

1. På Start sidan för Machine Learning-arbetsytan klickar du på **antecknings böcker** i den vänstra rutan.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Start sida för Azure ML.":::

1. Under fliken **Mina filer** klickar du på den lodräta pilen för att ladda upp din. ipynb-fil.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Start sidan som markerar ikonen Ladda upp fil.":::

1. Navigera till och välj [filen Transferlearningusing_SSDLiteV2 Model. ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) från din lokala kopia av Azure percept GitHub-lagringsplatsen. Klicka på **Öppna**. I fönstret **Ladda upp filer** markerar du kryss rutan bredvid **Jag litar på innehållet från den här filen** och klickar på **överför**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Skärm för val av fil.":::

1. I den övre högra meny raden kontrollerar du din **beräknings** status. Om inga beräkningar hittas klickar du på **+** ikonen för att skapa en ny beräkning.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Beräkna status med + ikon markerad." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. I fönstret **ny beräknings instans** anger du ett **beräknings namn**, väljer en **typ av virtuell dator** och väljer en **storlek på virtuell dator**. Klicka på **Skapa**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Sidan ny instans skapande av beräknings instans.":::

    När du klickar på **skapa** visas en blå cirkel och **\<your compute name> skapa** i din **beräknings** status

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Beräknings status när beräknings skapande fortfarande pågår.":::

    Din **beräknings** status visar en grön cirkel och **\<your compute name> -körs** när beräknings skapandet har slutförts.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="Beräknings status som visar att skapandet har skapats har slutförts.":::

1. När data bearbetningen körs väljer du **Python 3,6-azureml-** kärnan på den nedrullningsbara menyn bredvid **+** ikonen.

## <a name="working-with-the-notebook"></a>Arbeta med antecknings boken

Du är nu redo att köra antecknings boken för att träna din anpassade Bowl-detektor och distribuera den till din devkit. Se till att köra varje cell i antecknings boken individuellt eftersom några av cellerna kräver indataparametrar innan skriptet körs. Celler som kräver indataparametrar kan redige ras direkt i antecknings boken. Om du vill köra en cell klickar du på ikonen spela upp till vänster i cellen:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Cell ikonen för notebook-markering." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Nästa steg

Om du vill ha fler Azure Machine Learning service exempel på bärbara datorer går du till den här [lagrings platsen](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
