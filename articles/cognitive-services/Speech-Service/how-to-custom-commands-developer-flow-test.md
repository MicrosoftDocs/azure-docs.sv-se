---
title: Testa din app för anpassade kommandon
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig olika metoder för att testa ett program för anpassade kommandon.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021821"
---
# <a name="test-your-custom-commands-application"></a>Testa dina anpassade kommandon-program

I den här artikeln får du lära dig olika metoder för att testa ett program för anpassade kommandon.

## <a name="test-in-the-portal"></a>Testa i portalen

Test i portalen är det enklaste och snabbaste sättet att kontrol lera om ditt anpassade kommando program fungerar som förväntat. När appen har tränats klickar du på `Test` knappen för att starta testningen.

> [!div class="mx-imgBorder"]
> ![Testa i portalen](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Testa med Windows Voice Assistant-klienten

Windows Voice Assistant-klienten är ett Windows Presentation Foundation-program (WPF) i C# som gör det enkelt att testa interaktioner med din robot innan du skapar ett anpassat klient program.

Verktyget kan acceptera ett anpassat kommando program-ID. Det gör att du kan testa din slutförda aktivitet eller ett kommando-och-kontroll-scenario som finns i tjänsten för anpassade kommandon.

Installera klienten genom att checka in [Windows Voice Assistant-klienten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![Skapa profil för WVAC](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Testa med Speech SDK-aktiverade klient program 
Talfunktionerna Software Development Kit (SDK) visar många av funktionerna i tal tjänsten, vilket gör att du kan utveckla talbaserade program. Det finns också i många programmeringsspråk och på alla plattformar.

Konfigurera ett Universell Windows-plattform (UWP)-klient program med Speech SDK och integrera det med ditt anpassade kommando program:  
- [Gör så här: integrera med ett klient program med hjälp av tal-SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Gör så här: skicka aktivitet till klient program](./how-to-custom-commands-send-activity-to-client.md)
- [Gör så här: konfigurera webb slut punkter](./how-to-custom-commands-setup-web-endpoints.md)

För andra programmeringsspråk och plattformar:
- [Programmerings språk för tal SDK, plattformar, scenario kapacitet](./speech-sdk.md)
- [Exempel koder för röst assistenten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)