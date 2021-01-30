---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 7386cfaaf410614c6eeee19669f8f769ad691c1f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214162"
---
Om du vill skapa ett Visual Studio-projekt för Universell Windows-plattform (UWP)-utveckling måste du ställa in alternativ för Visual Studio-utveckling, skapa projektet, välja mål arkitektur, konfigurera ljud fångst och installera tal-SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera alternativ för Visual Studio-utveckling

Starta genom att kontrol lera att du är korrekt konfigurerad i Visual Studio för UWP-utveckling:

1. Öppna Visual Studio 2019 för att visa **Start** fönstret.

   ![Skärm bild som visar "Start"-fönstret med åtgärden "Fortsätt utan kod" markerat.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Välj **Fortsätt utan kod** för att gå till Visual Studio IDE.

1. I meny raden i Visual Studio väljer du **verktyg**  >  **Hämta verktyg och funktioner** för att öppna Visual Studio Installer och Visa dialog rutan **ändra** .

   ![Skärm bild som visar fliken "arbets belastningar" i dialog rutan "ändra" med "Universell Windows-plattform utveckling" markerad.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. På fliken **arbets belastningar** under **Windows** hittar du arbets belastningen **universell Windows-plattform utveckling** . Om kryss rutan bredvid arbets belastningen redan är vald stänger du dialog rutan **ändra** och går till steg 6.

1. Markera kryss rutan **universell Windows-plattform utveckling** , Välj **ändra**. i dialog rutan **innan vi kom igång** väljer du **Fortsätt** för att installera UWP Development-arbetsbelastningen. Det kan ta en stund att installera den nya funktionen.

1. Stäng Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Skapa projektet och välj mål arkitekturen

Skapa sedan ditt projekt:

1. I meny raden i Visual Studio väljer du **Arkiv**  >  **nytt**  >  **projekt** för att visa fönstret **skapa ett nytt projekt** .

   ![Skärm bild som visar fönstret "skapa ett nytt projekt" med "Tom app (Universal Windows)" markerat och knappen Nästa är markerad.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Hitta och välj **Tom app (Universal Windows)**. Kontrol lera att du väljer C#-versionen av den här projekt typen (till skillnad från Visual Basic).

1. Välj **Nästa** för att visa skärmen **Konfigurera ditt nya projekt** .

   ![Skärm bild som visar skärmen "Konfigurera ditt nya projekt" med fälten "projekt namn" och "plats" och "skapa" markerat.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. I **projekt namn** anger du `helloworld` .

1. I **plats** navigerar du till och väljer eller skapar mappen där du vill spara projektet i.

1. Välj **skapa** för att gå till det **nya universell Windows-plattform projekt** fönstret.

   ![Skärm bild som visar dialog rutan nytt Universell Windows-plattform projekt.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. I **lägsta version** (den andra List rutan) väljer du **Windows 10 hösten Creators Update (10,0; Build 16299)**, vilket är minimi kravet för tal-SDK.

1. I **mål version** (den första List rutan) väljer du ett värde som är identiskt med eller senare än värdet i **lägsta version**.

1. Välj **OK**. Du kommer tillbaka till Visual Studio IDE, där det nya projektet skapas och visas i rutan **Solution Explorer** .

   ![HelloWorld-projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Välj nu mål plattforms arkitektur. I Visual Studio-verktygsfältet hittar du List rutan med **plattforms plattformar** . (Om du inte ser det väljer du **Visa**  >  **Verktygsfält**  >  **Standard** för att visa det verktygsfält som innehåller **lösnings plattformar**.) Om du kör 64-bitars Windows väljer du **x64** i list rutan. 64-bitars Windows kan också köra 32-bitars program, så du kan välja **x86** om du föredrar det.

> [!NOTE]
> Tal-SDK: n stöder alla Intel-kompatibla processorer, men **bara x64** -versioner av ARM-processorer.

### <a name="set-up-audio-capture"></a>Konfigurera ljud inspelning

Tillåt att projektet fångar in ljud indata:

1. I **Solution Explorer** dubbelklickar du på **Package. appxmanifest** för att öppna paket applikations manifestet.

1. Välj fliken **Funktioner**.

   ![Fliken funktioner, paket program manifest – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Markera rutan för **mikrofonens** funktion.

1. Välj **Arkiv**  >  **Spara Package. appxmanifest** på Meny raden för att spara ändringarna.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Slutligen installerar du [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget)och refererar till tal-SDK: n i projektet:

1. I **Solution Explorer** högerklickar du på din lösning och väljer **Hantera NuGet-paket för lösning** för att gå till fönstret **NuGet-lösning** .

1. Välj **Bläddra**.

   ![Skärm bild som visar dialog rutan "Hantera paket för lösning" med fliken "Bläddra", "Sök"-rutan och "paket källa" markerad.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. I **paket källa** väljer du **NuGet.org**.

1. I **sökrutan anger du** `Microsoft.CognitiveServices.Speech` och väljer sedan det paketet efter att det visas i Sök resultatet.

   ![Skärm bild som visar "Microsoft. CognitiveServices. Speech" markerat med knappen projekt och knappen Installera markerad.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. I fönstret paket status bredvid Sök resultaten väljer du ditt **HelloWorld** -projekt.

1. Välj **installera**.

1. I dialog rutan **Förhandsgranska ändringar** väljer du **OK**.

1. I dialog rutan **licens godkännande** visar du licensen och väljer sedan **Jag accepterar**. Paket installationen startar och när installationen är klar visas ett meddelande som liknar följande text i fönstret **utdata** : `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
