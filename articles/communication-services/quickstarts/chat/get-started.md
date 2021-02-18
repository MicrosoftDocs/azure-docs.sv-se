---
title: Snabb start – Lägg till chatt till din app
titleSuffix: An Azure Communication Services quickstart
description: Den här snabb starten visar hur du lägger till kommunikations tjänster i din app.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: 6f4a4bc34705a67bd91bd05dca9e6786477be6d5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653569"
---
# <a name="quickstart-add-chat-to-your-app"></a>Snabb start: Lägg till chatt i appen

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Kom igång med Azure Communication Services genom att använda kommunikations tjänstens klient bibliotek för att lägga till real tids chatt i ditt program. I den här snabb starten använder vi klient biblioteket i Chat för att skapa chatt-trådar som gör det möjligt för användare att ha konversationer med varandra. Mer information om chatt-koncept finns i [dokumentationen om chatten](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android client library](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS client library](./includes/chat-swift.md)]
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
 - Bekanta dig med [klient biblioteket för chat](../../concepts/chat/sdk-features.md)
