---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 2960a4f55997c0db60c22e7841b4ba82708dd4e1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213557"
---
Om du vill skapa ett Visual Studio-projekt för C++ Desktop-utveckling måste du konfigurera alternativ för Visual Studio-utveckling, skapa projektet, välja mål arkitekturen och installera talet SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera alternativ för Visual Studio-utveckling

Starta genom att kontrol lera att du är korrekt konfigurerad i Visual Studio för C++ Desktop Development:

1. Öppna Visual Studio 2019 för att visa **Start** fönstret.

   ![Skärm bild som visar "Start"-fönstret i Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Välj **Fortsätt utan kod** för att gå till Visual Studio IDE.

1. I meny raden i Visual Studio väljer du **verktyg**  >  **Hämta verktyg och funktioner** för att öppna Visual Studio Installer och Visa dialog rutan **ändra** .

   ![Skärm bild som visar fliken "arbets belastningar" i dialog rutan "ändra" för "Visual Studio Installer".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. På fliken **arbets belastningar** , under **Windows**, letar du upp **Skriv bords utvecklingen med C++** -arbetsbelastningen. Om kryss rutan bredvid den arbets belastningen inte redan är markerad, markerar du den.

1. På fliken **enskilda komponenter** letar du upp kryss rutan **NuGet Package Manager** . Markera kryss rutan om den inte redan är markerad.

1. Välj knappen i hörnet som är märkt antingen **Stäng** eller **ändra**. (Knapp namnet varierar beroende på om du har valt några funktioner för installation.) Om du väljer **ändra** startar installationen, vilket kan ta en stund.

1. Stäng Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Skapa projektet och välj mål arkitekturen

Skapa sedan ditt projekt:

1. I meny raden i Visual Studio väljer du **Arkiv**  >  **nytt**  >  **projekt** för att visa fönstret **skapa ett nytt projekt** .

   ![Skapa ett nytt projekt, C++-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Sök efter och välj **konsol program**. Kontrol lera att du väljer C++-versionen av den här projekt typen (till skillnad från C# eller Visual Basic).

1. Välj **Nästa** för att visa skärmen **Konfigurera ditt nya projekt** .

   ![Konfigurera det nya projektet, C++-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. I **projekt namn** anger du `helloworld` .

1. I **plats** navigerar du till och väljer eller skapar mappen där du vill spara projektet i.

Välj nu mål plattforms arkitektur. I Visual Studio-verktygsfältet hittar du List rutan med **plattforms plattformar** . (Om du inte ser det väljer du **Visa**  >  **Verktygsfält**  >  **Standard** för att visa det verktygsfält som innehåller **lösnings plattformar**.) Om du kör 64-bitars Windows väljer du **x64** i list rutan. 64-bitars Windows kan också köra 32-bitars program, så du kan välja **x86** om du föredrar det.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Slutligen installerar du [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget)och refererar till tal-SDK: n i projektet:

1. I **Solution Explorer** högerklickar du på din lösning och väljer **Hantera NuGet-paket för lösning** för att gå till fönstret **NuGet-lösning** .

1. Välj **Bläddra**.

   ![NuGet – fliken lösning, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. I **paket källa** väljer du **NuGet.org**.

1. I **sökrutan anger du** `Microsoft.CognitiveServices.Speech` och väljer sedan det paketet efter att det visas i Sök resultatet.

   ![Installation av Microsoft. CognitiveServices. Speech C++-paketet-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. I fönstret paket status bredvid Sök resultaten väljer du ditt **HelloWorld** -projekt.

1. Välj **installera**.

1. I dialog rutan **Förhandsgranska ändringar** väljer du **OK**.

1. I dialog rutan **licens godkännande** visar du licensen och väljer sedan **Jag accepterar**. Paket installationen startar och när installationen är klar visas ett meddelande som liknar följande text i fönstret **utdata** : `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
