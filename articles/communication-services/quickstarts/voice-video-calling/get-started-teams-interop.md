---
title: Snabb start – Teams interop på Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du ansluter till ett team som är möte med Azure-kommunikationen som anropar SDK.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095619"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Snabb start: Anslut din anropande app till ett team möte

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Fyll i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)om du vill aktivera/inaktivera [team innehavarens interoperabilitet](../../concepts/teams-interop.md).

Kom igång med Azure Communication Services genom att ansluta din anropande lösning till Microsoft Teams med hjälp av Java Script SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

Funktioner som beskrivs i det här dokumentet använder den allmänna tillgänglighet-versionen av SDK: er för kommunikations tjänsterna. Team samverkan kräver Beta versionen av SDK: er för kommunikations tjänster. Beta-SDK: er kan utforskas på [sidan med viktig information](https://github.com/Azure/Communication/tree/master/releasenotes).

När du kör steget "installera paket" med beta-SDK: erna ändrar du versionen av paketet till den senaste beta versionen genom att ange version `@1.0.0-beta.10` (version när den här artikeln skrivs) i `communication-calling` paket namnet. Du behöver inte ändra `communication-common` paket kommandot. Exempel:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- Ta en titt på vårt [exempel på samtals hjälte](../../samples/calling-hero-sample.md)
- Läs mer om hur du [anropar SDK-funktioner](./calling-client-samples.md)
- Läs mer om [hur du anropar Works](../../concepts/voice-video-calling/about-call-types.md)
