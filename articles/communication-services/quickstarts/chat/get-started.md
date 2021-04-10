---
title: Snabb start – Lägg till chatt till din app
titleSuffix: An Azure Communication Services quickstart
description: Den här snabb starten visar hur du lägger till kommunikations tjänster i din app.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: 6d3f9f7fd30d2c6b1cbc3882a41546593ee1c156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726702"
---
# <a name="quickstart-add-chat-to-your-app"></a>Snabb start: Lägg till chatt i appen

Kom igång med Azure Communication Services genom att använda kommunikations tjänsterna Chat SDK för att lägga till real tids chatt i ditt program. I den här snabb starten använder vi chatt-SDK: n för att skapa chatt-trådar som gör det möjligt för användarna att ha konversationer med varandra. Mer information om chatt-koncept finns i [dokumentationen om chatten](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript SDK](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python SDK](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java SDK](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android SDK](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# SDK](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS SDK](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att:

> [!div class="checklist"]
> * Skapa en Chat-klient
> * Skapa en tråd med två användare
> * Skicka ett meddelande till tråden
> * Ta emot meddelanden från en tråd
> * Ta bort användare från en tråd

> [!div class="nextstepaction"]
> [Testa hjälte-appen för chatt](../../samples/chat-hero-sample.md)

Du kanske också vill:

 - Lär dig mer om [chatt-koncept](../../concepts/chat/concepts.md)
 - Bekanta dig med [chatt SDK](../../concepts/chat/sdk-features.md)
