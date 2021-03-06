---
title: Tjänstbegränsningar
titleSuffix: Azure Digital Twins
description: Diagram som visar gränserna för Azure Digital Twins tjänsten.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728036"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins tjänstbegränsningar

Det här är tjänstbegränsningarna för Azure Digital Twins.

> [!NOTE]
> Vissa områden i den här tjänsten har justerbara gränser. Detta visas i tabellerna nedan med kolumnen *Justerbar?* När gränsen kan justeras är *värdet Justerbar?* *Ja.*
>
> Om ditt företag kräver att en justerbar gräns eller kvot höjs över standardgränsen kan du begära ytterligare resurser genom [att öppna ett supportbegäran.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Begränsningar efter typ

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Arbeta med gränser

När en gräns nås begränsar tjänsten ytterligare begäranden. Detta resulterar i ett 429-felsvar från dessa begäranden.

Här är några rekommendationer för att arbeta med begränsningar för att hantera detta.
* **Använd logik för omprövning.** [SDK:Azure Digital Twins](how-to-use-apis-sdks.md) implementerar omprövningslogik för misslyckade begäranden, så om du arbetar med ett tillhandahållet SDK är detta redan inbyggt. I annat fall bör du överväga att implementera logik för omprövning i ditt eget program. Tjänsten skickar tillbaka ett huvud i felsvaret, som du kan använda för att `Retry-After` avgöra hur lång tid som ska vänta innan du försöker igen.
* **Använd tröskelvärden och meddelanden för att varna om närmar sig gränser.** Några av tjänstbegränsningarna för Azure Digital Twins har motsvarande [mått som](troubleshoot-metrics.md) kan användas för att spåra användning inom dessa områden. Information om hur du konfigurerar tröskelvärden och konfigurerar en avisering för alla mått när ett tröskelvärde närmar sig finns i anvisningarna i [*Felsökning: Konfigurera aviseringar.*](troubleshoot-alerts.md) Om du vill konfigurera meddelanden för andra gränser där mått inte tillhandahålls kan du implementera den här logiken i din egen programkod.

## <a name="next-steps"></a>Nästa steg

Läs mer om den aktuella versionen Azure Digital Twins i tjänstöversikten:
* [*Översikt: Vad är Azure Digital Twins?*](overview.md)
