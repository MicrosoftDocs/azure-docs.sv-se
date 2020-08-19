---
title: 'Självstudie: skapa en iOS-app som tar ett foto och startar den i den fördjupade läsaren (SWIFT)'
titleSuffix: Azure Cognitive Services
description: I den här självstudien kommer du att skapa en iOS-app från grunden och lägga till bilden i avancerad läsar funktion.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 968f3c9fbfea1e2f04fb029605173087f6f311c0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516511"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Självstudie: skapa en iOS-app som startar den fördjupade läsaren med innehåll från ett foto (SWIFT)

Den [fördjupade läsaren](https://www.onenote.com/learningtools) är ett särskilt utformat verktyg som implementerar beprövade tekniker för att förbättra läsningen av förståelse.

[Visuellt innehåll Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) identifierar text innehåll i en avbildning med hjälp av Microsofts senaste igenkännings modeller och konverterar den identifierade texten till en maskin läsnings bar tecken ström.

I den här självstudien skapar du en iOS-app från grunden och integrerar Read API och den fördjupade läsaren med hjälp av SDK för avancerad läsare. Ett komplett fungerande exempel på den här själv studie kursen finns [här](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar exempel projekt egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* Användningen av det här exemplet kräver en Azure-prenumeration på tjänsten Visuellt innehåll kognitiv. [Skapa en visuellt innehåll kognitiv tjänst resurs i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Skapa ett nytt projekt i Xcode.

![Nytt projekt](./media/ios/xcode-create-project.png)

Välj **en app för enskild vy**.

![Ny app för enskild vy](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Hämta SDK-CocoaPod
Det enklaste sättet att använda SDK: n för avancerad läsare är via CocoaPods. Installera via Cocoapods:
1. [Installera CocoaPods](http://guides.cocoapods.org/using/getting-started.html) – Följ guiden komma igång för att installera CocoaPods.
2. Skapa en Podfile genom `pod init` att köra i ditt Xcode-projekts rot Katalog.
3.  Lägg till CocoaPod till din Podfile genom att lägga till `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` . Din Podfile bör se ut så här, med målets namn, vilket ersätter Picture-to-intensivare-Reader-Swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. I terminalen, i katalogen för ditt Xcode-projekt, kör du kommandot `pod install` för att installera SDK-Pod för avancerad läsare.
5. Lägg till `import immersive_reader_sdk` alla filer som behöver referera till SDK: n.
6. Se till att öppna projektet genom att öppna `.xcworkspace` filen och inte `.xcodeproj` filen.

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

Du behöver vissa värden från det nödvändiga steget för Azure AD-autentisering ovan för den här delen. Se tillbaka till text filen som du sparade i sessionen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

I huvudprojektmappen som innehåller filen ViewController. SWIFT skapar du en SWIFT-klassfil som kallas konstanter. Swift. Ersätt klassen med följande kod och Lägg till i dina värden där det är tillämpligt. Behåll den här filen som en lokal fil som bara finns på din dator och se till att du inte ska spara filen i käll kontroll, eftersom den innehåller hemligheter som inte bör offentliggöras. Vi rekommenderar att du inte behåller hemligheterna i din app. I stället rekommenderar vi att du använder en backend-tjänst för att hämta token, där hemligheterna kan hållas utanför appen och av enheten. Server dels-API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth](https://oauth.net/2/)) för att förhindra att obehöriga användare får till gång till token som ska användas mot tjänsten för avancerad läsare och fakturering. Detta arbete ligger utanför den här självstudiens omfattning.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Konfigurera appen så att den körs utan en storyboard

Öppna AppDelegate. SWIFT och Ersätt filen med följande kod.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Lägg till funktioner för att ta och ladda upp foton

Byt namn på ViewController. SWIFT till PictureLaunchViewController. SWIFT och Ersätt filen med följande kod.

## <a name="build-and-run-the-app"></a>Kompilera och köra appen

Ange Arkiv schema i Xcode genom att välja en simulator eller ett enhets mål.
![Arkiv schema](./media/ios/xcode-archive-scheme.png)<br/>
![Välja mål](./media/ios/xcode-select-target.png)

Tryck på CTRL + R i Xcode eller klicka på uppspelnings knappen för att köra projektet och appen bör starta på den angivna simulatorn eller enheten.

I din app bör du se:

![Exempelapp](./media/ios/picture-to-immersive-reader-ipad-app.png)

I appen kan du ta eller ladda upp ett foto av text genom att trycka på knappen Ta foto eller klicka på knappen Välj foto från bibliotek. sedan startar den fördjupade läsaren visningen av texten från fotot.

![Avancerad läsare](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)
