---
title: 'Exempel: utforska en app för bild bearbetning i C #'
titleSuffix: Azure Cognitive Services
description: Utforska en grundläggande Windows-app som använder API för visuellt innehåll i Azure Cognitive Services. Utför OCR, skapa miniatyrer och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432595"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Exempel: utforska en app för bild bearbetning med C #

Utforska ett grundläggande Windows-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella egenskaper, inklusive ansikten, i en bild. Med exemplet nedan kan du skicka en bild-URL eller en lokalt lagrad fil. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med hjälp av API för visuellt innehåll och Windows Presentation Foundation (WPF), en del av .NET Framework.

> [!div class="checklist"]
> * Hämta exempelappen från GitHub
> * Öppna och skapa exempelappen i Visual Studio
> * Köra exempelappen och interagera med den för att utföra olika scenarier
> * Utforska de olika scenarier som ingår i exempelappen

## <a name="prerequisites"></a>Förutsättningar

Innan du utforskar exempelappen ska du uppfylla följande förutsättningar:

* Du måste ha [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) eller senare.
* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="get-the-sample-app"></a>Hämta exempelappen

Exempelappen för Visuellt innehåll är tillgänglig på GitHub från lagringsplatsen `Microsoft/Cognitive-Vision-Windows`. Den här lagringsplatsen innehåller även lagringsplatsen `Microsoft/Cognitive-Common-Windows` som en Git-undermodul. Du kan rekursivt klona den här lagringsplatsen, inklusive undermodulen, genom att antingen använda kommandot `git clone --recurse-submodules` från kommandoraden eller med hjälp av GitHub Desktop.

Till exempel kör du följande kommando från en kommandotolk för att rekursivt klona databasen för exempelappen för Visuellt innehåll:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Ladda inte ned den här lagringsplatsen som en ZIP-fil. Git inkluderar inte undermoduler vid nedladdning av en lagringsplats som en ZIP-fil.

### <a name="get-optional-sample-images"></a>Hämta valfria exempelbilder

Alternativt kan du använda de exempelbilder som ingår i exempelappen [Face](../../Face/Overview.md), som finns på GitHub från lagringsplatsen `Microsoft/Cognitive-Face-Windows`. Exempelappen innehåller en mapp, `/Data`, där det finns flera bilder på personer. Du kan rekursivt klona den här lagringsplatsen också, med de metoder som beskrivs för exempelappen för Visuellt innehåll.

Till exempel kör du följande kommando från en kommandotolk för att rekursivt klona databasen för exempelappen för Face:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Öppna och skapa exempelappen i Visual Studio

Du måste först skapa exempelappen så att Visual Studio kan lösa beroenden innan du kan köra eller utforska exempelappen. För att öppna och skapa exempelappen utför du följande steg:

1. Öppna Visual Studio-lösningsfilen `/Sample-WPF/VisionAPI-WPF-Samples.sln` i Visual Studio.
1. Se till att Visual Studio-lösningen innehåller två projekt:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Om projektet SampleUserControlLibrary är tillgängligt bekräftar du att du har klonat lagringsplatsen `Microsoft/Cognitive-Vision-Windows` rekursivt.
1. I Visual Studio trycker du antingen på Ctrl + Skift + B eller väljer **Build** (Skapa) i menyfliksområdet och sedan **Build Solution** (Skapa lösning) för att skapa lösningen.

## <a name="run-and-interact-with-the-sample-app"></a>Köra och interagera med exempelappen

Du kan köra exempelappen om du vill se hur den interagerar med dig och med klientbiblioteket för Visuellt innehåll när du utför olika uppgifter, till exempel att generera miniatyrbilder eller tagga bilder. Om du vill köra och interagera med exempelappen utför du följande steg:

1. När skapandet är klart trycker du antingen på **F5** eller väljer **Felsök** i menyfliksområdet följt av **Starta felsökning** för att köra exempelappen.
1. När exempelappen visas väljer du **Subscription Key Management** (Hantering av prenumerationsnyckel) från navigeringsfönstret för att visa sidan för hantering av prenumerationsnyckel.
   ![Sidan för hantering av prenumerationsnyckel](../Images/Vision_UI_Subscription.PNG)  
1. Ange din prenumerationsnyckel i **Prenumerationsnyckel**.
1. Ange slut punkts-URL: en i **slut punkten**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Om du inte vill ange prenumerationsnyckeln och slutpunkts-URL:en nästa gång du kör exempelappen väljer du **Save Setting** (Spara inställning) för att spara prenumerationsnyckeln och slutpunkts-URL:en till datorn. Om du vill ta bort din tidigare sparade prenumerationsnyckel och slutpunkts-URL väljer du **Delete Setting** (Ta bort inställning).

   > [!NOTE]
   > Exempelappen använder isolerad lagring och `System.IO.IsolatedStorage` för att lagra prenumerationsnyckeln och slutpunkts-URL.

