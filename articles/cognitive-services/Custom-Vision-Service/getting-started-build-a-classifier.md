---
title: Snabb start skapar en klassificerar-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du använder Custom Vision webbplats för att skapa en bild klassificerings modell.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 67632301b534f91c36de837bbfa12f9ec16ed58f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551360"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Snabb start: så här skapar du en klassificerare med Custom Vision

I den här snabb starten får du lära dig hur du skapar en klassificerare genom Custom Vision webbplats. När du har skapat en klassificerings modell kan du använda tjänsten Custom Vision för avbildnings klassificering.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En uppsättning avbildningar som du kan använda för att träna din klassificerare. Se nedan för tips om att välja bilder.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa Custom Vision resurser i Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Navigera till [Custom vision webb sida](https://customvision.ai) i webbläsaren och välj __Logga__in. Logga in med samma konto som du använde för att logga in på Azure Portal.

![Bild av inloggnings Sidan](./media/browser-home.png)


1. Välj **nytt projekt**för att skapa ditt första projekt. Dialog rutan **Skapa nytt projekt** visas.

    ![Dialog rutan nytt projekt innehåller fält för namn, beskrivning och domäner.](./media/getting-started-build-a-classifier/new-project.png)

1. Ange ett namn och en beskrivning för projektet. Välj sedan en resurs grupp. Om ditt inloggade konto är associerat med ett Azure-konto visas alla Azure-resurs grupper som innehåller en Custom Vision Service resurs i list rutan resurs grupp. 

   > [!NOTE]
   > Om ingen resurs grupp är tillgänglig bekräftar du att du har loggat in på [customvision.AI](https://customvision.ai) med samma konto som du använde för att logga in på [Azure Portal](https://portal.azure.com/). Kontrol lera också att du har valt samma "katalog" i Custom Vision portal som katalog i den Azure Portal där dina Custom Vision-resurser finns. På båda platserna kan du välja din katalog från menyn för List menyn i det övre högra hörnet på skärmen. 

1. Välj __klassificering__ under __projekt typer__. Välj antingen **multietikett** eller **Multiklass**under __klassificerings typer__, beroende på ditt användnings fall. Klassificering med flera etiketter använder valfritt antal taggar i en bild (noll eller flera), medan klassificering av flera klasser sorterar bilder i enskilda kategorier (varje bild som du skickar kommer att sorteras i den mest sannolika taggen). Du kan ändra klassificerings typen senare om du vill.

1. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar klassificeraren för vissa typer av bilder, enligt beskrivningen i följande tabell. Du kommer att kunna ändra domänen senare om du vill.

    |Domain|Syfte|
    |---|---|
    |__Allmänna__| Optimerad för ett brett utbud av bild klassificerings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen. |
    |__Kost__|Optimerad för foton av rätter på samma sätt som du ser dem på en restaurang meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker använder du livsmedels domänen.|
    |__Landmärken__|Optimerad för identifierbara landmärken, både naturliga och artificiella. Den här domänen fungerar bäst när landmärket är tydligt synligt i fotografiet. Den här domänen fungerar även om landmärken är något som är något som står framför det.|
    |__Retail__|Optimerad för avbildningar som finns i en shopping katalog eller shopping webbplats. Om du vill att hög precision ska klassificeras mellan dresses, Pants och skjortor använder du den här domänen.|
    |__Komprimerade domäner__| Optimerad för begränsningar i real tids klassificering på mobila enheter. Modeller som genereras av komprimerade domäner kan exporteras för att köras lokalt.|

1. Välj slutligen __skapa projekt__.

## <a name="choose-training-images"></a>Välj utbildnings avbildningar

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I det här avsnittet ska du ladda upp och tagga bilder manuellt för att träna klassificeraren. 

1. Om du vill lägga till avbildningar klickar du på knappen __Lägg till avbildningar__ och väljer sedan __Bläddra bland lokala filer__. Välj __Öppna__ för att flytta till taggning. Valet av val kommer att gälla för hela gruppen med avbildningar som du har valt att ladda upp, så det är enklare att ladda upp bilder i separata grupper i enlighet med deras önskade taggar. Du kan också ändra taggarna för enskilda bilder när de har överförts.

    ![Kontrollen Lägg till bilder visas i det övre vänstra hörnet och som en knapp längst ned i mitten.](./media/getting-started-build-a-classifier/add-images01.png)


1. Om du vill skapa en tagg anger du text i fältet __Mina Taggar__ och trycker på RETUR. Om taggen redan finns visas den i en nedrullningsbar meny. I ett projekt med flera etiketter kan du lägga till fler än en tagg till dina bilder, men i ett projekt med flera klasser kan du bara lägga till en. Om du vill slutföra överföringen av avbildningarna använder du knappen __Ladda upp [nummer] filer__ . 

    ![Bild av taggen och överförings Sidan](./media/getting-started-build-a-classifier/add-images03.png)

1. Välj __slutförd__ när bilderna har laddats upp.

    ![Förlopps indikatorn visar alla uppgifter som har slutförts.](./media/getting-started-build-a-classifier/add-images04.png)

Om du vill ladda upp en annan uppsättning bilder går du tillbaka till början av det här avsnittet och upprepar stegen.

## <a name="train-the-classifier"></a>Träna klassificeraren

Om du vill träna klassificeraren väljer du knappen **träna** . Klassificeraren använder alla aktuella avbildningar för att skapa en modell som identifierar de visuella egenskaperna för varje tagg.

![Knappen träna uppe till höger i webb sidans sidhuvud-verktygsfält](./media/getting-started-build-a-classifier/train01.png)

Inlärnings processen bör ta några minuter. Under den här tiden visas information om inlärnings processen på fliken **prestanda** .

![Webbläsarfönstret med en tränings dialog ruta i huvud avsnittet](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Utvärdera klassificeraren

När utbildningen har slutförts beräknas och visas modellens prestanda. Custom Vision Service använder de bilder som du har skickat in för utbildning för att beräkna precision och återkalla med hjälp av en process som kallas [k-vikning kors validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precision och återkallande är två olika mätningar av effektiviteten i en klassificerare:

- **Precision** anger fraktionen av identifierade klassificeringar som var korrekta. Om modellen till exempel har identifierat 100-bilder som hundar och 99 av dem var av hundar, skulle precisionen vara 99%.
- **Återkalla** anger den andel av faktiska klassificeringar som identifierades korrekt. Om det exempelvis fanns 100 avbildningar av äpplen och modellen identifierade 80 som äpplen, skulle återställningen bli 80%.

![Utbildnings resultatet visar den övergripande precisionen och återställningen samt precisionen och återställningen för varje tagg i klassificeraren.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Tröskelvärde för sannolikhet

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Hantera inlärnings iterationer

Varje gången du tränar din klassificerare skapar du en ny _iteration_ med sina egna uppdaterade prestanda mått. Du kan visa alla iterationer i den vänstra rutan på fliken **prestanda** . Du hittar också knappen **ta bort** , som du kan använda för att ta bort en iteration om den är föråldrad. När du tar bort en iteration tar du bort alla bilder som är unikt kopplade till den.

Se [använda din modell med förutsägelse-API](./use-prediction-api.md) för att lära dig hur du får åtkomst till dina utbildade modeller program mässigt.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar och tränar en bild klassificerings modell med hjälp av Custom Vision webbplats. Därefter får du mer information om den iterativa processen för att förbättra din modell.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)

