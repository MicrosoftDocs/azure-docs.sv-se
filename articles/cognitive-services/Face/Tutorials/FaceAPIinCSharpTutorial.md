---
title: 'Självstudie: Identifiera och visa ansiktsinformation i en bild med .NET SDK'
titleSuffix: Azure Cognitive Services
description: I den här självstudien kommer du att skapa en Windows-app som använder ansikts tjänsten för att identifiera och visa en bilds ansikten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: d5d16181eb4dda08f002587545f99fab757b6b0e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586434"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Självstudie: skapa en Windows Presentation Framework-app (WPF) för att Visa ansikts data i en bild

I den här självstudien får du lära dig hur du använder Azures ansikts tjänst via .NET-klient-SDK: n för att identifiera ansikten i en bild och sedan presentera dessa data i användar gränssnittet. Du skapar ett WPF-program som identifierar ansikten, ritar en ram runt varje ansikte och visar en beskrivning av FACET i statusfältet. 

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett WPF-program
> - Installera ansikts klient biblioteket
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte
> - Visa en beskrivning av det markerade ansikten i statusfältet

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)

Den fullständiga exempelkoden är tillgänglig på [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)-lagringsplatsen på GitHub.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 


## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, med namnet `FACE_SUBSCRIPTION_KEY` respektive `FACE_ENDPOINT` .
- Vilken version av [Visual Studio](https://www.visualstudio.com/downloads/)som helst.

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Följ dessa steg för att skapa ett nytt WPF-programprojekt.

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installed** (Installerat), sedan **Visual C#** och välj **WPF App (.NET Framework)**.
1. Ge programmet namnet **FaceTutorial** och klicka på **OK**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - [Microsoft. Azure. CognitiveServices. vision. Face 2.6.0 – för hands version. 1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>Lägg till den första koden

I det här avsnittet lägger du till appens grundläggande ramverk utan dess ansiktsspecifika funktioner.

### <a name="create-the-ui"></a>Skapa användargränssnittet

Öppna *MainWindow. XAML* och ersätt innehållet med följande kod &mdash; den här koden skapar användar gränssnitts fönstret. `FacePhoto_MouseMove`Metoderna och `BrowseButton_Click` är händelse hanterare som du senare ska definiera.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Skapa klassen main

Öppna *MainWindow.xaml.cs* och lägg till klientbibliotekets namnområden, tillsammans med andra nödvändiga namnrymder. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Infoga därefter följande kod i klassen **MainWindow**. Den här koden skapar en **FaceClient** -instans med hjälp av prenumerations nyckeln och slut punkten.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Lägg sedan till **MainWindow** -konstruktorn. Den kontrollerar slut punktens URL-sträng och kopplar den sedan till-klient-objektet.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Lägg slutligen till metoderna **BrowseButton_Click** och **FacePhoto_MouseMove** i klassen. Dessa metoder motsvarar de händelse hanterare som deklareras i *MainWindow. XAML*. Metoden **BrowseButton_Click** skapar en **OpenFileDialog**, där användaren kan välja en .jpg-bild. Sedan visas bilden i huvudfönstret. Du infogar de återstående koden för **BrowseButton_Click** och **FacePhoto_MouseMove** i senare steg. Observera även `faceList`-referensen&mdash;en lista över **DetectedFace**-objekt. Den här referensen är platsen där din app lagrar och anropar faktiska ansikts data.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Prova appen

Tryck på **Start** (Starta) på menyn för att testa din app. När appfönstret öppnas klickar du på **Browse** (Bläddra) i det nedre vänstra hörnet. Dialogrutan **File Open** (Öppna fil) bör visas. Välj en bild i filsystemet och verifiera att den visas i fönstret. Stäng sedan appen och gå till nästa steg.

![Skärmbild som visar oförändrad bild på ansikten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Ladda upp bilden och identifiera ansikten

Appen identifierar ansikten genom att anropa metoden **FaceClient.Face.DetectWithStreamAsync**, som omsluter REST API för [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för uppladdning av en lokal bild.

Infoga följande metod i klassen **MainWindow**, under metoden **FacePhoto_MouseMove**. Den här metoden definierar en lista över ansikts attribut som hämtar och läser den skickade avbildnings filen till en **data ström**. Sedan skickar den båda objekten till metodanropet **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rita rektanglar runt ansikten

Därefter lägger du till koden för att rita en rektangel runt varje identifierat ansikte i bilden. I klassen **MainWindow** infogar du följande kod i slutet av metoden **BrowseButton_Click**, efter `FacePhoto.Source = bitmapSource`-raden. Den här koden fyller i en lista över identifierade ansikten från anropet till **UploadAndDetectFaces**. Sedan ritar den en rektangel runt varje ansikte och visar den ändrade bilden i huvudfönstret.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Beskriv ansiktena

Lägg till följande metod i klassen **MainWindow**, under metoden **UploadAndDetectFaces**. Den här metoden konverterar de hämtade ansikts attributen till en sträng som beskriver FACET.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Visa ansiktsbeskrivning

Lägg till följande kod i metoden **FacePhoto_MouseMove**. Den här händelsehanteraren visar ansiktsbeskrivningssträngen i `faceDescriptionStatusBar` när markören hovrar över en identifierad ansiktsrektangel.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild som innehåller ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Du bör se en röd rektangel kring varje ansikte i bilden. Om du för muspekaren över en ansiktsrektangel bör beskrivningen av ansiktet visas i statusfältet.

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig den grundläggande processen för att använda Ansiktstjänstens .NET SDK och skapat ett program för att visa och rama in ansikten i en bild. Härnäst får du mer information om ansiktsigenkänning.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
