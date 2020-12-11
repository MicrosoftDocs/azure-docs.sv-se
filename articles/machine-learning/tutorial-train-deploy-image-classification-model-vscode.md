---
title: 'Självstudie: träna & distribuera modeller: VS Code (för hands version)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en bild klassificerings modell med TensorFlow och tillägget Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9ad96bdb632e134cf3e3a0f82bb97f88c87e72e9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033450"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Träna och distribuera en bild klassificering TensorFlow modell med Azure Machine Learning Visual Studio Code Extension (för hands version)

Lär dig hur du tränar och distribuerar en bild klassificerings modell för att identifiera handskrivna tal med TensorFlow och tillägget Azure Machine Learning Visual Studio Code.

I den här självstudien går du igenom följande aktiviteter:

> [!div class="checklist"]
> * Förstå koden
> * Skapa en arbetsyta
> * Skapa ett experiment
> * Konfigurera dator mål
> * Kör en konfigurations fil
> * Träna en modell
> * Registrera en modell
> * Distribuera en modell

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett kan du registrera dig och prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Installera [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), en lätt, plattforms oberoende kod redigerare.
- Azure Machine Learning Studio Visual Studio Code-tillägg. Installations anvisningar finns i [själv studie kursen installera Azure Machine Learning Visual Studio Code Extension](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Förstå koden

Koden för den här självstudien använder TensorFlow för att träna en modell för maskin inlärning i en bild som kategoriserar Skriv siffrorna från 0-9. Det gör du genom att skapa ett neurala-nätverk som tar pixel värden på 28 px x 28 BPT-bilder som indata och matar ut en lista med 10 sannolikheter, en för varje siffra som klassificeras. Nedan visas ett exempel på hur data ser ut.  

![MNIST siffror](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Hämta koden för den här själv studie kursen genom att ladda ned och zippa upp [vs Code-verktygen för AI-lagringsplats](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) var som helst på datorn.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Det första du behöver göra för att skapa ett program i Azure Machine Learning är att skapa en arbets yta. En arbets yta innehåller resurser för att träna modeller samt de tränade modellerna själva. Mer information finns i [Vad är en arbets yta](./concept-workspace.md). 

1. I aktivitets fältet i Visual Studio Code väljer du **Azure** -ikonen för att öppna vyn Azure Machine Learning.
1. Högerklicka på din Azure-prenumeration och välj **skapa arbets yta**. 
    
    > [!div class="mx-imgBorder"]
    > ![Skapa en arbetsyta](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Som standard genereras ett namn som innehåller datum och tid då filen skapades. Ändra namnet till "TeamWorkspace" i text rutan och tryck på **RETUR**.
1. Välj **skapa en ny resurs grupp**. 
1. Ge resurs gruppen namnet "TeamWorkspace-RG" och tryck på **RETUR**. 
1. Välj en plats för din arbets yta. Vi rekommenderar att du väljer en plats som ligger närmast platsen som du planerar att distribuera din modell. Till exempel "västra USA 2".
1. När du uppmanas att välja typ av arbets yta väljer du **Basic**.

I det här läget görs en begäran till Azure för att skapa en ny arbets yta i ditt konto. Efter några minuter visas den nya arbets ytan i noden prenumeration. 

## <a name="create-an-experiment"></a>Skapa ett experiment

Ett eller flera experiment kan skapas på arbets ytan för att spåra och analysera enskilda modell inlärnings körningar. Körningar kan göras i Azure-molnet eller på den lokala datorn.

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas.
1. Expandera noden prenumeration.
1. Expandera noden **TeamWorkspace** . 
1. Högerklicka på noden **experiment** .
1. Välj **Skapa experiment** på snabb menyn.

    > [!div class="mx-imgBorder"]
    > ![Skapa ett experiment](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Ge experimentet namnet "MNIST" och tryck på **RETUR** för att skapa det nya experimentet. 

Precis som arbets ytor skickas en begäran till Azure för att skapa ett experiment med de tillhandahållna konfigurationerna. Efter några minuter visas det nya experimentet i noden *experiment* i din arbets yta. 

## <a name="configure-compute-targets"></a>Konfigurera beräknings mål

Ett beräknings mål är den beräknings resurs eller miljö där du kör skript och distribuerar utbildade modeller. Mer information finns i dokumentationen för [Azure Machine Learning Compute-mål](./concept-compute-target.md).

Så här skapar du ett beräknings mål:

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas. 
1. Expandera noden prenumeration. 
1. Expandera noden **TeamWorkspace** . 
1. Under noden arbets yta högerklickar du på noden **Compute-kluster** och väljer **skapa beräkning**. 

    > [!div class="mx-imgBorder"]
    > ![Skapa ett beräknings mål](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Välj **Azure Machine Learning Compute (AmlCompute)**. Azure Machine Learning Compute är en hanterad beräknings infrastruktur som gör det möjligt för användaren att enkelt skapa en enda eller flera noder som kan användas med andra användare i din arbets yta.
1. Välj storlek på den virtuella datorn. Välj **Standard_F2s_v2** i listan med alternativ. Storleken på den virtuella datorn påverkar hur lång tid det tar att träna dina modeller. Mer information om storlekar för virtuella datorer finns i [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/sizes.md).
1. Namnge beräkningen "TeamWkspc-com" och tryck på **RETUR** för att skapa din beräkning.

    En fil visas i VS Code med innehåll som liknar det som visas nedan:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": "PT120S"
                }
            }
        }
    }
    ```

1. När du är nöjd med konfigurationen öppnar du paletten kommando genom att välja **visa > kommando palett**.
1. Ange följande kommando i kommando rads verktyget för att spara kör konfigurations filen.

    ```text
    Azure ML: Save and Continue
    ```

Efter några minuter visas det nya beräknings målet i noden *Compute Clusters* i din arbets yta.

## <a name="create-a-run-configuration"></a>Skapa en körnings konfiguration

När du skickar en övnings körning till ett beräknings mål skickar du även den konfiguration som krävs för att köra utbildnings jobbet. Till exempel skriptet som innehåller inlärnings koden och de python-beroenden som behövs för att köra det.

Så här skapar du en körnings konfiguration:

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas. 
1. Expandera noden prenumeration. 
1. Expandera noden **TeamWorkspace-> Compute Clusters** . 
1. Under Compute-noden högerklickar du på noden **TeamWkspc-com** och väljer **skapa kör konfiguration**.

    > [!div class="mx-imgBorder"]
    > ![Skapa en körnings konfiguration](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Ge kör konfigurationen "MNIST-RC" och tryck på **RETUR** för att skapa din körnings konfiguration.
1. Välj sedan **Skapa ny Azure ml-miljö**. Miljöer definierar de beroenden som krävs för att köra skripten.
1. Ge miljön namnet "MNIST-kuvert" och tryck på **RETUR**.
1. Välj **Conda-beroende fil** i listan.
1. Tryck på **RETUR** för att bläddra i filen med Conda-beroenden. I det här fallet är beroende filen `env.yml` filen i `vscode-tools-for-ai/mnist-vscode-docs-sample` katalogen.

    En fil visas i VS Code med innehåll som liknar det som visas nedan:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. När du är nöjd med konfigurationen sparar du den genom att öppna kommando-paletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

1. Det här exemplet använder inte någon data uppsättning som registrerats i Azure Machine Learning. I stället läses den in när *Train.py* körs. När du uppmanas att skapa en data referens för din utbildning, anger du "n" i prompten och trycker på **RETUR**.
1. Tryck på **RETUR** för att bläddra i skript filen som ska köras på beräkningen. I det här fallet är skriptet för att träna modellen en `train.py` fil inuti `vscode-tools-for-ai/mnist-vscode-docs-sample` katalogen.

    En fil som heter `MNIST-rc.runconfig` visas i vs Code med innehåll som liknar den nedan:

    ```json
    {
        "script": "train.py",
        "arguments": [],
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. När du är nöjd med konfigurationen sparar du den genom att öppna kommando-paletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

`MNIST-rc`Körnings konfigurationen läggs till i *TeamWkspc-com-Compute-* noden och `MNIST-env` miljö konfigurationen läggs till i noden *miljöer* .

## <a name="train-the-model"></a>Träna modellen

Under inlärnings processen skapas en TensorFlow-modell genom bearbetning av utbildnings data och inlärnings mönster som är inbäddade i den för var och en av de respektive siffrorna klassificerade. 

Köra ett Azure Machine Learning experiment:

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas. 
1. Expandera noden prenumeration. 
1. Expandera noden **TeamWorkspace > experiment** . 
1. Högerklicka på **MNIST** -experimentet.
1. Välj **Kör experiment**.

    > [!div class="mx-imgBorder"]
    > ![Köra ett experiment](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. I listan med alternativ för beräknings mål väljer du **TeamWkspc-com** Compute Target.
1. Välj sedan konfiguration av **MNIST-RC-** körning.
1. I det här läget skickas en begäran till Azure för att köra experimentet på det valda beräknings målet i din arbets yta. Den här processen tar flera minuter. Hur lång tid det får att köra utbildnings jobbet påverkas av flera faktorer, till exempel data storlek för beräknings typ och träning. Du kan följa förloppet för ditt experiment genom att högerklicka på den aktuella körnings noden och välja **Visa körning i Azure Portal**.
1. När dialog rutan för att begära att öppna en extern webbplats visas väljer du **Öppna**.

    > [!div class="mx-imgBorder"]
    > ![Spåra experiment förlopp](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

När modellen är färdig med träningen uppdateras status etiketten bredvid noden slutförs.

## <a name="register-the-model"></a>Registrera modellen

Nu när du har tränat din modell kan du registrera den i din arbets yta. 

Så här registrerar du din modell:

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas.
1. Expandera noden prenumeration. 
1. Expandera noden **TeamWorkspace > experiment > MNIST** .
1. Hämta modellens utdata som genereras från att träna modellen. Högerklicka på körnings-noden kör **1** och välj **Hämta utdata**. 

    > [!div class="mx-imgBorder"]
    > ![Ladda ned modell utdata](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Välj den katalog där du vill spara de hämtade utdata till. Som standard placeras utmatningarna i den katalog som för närvarande är öppnad i Visual Studio Code.
1. Högerklicka på noden **modeller** och välj **Registrera modell**.

    > [!div class="mx-imgBorder"]
    > ![Registrera en modell](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Namnge din modell "MNIST-TensorFlow-Model" och tryck på **RETUR**.
1. En TensorFlow-modell består av flera filer. Välj **modell-mapp** som modell Sök vägs format i listan med alternativ. 
1. Välj `azureml_outputs/Run_1/outputs/outputs/model` katalogen.

    En fil som innehåller dina modell konfigurationer visas i Visual Studio Code med liknande innehåll som du ser nedan:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. När du är nöjd med konfigurationen sparar du den genom att öppna kommando-paletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

Efter några minuter visas modellen under noden *modeller* .

## <a name="deploy-the-model"></a>Distribuera modellen

I Visual Studio Code kan du distribuera din modell som en webb tjänst för att:

+ Azure Container Instances (ACI).
+ Azure Kubernetes service (AKS).

Du behöver inte skapa en ACI-behållare för att testa i förväg eftersom ACI-behållare skapas vid behov. Du måste dock konfigurera AKS-kluster i förväg. Mer information om distributions alternativ finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md) .

Så här distribuerar du en webb tjänst som en ACI:

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Vyn Azure Machine Learning visas.
1. Expandera noden prenumeration. 
1. Expandera noden **TeamWorkspace > Models** . 
1. Högerklicka på **MNIST-TensorFlow-Model** och välj **distribuera tjänst från registrerad modell**.

    > [!div class="mx-imgBorder"]
    > ![Distribuera modellen](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Välj **Azure Container instances**.
1. Ge din tjänst namnet "mnist-tensorflow-SVC" och tryck på **RETUR**.
1. Välj det skript som ska köras i behållaren genom att trycka på **RETUR** i rutan indata och bläddra efter `score.py` filen i `mnist-vscode-docs-sample` katalogen.
1. Ange de beroenden som krävs för att köra skriptet genom att trycka på **RETUR** i rutan indata och bläddra efter `env.yml` filen i `mnist-vscode-docs-sample` katalogen.

    En fil som innehåller dina modell konfigurationer visas i Visual Studio Code med liknande innehåll som du ser nedan:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```

1. När du är nöjd med konfigurationen sparar du den genom att öppna kommando-paletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

I det här läget skickas en begäran till Azure för att distribuera din webb tjänst. Den här processen tar flera minuter. När den nya tjänsten har distribuerats visas den under noden *slut punkter* .

## <a name="next-steps"></a>Nästa steg

* En genom gång av hur du tränar med Azure Machine Learning utanför Visual Studio Code finns i [Självstudier: träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).
* En genom gång av hur du kan redigera, köra och felsöka kod lokalt finns i [själv studie kursen om python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).