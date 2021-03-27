---
title: Snabb start – hantera telefonnummer med Azure Communication Services
description: Lär dig hur du hanterar telefonnummer med Azure Communication Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629394"
---
# <a name="quickstart-manage-phone-numbers"></a>Snabb start: hantera telefonnummer

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Kom igång med Azure Communication Services med hjälp av klient biblioteket Azure Portal eller kommunikations tjänsternas telefonnummer för att hantera telefonnummer.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Felsökning

Vanliga frågor och problem:

- Köp telefon stöds endast i USA. För att köpa telefonnummer, se till att:
  - Den associerade fakturerings adressen för Azure-prenumerationen finns i USA. Det går för tillfället inte att flytta en resurs till en annan prenumeration.
  - Resursen för kommunikations tjänster är etablerad på USA data plats. Det går inte att flytta en resurs till en annan data plats för tillfället.

- När ett telefonnummer släpps, frigörs inte telefonnumret eller kan inte köpas förrän i slutet av fakturerings perioden.

- När en kommunikations tjänst resurs tas bort, släpps de telefonnummer som är associerade med resursen automatiskt samtidigt.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att:

> [!div class="checklist"]
> * Köp ett telefonnummer
> * Hantera ditt telefonnummer
> * Frisläpp ett telefonnummer

> [!div class="nextstepaction"]
> [Skicka ett SMS](../telephony-sms/send.md) 
>  [Kom igång med att anropa](../voice-video-calling/getting-started-with-calling.md)
