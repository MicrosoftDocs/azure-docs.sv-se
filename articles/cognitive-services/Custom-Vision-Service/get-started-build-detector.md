---
title: Snabb start Bygg en objekt detektor – Custom Vision Service
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
ms.openlocfilehash: 57fb91e9ead98f6c52fd3f659f4aa12692816297
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549555"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Snabb start: så här skapar du en objekt detektor med Custom Vision

I den här snabb starten får du lära dig hur du skapar en objekt detektor via Custom Vision webbplats. När du har skapat en detektor modell kan du använda tjänsten Custom Vision för objekt identifiering.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En uppsättning avbildningar som du kan använda för att träna din detektor modell. Du kan använda en uppsättning [exempel bilder](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) på GitHub. Du kan också välja egna bilder med hjälp av tipsen nedan.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa Custom Vision resurser i Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Navigera till [Custom vision webb sida](https://customvision.ai) i webbläsaren och välj __Logga__in. Logga in med samma konto som du använde för att logga in på Azure Portal.

![Bild av inloggnings Sidan](./media/browser-home.png)


1. Välj **nytt projekt**för att skapa ditt första projekt. Dialog rutan **Skapa nytt projekt** visas.

    ![Dialog rutan nytt projekt innehåller fält för namn, beskrivning och domäner.](./media/get-started-build-detector/new-project.png)

1. Ange ett namn och en beskrivning för projektet. Välj sedan en resurs grupp. Om ditt inloggade konto är associerat med ett Azure-konto visas alla Azure-resurs grupper som innehåller en Custom Vision Service resurs i list rutan resurs grupp. 

   > [!NOTE]
   > Om ingen resurs grupp är tillgänglig bekräftar du att du har loggat in på [customvision.AI](https://customvision.ai) med samma konto som du använde för att logga in på [Azure Portal](https://portal.azure.com/). Kontrol lera också att du har valt samma "katalog" i Custom Vision portal som katalog i den Azure Portal där dina Custom Vision-resurser finns. På båda platserna kan du välja din katalog från menyn för List menyn i det övre högra hörnet på skärmen. 

1. Välj __objekt identifiering__ under __projekt typer__.

1. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar detektorn för vissa typer av avbildningar, enligt beskrivningen i följande tabell. Du kommer att kunna ändra domänen senare om du vill.

    |Domain|Syfte|
    |---|---|
    |__Allmänt__| Optimerad för ett brett utbud av objekt identifierings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen. |
    |__Logotyp__|Optimerad för att hitta varumärkes logo typer i bilder.|
    |__Produkter på hyllor__|Optimerad för att identifiera och klassificera produkter på hyllor.|
    |__Komprimerade domäner__| Optimerad för begränsningar av objekt identifiering i real tid på mobila enheter. Modeller som genereras av komprimerade domäner kan exporteras för att köras lokalt.|

1. Välj slutligen __skapa projekt__.

## <a name="choose-training-images"></a>Välj utbildnings avbildningar

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I det här avsnittet ska du ladda upp och tagga bilder manuellt för att träna detektorn. 

1. Om du vill lägga till avbildningar klickar du på knappen __Lägg till avbildningar__ och väljer sedan __Bläddra bland lokala filer__. Välj __Öppna__ för att ladda upp avbildningarna.

    ![Kontrollen Lägg till bilder visas i det övre vänstra hörnet och som en knapp längst ned i mitten.](./media/get-started-build-detector/add-images.png)

1. Du ser dina överförda bilder i avsnittet **omärkta** i användar gränssnittet. Nästa steg är att manuellt tagga de objekt som du vill att detektorn ska lära sig att känna igen. Klicka på den första bilden för att öppna dialog rutan taggning. 

    ![Överförda bilder, i otaggade avsnitt](./media/get-started-build-detector/images-untagged.png)

1. Klicka och dra en rektangel runt objektet i bilden. Ange sedan ett nytt taggnamn med **+** knappen eller Välj en befintlig tagg i list rutan. Det är mycket viktigt att tagga varje instans av de objekt som du vill identifiera, eftersom detektorn använder det otaggade bakgrunds områden som ett negativt exempel i träning. När du är klar med taggning klickar du på pilen till höger för att spara taggarna och gå vidare till nästa bild.

    ![Tagga ett objekt med ett rektangulärt val](./media/get-started-build-detector/image-tagging.png)

Om du vill ladda upp en annan uppsättning bilder går du tillbaka till början av det här avsnittet och upprepar stegen.

## <a name="train-the-detector"></a>Träna detektorn

Om du vill träna detektor modellen väljer du knappen **träna** . Detektorn använder alla aktuella bilder och deras taggar för att skapa en modell som identifierar varje taggat objekt.

![Knappen träna uppe till höger i webb sidans sidhuvud-verktygsfält](./media/getting-started-build-a-classifier/train01.png)

Inlärnings processen bör ta några minuter. Under den här tiden visas information om inlärnings processen på fliken **prestanda** .

![Webbläsarfönstret med en tränings dialog ruta i huvud avsnittet](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Utvärdera detektorn

När utbildningen har slutförts beräknas och visas modellens prestanda. I Custom Vision tjänsten används de avbildningar som du har skickat in för utbildning för att beräkna precision, återkallning och genomsnitts precision. Precision och återkallande är två olika mätningar av effektiviteten hos en detektor:

- **Precision** anger fraktionen av identifierade klassificeringar som var korrekta. Om modellen till exempel har identifierat 100-bilder som hundar och 99 av dem var av hundar, skulle precisionen vara 99%.
- **Återkalla** anger den andel av faktiska klassificeringar som identifierades korrekt. Om det exempelvis fanns 100 avbildningar av äpplen och modellen identifierade 80 som äpplen, skulle återställningen bli 80%.

![Utbildnings resultatet visar den övergripande precisionen och återställningen och genomsnitts precisionen.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Tröskelvärde för sannolikhet

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Hantera inlärnings iterationer

Varje gången du tränar din detektor skapar du en ny _iteration_ med sina egna uppdaterade prestanda mått. Du kan visa alla iterationer i den vänstra rutan på fliken **prestanda** . I det vänstra fönstret hittar du också knappen **ta bort** , som du kan använda för att ta bort en iteration om den är föråldrad. När du tar bort en iteration tar du bort alla bilder som är unikt kopplade till den.

Se [använda din modell med förutsägelse-API](./use-prediction-api.md) för att lära dig hur du får åtkomst till dina utbildade modeller program mässigt.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar och tränar en objekt detektor modell med hjälp av Custom Vision webbplats. Därefter får du mer information om den iterativa processen för att förbättra din modell.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)

