---
title: Azure CLI-exempel – Azure SignalR Service
description: Använd riktiga exempel för att lära dig Azure CLI för Azure SignalR-tjänsten. Du får lära dig hur du skapar SignalR service med fler Azure-tjänster.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86515248"
---
# <a name="azure-cli-reference"></a>Azure CLI-referens

Följande tabell inkluderar länkar till bash-skript som skapats för Azure SignalR Service med Azure CLI.

| Skript | Förklaringar |
|-|-|
|**Skapa**||
| [Skapa en ny SignalR Service och resursgrupp](scripts/signalr-cli-create-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn.  |
|**Integrera**||
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR](scripts/signalr-cli-create-with-app-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny webbapp och App Service plan som värd för en ASP.NET Core webbapp som använder SignalR-tjänsten. Webbappen konfigureras med en appinställning för att ansluta till den nya SignalR Service-resursen. |
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR och GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny Azure-webbapp och en värd plan som är värd för en ASP.NET Core webbapp som använder SignalR-tjänsten. Webbappen konfigureras med appinställningar för anslutningssträngen till den nya SignalR Service-resursen, och klienthemligheter för att stödja [GitHub-autentisering](https://developer.github.com/v3/guides/basics-of-authentication/) som det visas i [självstudien om autentisering](signalr-concept-authenticate-oauth.md). Webbappen är också konfigurerad för att använda en lokal distributionskälla för Git-lagringsplatsen. |
| | |
