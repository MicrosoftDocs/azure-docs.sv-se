---
title: 'Självstudie: skapa ett etikett projekt för bild klassificering'
titleSuffix: Azure Machine Learning
description: Lär dig hur du hanterar processen för att märka bilder så att de kan användas i modell modeller med flera klasser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 238801171d19191bcac505f0c95395320f96d75c
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882474"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Självstudie: skapa ett etikett projekt för bild klassificering med flera klasser 


Den här självstudien visar hur du hanterar processen för etiketter (kallas även för taggning) som ska användas som data för att skapa maskin inlärnings modeller. Data etiketter i Azure Machine Learning finns i offentlig för hands version.

Om du vill träna en maskin inlärnings modell för att klassificera bilder, behöver du hundratals eller till och med tusentals bilder som är korrekt märkta.  Azure Machine Learning hjälper dig att hantera förloppet för ditt privata team av domän experter när de etiketterar dina data.
 
I den här självstudien använder du bilder av katter och hundar.  Eftersom varje avbildning är antingen en katt eller en hund, är detta ett projekt *med flera klass* etiketter. Du lär dig följande:

> [!div class="checklist"]
>
> * Skapa ett Azure Storage-konto och ladda upp avbildningar till kontot.
> * Skapa en Azure Machine Learning-arbetsyta.
> * Skapa ett projekt med etikettering med flera klasser.
> * Etikettera dina data.  Antingen du eller dina etiketter kan utföra den här uppgiften.
> * Slutför projektet genom att granska och exportera data.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten.

Det finns många [sätt att skapa en arbets yta](how-to-manage-workspace.md). I den här självstudien skapar du en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Starta ett etikettande projekt

Härnäst ska du hantera projektet för data etiketter i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap. Studio stöds inte i Internet Explorer-webbläsare.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj din prenumeration och arbets ytan du skapade.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Skapa ett data lager

Azure Machine Learning data lager används för att lagra anslutnings information, t. ex. prenumerations-ID och token-auktorisering. Här använder du ett data lager för att ansluta till det lagrings konto som innehåller avbildningarna för den här självstudien.

1. På vänster sida av arbets ytan väljer du **data lager**.

1. Välj **+ nytt data lager**.

1. Fyll i formuläret med följande inställningar:

    Fält|Beskrivning 
    ---|---
    Data lager namn | Ge data lagret ett namn.  Här använder vi **labeling_tutorial**.
    Data lager typ | Välj lagrings typ.  Här använder vi **Azure Blob Storage**, den önskade lagringen för avbildningar.
    Val av konto | Välj **ange manuellt**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Autentiseringstyp | Välj **SAS-token**.
    Kontonyckel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Välj **skapa** för att skapa data lagret.

### <a name="create-a-labeling-project"></a>Skapa ett etikett projekt

Nu när du har åtkomst till de data som du vill ha med etikett, skapar du ett etikett projekt.

1. Välj **projekt** längst upp på sidan.

1. Välj **+ Lägg till projekt**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Skapa ett projekt":::

### <a name="project-details"></a>Projektinformation

1. Använd följande indata för formuläret **projekt information** :

    Fält|Beskrivning 
    ---|---
    Projektnamn | Ge ditt projekt ett namn.  Här kommer vi att använda **självstudie – katter-n-hundar**.
    Etikettering av uppgifts typ | Välj **bild klassificering flera klasser**.  
    
    Välj **Nästa** för att fortsätta skapa projektet.

### <a name="select-or-create-a-dataset"></a>Välj eller skapa en data uppsättning

1.   I formuläret **Välj eller skapa en data uppsättning** väljer du det andra alternativet, **skapar en data uppsättning** och väljer sedan länken **från data lagret**.

1. Använd följande indata för formuläret **skapa data uppsättning från data lager** :

    1. I formuläret **grundläggande information** lägger du till ett namn. här kommer vi att använda **bilder för självstudier**.  Lägg till en beskrivning om du vill.  Välj sedan **Nästa**.
    1. Välj **tidigare skapade data lager** på sidan **urval** av data lager och klicka sedan på data lager namnet och välj **Välj data lager**.
    1. På nästa sida kontrollerar du att det aktuella data lagret är korrekt. Om inte väljer du **tidigare skapade data lager** och upprepar föregående steg.
    1. Sedan väljer du **Bläddra** i formuläret lagrings **område** och väljer sedan **DogsCats**.  Välj **Spara** för att använda **/MultiClass-DogsCats** som sökväg.
    1. Välj **Nästa** för att bekräfta informationen och **skapa** sedan för att skapa data uppsättningen.
    1. Välj cirkeln bredvid data uppsättningens namn i listan, till exempel **bilder – självstudier**.

1. Välj **Nästa** för att fortsätta skapa projektet.

### <a name="incremental-refresh"></a>Inkrementell uppdatering

