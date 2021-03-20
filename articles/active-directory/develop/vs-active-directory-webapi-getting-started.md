---
title: Kom igång med Azure AD i Visual Studio WebApi-projekt
description: Komma igång med att använda Azure Active Directory i WebApi-projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: fb8c1f25c8a22c96679a0310353f474f6b9d5ba5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165438"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Kom igång med Azure Active Directory (WebApi-projekt)

> [!div class="op_single_selector"]
> - [Kom igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln ger ytterligare vägledning när du har lagt till Active Directory i ett ASP.NET WebAPI-projekt med kommandot **project > Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [vad som hände med mitt WebAPI-projekt?](vs-active-directory-webapi-what-happened.md) för de ändringar som gjorts i projektet när du lade till den anslutna tjänsten.

## <a name="requiring-authentication-to-access-controllers"></a>Kräver autentisering för åtkomst till kontrollanter

Alla kontrollanter i projektet har dekorationer med `[Authorize]` attributet. Det här attributet kräver att användaren autentiseras innan de får åtkomst till de API: er som definierats av dessa styrenheter. Om du vill tillåta att styrenheten används anonymt tar du bort attributet från kontroll enheten. Om du vill ange behörigheterna på en mer detaljerad nivå använder du attributet för varje metod som kräver auktorisering i stället för att använda den i kontroll enhets klassen.

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](./authentication-vs-authorization.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)