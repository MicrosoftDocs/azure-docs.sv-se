---
title: 'Självstudie: Använd anpassad logo typ detektor för att identifiera Azure-tjänster – Custom Vision'
titleSuffix: Azure Cognitive Services
description: I den här självstudien kommer du att gå igenom en exempel-app som använder Custom Vision som en del av ett scenario för att identifiera en logo typ. Lär dig hur Custom Vision ska användas med andra komponenter för att leverera ett program för slutpunkt till slutpunkt.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 76e7b061670f603007cefd6d7aed503e0e0d3d6b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941771"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Självstudie: identifiera Azure-tjänstens logo typer i kamera bilder

I den här självstudien får du utforska en exempel-app som använder Custom Vision som en del av ett större scenario. Appen AI Visual Provision, en Xamarin.Forms-app för mobila plattformar, analyserar kamerabilder av Azure-tjänstens logotyper och distribuerar sedan de faktiska tjänsterna till användarens Azure-konto. Här lär du dig hur det använder Custom Vision tillsammans med andra komponenter för att leverera ett användbart slutpunkt till slutpunkt-program. Du kan köra hela appens scenario själv, eller så kan du bara slutföra Custom Vision delen av installationen och utforska hur appen använder den.

De här självstudierna visar hur du:

> [!div class="checklist"]
> - Skapa en anpassad objektdetektor för att identifiera Azure-tjänstens logotyper.
> - Anslut appen till Azure-tjänsten Visuellt innehåll och Custom Vision.
> - Skapa ett konto för Azure-tjänstens huvudnamn för att distribuera Azure-tjänster från appen.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/)
- Xamarin-arbetsbelastningen för Visual Studio (se [Installera Xamarin](/xamarin/cross-platform/get-started/installation/windows))
- En iOS- eller Android-emulator för Visual Studio
- [Azure CLI](/cli/azure/install-azure-cli-windows) (valfritt)

## <a name="get-the-source-code"></a>Hämta källkoden

Om du vill använda den webbapp som tillhandahålls kan du klona eller ladda ned appens källkod från lagringsplatsen [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) på GitHub. Öppna filen *Source/VisualProvision.sln* i Visual Studio. Senare redigerar du några av projektfilerna så att du kan köra appen.

## <a name="create-an-object-detector"></a>Skapa en objektidentifierare

