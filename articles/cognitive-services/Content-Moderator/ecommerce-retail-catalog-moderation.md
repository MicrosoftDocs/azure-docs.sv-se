---
title: 'Självstudie: medelhög produkt avbildning för e-handel – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Den här självstudien visar hur du konfigurerar ett program för att analysera och klassificera produkt avbildningar med angivna etiketter (med Azure Visuellt innehåll och Custom Vision). Tagga olämpliga bilder för att granskas ytterligare (med Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: ff56a3e9c15d6f4bf04765c30084a9ca68df0e73
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544765"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Självstudie: medelhög produkt avbildning för e-handel med Azure Content Moderator

I den här självstudien får du lära dig hur du använder Azure Cognitive Services, inklusive Content Moderator, för att klassificera och ändra produkt avbildningar för ett e-handelsscenario. Du ska använda visuellt innehåll och Custom vision för att lägga till taggar (etiketter) till bilder, och sedan skapar du en grupp granskning som kombinerar Content moderator dator inlärnings tekniker med mänsklig gransknings grupp för att tillhandahålla ett intelligent moderator system.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera dig för Content Moderator och skapa ett granskningsteam.
> * Använd Content Moderators bild-API för att söka efter potentiellt vuxet eller vågat innehåll.
> * Använd tjänsten för visuellt innehåll för att söka efter kändisinnehåll (eller andra identifierbara taggar för visuellt innehåll).
> * Använd tjänsten Custom Vision om du vill söka efter flaggor, leksaker och pennor (eller andra anpassade taggar).
> * Presentera kombinerade sökresultat för mänsklig granskning och slutgiltigt beslutsfattande.

Den fullständiga exempelkoden är tillgänglig på lagringsplatsen [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på GitHub.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- En prenumerationsnyckel för visuellt innehåll (samma instruktioner som ovan).
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
- En uppsättning bilder för varje etikett som klassificeraren för Custom Vision ska använda (i det här fallet leksaker, pennor och amerikanska flaggor).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Mer information om hur du registrerar dig för [Content moderator gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) och hur du skapar en gransknings grupp finns i [försök Content moderator på webb](quick-start.md) snabb starten. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="create-custom-moderation-tags"></a>Skapa anpassade modereringstaggar

Skapa sedan anpassade taggar i gransknings verktyget (se artikeln [taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) om du behöver hjälp med den här processen). I det här fallet ska vi lägga till följande taggar: **kändisar**, **USA**, **flagga**, **leksak** och **penna**. Alla Taggar behöver inte vara identifierings bara kategorier i Visuellt innehåll (som **kändis**). Du kan lägga till egna anpassade taggar så länge du tränar Custom Vision klassificeraren att identifiera dem senare.

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installerat**, sedan **Visual C#** och välj **Konsolapp (.NET Framework)**.
1. Ge programmet namnet **EcommerceModeration** och klicka sedan på **OK**.
1. Om du lägger till det här projektet i en befintlig lösning väljer du det här projektet som ett enskilt start projekt.

Den här självstudien visar koden som är central för projektet, men som inte behandlar varje kodrad. Kopiera hela innehållet i _Program.cs_ från exempelprojektet ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) till filen _Program.cs_ för det nya projektet. Gå sedan igenom avsnitten nedan om du vill veta mer om hur projektet fungerar och hur du använder det själv.

## <a name="define-api-keys-and-endpoints"></a>Definiera API-nycklar och slutpunkter

I den här självstudien används tre kognitiva tjänster; Därför krävs tre motsvarande nycklar och API-slutpunkter. Se följande fält i klassen **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Du måste uppdatera `___Key` fälten med värdena för dina prenumerations nycklar och du måste ändra `___Uri` fälten till rätt slut punkts-URL: er (du får Custom vision nyckeln och slut punkten senare). Du hittar dessa värden på **snabb start** -flikarna för varje Azure-resurs. Fyll i `YOURTEAMID`-delen av fältet `ReviewUri` med ID:t för granskningsteamet som du skapade tidigare. Du kommer att fylla i den sista delen av `CustomVisionUri` fältet senare.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Primära metodanrop

