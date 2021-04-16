---
title: Snabbstart – Hantera telefonnummer med hjälp av Azure Communication Services
description: Lär dig hur du hanterar telefonnummer med hjälp av Azure Communication Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498207"
---
# <a name="quickstart-manage-phone-numbers"></a>Snabbstart: Hantera telefonnummer

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

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

- Köp av telefon stöds endast i USA. Om du vill köpa telefonnummer ser du till att:
  - Den associerade faktureringsadressen för Azure-prenumerationen finns i USA. Du kan inte flytta en resurs till en annan prenumeration just nu.
  - Din Communication Services-resurs etableras på den USA dataplatsen. Du kan inte flytta en resurs till en annan dataplats just nu.

- När ett telefonnummer släpps släpps inte telefonnumret eller kan köpas på nytt förrän faktureringsperioden är slut.

- När en Communication Services resurs tas bort släpps de telefonnummer som är associerade med den resursen automatiskt samtidigt.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Köpa ett telefonnummer
> * Hantera ditt telefonnummer
> * Släpp ett telefonnummer

> [!div class="nextstepaction"]
> [Skicka ett SMS](../telephony-sms/send.md) 
>  [Kom igång med att anropa](../voice-video-calling/getting-started-with-calling.md)
