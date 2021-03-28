---
title: Snabb start – skicka ett SMS-meddelande
titleSuffix: An Azure Communication Services quickstart
description: Lär dig hur du skickar ett SMS-meddelande med Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 9514f1616c0d70d7d195af2f052c604973709204
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644374"
---
# <a name="quickstart-send-an-sms-message"></a>Snabb start: Skicka ett SMS-meddelande

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> SMS-meddelanden kan skickas till och tas emot från USA telefonnummer. Telefonnummer som finns i andra geografiska områden stöds ännu inte av kommunikations tjänsterna SMS.
> Mer information finns i **[telefonnummer typer](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka problem som rör SMS-leverans kan du [Aktivera leverans rapportering med event Grid](./handle-sms-events.md) för att hämta leverans information.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skickar SMS-meddelanden med Azure Communication Services.

> [!div class="nextstepaction"]
> [Ta emot SMS-och leverans rapport händelser](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Telefonnummer typer](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Läs mer om SMS](../../concepts/telephony-sms/concepts.md)
