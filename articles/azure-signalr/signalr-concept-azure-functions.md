---
title: Bygg real tids app-Azure Functions & Azure SignalR service
description: Lär dig hur du utvecklar Server lös webb program i real tid med Azure SignalR service genom att följa exemplet.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: bf0b5f19d1aa224fa4a2e79c20f2900e3400fce0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151124"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Bygg appar i real tid med Azure Functions och Azure SignalR-tjänsten

Eftersom Azure SignalR Service och Azure Functions båda är fullständigt hanterade, mycket skalbara tjänster som gör att du kan fokusera på att bygga program i stället för att hantera infrastrukturen är det vanligt att använda de två tjänsterna tillsammans för att tillhandahålla realtidskommunikation i en [serverlös](https://azure.microsoft.com/solutions/serverless/) miljö.

> [!NOTE]
> Lär dig att använda Signalerare och Azure Functions tillsammans i den interaktiva självstudien [Aktivera automatiska uppdateringar i ett webb program med hjälp av tjänsten Azure Functions och SignalR](/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Integrera realtidskommunikation med Azure-tjänster

Azure Functions kan du skriva kod på [flera språk](../azure-functions/supported-languages.md), inklusive Java Script, python, C# och Java, som utlöses när händelser inträffar i molnet. Exempel på dessa händelser:

* HTTP- och webhook-begäranden
* Periodiska timers
* Händelser från Azure-tjänster såsom:
    - Event Grid
    - Event Hubs
    - Service Bus
    - Cosmos DB-ändringsflöde
    - Storage-blobbar och köer
    - Logic Apps-anslutningsappar som Salesforce och SQL Server

Genom att använda Azure Functions för att integrera dessa händelser med Azure SignalR Service har du möjligheten att meddela tusentals olika klienter när händelser inträffar.

Några vanliga scenarier för serverlösa realtidsmeddelanden som du kan implementera med Azure Functions och SignalR Service är:

* Visualisera IoT-enhetstelemetri på en realtidsinstrumentpanel eller karta
* Uppdatera data i ett program när dokument uppdateras i Cosmos DB
* Skicka aviseringar via app när nya beställningar skapas i Salesforce

## <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

SignalR Service-bindningar för Azure Functions gör att en Azure Functions-app kan publicera meddelanden till klienter som är anslutna till SignalR Service. Klienter kan ansluta till tjänsten med hjälp av en SignalR-klient-SDK som är tillgänglig i .NET, JavaScript och Java. Fler språk kommer snart.

### <a name="an-example-scenario"></a>Ett exempelscenario

Ett exempel på hur du använder SignalR Service-bindningar är att använda Azure Functions för att integrera med Azure Cosmos DB och SignalR Service för att skicka realtidsmeddelanden när nya händelser visas i ett Cosmos-DB-ändringsflöde.

![Cosmos DB, Azure Functions, SignalR Service](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. En ändring görs i en Cosmos DB-samling
2. Ändringshändelsen sprids till Cosmos DB-ändringsflödet
3. En Azure Functions utlöses av ändringshändelsen med hjälp av Cosmos DB-utlösaren
4. SignalR Service-utdatabindningen publicerar ett meddelande till SignalR Service
5. SignalR Service publicerar meddelandet till alla anslutna klienter

### <a name="authentication-and-users"></a>Autentisering och användare

SignalR Service gör att du kan skicka broadcast-meddelanden till alla klienter eller bara till en delmängd av klienter, till exempel dem som tillhör en enskild användare. SignalR Service-bindningar för Azure Functions kan kombineras med App Service-autentisering för att autentisera användare med leverantörer, till exempel Azure Active Directory, Facebook och Twitter. Du kan sedan skicka meddelanden direkt till dessa autentiserade användare.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du en översikt över hur du använder Azure Functions med SignalR Service för att aktivera en mängd olika scenarier med serverlösa realtidsmeddelanden.

Fullständig information om hur du använder Azure Functions-och SignalR-tjänsten finns på följande resurser:

* [Azure Functions utveckling och konfiguration med signal tjänsten](signalr-concept-serverless-development-config.md)
* [Aktivera automatiska uppdateringar i en webbapp med Azure Functions och SignalR Service](/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

Följ någon av de här snabbstarterna om du vill veta mer.

* [Snabbstart för serverlös Azure SignalR Service – C#](signalr-quickstart-azure-functions-csharp.md)
* [Snabbstart för serverlös Azure SignalR Service – JavaScript](signalr-quickstart-azure-functions-javascript.md)