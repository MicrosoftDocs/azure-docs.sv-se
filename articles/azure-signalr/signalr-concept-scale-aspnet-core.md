---
title: Skala ASP.NET Core SignalR med Azure SignalR
description: En översikt över hur du använder Azure SignalR Service för att skala ASP.NET Core SignalR-program.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 90853b5ff769b710c6c95e4f6e62b3a4aa19fadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151076"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Skala ASP.NET Core SignalR-program med hjälp av Azure SignalR Service

## <a name="developing-signalr-apps"></a>Utveckla SignalR-appar

För närvarande finns det [två versioner](/aspnet/core/signalr/version-differences) av signalerer som du kan använda med dina webb program: signalerare för ASP.net, och ASP.net Core SignalR, som är den senaste versionen. Azure SignalR Service är en Azure-hanterad tjänst som bygger på ASP.NET Core SignalR.

ASP.NET Core SignalR är en omarbetning av den föregående versionen. Därför är ASP.NET Core SignalR inte bakåtkompatibel med den tidigare SignalR-versionen. Både API:er och beteenden skiljer sig åt. ASP.NET Core SignalR SDK riktar in sig på NET-standarden, så du kan fortfarande använda den med .NET Framework. Du måste dock använda de nya API:erna i stället för de gamla. Om du använder SignalR och vill flytta till ASP.NET Core SignalR eller Azure SignalR Service måste du ändra din kod för att hantera API-skillnaderna.

Hos Azure SignalR Service finns serversidans komponent av ASP.NET Core SignalR i Azure. Men eftersom tekniken bygger på ASP.NET Core, har du möjlighet att köra din webbapp på flera plattformar (Windows, Linux och MacOS) om hanteringen sker med [Azure App Service](../app-service/overview.md), [IIS](/aspnet/core/host-and-deploy/iis/index), [Nginx](/aspnet/core/host-and-deploy/linux-nginx), [Apache](/aspnet/core/host-and-deploy/linux-apache) eller [Docker](/aspnet/core/host-and-deploy/docker/index). Du kan också själv stå för anslutningen till Internet i en egen process.

Om din app ska ha stöd för de senaste funktionerna för innehållsuppdatering av webbklienter i realtid, körning på flera plattformar (Azure, Windows, Linux och macOS) och hantering i olika miljöer kan Azure SignalR Service vara det bästa valet.

## <a name="why-not-deploy-signalr-myself"></a>Varför inte distribuera SignalR på egen hand?

Det är fortfarande möjligt att distribuera en Azure-webbapp på egen hand med stöd för ASP.NET Core SignalR som backend-komponent för din övergripande webbapp.

Ett av de viktigaste skälen till att använda Azure SignalR Service är enkelheten. Med Azure SignalR Service behöver du inte hantera problem med prestanda, skalbarhet och tillgänglighet. Sådana problem hanteras åt dig med ett servicenivåavtal på 99,9 %.

WebSockets är också normalt den teknik som föredras för stöd för innehållsuppdateringar i realtid. Belastningsutjämning av ett stort antal beständiga WebSocket-anslutningar blir dock ett komplicerat problemet att lösa vid skalning. Vanliga lösningar använder sig av DNS-lastbalansering, maskinvarubaserade lastbalanserare och programvarubaserad lastbalansering. Azure SignalR Service hanterar problemen åt dig.

En annan orsak kan vara att du inte har något behov av att vara värd för webbappen. Logiken för din webbappp kan använda sig av [serverlös databehandling](https://azure.microsoft.com/overview/serverless-computing/). Som exempel kanske din kod bara hanteras och körs på begäran med [Azure Functions](../azure-functions/index.yml)-utlösare. Det här scenariot kan vara komplicerat eftersom koden endast körs på begäran och inte upprätthåller längre anslutningar med klienter. Azure SignalR Service kan hantera den detta eftersom tjänsten redan hanterar anslutningar för dig. Mer information finns i [översikten över hur du använder SignalR Service med Azure Functions](signalr-concept-azure-functions.md).

## <a name="how-does-it-scale"></a>Hur går skalningen till?

Det är vanligt att skala SignalR med SQL Server, Azure Service Bus eller Azure Cache för Redis. Azure SignalR Service hanterar skalningsarbetet åt dig. Prestandan och kostnaden kan jämföras med dessa metoder utan komplexiteten som är förknippad med hanteringen av dessa övriga tjänster. Allt du behöver göra är att uppdatera antal enheter för din tjänst. Varje enhet stöder upp till 1 000 klientanslutningar.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett chattrum med Azure SignalR](signalr-quickstart-dotnet-core.md)