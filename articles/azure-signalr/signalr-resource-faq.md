---
title: Vanliga frågor och svar om Azure SignalR Service
description: Få svar på vanliga frågor om Azure SignalR-tjänsten, inklusive fel sökning och vanliga användnings scenarier.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579310"
---
# <a name="azure-signalr-service-faq"></a>Vanliga frågor och svar Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Är Azure SignalR Service redo för användning i produktion?

Ja, både supporten för [ASP.net Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) och [ASP.net-signalerare](/aspnet/signalr/overview/getting-started/introduction-to-signalr) är allmänt tillgängliga.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>När det finns flera programservrar, skickas klientmeddelanden till alla servrar eller bara en av dem?

Det finns en en-till-en-mappning mellan en klient och en program Server. Meddelanden från en klient skickas alltid till samma programserver.

Mappningen upprätthålls tills klienten eller program servern kopplas från.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Om en av mina programservrar inte är tillgänglig, hur kan jag hitta den och bli meddelad?

Azure SignalR-tjänsten övervakar pulsslag från program servrar.
Om pulsslag inte tas emot för en angiven tidsperiod, anses programservern vara offline. Alla klientanslutningar som mappats till den här programservern kommer att kopplas från.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Varför genererar mitt eget `IUserIdProvider` undantag när jag byter från ASP.net Core SignalR SDK till Azure SignalR service SDK?

Parametern `HubConnectionContext context` skiljer sig mellan ASP.net Core signalerare SDK och Azure SignalR service SDK när `IUserIdProvider` anropas.

I ASP.NET Core SignalR är `HubConnectionContext context` kontexten från den fysiska klientanslutningen med giltiga värden för alla egenskaper.

I Azure SignalR service SDK `HubConnectionContext context` är kontexten från den logiska klient anslutningen. Den fysiska klienten är ansluten till Azure SignalR service-instansen, så endast ett begränsat antal egenskaper tillhandahålls.

För tillfället är endast `HubConnectionContext.GetHttpContext()` och `HubConnectionContext.User` tillgängliga för åtkomst.
Du kan [kontrol lera käll koden](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Kan jag konfigurera transporter som är tillgängliga i Azure SignalR-tjänsten på Server sidan med ASP.NET Core Signalerare? Kan jag till exempel inaktivera WebSocket-transport?

Nej.

Azure SignalR Service tillhandahåller alla tre transporter som ASP.NET Core SignalR stöder som standard. Det går inte att konfigurera. Azure SignalR service hanterar anslutningar och transporter för alla klient anslutningar.

Du kan konfigurera transporter på klient sidan som dokumenterade i [ASP.net Core signaler-konfiguration](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Vad är syftet med mått som antal meddelanden eller antal anslutningar som visas i Azure Portal? Vilken typ av agg regerings typ ska jag välja?

Du hittar information om hur vi beräknar dessa mått i [meddelanden och anslutningar i Azure SignalR-tjänsten](signalr-concept-messages-and-connections.md).

I översikts fönstret för Azure SignalR service-resurser har vi redan valt rätt agg regerings typ åt dig. Om du går till fönstret mått kan du ta sammansättnings typen till [meddelanden och anslutningar i Azure SignalR-tjänsten](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) som en referens.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Vad är syftet med `Default` `Serverless` `Classic` tjänst lägena, och. Hur kan jag välja?

För nya program ska endast standard läge och Server lös läge användas. Den största skillnaden är om du har program servrar som etablerar Server anslutningar till tjänsten (d.v.s. används `AddAzureSignalR()` för att ansluta till tjänsten). Om ja använder standard läget använder du annars utan server läge.

Klassiskt läge är utformat för bakåtkompatibilitet för befintliga program, så bör inte användas för nya program.

Mer information om tjänst läge i [det här dokumentet](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Kan jag skicka ett meddelande från klienten i Server lös läge?

Du kan skicka ett meddelande från klienten om du konfigurerar uppströms i signal instansen. Överordnad är en uppsättning slut punkter som kan ta emot meddelanden och anslutnings händelser från SignalR-tjänsten. Om ingen överordnad uppladdning har kon figurer ATS kommer meddelanden från klienten att ignoreras.

Mer information om överordnad finns i [det här dokumentet](concept-upstream.md).

Överström är för närvarande en offentlig för hands version.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Finns det några funktions skillnader i att använda Azure SignalR service med ASP.NET-Signalerare?

När du använder Azure SignalR-tjänsten stöds inte vissa API: er och funktioner i ASP.NET-signaler:
- Möjligheten att skicka godtyckligt tillstånd mellan klienter och hubben (kallas ofta `HubState` ) stöds inte.
- `PersistentConnection`Klassen stöds inte.
- För *alltid-Frame-transport* stöds inte.
- Azure SignalR-tjänsten spelar inte längre upp meddelanden som skickas till klienten när klienten är offline.
- När du använder Azure SignalR-tjänsten dirigeras trafiken för en klient anslutning alltid (kallas även *trög*) till en app Server-instans under anslutningens varaktighet.

Stöd för ASP.NET-signaler är inriktad på kompatibilitet, så alla nya funktioner från ASP.NET Core-Signalerare stöds inte. Till exempel är *MessagePack* och *streaming* endast tillgängligt för ASP.net Core signalerare-program.

Du kan konfigurera Azure SignalR service för olika tjänst lägen: `Classic` , `Default` och `Serverless` . `Serverless`Läget stöds inte för ASP.net. Data Plans REST API stöds inte heller.

## <a name="where-does-my-data-reside"></a>Var finns mina data?

Azure SignalR service fungerar som en data behandlings tjänst. Det kommer inte att lagra något kund innehåll och data placering ingår i design. Om du använder Azure SignalR-tjänsten tillsammans med andra Azure-tjänster, t. ex. Azure Storage för diagnostik, se [den här White Paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) för vägledning om hur du håller data placering i Azure-regioner.