---
title: Bibliotek för Microsoft Identity Platform-autentisering | Azure
description: Lista över klient bibliotek och mellanprogram som är kompatibla med Microsoft Identity Platform. Använd de här biblioteken för att lägga till stöd för användar inloggning (autentisering) och skyddat webb-API-åtkomst (auktorisering) till dina program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060388"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotek för Microsoft Identity Platform-autentisering

Följande tabeller visar stöd för Microsoft Authentication Library för flera program typer. De innehåller länkar till bibliotekets källkod, där paketet för appens projekt hämtas, och om biblioteket stöder användar inloggning (autentisering), åtkomst till skyddade webb-API: er (auktorisering) eller både och.

Microsoft Identity Platform har certifierats av OpenID Foundation som en [certifierad OpenID-Provider](https://openid.net/certification/). Om du föredrar att använda ett annat bibliotek än Microsoft Authentication Library (MSAL) eller ett annat bibliotek som stöds av Microsoft väljer du ett med en [certifierad OpenID Connect-implementering](https://openid.net/developers/certified/).

Om du väljer att manuellt koda din egen protokoll nivå implementering av [OAuth 2,0 eller OpenID Connect 1,0](active-directory-v2-protocols.md), bör du gå noga med säkerhets aspekterna i varje Standards specifikation och följa en sdl-metod (Software Development Lifecycle) som [Microsoft sdl][Microsoft-SDL].

## <a name="single-page-application-spa"></a>SPA (Single-Side Application)

Ett program med en enda sida körs helt i webbläsaren och hämtar sid data (HTML, CSS och Java Script) dynamiskt eller vid programmets inläsnings tid. Den kan anropa webb-API: er för att interagera med Server dels data källor.

Eftersom en SPA-kod körs helt i webbläsaren, betraktas den som en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Webbprogram

Ett webb program kör kod på en server som genererar och skickar HTML, CSS och Java Script till en användares webbläsare som ska återges. Användarens identitet upprätthålls som en session mellan användarens webbläsare (klient delen) och webb servern (Server delen).

Eftersom ett webb programs kod körs på webb servern betraktas det som en *konfidentiell klient* som kan lagra hemligheter på ett säkert sätt.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Skriv bords program

Ett Skriv bords program är vanligt vis binär (kompilerad) kod som visar ett användar gränssnitt och som är avsett att köras på en användares skriv bord.

Eftersom ett Skriv bords program körs på användarens skriv bord, betraktas det som en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Mobilprogram

Ett mobil program är vanligt vis binär (kompilerad) kod som visar ett användar gränssnitt och som är avsett att köras på en användares mobila enhet.

Eftersom ett mobil program körs på användarens mobila enhet anses det vara en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Service/daemon

Tjänster och daemon används ofta för server-till-Server och annan obevakad kommunikation (kallas ibland för kommunikation *).* Eftersom det inte finns någon användare på tangent bordet för att ange autentiseringsuppgifter eller medgivande till resurs åtkomst, autentiseras dessa program som de ska, inte en användare, när de begär åtkomst till en webb-API-resurs.

En tjänst eller daemon som körs på en server betraktas som en *konfidentiell klient* som kan lagra sina hemligheter på ett säkert sätt.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Microsoft Authentication Library finns i [Översikt över Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
