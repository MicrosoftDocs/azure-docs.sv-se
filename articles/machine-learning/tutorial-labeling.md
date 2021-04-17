---
title: 'Självstudie: Skapa ett etiketteringsprojekt för bildklassificering'
titleSuffix: Azure Machine Learning
description: Lär dig hur du hanterar processen med att märka bilder så att de kan användas i bildklassificeringsmodeller med flera klasser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 41e93584937ca10740e9ee0be3353d1edf5efb3e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587688"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Självstudie: Skapa ett etiketteringsprojekt för bildklassificering med flera klasser 


Den här självstudien visar hur du hanterar processen för att märka (kallas även taggning) bilder som ska användas som data för att skapa maskininlärningsmodeller. Dataetiketter i Azure Machine Learning är i offentlig förhandsversion.

Om du vill träna en maskininlärningsmodell för att klassificera bilder behöver du hundratals eller till och med tusentals bilder som är korrekt märkta.  Azure Machine Learning hjälper dig att hantera förloppet för ditt privata team med domänexperter när de etiketterar dina data.
 
I den här självstudien använder du bilder av katter och hundar.  Eftersom varje bild antingen är en katt eller en hund är det här ett projekt *för* etikettering i flera klasser. Du lär dig följande:

> [!div class="checklist"]
>
> * Skapa ett Azure Storage-konto och ladda upp bilder till kontot.
> * Skapa en Azure Machine Learning arbetsyta.
> * Skapa ett bildmärkningsprojekt med flera klasser.
> * Märk dina data.  Antingen kan du eller dina etiketterare utföra den här uppgiften.
> * Slutför projektet genom att granska och exportera data.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbetsyta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskininlärningsmodeller. Det binder din Azure-prenumeration och resursgrupp till ett objekt som är enkelt att använda i tjänsten.

Det finns många [sätt att skapa en arbetsyta](how-to-manage-workspace.md)på. I den här självstudien skapar du en arbetsyta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Starta ett etiketteringsprojekt

Därefter hanterar du datamärkningsprojektet i Azure Machine Learning-studio, ett konsoliderat gränssnitt som innehåller maskininlärningsverktyg för att utföra datavetenskapsscenarier för datavetenskapsutövare på alla kunskapsnivåer. Studio stöds inte i Internet Explorer webbläsare.

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com).

1. Välj din prenumeration och den arbetsyta som du skapade.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Skapa ett datalager

Azure Machine Learning används för att lagra anslutningsinformation, till exempel ditt prenumerations-ID och din tokenauktorisering. Här använder du ett datalager för att ansluta till lagringskontot som innehåller avbildningarna för den här självstudien.

1. På vänster sida av arbetsytan väljer du **Datalager.**

1. Välj **+ Nytt datalager.**

1. Fyll i formuläret med följande inställningar:

    Fält|Beskrivning 
    ---|---
    Namn på datalager | Ge datalagringen ett namn.  Här använder vi **labeling_tutorial**.
    Typ av datalager | Välj typ av lagring.  Här använder vi **Azure Blob Storage**, den föredragna lagringen för bilder.
    Metod för kontoval | Välj **Ange manuellt.**
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Autentiseringstyp | Välj **SAS-token**.
    Kontonyckel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Välj **Skapa** för att skapa datalagringen.

### <a name="create-a-labeling-project"></a>Skapa ett etiketteringsprojekt

Nu när du har åtkomst till de data som du vill ha märkta skapar du ditt etiketteringsprojekt.

1. Längst upp på sidan väljer du **Projekt**.

1. Välj **+ Lägg till projekt**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Skapa ett projekt":::

### <a name="project-details"></a>Projektinformation

1. Använd följande indata för **formuläret Projektinformation:**

    Fält|Beskrivning 
    ---|---
    Projektnamn | Ge projektet ett namn.  Här använder vi **tutorial-cats-n-dogs**.
    Uppgiftstyp för etikettering | Välj **Bildklassificering Med flera klasser.**  
    
    Välj **Nästa** för att fortsätta att skapa projektet.

### <a name="select-or-create-a-dataset"></a>Välj eller skapa en datauppsättning

1.   I formuläret **Välj eller skapa en datauppsättning** väljer du det andra alternativet, Skapa en **datauppsättning** och väljer sedan **länken Från datalager**.

1. Använd följande indata för formuläret **Skapa datauppsättning från** datalager:

    1. I formuläret **Grundläggande information** lägger du till ett namn. Här ska vi använda **images-for-tutorial**.  Lägg till en beskrivning om du vill.  Välj sedan **Nästa**.
    1. I **urvalsformuläret Datalager** väljer **du Datalager** som skapats tidigare och klickar sedan på datalagernamnet och väljer **Välj datalager.**
    1. På nästa sida kontrollerar du att det valda dataarkivet är korrekt. Om inte väljer du **Datalager som skapats tidigare** och upprepar föregående steg.
    1. Sedan, fortfarande i **urvalsformuläret Datalager,** väljer **du Bläddra** och sedan **MultiClass – DogsCats**.  Välj **Spara** för att **använda /MultiClass – DogsCats** som sökväg.
    1. Välj **Nästa för** att bekräfta informationen och sedan Skapa **för** att skapa datauppsättningen.
    1. Välj cirkeln bredvid datauppsättningens namn i listan, till exempel **images-for-tutorial**.