Se följande kod i **Main**-metoden, som loopar igenom en lista över webbadresser till bilder. Den analyserar varje avbildning med de tre olika tjänsterna, registrerar de använda taggarna i **ReviewTags** -matrisen och skapar sedan en granskning för de mänskliga moderatorerna genom att skicka avbildningarna till verktyget för Content moderator granskning. Du utforskar dessa metoder i följande avsnitt. Om du vill kan du styra vilka bilder som ska granskas med hjälp av **ReviewTags** -matrisen i en villkorlig instruktion för att kontrol lera vilka taggar som har tillämpats.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-metod

Se metoden **EvaluateAdultRacy** i klassen **Program**. Den här metoden tar en bild-URL och en matris med nyckel/värde-par som parametrar. Den anropar Content Moderators bild-API (med REST) för att få poängen för vuxet och vågat innehåll på bilden. Om poängen för antingen är större än 0,4 (intervallet är mellan 0 och 1), anges motsvarande värde i **ReviewTags** -matrisen till **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags-metod

Nästa metod tar en bild-URL och din prenumerationsinformation för visuellt innehåll och analyserar bilden för förekomst av kändisar. Om det finns en eller flera kändisar ställs motsvarande värde i matrisen **ReviewTags** in på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags-metod

Sedan kan du titta på metoden **EvaluateCustomVisionTags** som klassificerar de faktiska produkterna&mdash;i det här fallet flaggor, leksaker och pennor. Följ anvisningarna i avsnittet [så här skapar du en klassificerings](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) guide för att bygga egna anpassade bildklassificerare och identifiera flaggor, leksaker och pennor (eller vad du väljer som anpassade taggar) i bilder. Du kan använda avbildningarna i mappen **Sample-images** i [GitHub-lagrings platsen](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) för att snabbt träna några av kategorierna i det här exemplet.

![Custom Vision-webbsida med inlärningsbilder av pennor, leksaker och flaggor](images/tutorial-ecommerce-custom-vision.PNG)

När du har tränat klassificeraren kan du hämta URL: en för förutsägelse-och förutsägelse slut punkt (se [Hämta URL-adressen och förutsägelse nyckeln](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) om du behöver hjälp med att hämta dem) och tilldela dessa värden till dina respektive `CustomVisionKey` `CustomVisionUri` fält. Metoden använder dessa värden för att fråga klassificeraren. Om klassificeraren hittar en eller flera av de anpassade taggarna i bilden ställer den här metoden in de motsvarande värdena i matrisen **ReviewTags** på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Skapa granskningar för granskningsverktyg

I föregående avsnitt har du utforskat hur appen genomsöker inkommande bilder för vuxen och vågat innehåll (Content Moderator), kändisar (Visuellt innehåll) och olika andra objekt (Custom Vision). Sedan kan du se metoden **CreateReview** , som överför avbildningarna med alla tillämpade Taggar (som skickas in som _metadata_) till gransknings verktyget för Content moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Bilderna visas på granskningsfliken i [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/).

![Skärmbild av Content Moderator-granskningsverktyget med flera bilder och deras markerade taggar](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Skicka en lista över bilder för testning

Som du ser i metoden **Main** söker det här programmet efter en ”C:Test”-katalog med en _Urls.txt_ -fil som innehåller en lista över bild-URL:er. Skapa filen och katalogen eller ändra sökvägen så att den pekar på text filen. Fyll sedan i den här filen med URL: er för de avbildningar som du vill testa.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Köra programmet

Om du har följt alla ovanstående steg bör programmet bearbeta varje avbildning (fråga alla tre tjänsterna efter relevanta taggar) och sedan ladda upp avbildningarna med tag-information till verktyget Content Moderator granskning.

## <a name="next-steps"></a>Nästa steg

I den här självstudien ställer du in ett program för att analysera produkt bilder, tagga dem efter produkt typ och göra det möjligt för en gransknings grupp att fatta välgrundade beslut om innehålls moderator. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Granska modererade bilder](./review-tool-user-guide/review-moderated-images.md)
