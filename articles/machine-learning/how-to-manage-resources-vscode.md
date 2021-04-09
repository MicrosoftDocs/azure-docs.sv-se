---
title: Skapa och hantera resurser VS Code Extension (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och hanterar Azure Machine Learning-resurser med hjälp av Azure Machine Learning kod tillägget för Visual Studio.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010681"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Hantera Azure Machine Learning-resurser med VS Code-tillägget (förhands granskning)

Lär dig hur du hanterar Azure Machine Learning-resurser med VS Code-tillägget.

![Azure Machine Learning VS Code-tillägg](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett kan du registrera dig och prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Om du inte har det kan du [installera det](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code Azure Machine Learning-tillägg. Följ [installations guiden för Azure Machine Learning vs Code Extension](tutorial-setup-vscode-extension.md#install-the-extension) för att installera tillägget.

Alla processer nedan förutsätter att du är i vyn Azure Machine Learning i Visual Studio Code. Om du vill starta tillägget väljer du **Azure** -ikonen i AKTIVITETS fältet vs-kod.

## <a name="workspaces"></a>Arbetsytor

Mer information finns i [arbets ytor](concept-workspace.md).

### <a name="create-a-workspace"></a>Skapa en arbetsyta

1. I vyn Azure Machine Learning högerklickar du på noden prenumeration och väljer **skapa arbets yta**.
1. I prompten:
    1. Ange ett namn för din arbets yta
    1. Välj en Azure-prenumeration
    1. Välj eller skapa en ny resurs grupp för att etablera arbets ytan i
    1. Välj den plats där arbets ytan ska etableras.

Alternativa metoder för att skapa en arbets yta är:

- Öppna vyn för kommandosyntaxen **> kommando paletten** och ange text rutan **Azure ml: skapa arbets yta**.
- Klicka på `+` ikonen överst i vyn Azure Machine Learning.
- Skapa en ny arbets yta när du uppmanas att välja en arbets yta under etableringen av andra resurser.

### <a name="remove-a-workspace"></a>Ta bort en arbets yta

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Högerklicka på arbets ytan som du vill ta bort.
1. Välj om du vill ta bort:
    - *Endast arbets ytan*: det här alternativet tar **bara** bort arbets ytans Azure-resurs. Resurs gruppen, lagrings kontona och alla andra resurser som arbets ytan var kopplad till är fortfarande i Azure.
    - *Med associerade resurser*: det här alternativet tar bort arbets ytan **och** alla resurser som är kopplade till den.

## <a name="datastores"></a>Datalager

VS Code-tillägget stöder för närvarande data lager av följande typer:

- Azure-filresurs
- Azure Blob Storage

När du skapar en arbets yta skapas ett data lager för var och en av dessa typer.

Mer information finns i [data lager](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Skapa ett data lager

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta som du vill skapa data lagret under.
1. Högerklicka på noden **data lager** och välj **registrera data lager**.
1. I prompten:
    1. Ange ett namn för ditt data lager.
    1. Välj typ av data lager.
    1. Välj din lagrings resurs. Du kan antingen välja en lagrings resurs som är kopplad till din arbets yta eller välja från en giltig lagrings resurs i dina Azure-prenumerationer.
    1. Välj den behållare där dina data finns i den tidigare valda lagrings resursen.
1. En konfigurations fil visas i VS Code. Om du är nöjd med konfigurations filen väljer du **Spara och fortsätter** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="manage-a-datastore"></a>Hantera ett data lager

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Expandera noden **data lager** i din arbets yta.
1. Välj det data lager du vill:
    - *Ange som standard*. När du kör experiment är detta det data lager som ska användas.
    - *Granska skrivskyddade inställningar*.
    - *Modify* (Ändra). Ändra autentiseringstyp och autentiseringsuppgifter. De autentiseringstyper som stöds är konto nyckel och SAS-token.

## <a name="datasets"></a>Datauppsättningar

Tillägget stöder för närvarande följande typer av data uppsättningar:

- *Tabell*: låter dig materialisera data till en DataFrame (Pandas eller PySpark).
- *Fil*: en fil eller samling av filer. Gör att du kan hämta eller montera filer till din beräkning.

Mer information finns i [data uppsättningar](concept-data.md#datasets)

### <a name="create-dataset"></a>Skapa datauppsättning

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta som du vill skapa data lagret under.
1. Högerklicka på noden **data uppsättningar** och välj **skapa data uppsättning**.
1. I prompten:
    1. Välj data uppsättnings typ
    1. Ange om data finns på din dator eller på webben
    1. Ange platsen för dina data. Detta kan antingen vara en enskild fil eller en katalog som innehåller dina datafiler.
    1. Välj det data lager du vill överföra data till.
    1. Ange ett prefix som hjälper dig att identifiera din data uppsättning i data lagret.

### <a name="version-datasets"></a>Versions data uppsättningar

När du skapar Machine Learning-modeller, som data ändringar, kan du behöva version av din data uppsättning. För att göra det i VS Code-tillägget:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Expandera noden **data uppsättningar** .
1. Högerklicka på den data uppsättning som du vill skapa version för och välj **Skapa ny version**.
1. I prompten:
    1. Välj data uppsättnings typ
    1. Ange om data finns på din dator eller på webben.
    1. Ange platsen för dina data. Detta kan antingen vara en enskild fil eller en katalog som innehåller dina datafiler.
    1. Välj det data lager du vill överföra data till.
    1. Ange ett prefix som hjälper dig att identifiera din data uppsättning i data lagret.

### <a name="view-dataset-properties"></a>Visa egenskaper för data mängd

Med det här alternativet kan du se metadata som är associerade med en angiven data uppsättning. För att göra det i VS Code-tillägget:

1. Expandera noden arbets yta.
1. Expandera noden **data uppsättningar** .
1. Högerklicka på den data uppsättning som du vill granska och välj **Visa data uppsättnings egenskaper**. Då visas en konfigurations fil med egenskaperna för den senaste data uppsättnings versionen.

> [!NOTE]
> Om du har flera versioner av din data uppsättning kan du välja att endast visa egenskaperna för data uppsättningen för en speciell version genom att expandera noden data uppsättning och utföra samma steg som beskrivs i det här avsnittet om versionen av intresse.

### <a name="unregister-datasets"></a>Avregistrera data uppsättningar

Ta bort en data uppsättning och alla versioner av den genom att avregistrera den. För att göra det i VS Code-tillägget:

1. Expandera noden arbets yta.
1. Expandera noden **data uppsättningar** .
1. Högerklicka på den data uppsättning som du vill avregistrera och välj **avregistrera data uppsättning**.

## <a name="environments"></a>Miljöer

Mer information finns i [miljöer](concept-environments.md).

### <a name="create-environment"></a>Skapa miljö

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta som du vill skapa data lagret under.
1. Högerklicka på noden **miljöer** och välj **skapa miljö**.
1. I prompten:
    1. Ange ett namn för din miljö
    1. Definiera din miljö konfiguration:
        - *Granskade miljöer*: förkonfigurerade miljöer i Azure Machine Learning. Du kan anpassa miljön ytterligare genom att ändra `dependencies` egenskapen i JSON-filen. Läs mer om [granskade miljöer](resource-curated-environments.md).
        - *Conda-beroende fil*: för Anaconda-miljöer kan filen som innehåller din miljö definition tillhandahållas.
        - *Pip-krav fil*: för pip-miljöer kan filen som innehåller din miljö definition tillhandahållas.
        - *Befintlig Conda-miljö*: det här alternativet söker efter Conda-miljöer i din lokala dator och försöker bygga en miljö från den valda miljön.
        - *Anpassad*: definiera egna kanaler och beroenden
    1. En konfigurations fil öppnas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="view-environment-configurations"></a>Visa miljö konfigurationer

Visa beroenden och konfigurationer för en speciell miljö i tillägget:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Expandera noden **miljöer** .
1. Högerklicka på den miljö som du vill visa och välj **Visa miljö**.

### <a name="edit-environment-configurations"></a>Redigera miljö konfigurationer

Redigera beroenden och konfigurationer för en speciell miljö i tillägget:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **miljöer** i din arbets yta.
1. Högerklicka på den miljö som du vill visa och välj **Redigera miljö**.
1. När du har gjort ändringarna, om du är nöjd med konfigurationen, väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

## <a name="experiments"></a>Experiment

Mer information finns i [experiment](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Skapa experiment

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Högerklicka på noden **experiment** i arbets ytan och välj **Skapa experiment**.
1. Ange ett namn på experimentet i prompten.

### <a name="run-experiment"></a>Kör experiment

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **experiment** i din arbets yta.
1. Högerklicka på det experiment som du vill köra.
1. Välj **Kör experiment** -ikonen i aktivitets fältet.
1. Välj om du vill köra experimentet lokalt eller via fjärr anslutning. Se [fel söknings guiden](how-to-debug-visual-studio-code.md) för mer information om att köra och felsöka experiment lokalt.
1. Välj din prenumeration.
1. Välj Azure-ML-arbetsyta för att köra experimentet under.
1. Välj experimentet.
1. Välj eller skapa en beräkning för att köra experimentet på.
1. Välj eller skapa en körnings konfiguration för experimentet.

Alternativt kan du välja knappen **Kör experiment** överst i tillägget och konfigurera experimentet som ska köras i prompten.

### <a name="view-experiment"></a>Visa experiment

Så här visar du experimentet i Azure Machine Learning Studio:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **experiment** i din arbets yta.
1. Högerklicka på det experiment du vill visa och välj **Visa experiment**. 
1. En uppmaning visas där du uppmanas att öppna experiment-URL: en i Azure Machine Learning Studio. Välj **Öppna**.

### <a name="track-run-progress"></a>Spåra körnings förlopp

När du kör experimentet kanske du vill se hur det fortskrider. Spåra förloppet för en körning i Azure Machine Learning Studio från tillägget:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **experiment** i din arbets yta.
1. Expandera den experiment-nod som du vill spåra förloppet för.
1. Högerklicka på Kör och välj **Visa körs i Azure Portal**.
1. En uppmaning visas där du uppmanas att öppna körnings-URL: en i Azure Machine Learning Studio. Välj **Öppna**.

### <a name="download-run-logs--outputs"></a>Hämta körnings loggar & utdata

När en körning har slutförts kanske du vill hämta loggarna och till gångarna, till exempel modellen som genereras som en del av en experiment körning.

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **experiment** i din arbets yta.
1. Expandera den experiment-nod som du vill spåra förloppet för.
1. Högerklicka på kör:
    - Hämta utdata genom att välja **Hämta utdata**.
    - Om du vill hämta loggarna väljer du **hämtnings loggar**.

### <a name="view-run-metadata"></a>Visa körnings-metadata

I tillägget kan du kontrol lera metadata, till exempel den körnings konfiguration som används för körnings-och körnings information.

## <a name="compute-instances"></a>Beräkningsinstanser

Mer information finns i [Compute instances](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Skapa beräknings instans

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta som du vill skapa beräknings instansen under.
1. Högerklicka på noden **Compute instances** och välj **skapa beräknings instans**.
1. I prompten:
    1. Ange ett namn för beräknings instansen.
    1. Välj en storlek på virtuell dator i listan.
    1. Välj om du vill aktivera SSH-åtkomst.
        1. Om du aktiverar SSH-åtkomst måste du också ange den offentliga SSH-nyckeln eller filen som innehåller nyckeln. Mer information finns i [guiden om att skapa och använda SSH-nycklar på Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>Stoppa eller starta om beräknings instans

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute instance** i din arbets yta.
1. Högerklicka på den beräknings instans som du vill stoppa eller starta om och välj **stoppa Compute instance** eller **starta om Compute-instansen** .

### <a name="view-compute-instance-configuration"></a>Visa konfiguration av beräknings instans

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute instance** i din arbets yta.
1. Högerklicka på den beräknings instans som du vill granska och välj **Visa egenskaper för beräknings instans**.

### <a name="delete-compute-instance"></a>Ta bort beräknings instans

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute instance** i din arbets yta.
1. Högerklicka på den beräknings instans som du vill ta bort och välj **ta bort beräknings instans**.

## <a name="compute-clusters"></a>Beräknings kluster

Tillägget stöder följande beräknings typer:

- Azure Machine Learning beräknings kluster
- Azure Kubernetes Service

Mer information finns i [Compute-mål](concept-compute-target.md#train).

### <a name="create-compute"></a>Skapa beräkning

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta som du vill skapa beräknings klustret under.
1. Högerklicka på noden **Compute Clusters** och välj **skapa beräkning**.
1. I prompten:
    1. Välj en beräknings typ
    1. Välj storlek på den virtuella datorn. Läs mer om [storlekar på virtuella datorer](../virtual-machines/sizes.md).
    1. Ange ett namn för din beräkning.

### <a name="view-compute-configuration"></a>Visa beräknings konfiguration

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute Clusters** i din arbets yta.
1. Högerklicka på den beräkning som du vill visa och välj **Visa beräknings egenskaper**.

### <a name="edit-compute-scale-settings"></a>Redigera inställningar för beräknings skala

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute Clusters** i din arbets yta.
1. Högerklicka på den beräkning som du vill redigera och välj **Redigera beräkning**.
1. En konfigurations fil för beräkningen öppnas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="delete-compute"></a>Ta bort beräkning

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute Clusters** i din arbets yta.
1. Högerklicka på den beräkning som du vill ta bort och välj **ta bort beräkning**.

### <a name="create-run-configuration"></a>Skapa körnings konfiguration

Så här skapar du en körnings konfiguration i tillägget:

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **Compute Clusters** i din arbets yta.
1. Högerklicka på det beräknings mål som du vill skapa Run-konfigurationen under och välj **skapa körnings konfiguration**.
1. I prompten:
    1. Ange ett namn för ditt beräknings mål
    1. Välj eller skapa en ny miljö.
    1. Skriv namnet på skriptet som du vill köra eller tryck på **RETUR** för att läsa in skriptet på den lokala datorn.
    1. Valfritt Välj om du vill skapa en data referens för din utbildnings körning. Då uppmanas du att definiera en data uppsättning i din körnings konfiguration.
        1. Välj någon av dina registrerade data uppsättningar för att länka till körnings konfigurationen en konfigurations fil för din data uppsättning öppnas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.
    1. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="edit-run-configuration"></a>Redigera körnings konfiguration

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera din Compute Cluster-nod i noden **Compute Clusters** i din arbets yta.
1. Högerklicka på den körnings konfiguration som du vill redigera och välj **Redigera kör konfiguration**.
1. En konfigurations fil för din körnings konfiguration öppnas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="delete-run-configuration"></a>Ta bort körnings konfiguration

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Expandera Compute Cluster-noden av intresse i noden **Compute Clusters** .
1. Högerklicka på den körnings konfiguration som du vill redigera och välj **ta bort körnings konfiguration**.

## <a name="models"></a>Modeller

Mer information finns i [modeller](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Registrera modellen

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Högerklicka på noden **modeller** och välj **Registrera modell**.
1. I prompten:
    1. Ange ett namn för din modell
    1. Välj om din modell är en fil eller mapp.
    1. Hitta modellen i din lokala dator.
    1. En konfigurations fil för din modell i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

### <a name="view-model-properties"></a>Visa modell egenskaper

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **modeller** i din arbets yta.
1. Högerklicka på den modell vars egenskaper du vill visa och välj **Visa modell egenskaper**. En fil öppnas i den redigerare som innehåller dina modell egenskaper.

### <a name="download-model"></a>Ladda ned modell

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **modeller** i din arbets yta.
1. Högerklicka på den modell som du vill ladda ned och välj **Ladda ned modell fil**.

### <a name="delete-a-model"></a>Ta bort en modell

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **modeller** i din arbets yta.
1. Högerklicka på den modell som du vill ta bort och välj **ta bort modell**.

## <a name="endpoints"></a>Slutpunkter

VS Code-tillägget har stöd för följande distributions mål:

- Azure Container Instances
- Azure Kubernetes Service

Mer information finns i [webb tjänst slut punkter](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Skapa distributioner

> [!NOTE]
> Att skapa distribution för närvarande fungerar bara med Conda-miljöer.

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden arbets yta.
1. Högerklicka på noden **slut punkter** och välj **distribuera tjänst**.
1. I prompten:
    1. Välj om du vill använda en redan registrerad modell eller en lokal modell fil.
    1. Välj din modell
    1. Välj det distributions mål som du vill distribuera din modell till.
    1. Ange ett namn för din modell.
    1. Ange det skript som ska köras vid beräkning av modellen.
    1. Ange en Conda-beroende fil.
    1. En konfigurations fil för distributionen visas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.

> [!NOTE]
> Du kan också högerklicka på en registrerad modell i noden *modeller* och välja **distribuera tjänst från registrerad modell**.

### <a name="delete-deployments"></a>Ta bort distributioner

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **slut punkter** i din arbets yta.
1. Högerklicka på den distribution som du vill ta bort och välj **ta bort tjänst**.
1. En uppfråga visas som bekräftar att du vill ta bort tjänsten. Välj **OK**.

### <a name="manage-deployments"></a>Hantera distributioner

Förutom att skapa och ta bort distributioner kan du Visa och redigera inställningar som är associerade med distributionen.

1. Expandera noden prenumeration som innehåller din arbets yta.
1. Expandera noden **slut punkter** i din arbets yta.
1. Högerklicka på den distribution som du vill hantera:
    - Om du vill redigera inställningarna väljer du **Redigera tjänst**.
        - En konfigurations fil för distributionen visas i redigeraren. Om du är nöjd med konfigurationen väljer du **Spara och fortsätt** eller öppnar kommando paletten vs Code (**Visa > kommando palett**) och skriver **Azure ml: Spara och fortsätt**.
    - Om du vill visa distributions konfigurations inställningarna väljer du **Visa tjänst egenskaper**.

## <a name="next-steps"></a>Nästa steg

[Träna en bild klassificerings modell](tutorial-train-deploy-image-classification-model-vscode.md) med vs Code-tillägget.