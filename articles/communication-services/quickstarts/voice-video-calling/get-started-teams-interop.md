---
title: Snabb start – Teams interop på Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du ansluter till ett team som är möte med Azure-kommunikationen som anropar SDK.
author: matthewrobertson
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: c2cda93534a2010ced5c98f8e1a3563f8446ea84
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488028"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Snabb start: Anslut din anropande app till ett team möte

> [!IMPORTANT]
> Fyll i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)om du vill aktivera/inaktivera [team innehavarens interoperabilitet](../../concepts/teams-interop.md).

Kom igång med Azure Communication Services genom att ansluta din anropande lösning till Microsoft Teams med hjälp av Java Script-klient biblioteket.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- Ta en titt på vårt [exempel på samtals hjälte](../../samples/calling-hero-sample.md)
- Läs om hur du [anropar klient biblioteks funktioner](./calling-client-samples.md)
- Läs mer om [hur du anropar Works](../../concepts/voice-video-calling/about-call-types.md)
