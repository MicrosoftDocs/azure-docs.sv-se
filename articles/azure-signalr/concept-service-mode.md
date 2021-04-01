---
title: Tjänst läge i Azure SignalR-tjänsten
description: En översikt över olika tjänst lägen i Azure SignalR-tjänsten, förklara skillnaderna och tillämpliga användar scenarier
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143257"
---
# <a name="service-mode-in-azure-signalr-service"></a>Tjänst läge i Azure SignalR-tjänsten

Service Mode är ett viktigt begrepp i Azure SignalR-tjänsten. När du skapar en ny signal resurs uppmanas du att ange ett tjänst läge:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Välj tjänst läge när du skapar":::

Du kan också ändra den senare på menyn Inställningar:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Uppdatera tjänst läge":::

Azure SignalR service stöder för närvarande tre tjänst lägen: **standard**, utan **Server** och **klassisk**. Signal resursen fungerar annorlunda i olika lägen. I den här artikeln får du lära dig skillnaderna och hur du väljer rätt tjänst läge baserat på ditt scenario.

## <a name="default-mode"></a>Standard läge

Standard läget är standardvärdet för tjänst läge när du skapar en ny Signals-resurs. I det här läget fungerar ditt program som ett typiskt ASP.NET Core-(eller ASP.NET)-signal program, där du har en webb server som är värd för ett nav (kallas Hub-Server) och klienter kan ha dubbels idig kommunikation i real tid med nav servern. Den enda skillnaden är i stället för att ansluta klient och server direkt, både klienten och servern ansluter till signal tjänsten och använder tjänsten som proxy. Nedan visas ett diagram som illustrerar den typiska program strukturen i standard läget:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Program struktur i standard läge":::

Så om du har ett signal program och vill integrera med signaler-tjänsten bör standard läget vara det bästa valet för de flesta fall.

### <a name="connection-routing-in-default-mode"></a>Anslutnings dirigering i standard läge

I standard läget kommer det att finnas WebSocket-anslutningar mellan Hub Server och SignalR service (kallas Server anslutningar). Dessa anslutningar används för att överföra meddelanden mellan server och klient. När en ny klient är ansluten skickar signal tjänsten klienten till en NAV Server (anta att du har mer än en server) via befintliga Server anslutningar. Sedan kommer klient anslutningen till samma NAV Server under dess livs längd. När klienten skickar meddelanden går de alltid till samma Hubbs Server. Med det här beteendet kan du på ett säkert sätt upprätthålla vissa tillstånd för enskilda anslutningar på NAV servern. Om du till exempel vill strömma något mellan server och klient, behöver du inte tänka på att data paketen går till olika servrar.

> [!IMPORTANT]
> Det innebär också att klienten inte ansluter förrän servern är ansluten först i standard läge. Om alla nav servrar kopplas från på grund av nätverks avbrott eller Server omstart, får klient anslutningen ett fel meddelande om att ingen server är ansluten. Så det är ditt ansvar att alltid vara säker på att det finns minst en NAV Server som är ansluten till signal tjänsten (till exempel har flera Hubbs servrar och se till att de inte går offline samtidigt för saker som underhåll).

Den här cirkulations modellen innebär också att när en NAV Server kopplas från, kommer de anslutningar som dirigeras till servern att tas bort. Det innebär att du bör ta bort anslutningen när Hub-servern är offline för underhåll och hantera åter anslutning så att den inte påverkar ditt program negativt.

## <a name="serverless-mode"></a>Läge utan Server

Server lös läge, som namnet antyder, är ett läge som du inte kan ha någon NAV-Server. Jämfört med standard läget, i den här läge klienten behövs ingen NAV Server för att ansluta. Alla anslutningar är anslutna till tjänsten i ett "Server fritt" läge och tjänsten ansvarar för att underhålla klient anslutningar som att hantera klient-pingar (i standard läge detta hanteras av Hub-servrar).

Det finns även ingen server anslutning i det här läget (om du försöker använda service SDK för att upprätta en server anslutning får du ett fel meddelande). Därför finns det inte heller någon anslutnings dirigering och Server-varaktighet (enligt beskrivningen i avsnittet standard läge). Men du kan fortfarande ha program på Server sidan för att skicka meddelanden till klienter. Detta kan göras på två sätt, använda [REST-API: er](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) för överföring i taget eller via en WebSocket-anslutning så att du kan skicka flera meddelanden mer effektivt (Observera att WebSocket-anslutningen är annorlunda än Server anslutning).

