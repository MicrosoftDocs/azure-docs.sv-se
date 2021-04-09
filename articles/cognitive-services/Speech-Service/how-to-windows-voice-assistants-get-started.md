---
title: Röst assistenter i Windows – kom igång
titleSuffix: Azure Cognitive Services
description: Stegen för att börja utveckla en Windows Voice-agent, inklusive en referens till exempel koden snabb start.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713056"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Komma igång med röst assistenter i Windows

Den här guiden tar dig igenom stegen för att börja utveckla en röst assistent i Windows.

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

För att börja utveckla en röst assistent för Windows måste du kontrol lera att du har rätt utvecklings miljö.

- **Visual Studio:** Du måste installera [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition eller senare
- **Windows-version**: en dator med en Windows Insider snabb rings version av Windows och Windows Insider-versionen av Windows SDK. Den här exempel koden verifieras som att fungera på Windows Insider-versionen build 19025.vb_release_analog .191112-1600 med Windows SDK 19018. Alla build-eller SDK: er utöver de angivna versionerna bör vara kompatibla.
- **Utvecklings verktyg för UWP**: arbets belastningen universell Windows-plattform utveckling i Visual Studio. Se sidan för att [Hämta](/windows/uwp/get-started/get-set-up) UWP för att förbereda din dator för att utveckla UWP-program.
- **En fungerande mikrofon och ljud uppspelning**

## <a name="obtain-resources-from-microsoft"></a>Hämta resurser från Microsoft

Vissa resurser som krävs för en helt anpassad röst agent i Windows kräver resurser från Microsoft. [Exemplet på UWP röst assistenten](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) innehåller exempel versioner av dessa resurser för inledande utveckling och testning, så det här avsnittet är inte nödvändigt för inledande utveckling.

- **Nyckelords modell:** Röst aktivering kräver en nyckelords modell från Microsoft i form av en. bin-fil. . Bin-filen som anges i exemplet på UWP röst assistenten har tränats på nyckelordet *contoso*.
- **Token för begränsad åtkomst funktion:** Eftersom ConversationalAgent-API: erna ger åtkomst till mikrofon ljud, skyddas de under begränsade åtkomst funktions begränsningar. Om du vill använda en begränsad åtkomst funktion måste du skaffa en begränsad åtkomst till funktions-token som är ansluten till paket identiteten för ditt program från Microsoft.

## <a name="establish-a-dialog-service"></a>Upprätta en dialog tjänst

För att få en fullständig röst assistent måste programmet ha en dialog tjänst som

- Identifiera ett nyckelord i en specifik ljudfil
- Lyssna på användarindata och konvertera den till text
- Ange texten till en robot
- Översätt text svaret från roboten till en ljud signal

Detta är kraven för att skapa en grundläggande dialog tjänst med hjälp av direkt linje tal.

- **Speech Services-prenumeration:** En prenumeration för kognitiva tal tjänster för tal-till-text-och text till tal-konverteringar. Prova Speech Services kostnads fritt [här](./overview.md#try-the-speech-service-for-free).
- Robot **Framework-robot:**  En robot som skapats med bot Framework version 4,2 eller senare och som prenumererar på [direkt linje tal](./direct-line-speech.md) för att aktivera röst indata och utdata. [Den här guiden](./tutorial-voice-enable-your-bot-speech-sdk.md) innehåller steg-för-steg-instruktioner för att göra en "eko robot" och prenumerera på direkt rad tal. Du kan också gå [hit](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) för att få anvisningar om hur du skapar en anpassad bot och följer sedan samma steg [här](./tutorial-voice-enable-your-bot-speech-sdk.md) för att prenumerera på det direkta rad talet, men med din nya robot i stället för "ECHO bot".

## <a name="try-out-the-sample-app"></a>Prova appen exempel

Med din prenumerations nyckel och eko robotens bot-ID för tal tjänster är du redo att testa [exemplet på UWP röst assistent](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Följ anvisningarna i Readme för att köra appen och ange dina autentiseringsuppgifter.

## <a name="create-your-own-voice-assistant-for-windows"></a>Skapa din egen röst assistent för Windows

När du har tagit emot token och bin-filen med begränsad åtkomst från Microsoft kan du börja med din egen röst assistent i Windows.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs Guide för implementering av röst assistenten](windows-voice-assistants-implementation-guide.md)