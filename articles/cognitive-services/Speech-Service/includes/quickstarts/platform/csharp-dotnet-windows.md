---
title: 'Snabb start: tal-SDK för .NET Framework (Windows) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för C# under .NET Framework för Windows med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1a9faf24c5a82b815b40afe15769480b69074dc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551810"
---
Den här guiden visar hur du installerar [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för .NET Framework (Windows). Om du bara vill att paket namnet ska komma igång med ditt eget kör `Install-Package Microsoft.CognitiveServices.Speech` du i NuGet-konsolen.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) för din plattform. Det kan krävas en omstart för att installera det första gången.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Skapa ett Visual Studio-projekt och installera talet SDK

Du måste installera [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget) så att du kan referera till det i din kod. För att göra det måste du först skapa ett **HelloWorld** -projekt. Om du redan har ett projekt med arbets belastningen **.net Desktop Development** tillgängligt kan du använda det projektet och hoppa över att [använda NuGet Package Manager för att installera tal-SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Skapa HelloWorld-projekt

1. Öppna Visual Studio 2019.

1. I fönstret Starta väljer du **skapa ett nytt projekt**. 

1. Välj **konsol program (.NET Framework)** i fönstret **skapa ett nytt projekt** och välj sedan **Nästa**.

1. I fönstret **Konfigurera ditt nya projekt** anger du *HelloWorld* i **projekt namn**, väljer eller skapar sökvägen till katalogen på **platsen** och väljer sedan **skapa**.

1. I meny raden i Visual Studio väljer du **verktyg**  >  **Hämta verktyg och funktioner**, som öppnar Visual Studio Installer och visar dialog rutan **ändra** .

1. Kontrol lera om arbets belastningen **.net Desktop Development** är tillgänglig. Om arbets belastningen inte har installerats markerar du kryss rutan bredvid den och väljer sedan **ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryss rutan bredvid **.net Desktop Development** redan är markerad väljer du **Stäng** för att stänga dialog rutan.

   ![Aktivera .NET-skrivbordsutveckling](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Stäng Visual Studio Installer.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Använd NuGet Package Manager för att installera talet SDK

1. I Solution Explorer högerklickar du på projektet **HelloWorld** och väljer sedan **Hantera NuGet-paket** för att Visa NuGet Package Manager.

   ![NuGet-pakethanteraren](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Leta upp List rutan **paket källa** i det övre högra hörnet och se till att **`nuget.org`** är markerad.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. Skriv *Microsoft. CognitiveServices. Speech* i rutan Sök och välj **RETUR**.

1. Från Sök resultaten väljer du paketet **Microsoft. CognitiveServices. Speech** och väljer sedan **Installera** för att installera den senaste stabila versionen.

   ![Installera Microsoft. CognitiveServices. Speech NuGet-paketet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager-konsol** .

### <a name="choose-target-architecture"></a>Välj mål arkitektur

Skapa och kör konsol programmet genom att skapa en plattforms konfiguration som matchar datorns arkitektur.

1. Välj **build**  >  **Configuration Manager** på Meny raden. Dialog rutan **Configuration Manager** visas.

   ![Configuration Manager dialog ruta](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. I list rutan **aktiv lösnings plattform** väljer du **ny**. Dialog rutan **ny lösnings plattform** visas.

1. I list rutan **Skriv eller Välj ny plattform** :
   - Om du kör 64-bitars Windows väljer du **x64**.
   - Om du kör 32-bitars Windows väljer du **x86**.

1. Välj **OK** och sedan **Stäng**.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
