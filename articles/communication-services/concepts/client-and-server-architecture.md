---
title: Klient- och serverarkitektur
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om kommunikations tjänsternas arkitektur.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2cc7e1d0c108d45383a68258fb6f62fcab87d1f7
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653823"
---
# <a name="client-and-server-architecture"></a>Klient-och server arkitektur

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Alla Azure Communication Services-program kommer att ha **klient program** som använder **tjänster** för att under lätta anslutning från person till person. Den här sidan illustrerar vanliga arkitektur element i flera olika scenarier.

## <a name="user-access-management"></a>Hantering av användar åtkomst

Klient bibliotek för Azure Communication Services kräver `user access tokens` åtkomst till kommunikations tjänsternas resurser på ett säkert sätt. `User access tokens` bör skapas och hanteras av en betrodd tjänst på grund av den känsliga beskaffenheten hos token och den anslutnings sträng som krävs för att skapa dem. Om du inte hanterar åtkomsttoken korrekt kan det leda till ytterligare avgifter på grund av missbruk av resurser. Vi rekommenderar starkt att du använder en betrodd tjänst för användar hantering. Den betrodda tjänsten genererar tokens och skickar tillbaka dem till klienten med korrekt kryptering. Du hittar ett exempel på ett arkitektur flöde nedan:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagram över arkitektur för användar åtkomst-token.":::

För ytterligare information, se [metod tips för identitets hantering](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>Webb läsar kommunikation

Klient bibliotek för Azure-kommunikation i Java Script kan aktivera webb program med funktioner för text-, röst-och video interaktion. Programmet interagerar direkt med Azure Communication Services via klient biblioteket för att komma åt data planet och leverera text-, röst-och video kommunikation i real tid. Du hittar ett exempel på ett arkitektur flöde nedan:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagram som visar webbläsaren för webb läsar arkitekturen för kommunikations tjänster.":::

## <a name="native-app-communication"></a>Intern app-kommunikation

Många scenarier hanteras bäst med inbyggda program. Azure Communication Services stöder både webb läsar-till-app-och app-till-app-kommunikation.  När du skapar en inbyggd program upplevelse gör det möjligt för användarna att ta emot samtal även när programmet inte körs. Azure Communication Services gör det enkelt med integrerade push-meddelanden till Google Firebase, Apple Push Notification Service och Windows Push-meddelanden. Du hittar ett exempel på ett arkitektur flöde nedan:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagram som visar arkitektur för kommunikations tjänster för intern app-kommunikation.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Voice och SMS via det offentliga växlade telefoni nätverket (PSTN)

Att kommunicera via telefon systemet kan dramatiskt öka räckvidden för ditt program. För att stödja PSTN-röst-och SMS-scenarier hjälper Azure Communication Services dig att [få telefonnummer](../quickstarts/telephony-sms/get-phone-number.md) direkt från Azure Portal eller med hjälp av REST-API: er och klient bibliotek. När telefonnumren har hämtats kan de användas för att uppnå kunder som använder både PSTN-samtal och SMS i både inkommande och utgående scenarier. Du hittar ett exempel på ett arkitektur flöde nedan:

> [!Note]
> Under en offentlig för hands version är etableringen av amerikanska telefonnummer tillgängliga för kunder med fakturerings adresser som finns i USA och Kanada. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagram som visar PSTN-arkitektur för kommunikations tjänster.":::

Mer information om PSTN-telefonnummer finns i [nummer typer](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Människor som kommunicerar med robotar och andra tjänster

Azure Communication Services stöder kommunikation från människa till system, även om text-och röst kanaler, med tjänster som har direkt åtkomst till Azure Communication Services-dataplanen. Du kan till exempel ha en bot-svar på inkommande telefonsamtal eller delta i en webbchatt. Azure Communication Services tillhandahåller klient bibliotek som möjliggör de här scenarierna för att anropa och chatta. Du hittar ett exempel på ett arkitektur flöde nedan:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagram som visar robot arkitektur för kommunikations tjänster.":::

## <a name="networking"></a>Nätverk

Du kanske vill utbyta godtyckliga data mellan användare, till exempel för att synkronisera en delad, blandad verklighet eller spel upplevelse. Data planet i real tid som används för text-, röst-och video kommunikation är tillgängligt direkt på två sätt:

- **Anropa klient bibliotek** – enheter i ett samtal har åtkomst till API: er för att skicka och ta emot data via anrops kanalen. Detta är det enklaste sättet att lägga till data kommunikation till en befintlig interaktion.
- **Stun/turn** – Azure Communication Services gör standardkompatibla STUN och aktiverar tjänster som är tillgängliga för dig. På så sätt kan du bygga ett kraftigt anpassat transport lager ovanpå dessa standardiserade primitiver. Du kan redigera din egen standard klient eller använda bibliotek med öppen källkod, till exempel [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)

Mer information finns i följande artiklar:

- Läs mer om [autentisering](../concepts/authentication.md)
- Lär dig mer om [telefonnummer typer](../concepts/telephony-sms/plan-solution.md)

- [Lägga till chatt i din app](../quickstarts/chat/get-started.md)
- [Lägga till röstsamtal i din app](../quickstarts/voice-video-calling/getting-started-with-calling.md)