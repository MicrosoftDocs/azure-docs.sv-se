---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001121"
---
Om du vill skapa ett Visual Studio-projekt för Windows-utveckling måste du skapa projektet, konfigurera Visual Studio för .NET Desktop Development, installera Speech SDK och välja mål arkitektur.

### <a name="create-the-project-and-add-the-workload"></a>Skapa projektet och Lägg till arbets belastningen

Starta genom att skapa projektet i Visual Studio och se till att Visual Studio har kon figurer ATS för .NET Desktop-utveckling:

1. Öppna Visual Studio 2019.

1. I fönstret Starta väljer du **skapa ett nytt projekt**. 

1. Välj **konsol program (.NET Framework)** i fönstret **skapa ett nytt projekt** och välj sedan **Nästa**.

1. I fönstret **Konfigurera ditt nya projekt** anger du *HelloWorld* i **projekt namn**, väljer eller skapar sökvägen till katalogen på **platsen** och väljer sedan **skapa**.

1. I meny raden i Visual Studio väljer du **verktyg**  >  **Hämta verktyg och funktioner**, som öppnar Visual Studio Installer och visar dialog rutan **ändra** .

1. Kontrol lera om arbets belastningen **.net Desktop Development** är tillgänglig. Om arbets belastningen inte har installerats markerar du kryss rutan bredvid den och väljer sedan **ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryss rutan bredvid **.net Desktop Development** redan är markerad väljer du **Stäng** för att stänga dialog rutan.

   ![Aktivera .NET-skrivbordsutveckling](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Stäng Visual Studio Installer.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Nästa steg är att installera NuGet- [paketet för tal-SDK](https://aka.ms/csspeech/nuget)så att du kan referera till det i koden.

1. I Solution Explorer högerklickar du på projektet **HelloWorld** och väljer sedan **Hantera NuGet-paket** för att Visa NuGet Package Manager.

   ![NuGet-pakethanteraren](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Leta upp List rutan **paket källa** i det övre högra hörnet och kontrol lera att **NuGet.org** är markerat.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. Skriv *Microsoft. CognitiveServices. Speech* i rutan Sök och välj **RETUR**.

1. Från Sök resultaten väljer du paketet **Microsoft. CognitiveServices. Speech** och väljer sedan **Installera** för att installera den senaste stabila versionen.

   ![Installera Microsoft. CognitiveServices. Speech NuGet-paketet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager-konsol** .

### <a name="choose-the-target-architecture"></a>Välj mål arkitektur

Skapa och kör konsol programmet nu genom att skapa en plattforms konfiguration som matchar datorns arkitektur.

1. Välj **build**  >  **Configuration Manager** på Meny raden. Dialog rutan **Configuration Manager** visas.

   ![Configuration Manager dialog ruta](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. I list rutan **aktiv lösnings plattform** väljer du **ny**. Dialog rutan **ny lösnings plattform** visas.

1. I list rutan **Skriv eller Välj ny plattform** :
   - Om du kör 64-bitars Windows väljer du **x64**.
   - Om du kör 32-bitars Windows väljer du **x86**.

1. Välj **OK** och sedan **Stäng**.
