---
title: Komma igång med Teams interop i Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du ansluter till ett team som är möte med Azure Communication Chat SDK
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d7ea3b67c3ce85ce104d16785e4e5f4d45b138f6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106808"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Snabb start: Anslut chatt-appen till ett team möte

> [!IMPORTANT]
> Fyll i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)om du vill aktivera/inaktivera [team innehavarens interoperabilitet](../../concepts/teams-interop.md).

Kom igång med Azure Communication Services genom att ansluta din chatt-lösning till Microsoft Teams med hjälp av Java Script SDK. 

## <a name="prerequisites"></a>Förutsättningar 

1. En [team distribution](/deployoffice/teams-install). 
2. En app för fungerande [chatt](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Aktivera team samverkan 

En kommunikations tjänst användare som ansluts till ett team som är ett möte som gäst användare kan bara komma åt mötets chatt när de har anslutit sig till teamen för Mötes samtal. Se [Teams interop](../voice-video-calling/get-started-teams-interop.md) -dokumentation för att lära dig hur du lägger till en kommunikations tjänst användare till ett team som är Mötes samtal.

Du måste vara medlem i den ägande organisationen av båda entiteterna för att kunna använda den här funktionen.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- Kolla vårt [hjälte exempel för chatt](../../samples/chat-hero-sample.md)
- Lär dig mer om [hur chatt fungerar](../../concepts/chat/concepts.md)