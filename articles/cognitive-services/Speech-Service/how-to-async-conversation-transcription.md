---
title: Skrift om asynkron konversation – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder asynkron konversations avskrift med hjälp av tal tjänsten. Endast tillgängligt för Java och C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934657"
---
# <a name="asynchronous-conversation-transcription"></a>Asynkron samtalstranskription

I den här artikeln visas den asynkrona konversations avskriften med hjälp av **RemoteConversationTranscriptionClient** -API: et. Om du har konfigurerat konversations avskrift för asynkron avskrift och har en kan `conversationId` du hämta avskriften som är associerad med med `conversationId` **RemoteConversationTranscriptionClient** -API: et.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynkron vs. real tids + asynkron

Med asynkron avskrift strömmar du konversations ljudet, men behöver ingen avskrift i real tid. När ljudet har skickats använder du i stället för `conversationId` `Conversation` att fråga efter status för den asynkrona avskriften. När den asynkrona avskriften är klar får du en `RemoteConversationTranscriptionResult` .

Med real tids plus asynkront får du avskrifter i real tid, men du kan också få avskriften genom att fråga med `conversationId` (liknar asynkront scenario).

Två steg krävs för att utföra asynkron avskriftering. Det första steget är att ladda upp ljudet, välja antingen asynkront eller i real tid och asynkront. Det andra steget är att hämta resultatet av avskriften.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