Om du planerar att lägga till nya avbildningar i din data uppsättning kommer den stegvisa uppdateringen att hitta dessa nya avbildningar och lägga till dem i projektet.  När du aktiverar den här funktionen kommer projektet att regelbundet söka efter nya avbildningar.  Du kommer inte att lägga till nya avbildningar i data lagret för den här själv studie kursen, så lämna den här funktionen omarkerad.

Välj **Nästa** för att fortsätta.

### <a name="label-classes"></a>Etikett klasser

1. I formuläret **etikett klasser** anger du ett etikett namn och väljer sedan **+ Lägg till etikett** för att skriva nästa etikett.  För det här projektet är etiketterna **katt**, **hund** och **osäkra**.

1. Välj **Nästa** när du har lagt till alla etiketter.

### <a name="labeling-instructions"></a>Etiketter-instruktioner

1. I formuläret **etikett instruktioner** kan du ange en länk till en webbplats som innehåller detaljerade instruktioner för dina etiketter.  Vi lämnar det tomt för den här självstudien.

1. Du kan också lägga till en kort beskrivning av uppgiften direkt i formuläret.  Vägledning för typ **Etiketter – katter & hundar.**

1. Välj **Nästa**.

1. Lämna kryss rutan omarkerad i avsnittet **ml-märkning** . ML-etiketter kräver mer data än vad du kommer att använda i den här självstudien.

1. Välj **Skapa projekt**.

Den här sidan uppdateras inte automatiskt. Efter en paus uppdaterar du sidan manuellt tills projektets status ändras till **skapad**.

## <a name="start-labeling"></a>Starta märkning

Nu har du konfigurerat dina Azure-resurser och konfigurerat ett projekt med data etiketter. Det är dags att lägga till etiketter till dina data.

### <a name="tag-the-images"></a>Tagga bilderna

I den här delen av självstudien byter du roller från *projekt administratören* till en *Labeler*.  Alla som har deltagar åtkomst till din arbets yta kan bli en Labeler.

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **data etiketter** till vänster för att hitta ditt projekt.  

1. Välj **etikett länk** för projektet.

1. Läs anvisningarna och välj sedan **uppgifter**.

1. Välj en miniatyr bild till höger om du vill visa hur många bilder du vill etikettera i en go. Du måste märka alla de här bilderna innan du kan gå vidare. Växla bara layouter när du har en ny sida med omärkta data. När du växlar layouter rensas sidans pågående märknings arbete.

1. Välj en eller flera avbildningar och välj sedan en tagg som ska användas för markeringen. Taggen visas under bilden.  Fortsätt att markera och tagga alla avbildningar på sidan.  Välj **Markera alla** för att välja alla bilder som visas samtidigt. Välj minst en bild för att tillämpa en tagg.


    > [!TIP]
    > Du kan välja de första nio taggarna med hjälp av siffer tangenterna på tangent bordet.

1. När alla bilder på sidan är taggade väljer du **Skicka** för att skicka dessa etiketter.

    ![Tagga bilder](media/tutorial-labeling/catsndogs.gif)

1. När du har skickat taggar för data till handen uppdaterar Azure sidan med en ny uppsättning avbildningar från arbets kön.

## <a name="complete-the-project"></a>Slutför projektet

Nu ska du växla roller tillbaka till *projekt administratören* för att märka projektet.

Som chef kanske du vill granska arbetet i din Labeler.  

### <a name="review-labeled-data"></a>Granska märkta data

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **data etiketter** till vänster för att hitta ditt projekt.  

1. Välj länken projekt namn.

1. På instrument panelen visas projektets förlopp.

1. Välj **data** längst upp på sidan.

1. På den vänstra sidan väljer du **märkta data** för att se dina taggade bilder.  

1. När du inte samtycker med en etikett väljer du bilden och väljer sedan **avvisa** längst ned på sidan.  Taggarna tas bort och bilden placeras tillbaka i kön med omärkta bilder.

### <a name="export-labeled-data"></a>Exportera märkta data

Du kan när som helst exportera etikett data för Machine Learning experimentering. Användare exporterar ofta flera gånger och tränar olika modeller, i stället för att vänta på att alla bilder får etiketter.

Bild etiketter kan exporteras i [Coco-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning data uppsättning. I data uppsättnings formatet är det enkelt att använda för utbildning i Azure Machine Learning.  

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **data etiketter** till vänster för att hitta ditt projekt.  

1. Välj länken projekt namn.

1. Välj **Exportera** och välj **Exportera som Azure ml-datauppsättning**. 

    Status för exporten visas strax under knappen **Exportera** . 

1. När etiketterna har exporter ATS väljer du **data uppsättningar** på vänster sida för att visa resultatet.

## <a name="clean-up-resources"></a>Rensa resurser


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du etiketterade bilder.  Använd nu dina märkta data:

> [!div class="nextstepaction"]
> [Träna en modell för maskin inlärnings avbildnings igenkänning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