1. Välj **Nästa** för att fortsätta att skapa projektet.

### <a name="incremental-refresh"></a>Inkrementell uppdatering

Om du planerar att lägga till nya bilder i datauppsättningen hittar inkrementell uppdatering dessa nya bilder och lägger till dem i projektet.  När du aktiverar den här funktionen söker projektet regelbundet efter nya avbildningar.  Du kommer inte att lägga till nya avbildningar i datalagringen för den här självstudien, så lämna den här funktionen avmarkerad.

Fortsätt genom att välja **Nästa**.

### <a name="label-classes"></a>Etikettklasser

1. I formuläret **Etikettklasser** skriver du ett etikettnamn och väljer sedan **+Lägg till etikett för** att skriva nästa etikett.  För det här projektet är etiketterna **Cat**, **Dog** och **Är osäker.**

1. Välj **Nästa** när har lagt till alla etiketter.

### <a name="labeling-instructions"></a>Instruktioner för etikettering

1. I formuläret **Med instruktioner för** etiketter kan du ange en länk till en webbplats som innehåller detaljerade anvisningar för dina etiketter.  Vi lämnar det tomt för den här självstudien.

1. Du kan också lägga till en kort beskrivning av uppgiften direkt i formuläret.  Skriv **Labeling tutorial - Cats & Dogs.**

1. Välj **Nästa**.

1. I avsnittet **ML-assisterad** etikettering lämnar du kryssrutan avmarkerad. ML-assisterad etikettering kräver mer data än du kommer att använda i den här självstudien.

1. Välj **Skapa projekt**.

Den här sidan uppdateras inte automatiskt. Efter en paus uppdaterar du sidan manuellt tills projektets status ändras till **Skapad.**

## <a name="start-labeling"></a>Börja etikettera

Nu har du konfigurerat dina Azure-resurser och konfigurerat ett datamärkningsprojekt. Det är dags att lägga till etiketter i dina data.

### <a name="tag-the-images"></a>Tagga bilderna

I den här delen av självstudien växlar du roller från *projektadministratören* till rollen för *en etiketterare*.  Alla som har deltagaråtkomst till din arbetsyta kan bli en etiketterare.

1. I [Machine Learning studio](https://ml.azure.com)väljer du **Dataetiketter** till vänster för att hitta projektet.  

1. Välj **etikettlänken** för projektet.

1. Läs anvisningarna och välj sedan **Uppgifter.**

1. Välj en miniatyrbild till höger för att visa det antal bilder som du vill märka i ett enda go. Du måste märka alla dessa bilder innan du kan gå vidare. Växla endast layouter när du har en ny sida med omärkta data. Om du växlar layout rensas sidans pågående taggningsarbete.

1. Välj en eller flera bilder och välj sedan en tagg som ska tillämpas på valet. Taggen visas under bilden.  Fortsätt att markera och tagga alla bilder på sidan.  Om du vill markera alla bilder som visas samtidigt väljer du **Markera alla**. Välj minst en avbildning för att tillämpa en tagg.


    > [!TIP]
    > Du kan välja de första nio taggarna med hjälp av taltangenterna på tangentbordet.

1. När alla bilder på sidan har taggats väljer du Skicka **för att** skicka etiketterna.

    ![Tagga bilder](media/tutorial-labeling/catsndogs.gif)

1. När du har skickat taggar för de data som finns till hands uppdaterar Azure sidan med en ny uppsättning bilder från arbetskön.

## <a name="complete-the-project"></a>Slutför projektet

Nu ska du växla tillbaka roller till *projektadministratören* för etiketteringsprojektet.

Som chef kanske du vill granska etiketterarens arbete.  

### <a name="review-labeled-data"></a>Granska märkta data

1. I [Machine Learning studio](https://ml.azure.com)väljer du **Dataetiketter** till vänster för att hitta projektet.  

1. Välj länken för projektnamnet.

1. Instrumentpanelen visar förloppet för projektet.

1. Längst upp på sidan väljer du **Data**.

1. På vänster sida väljer du Märkta **data för att se** dina taggade bilder.  

1. Om du inte håller med om en etikett väljer du bilden **och** sedan Avvisa längst ned på sidan.  Taggarna tas bort och avbildningen läggs tillbaka i kön med omärkta bilder.

### <a name="export-labeled-data"></a>Exportera märkta data

Du kan exportera etikettdata för Machine Learning när som helst. Användare exporterar ofta flera gånger och tränar olika modeller i stället för att vänta på att alla bilder märks.

Bildetiketter kan exporteras i [FORMAT ELLER](http://cocodataset.org/#format-data) som en Azure Machine Learning datauppsättning. Datamängdsformatet gör det enkelt att använda för träning i Azure Machine Learning.  

1. I [Machine Learning studio](https://ml.azure.com)väljer du **Dataetiketter** till vänster för att hitta projektet.  

1. Välj länken för projektnamnet.

1. Välj **Exportera och** välj Exportera som Azure **ML-datauppsättning.** 

    Status för exporten visas precis under **knappen** Exportera. 

1. När etiketterna har exporterats väljer du **Datauppsättningar** till vänster för att visa resultatet.

## <a name="clean-up-resources"></a>Rensa resurser


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Träna en bildigenkänningsmodell för maskininlärning.](/azure/machine-learning/how-to-use-labeled-dataset)