Logga in på [Custom Visions webbplats](https://customvision.ai/) och skapa ett nytt projekt. Ange ett objektidentifieringsprojekt och använd logotyp-domänen. Detta gör att tjänsten kan använda en algoritm som är optimerad för identifiering av logotyp. 

![Nytt projektfönster på Custom Vision-webbplatsen i webbläsaren Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I nästa steg tränar du algoritmen för logotypidentifiering genom att ladda upp bilder på Azure-tjänstens logotyper och tagga dem manuellt. AIVisualProvision-lagringsplatsen innehåller en uppsättning inlärningsbilder som du kan använda. På webbplatsen väljer du knappen **Lägg till avbildningar** på fliken **träna bilder** . Gå sedan till mappen **dokument/bilder/Training_DataSet** i lagrings platsen. Du måste manuellt tagga logotyperna i varje bild, så om du bara testar det här projektet kan det vara bra att ladda upp endast en delmängd av bilderna. Ladda upp minst 15 instanser av varje tagg som du tänker använda.

När du har laddat upp träningsbilderna väljer du den första på skärmen. Taggnings fönstret visas. Rita rutor och tilldela taggar för en logotyp i varje bild. 

![Logotyptaggning på Custom Vision-webbplatsen](media/azure-logo-tutorial/tag-logos.png)

Appen är konfigurerad för att fungera med specifika taggsträngar. Definitionerna finns i filen *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

När du har taggat en bild går du till höger för att tagga nästa. Stäng taggningsfönstret när du är klar.

## <a name="train-the-object-detector"></a>Träna objektidentifieraren

I den vänstra rutan ställer du in reglaget **Taggar** på **Tagged** (Taggad) för att visa bilderna. Välj sedan den gröna knappen överst på sidan för att träna modellen. Algoritmen kommer att träna att identifiera samma Taggar i nya bilder. Det testar också modellen på några av dina befintliga bilder för att generera noggrannhetspoäng.

![Custom Vision webbplats på fliken träna bilder. I den här skärm bilden beskrivs knappen träna](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Hämta förutsägelse-URL

När din modell har tränats är du redo att integrera den med din app. Du måste hämta slut punkts-URL: en (adressen till din modell som appen kommer att fråga) och förutsägelse nyckeln (för att ge appen åtkomst till förutsägelse begär Anden). På fliken **Prestanda** väljer du knappen **Prediction URL** (Förutsägelse-URL) längst upp på sidan.

![Custom Vision-webbplatsen med en förutsägelse-API-skärm som visar en URL-adress och en API-nyckel](media/azure-logo-tutorial/cusvis-endpoint.png)

Kopiera slut punkts **-** URL: en och resultatvärdet till lämpliga fält i *Source\VisualProvision\AppSettings.cs* -filen:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Granska Custom Vision-användning

Öppna filen *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* för att se hur appen använder din Custom Vision-nyckel och slutpunkts-URL. Metoden **PredictImageContentsAsync** tar en byteström av en bildfil tillsammans med en annuleringstoken (för hantering av asynkron aktivitet), anropar förutsägelse-API:t för Custom Vision och returnerar förutsägelseresultatet. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Resultatet får formen av en **PredictionResult**-instans, som innehåller en lista över **förutsägelseinstanser**. En **förutsägelse** innehåller en identifierad tagg och dess avgränsningsfältsplats i bilden.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Om du vill lära dig mer om hur appen hanterar dessa data börjar du med metoden **GetResourcesAsync**. Den här metoden definieras i filen *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Lägga till Visuellt innehåll

Custom Vision-delen av självstudien är klar. Om du vill att köra appen måste du integrera tjänsten Visuellt innehåll också. Appen använder textidentifieringsfunktionen i Visuellt text för att komplettera processen för logotypidentifiering. En Azure-logotyp kan identifieras utefter sitt utseende *eller* med den text som är skriven nära den. Till skillnad från Custom Vision-modeller är Visuellt innehåll tränat i förväg för att utföra vissa åtgärder på bilder eller videor.

Prenumerera på tjänsten Visuellt innehåll för att hämta en nyckel och slutpunkts-URL. Hjälp om det här steget finns i [Skaffa prenumerationsnycklar](../cognitive-services-apis-create-account.md?tabs=singleservice%2Cwindows).

![Tjänsten Visuellt innehåll i Azure Portal, med snabb starts menyn vald. En länk för nycklar samt slutpunkts-URL för API är markerade](media/azure-logo-tutorial/comvis-keys.png)

Nu öppnar du filen *Source\VisualProvision\AppSettings.cs* och fyller i variablerna `ComputerVisionEndpoint` och `ComputerVisionKey` med rätt värden.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Appen kräver ett Azure-konto för tjänstens huvudnamn för att distribuera tjänster till din Azure-prenumeration. Med ett huvud namn för tjänsten kan du delegera särskilda behörigheter till en app med hjälp av rollbaserad åtkomst kontroll i Azure. Mer information finns i [guiden för tjänstens huvudnamn](/azure-stack/operator/azure-stack-create-service-principals).

Du kan skapa ett huvudnamn för tjänsten med hjälp av Azure Cloud Shell eller Azure CLI såsom det visas här. Börja med att logga in och välja den prenumeration som du vill använda.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Skapa sedan tjänstens huvudnamn. (Den här processen kan ta lite tid att slutföra.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Vid slutförande bör du se följande JSON-utdata, däribland nödvändiga autentiseringsuppgifter.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Anteckna värdena `clientId` och `tenantId`. Lägg till dem i rätt fält i filen *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Kör appen

Nu har du gett appen åtkomst till:

- En tränad Custom Vision-modell
- Tjänsten Visuellt innehåll
- Ett tjänsthuvudnamn

Följ dessa steg om du vill köra appen:

1. I Visual Studio Solution Explorer väljer du antingen projektet **VisualProvision.Android** eller projektet **VisualProvision.iOS**. Välj en motsvarande emulator eller ansluten mobil enhet från den nedrullningsbara menyn på huvudverktygsfältet. Kör sedan appen.

    > [!NOTE]
    > Du behöver en MacOS-enhet för att köra en iOS-emulator.

1. På den första skärmen anger du klient-ID för tjänstens huvudnamn, klientorganisations-ID och lösenord. Välj knappen **Logga in**.

    > [!NOTE]
    > På vissa emulatorer har knappen **Logga in** kanske inte aktiverats i det här steget. Om det här händer stoppar du appen, öppnar filen *Source/VisualProvision/Pages/LoginPage.xaml*, letar rätt på det `Button`-element som är märkt **LOGIN BUTTON** (Inloggningsknapp), tar bort följande rad och kör sedan appen igen.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Appskärmen som visar fält för autentiseringsuppgifter för tjänstens huvudnamn](media/azure-logo-tutorial/app-credentials.png)

1. På nästa skärm väljer du din Azure-prenumeration i den nedrullningsbara menyn. (Den här menyn ska innehålla alla prenumerationer som tjänstens huvud namn har åtkomst till.) Välj knappen **Fortsätt** . I det här läget kan appen uppmana dig att ge åtkomst till enhetens kamera och fotolagring. Bevilja åtkomstbehörigheterna.

    ![Appskärmen som visar en listruta för mål-Azure-prenumeration](media/azure-logo-tutorial/app-az-subscription.png)


1. Kameran på enheten aktiveras. Ta ett foto av en av Azure-tjänstens logotyper som du har tränat. En distributionsfönster uppmanar dig att välja en region och en resursgrupp för de nya tjänsterna (som du skulle göra om du hade distribuerat dem från Azure-portalen). 

    ![En smartphonekameraskärm som fokuserar på två pappersurklipp med Azure-logotyper](media/azure-logo-tutorial/app-camera-capture.png)

    ![En appskärm som visar fält för distributionsregion och resursgrupp](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du har följt alla steg i det här scenariot och använt appen för att distribuera Azure-tjänster till ditt konto går du till [Azure-portalen](https://ms.portal.azure.com/). Där avbryter du de tjänster som du inte vill använda.

Om du planerar att skapa egna objektidentifieringsprojekt med Custom Vision kan det vara bra att ta bort det logotypidentifieringsprojekt som du skapade i den här självstudien. En kostnads fri prenumeration på Custom Vision tillåter endast två projekt. För att ta bort logotypidentifieringsprojektet går du till [Custom Vision-webbplatsen](https://customvision.ai), öppnar **Projekt** och väljer papperskorgsikonen under **Mitt nya projekt**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du och utforskar en komplett Xamarin.Forms-app som använder Custom Vision-tjänsten för att identifiera logotyper i mobilkamerabilder. Nu kan du läsa om metodtips för att skapa en Custom Vision-modell, så att du kan göra den kraftfull och korrekt när du skapar en modell för din egen app.

> [!div class="nextstepaction"]
> [Förbättra din klassificerare](getting-started-improving-your-classifier.md)
