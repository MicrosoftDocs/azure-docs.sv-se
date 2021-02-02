---
title: Förstå och lösa problem med Azure AD-programproxy CORS
description: Ger en förståelse för CORS i Azure AD-programproxy och hur du kan identifiera och lösa CORS-problem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b57fc7e3af99819c9b27b6bc796e501d1db02818
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259176"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Förstå och lösa CORS-problem för Azure Active Directory-programproxy

[Resurs delning mellan ursprung (CORS)](https://www.w3.org/TR/cors/) kan ibland innebära utmaningar för appar och API: er som du publicerar via Azure Active Directory-programproxy. I den här artikeln beskrivs problem och lösningar för Azure AD-programproxy CORS.

Webb läsar säkerhet förhindrar vanligt vis en webb sida från att göra AJAX-begäranden till en annan domän. Den här begränsningen kallas *samma ursprungs princip* och förhindrar att en skadlig webbplats läser känsliga data från en annan plats. Men ibland kanske du vill låta andra platser anropa ditt webb-API. CORS är en W3C-standard som gör det möjligt för en server att öka principen för samma ursprung och tillåta vissa frågor om flera ursprung medan andra nekas.

## <a name="understand-and-identify-cors-issues"></a>Förstå och identifiera CORS-problem

Två URL: er har samma ursprung om de har identiska scheman, värdar och portar ([RFC 6454](https://tools.ietf.org/html/rfc6454)), till exempel:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

Följande URL: er har olika ursprung än de föregående två:

-   http: \/ /contoso.net-annan domän
-   http: \/ /contoso.com:9000/foo.html-annan port
-   https: \/ /contoso.com/foo.html-annat schema
-   http: \/ /www.contoso.com/foo.html-annan under domän

Principen med samma ursprung hindrar appar från att komma åt resurser från andra ursprung om de inte använder rätt rubrik för åtkomst kontroll. Om CORS-huvudena saknas eller är felaktiga, så går det inte att utföra cross-origin-begäranden. 

Du kan identifiera CORS-problem med hjälp av webbläsarens fel söknings verktyg:

1. Starta webbläsaren och gå till webbappen.
1. Tryck på **F12** för att öppna felsökningskonsolen.
1. Försök att återskapa transaktionen och granska konsolmeddelandet. En CORS-överträdelse skapar ett konsolfel om ursprung.

I följande skärm bild kan du välja knappen **testa** som orsakade ett CORS-fel meddelande om att https: \/ /corswebclient-contoso.msappproxy.net inte hittades i rubriken Access-Control-Allow-Origin.

![CORS-problem](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS-utmaningar med Application Proxy

I följande exempel visas ett typiskt Azure AD-programproxy CORS-scenario. Den interna servern är värd för en **CORSWebService** webb-API-kontrollant och en **CORSWebClient** som anropar **CORSWebService**. Det finns en AJAX-begäran från **CORSWebClient** till **CORSWebService**.

![Lokal begäran om samma ursprung](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient-appen fungerar när du är värd för den lokalt, men det går inte att läsa in eller fel vid publicering via Azure AD-programproxy. Om du har publicerat CORSWebClient-och CORSWebService-apparna separat som olika appar via programproxyn, finns de två apparna i olika domäner. En AJAX-begäran från CORSWebClient till CORSWebService är en begäran om Cross-Origin och den Miss lyckas.

![CORS-begäran för Application Proxy](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Lösningar för CORS-problem i Application Proxy

Du kan lösa föregående CORS-problem på något av flera sätt.

### <a name="option-1-set-up-a-custom-domain"></a>Alternativ 1: Konfigurera en anpassad domän

Använd en anpassad Azure AD-programproxy- [domän](./application-proxy-configure-custom-domain.md) för att publicera från samma ursprung, utan att behöva göra några ändringar i appens ursprung, kod eller rubriker. 

### <a name="option-2-publish-the-parent-directory"></a>Alternativ 2: publicera den överordnade katalogen

Publicera den överordnade katalogen för båda apparna. Den här lösningen fungerar särskilt bra om du bara har två appar på webb servern. I stället för att publicera varje app separat kan du publicera den gemensamma överordnade katalogen, vilket resulterar i samma ursprung.

I följande exempel visas portalens Azure AD-programproxy-sida för CORSWebClient-appen.  När den **interna URL:** en är inställd på *contoso.com/CORSWebClient* kan appen inte göra lyckade förfrågningar till *contoso.com/CORSWebService* -katalogen, eftersom de är mellan ursprung. 

![Publicera app individuellt](./media/application-proxy-understand-cors-issues/image4.png)

Ange i stället den **interna URL** : en för att publicera den överordnade katalogen, som innehåller både *CORSWebClient* -och *CORSWebService* -katalogerna:

![Publicera överordnad katalog](./media/application-proxy-understand-cors-issues/image5.png)

De resulterande App-URL: erna löser effektivt CORS-problemet:

- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Alternativ 3: uppdatera HTTP-rubriker

Lägg till ett anpassat HTTP-svarshuvuden på webb tjänsten för att matcha ursprungs förfrågan. För webbplatser som körs i Internet Information Services (IIS) använder du IIS-hanteraren för att ändra rubriken:

![Lägg till anpassat svars huvud i IIS-hanteraren](./media/application-proxy-understand-cors-issues/image6.png)

Ändringen kräver inga kod ändringar. Du kan kontrol lera det i Fiddler-spåren:

**Publicera rubriken addition**\
HTTP/1.1 200 OK \
Cache-kontroll: no-cache \
Pragma: no-cache \
Content-Type: text/plain charset = utf-8 \
Upphör att gälla:-1 \
Variera: Accept-Encoding \
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0 \
**Åtkomst kontroll-Allow-Origin: https \: //corswebclient-contoso.msappproxy.net**\
X-ASPNET-version: 4.0.30319 \
X-driven-av: ASP.NET \
Innehålls längd: 17

### <a name="option-4-modify-the-app"></a>Alternativ 4: ändra appen

Du kan ändra din app så att den stöder CORS genom att lägga till rubriken Access-Control-Allow-Origin med lämpliga värden. Hur du lägger till sidhuvudet beror på appens kod språk. Att ändra koden är det minst rekommenderade alternativet, eftersom det kräver störst ansträngning.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Alternativ 5: Förläng livs längden för åtkomsttoken

Vissa CORS-problem kan inte lösas, till exempel när appen omdirigeras till *login.microsoftonline.com* för autentisering och åtkomsttoken upphör att gälla. CORS-anropet misslyckas då. En lösning för det här scenariot är att utöka livslängden för åtkomsttoken, för att förhindra att den upphör att gälla under en användarsession. Mer information om hur du gör detta finns i [konfigurations bara livs längder för token i Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Se även
- [Självstudie: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planera en distribution av Azure AD-programproxy](application-proxy-deployment-plan.md) 
- [Fjärråtkomst till lokala program via Azure Active Directory-programproxy](application-proxy.md)