> [!NOTE]
> Både REST API-och WebSocket-sätt stöds i SignalR service [Management SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md). Om du använder ett annat språk än .NET kan du också anropa REST-API: erna manuellt enligt den här [specifikationen](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Om du använder Azure Functions kan du använda [signal tjänst bindningar för Azure Functions](../azure-functions/functions-bindings-signalr-service.md) (kallas ibland funktions bindning) för att skicka meddelanden som en utgående bindning.

Det är också möjligt att Server programmet tar emot meddelanden och anslutnings händelser från klienter. Tjänsten levererar meddelanden och anslutnings händelser till förkonfigurerade slut punkter (kallas överordnade) med Webhooks. Jämfört med standard läget finns det ingen garanti för varaktighet och HTTP-begäranden kan vara mindre effektiva än WebSocket-anslutningar.

Mer information om hur du konfigurerar uppströms finns i det här [dokumentet](./concept-upstream.md).

Nedan visas ett diagram som illustrerar hur Server fritt läge fungerar:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Program struktur i Server fritt läge":::

> [!NOTE]
> Observera att du kan använda standard läget för REST API/Management SDK/funktions bindning för att direkt skicka meddelanden till klienten om du inte vill gå via NAV Server. Men i standard läge är klient anslutningar fortfarande hanterade av Hub-servrar och överordnade fungerar inte i det läget.

## <a name="classic-mode"></a>Klassiskt läge

Klassisk är ett blandat läge som standard och utan server läge. I det här läget bestäms anslutnings läget av om det är NAV Server som är ansluten när klient anslutningen upprättas. Om det finns en NAV Server kommer klient anslutning att dirigeras till en NAV Server. Annars kommer den att ange ett Server fritt läge där klient-till-Server-meddelande inte kan levereras till NAV Server. Detta kommer att orsaka vissa avvikelser, till exempel om alla nav servrar inte är tillgängliga under en kort tid, kommer alla klient anslutningar som skapas under den tiden att vara i läget utan server och kan inte skicka meddelanden till nav servern.

> [!NOTE]
> Klassiskt läge är huvudsakligen för bakåtkompatibilitet för de program som skapats innan det är standard och utan server läge. Vi rekommenderar starkt att inte använda detta läge längre. För nya program väljer du standard eller Server lös baserat på ditt scenario. För befintliga program rekommenderar vi också att du granskar dina användnings fall och väljer rätt tjänst läge.

Klassiskt läge stöder inte heller vissa nya funktioner som överordnat i Server fritt läge.

## <a name="choose-the-right-service-mode"></a>Välj rätt tjänst läge

Nu bör du förstå skillnaderna mellan tjänst lägen och veta hur du väljer mellan dem. Som du redan lärt dig i föregående avsnitt, uppmuntras inte klassiskt läge och du bör bara välja mellan standard servern och servern. Här följer några fler tips som kan hjälpa dig att fatta rätt alternativ för nya program och dra tillbaka klassiskt läge för befintliga program.

* Om du redan är bekant med hur signalerar bibliotek fungerar och vill flytta från en egen värdbaserad SignalR till att använda Azure SignalR-tjänsten väljer du standard läge. Standard läget fungerar exakt på samma sätt som den egna-baserade signalen (och du kan använda samma programmerings modell i signaler bibliotek), signal tjänsten fungerar bara som en proxy mellan klienter och nav servrar.

* Om du skapar ett nytt program och inte vill behålla NAV Server-och Server anslutningar väljer du utan server läge. Det här läget fungerar vanligt vis tillsammans med Azure Functions så att du inte behöver ha någon server alls. Du kan fortfarande ha duplex-kommunikation (med REST API/Management SDK/Function binding och Stream), men programmerings modellen skiljer sig från bibliotek för signalering.

* Om du har båda nav servrarna för att hantera klient anslutningar och backend-program för att skicka meddelanden direkt till klienter (till exempel via REST API) bör du fortfarande välja standard läge. Tänk på att den största skillnaden mellan standard läge och Server lös läge är om du har nav-servrar och hur klient anslutningar dirigeras. REST API/Management SDK/funktions bindning kan användas i båda lägena.

* Om du har ett blandat scenario kan du till exempel ha två olika hubbar på samma signal-resurs, en som används som en traditionell Signals Hub och den andra som används med Azure Functions och inte har någon NAV-Server, bör du tänka på att dela upp dem i två signal resurser, en i standard läge och en i Server fritt läge.

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer om hur du använder standard läge och Server lös läge:

* [Azure SignalR Service – internt](signalr-concept-internals.md)

* [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)