1. Under **Välj ett scenario** i navigeringsfönstret väljer du något av de scenarier som för närvarande ingår i exempelappen:  

   | Scenario | Beskrivning |
   |----------|-------------|
   |Analyze Image (Analysera bild) | Använder åtgärden [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analysera bild) för att analysera en lokal bild eller en fjärrbild. Du kan välja visuella egenskaper och språk för analysen och se både bilden och resultatet.  |
   |Analyze Image with Domain Model (Analysera bild med domänmodell) | Använder åtgärden [List Domain Specific Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) (Lista domänspecifika modeller) för att lista de domänmodeller som du kan välja bland, och åtgärden [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Identifiera domänspecifikt innehåll) för att analysera en lokal bild eller en fjärrbild med hjälp av vald domänmodell. Du kan även välja önskat språk för analysen. |
   |Describe Image (Beskriv bild) | Använder åtgärden [Describe Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) (Beskriv bild) för att skapa en beskrivning av en lokal bild eller en fjärrbild som kan läsas av människor. Du kan även välja önskat språk för beskrivningen. |
   |Generate Tags (Skapa taggar) | Använder åtgärden [Tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) (Tagga bild) för att tagga de visuella egenskaperna i en lokal bild eller en fjärrbild. Du kan även välja önskat språk som används för taggarna. |
   |Recognize Text (OCR) (Identifiera text (OCR)) | Använder åtgärden [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) för att identifiera och extrahera tryckt text från en bild. Du kan antingen välja det språk som ska användas eller låta Visuellt innehåll identifiera språket automatiskt. |
   |Recognize Text V2 (English) (Identifiera text V2 (engelska)) | Använder åtgärderna [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) (Identifiera text) och [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) (Hämta resultat från åtgärden Identifiera text) för att asynkront identifiera och extrahera tryckt eller handskriven text från en bild. |
   |Get Thumbnail (Hämta miniatyrbild) | Använder åtgärden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Hämta miniatyrbild) för att skapa en miniatyrbild för en lokal bild eller en fjärrbild. |

   Följande skärmbild visar den sida som tillhandahålls för scenariot Analyze Image (Analysera bild) när en exempelbild har analyserats.
   ![Skärmbild av sidan Analysera bild](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Utforska exempelappen

Visual Studio-lösningen för Visuellt innehåll-exempelappen innehåller två projekt:

* SampleUserControlLibrary  
  Projektet SampleUserControlLibrary innehåller funktioner som delas av flera Cognitive Services-exempel. Projektet innehåller följande:
  * SampleScenarios  
    En UserControl som ger en standardiserad presentation, till exempel namnlisten, navigeringsfönstret och innehållsrutan för exempel. Visuellt innehåll-exempelappen använder den här kontrollen i MainWindow.xaml-fönstret för att visa scenariosidor och komma åt information som delas mellan scenarier, till exempel prenumerationsnyckel och slutpunkts-URL.
  * SubscriptionKeyPage  
    En sida som ger en standardiserad layout för inmatning av en prenumerationsnyckel och slutpunkts-URL för exempelappen. Visuellt innehåll-exempelappen använder den här sidan för att hantera den prenumerationsnyckel och den slutpunkts-URL som används av scenariosidorna.
  * VideoResultControl  
    En UserControl som ger en standardiserad presentation för videoinformation. Visuellt innehåll-exempelappen använder inte den här kontrollen.
* VisionAPI-WPF-Samples  
  Det här är huvudprojektet för Visuellt innehåll-exempelappen och innehåller alla intressanta funktioner för Visuellt innehåll. Projektet innehåller följande:
  * AnalyzeInDomainPage.xaml  
    Scenariosidan för scenariot Analyze Image with Domain Model (Analysera bild med domänmodell)
  * AnalyzeImage.xaml  
    Scenariosidan för scenariot Analyze Image (Analysera bild)
  * DescribePage.xaml  
    Scenariosidan för scenariot Describe Image (Beskriv bild)
  * ImageScenarioPage.cs  
    ImageScenarioPage-klassen, där alla scenariosidor i exempelappen kommer från. Den här klassen hanterar funktioner såsom att ange autentiseringsuppgifter och formatera utdata, som delas av alla scenariosidor.
  * MainWindow.xaml  
    Huvudfönstret för exempelappen. Den använder SampleScenarios-kontrollen för att presentera SubscriptionKeyPage och scenariosidorna.
  * OCRPage.xaml  
    Scenariosidan för scenariot Recognize Text (OCR) (Identifiera text (OCR)).
  * RecognizeLanguage.cs  
    Klassen RecognizeLanguage, som innehåller information om de språk som stöds av de olika metoderna i exempelappen.
  * TagsPage.xaml  
    Scenariosidan för scenariot Generate Tags (Skapa taggar).
  * TextRecognitionPage.xaml  
    Scenariosidan för scenariot Recognize Text V2 (English) (Identifiera text V2 (engelska)).
  * ThumbnailPage.xaml  
    Scenariosidan för scenariot Get Thumbnail (Hämta miniatyrbild).

### <a name="explore-the-sample-code"></a>Utforska exempelkoden

Viktiga delar av exempelkoden är inramade med kommentarblock som börjar med `KEY SAMPLE CODE STARTS HERE` och slutar med `KEY SAMPLE CODE ENDS HERE`, så att det blir enklare att utforska exempelappen. De här viktiga delarna av exempelkoden innehåller den kod som är mest relevant för att lära sig använda klientbiblioteket för API för visuellt innehåll för att utföra olika uppgifter. Du kan söka efter `KEY SAMPLE CODE STARTS HERE` i Visual Studio för att flytta mellan de mest relevanta kodavsnitten i Visuellt innehåll-exempelappen. 

Till exempel demonstrerar metoden `UploadAndAnalyzeImageAsync`, som visas nedan och ingår i AnalyzePage.xaml, hur du använder klientbiblioteket för att analysera en lokal bild genom att anropa metoden `ComputerVisionClient.AnalyzeImageInStreamAsync`.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Utforska klientbiblioteket

Den här exempelappen använder klientbiblioteket för API för visuellt innehåll, en tunn C#-klientomslutning för API för visuellt innehåll i Azure Cognitive Services. Klientbiblioteket är tillgängligt från NuGet i paketet [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/). När du skapade Visual Studio-programmet hämtade du klientbiblioteket från dess motsvarande NuGet-paketet. Du kan även visa källkoden för klientbiblioteket i mappen `/ClientLibrary` på lagringsplatsen `Microsoft/Cognitive-Vision-Windows`.

Klientbibliotekets funktioner är inriktade på klassen `ComputerVisionClient` i namnrymden `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`, medan de modeller som används av klassen `ComputerVisionClient` vid interaktion med Visuellt innehåll finns i namnrymden `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. I de olika XAML-scenariosidor som ingår i exempelappen hittar du följande `using`-direktiv för de namnrymderna:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Du lär dig mer om de olika metoderna som ingår i klassen `ComputerVisionClient` när du utforskar de scenarier som ingår i Visuellt innehåll-exempelappen.

## <a name="explore-the-analyze-image-scenario"></a>Utforska scenariot Analyze Image (Analysera bild)

Det här scenariot hanteras av sidan AnalyzePage.xaml. Du kan välja visuella egenskaper och språk för analysen och se både bilden och resultatet. Scenariosidan gör detta genom att använda en av följande metoder, beroende på bildens källa:

* UploadAndAnalyzeImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.AnalyzeImageInStreamAsync` anropas.
* AnalyzeUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.AnalyzeImageAsync` anropas.

Metoden `UploadAndAnalyzeImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala filen enligt det som anges i `imageFilePath` för läsning som en `Stream` och hämtar sedan de visuella egenskaper och det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.AnalyzeImageInStreamAsync`, skickar `Stream` för filen, de visuella egenskaperna och språket, och returnerar sedan resultatet som en `ImageAnalysis`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `AnalyzeUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den hämtar de visuella egenskaper och det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.AnalyzeImageInStreamAsync`, skickar bild-URL, de visuella egenskaperna och språket, och returnerar sedan resultatet som en `ImageAnalysis`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Utforska scenariot Analyze Image with Domain Model (Analysera bild med domänmodell)

Det här scenariot hanteras av sidan AnalyzeInDomainPage.xaml. Du kan välja en domänmodell, till exempel `celebrities` eller `landmarks`, och språk för att utföra en domänspecifik analys av bilden och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* GetAvailableDomainModelsAsync  
  Den här metoden hämtar listan över tillgängliga domänmodeller från Visuellt innehåll och fyller kombinationsrutekontrollen `_domainModelComboBox` på sidan med hjälp av metoden `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` anropas.
* AnalyzeInDomainUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.AnalyzeImageByDomainAsync` anropas.

Metoden `UploadAndAnalyzeInDomainImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala filen enligt det som anges i `imageFilePath` för läsning som en `Stream` och hämtar sedan det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`, skickar `Stream` för filen, namnet på domänmodellen samt språket, och returnerar sedan resultatet som en `DomainModelResults`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `AnalyzeInDomainUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den hämtar det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.AnalyzeImageByDomainAsync`, skickar bild-URL, de visuella egenskaperna och språket, och returnerar sedan resultatet som en `DomainModelResults`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="explore-the-describe-image-scenario"></a>Utforska scenariot Describe Image (Beskriv bild)

Det här scenariot hanteras av sidan DescribePage.xaml. Du kan välja ett språk för att skapa en beskrivning av bilden som kan läsas av människor och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* UploadAndDescribeImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.DescribeImageInStreamAsync` anropas.
* DescribeUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.DescribeImageAsync` anropas.

Metoden `UploadAndDescribeImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala filen enligt det som anges i `imageFilePath` för läsning som en `Stream` och hämtar sedan det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.DescribeImageInStreamAsync`, skickar `Stream` för filen, det maximala antalet kandidater (i det här fallet 3) samt språket, och returnerar sedan resultatet som en `ImageDescription`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `DescribeUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den hämtar det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.DescribeImageAsync`, skickar bild-URL:en, det maximala antalet kandidater (i det här fallet 3) samt språket, och returnerar sedan resultatet som en `ImageDescription`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="explore-the-generate-tags-scenario"></a>Utforska scenariot Generate Tags (Skapa taggar)

Det här scenariot hanteras av sidan TagsPage.xaml. Du kan välja ett språk för att tagga de visuella egenskaperna på en bild och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* UploadAndGetTagsForImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.TagImageInStreamAsync` anropas.
* GenerateTagsForUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.TagImageAsync` anropas.

Metoden `UploadAndGetTagsForImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala filen enligt det som anges i `imageFilePath` för läsning som en `Stream` och hämtar sedan det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.TagImageInStreamAsync`, skickar `Stream` för filen och språket, och returnerar sedan resultatet som en `TagResult`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `GenerateTagsForUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den hämtar det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.TagImageAsync`, skickar bild-URL:en samt språket, och returnerar sedan resultatet som en `TagResult`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Utforska scenariot Recognize Text (OCR) (Identifiera text (OCR))

Det här scenariot hanteras av sidan OCRPage.xaml. Du kan välja ett språk för att identifiera och extrahera tryckt text från en bild och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* UploadAndRecognizeImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.RecognizePrintedTextInStreamAsync` anropas.
* RecognizeUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.RecognizePrintedTextAsync` anropas.

Metoden `UploadAndRecognizeImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala filen enligt det som anges i `imageFilePath` för läsning som en `Stream` och hämtar sedan det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.RecognizePrintedTextInStreamAsync`, indikerar att orienteringen inte har identifierats och skickar `Stream` för filen och språket. Sedan returnerar den resultatet som en `OcrResult`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `RecognizeUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den hämtar det språk som valts på scenariosidan. Den anropar metoden `ComputerVisionClient.RecognizePrintedTextAsync`, indikerar att orienteringen inte har identifierats och skickar bild-URL:en samt språket. Sedan returnerar den resultatet som en `OcrResult`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Utforska scenariot Recognize Text V2 (English) (Identifiera text V2 (engelska))

Det här scenariot hanteras av sidan TextRecognitionPage.xaml. Du kan välja identifieringsläge och ett språk för att asynkront identifiera och extrahera antingen tryckt eller handskriven text från en bild och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* UploadAndRecognizeImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `RecognizeAsync` anropas och en parametriserad delegat skickas för metoden `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `RecognizeAsync` anropas och en parametriserad delegat skickas för metoden `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync Den här metoden hanterar de asynkrona anropen för de båda metoderna `UploadAndRecognizeImageAsync` och `RecognizeUrlAsync` samt avsökning för resultat genom att anropa metoden `ComputerVisionClient.GetTextOperationResultAsync`.

Till skillnad från andra scenarier som ingår i Visuellt innehåll-exempelappen är det här scenariot asynkront på så sätt att en metod anropas för att starta processen, men en annan metod anropas för att kontrollera status och returnera resultatet för den processen. Det logiska flödet i det här scenariot skiljer sig något från flödet i andra scenarier.

Metoden `UploadAndRecognizeImageAsync` öppnar den lokala fil som anges i `imageFilePath` för läsning som en `Stream`. Sedan anropar den metoden `RecognizeAsync` och skickar:

* Ett lambda-uttryck för en parametriserad asynkron delegat för metoden `ComputerVisionClient.RecognizeTextInStreamAsync` med `Stream` för filen och identifieringsläget som parametrar i `GetHeadersAsyncFunc`.
* En lambda-uttryck för att en delegat ska hämta `Operation-Location`-svarshuvudvärdet i `GetOperationUrlFunc`.

Metoden `RecognizeUrlAsync` anropar metoden `RecognizeAsync` och skickar:

* Ett lambda-uttryck för en parametriserad asynkron delegat för metoden `ComputerVisionClient.RecognizeTextAsync` med URL:en för fjärrbilden och identifieringsläget som parametrar i `GetHeadersAsyncFunc`.
* En lambda-uttryck för att en delegat ska hämta `Operation-Location`-svarshuvudvärdet i `GetOperationUrlFunc`.

När metoden `RecognizeAsync` slutförs returnerar de båda metoderna `UploadAndRecognizeImageAsync` och `RecognizeUrlAsync` resultatet som en `TextOperationResult`-instans. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `RecognizeAsync` anropar den parametriserade delegaten för antingen metoden `ComputerVisionClient.RecognizeTextInStreamAsync` eller `ComputerVisionClient.RecognizeTextAsync` som skickas i `GetHeadersAsyncFunc` och väntar på svaret. Metoden anropar sedan den delegat som skickas i `GetOperationUrlFunc` för att hämta `Operation-Location`-svarshuvudvärdet från svaret. Det här värdet är den URL som används för att hämta resultatet av den metod som skickas i `GetHeadersAsyncFunc` från Visuellt innehåll.

Metoden `RecognizeAsync` anropar sedan metoden `ComputerVisionClient.GetTextOperationResultAsync` och skickar den URL som hämtas från `Operation-Location`-svarshuvudet för att hämta status och resultatet av den metod som skickas i `GetHeadersAsyncFunc`. Om statusen inte indikerar att metoden slutfördes, korrekt eller felaktigt, gör metoden `RecognizeAsync` anrop till `ComputerVisionClient.GetTextOperationResultAsync` ytterligare 3 gånger och väntar 3 sekunder mellan anropen. Metoden `RecognizeAsync` returnerar resultatet till den metod som anropade den.

## <a name="explore-the-get-thumbnail-scenario"></a>Utforska scenariot Get Thumbnail (Hämta miniatyrbild)

Det här scenariot hanteras av sidan ThumbnailPage.xaml. Du kan ange huruvida du vill använda smart beskärning och ange önskad höjd och bredd för att generera en miniatyrbild från en bild, och se både bilden och resultatet. Scenariosidan använder följande metoder, beroende på bildens källa:

* UploadAndThumbnailImageAsync  
  Den här metoden används för lokala bilder där bilden måste kodas som en `Stream` och skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.GenerateThumbnailInStreamAsync` anropas.
* ThumbnailUrlAsync  
  Den här metoden används för fjärrbilder där URL:en för bilden skickas till Visuellt innehåll genom att metoden `ComputerVisionClient.GenerateThumbnailAsync` anropas.

Metoden `UploadAndThumbnailImageAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Eftersom exempelappen analyserar en lokal bild måste den skicka innehållet i den bilden till Visuellt innehåll. Den öppnar den lokala fil som anges i `imageFilePath` för läsning som en `Stream`. Den anropar metoden `ComputerVisionClient.GenerateThumbnailInStreamAsync` och skickar bredd, höjd, `Stream` för filen samt huruvida du vill använda smart beskärning, och returnerar sedan resultatet som en `Stream`. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

Metoden `RecognizeUrlAsync` skapar en ny `ComputerVisionClient`-instans med hjälp av den angivna prenumerationsnyckeln och slutpunkts-URL:en. Den anropar metoden `ComputerVisionClient.GenerateThumbnailAsync` och skickar bredd, höjd, URL:en för bilden samt huruvida du vill använda smart beskärning, och returnerar sedan resultatet som en `Stream`. De metoder som ärvs från klassen `ImageScenarioPage` visar det returnerade resultatet på scenariosidan.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver den mapp dit du klonade lagringsplatsen `Microsoft/Cognitive-Vision-Windows` kan du ta bort den. Om du valde att använda exempelbilderna tar du även bort den mapp dit du klonade lagringsplatsen `Microsoft/Cognitive-Face-Windows`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med ansikts tjänsten](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
