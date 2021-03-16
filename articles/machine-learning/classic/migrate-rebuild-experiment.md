---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning-återskapa experiment'
description: Bygg om Studio (klassiska) experiment i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565424"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Återskapa ett Studio-experiment (klassisk) i Azure Machine Learning

I den här artikeln får du lära dig att återskapa ett Studio-experiment (klassisk) i Azure Machine Learning. Mer information om hur du migrerar från Studio (klassisk) finns i [översikts artikeln om migrering](migrate-overview.md).

Studio (klassiska) **experiment** liknar **pipelines** i Azure Machine Learning. I Azure Machine Learning pipeliner bygger dock på samma backend-slutpunkt som ger SDK: n. Det innebär att du har två alternativ för maskin inlärnings utveckling: Dra-och-släpp-designer eller kod-första SDK: er.

Mer information om hur du skapar pipelines med SDK finns i [Vad är Azure Machine Learning pipelines](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Azure Machine Learning-arbetsyta. [Skapa en Azure Machine Learning-arbetsyta](../how-to-manage-workspace.md#create-a-workspace).
- Ett Studio-experiment (klassisk) som ska migreras.
- [Ladda upp data uppsättningen](migrate-register-dataset.md) till Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Återskapa pipelinen

När du har [migrerat data uppsättningen till Azure Machine Learning](migrate-register-dataset.md)är du redo att återskapa experimentet.

I Azure Machine Learning kallas det visuella diagrammet för ett **pipeline-utkast**. I det här avsnittet ska du återskapa det klassiska experimentet som ett utkast till pipeline.

1. Gå till Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. I det vänstra navigerings fönstret väljer du **Designer** > -skärm bild som är **lättanvända för inbyggda moduler** ![ som visar hur du skapar ett nytt pipeline-utkast.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Återskapa experimentet manuellt med designer-moduler.
    
    Se [tabellen modul-mappning](migrate-overview.md#studio-classic-and-designer-module-mapping) för att hitta ersättnings moduler. Många av Studio (klassiska) är de populäraste modulerna har identiska versioner i designern.

    > [!Important]
    > Om experimentet använder modulen kör R-skript måste du utföra ytterligare steg för att migrera experimentet. Mer information finns i [migrera R-skript-moduler](migrate-execute-r-script.md).

1. Justera parametrar.
    
    Välj varje modul och justera parametrarna i panelen för modul inställningar till höger. Använd parametrarna för att återskapa funktionerna i det klassiska Studio-experimentet. Mer information om varje modul finns i [modulreferensen](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Skicka in en körnings-och kontroll resultat

När du har återskapat din Studio (klassiska) experiment är det dags att skicka in en **pipeline-körning**.

En pipeline-körning körs på ett **beräknings mål** som är kopplat till din arbets yta. Du kan ange ett standard beräknings mål för hela pipelinen, eller så kan du ange beräknings mål per modul.

När du har skickat in en körning från ett pipeline-utkast förvandlas den till en **pipeline-körning**. Varje pipeline-körning registreras och loggas in Azure Machine Learning.

Ange ett standard beräknings mål för hela pipelinen:
1. Välj kugg **hjuls ikonen** ![ i designern ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) bredvid pipelinens namn.
1. Välj **Välj Compute Target (Välj Compute Target**).
1. Välj en befintlig beräkning eller skapa en ny beräkning genom att följa anvisningarna på skärmen.

Nu när du har angett ditt beräknings mål kan du skicka en pipeline-körning:

1. Välj **Skicka** på arbets ytans överkant.
1. Välj **Skapa nytt** för att skapa ett nytt experiment.
    
    Experiment som organiserar liknande pipelines körs tillsammans. Om du kör en pipeline flera gånger kan du välja samma experiment för efterföljande körningar. Detta är användbart för loggning och spårning.
1. Ange ett experiment namn. Välj sedan **Skicka**.

    Den första körningen kan ta upp till 20 minuter. Eftersom standard beräknings inställningarna har en minsta Node-storlek på 0, måste designer allokera resurser efter inaktivitet. Efterföljande körningar tar mindre tid eftersom noderna redan har allokerats. För att påskynda körnings tiden kan du skapa en beräknings resurs med en minsta Node-storlek på 1 eller större.

När körningen är klar kan du kontrol lera resultaten för varje modul:

1. Högerklicka på den modul vars utdata du vill se.
1. Välj antingen **visualisera**, **Visa utdata** eller **Visa logg**.

    - **Visualisera**: Förhandsgranska resultat data uppsättningen.
    - **Visa utdata**: öppna en länk till lagrings platsen för utdata. Använd detta för att utforska eller hämta utdata. 
    - **Visa logg**: Visa driv rutiner och system loggar. Använd **70_driver_log** för att se information om ditt skript som har skickats till användaren, till exempel fel och undantag.

> [!IMPORTANT]
> Designer-moduler använder python-paket med öppen källkod jämfört med C#-paket i Studio (klassisk). Därför kan modulens utdata variera något mellan designer och Studio (klassisk). 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du återskapar ett Studio-experiment (klassisk) i Azure Machine Learning. Nästa steg är att [Återskapa webb tjänster i Azure Machine Learning](migrate-rebuild-web-service.md).


Se de andra artiklarna i serien Studio (klassisk) migrering:

1. [Översikt över migrering](migrate-overview.md).
1. [Migrera data uppsättning](migrate-register-dataset.md).
1. **Återskapa en Studio (klassisk) utbildnings pipeline**.
1. [Återskapa en Studio-webbtjänst (klassisk)](migrate-rebuild-web-service.md).
1. [Integrera en Azure Machine Learning-webbtjänst med klient program](migrate-rebuild-integrate-with-client-app.md).
1. [Migrera kör R-skript](migrate-execute-r-script.md).
