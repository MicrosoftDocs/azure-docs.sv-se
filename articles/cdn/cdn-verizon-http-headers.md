---
title: Verizon-Specific HTTP-huvuden för Azure CDN-regel motor | Microsoft Docs
description: Den här artikeln beskriver hur du använder Verizon-Specific HTTP-huvuden med Azure CDN Rules Engine.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343225"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-Specific HTTP-huvuden för Azure CDN-regel motor

För **Azure CDN Premium från Verizon** -produkter kan du när en http-begäran skickas till ursprungs servern, lägga till en eller flera reserverade huvuden (eller särskilda huvud namn för proxy) i klient förfrågan till POP-servern. De här rubrikerna är utöver de vanliga vidarebefordrings rubrikerna som tas emot. Information om huvud för begärandehuvuden finns i [förfrågnings fält](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Om du vill förhindra att någon av dessa reserverade huvuden läggs till i Azure CDN (Content Delivery Network) POP-begäran till ursprungs servern, måste du skapa en regel med [funktionen för Special rubriker för proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) i regel motorn. I den här regeln utelämnar du rubriken som du vill ta bort från standard listan över rubriker i fältet rubriker. Om du har aktiverat [funktionen Felsök cache](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)-svarshuvuden, se till att lägga till de nödvändiga `X-EC-Debug` rubrikerna. 

Om du t. ex. vill ta bort `Via` rubriken ska rubrik fältet i regeln innehålla följande lista över huvuden: *X-forwarded – for, x-forwardd-proto, x-Host, x-Midgress, x-Gateway-List, x-EG-Name, Host*. 

![Särskild rubrik regel för proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

I följande tabell beskrivs de huvuden som kan läggas till av Verizon CDN POP i förfrågan:

Begärandehuvud | Beskrivning | Exempel
---------------|-------------|--------
[Rapportör](#via-request-header) | Identifierar den POP-server som proxy-begäran till en ursprungs Server. | HTTP/1.1-ECS (DCA/1A2B)
X-vidarebefordrad – för | Anger begär andeens IP-adress.| 10.10.10.10
X-vidarebefordrad – proto | Anger begärans protokoll. | http
X-värd | Anger begärans värdnamn. | cdn.mydomain.com
X-Midgress | Anger om begäran anropades via en ytterligare CDN-Server. Till exempel en POP server till ursprungs skärm Server eller en POP server-to-och gateway-server. <br />Den här rubriken läggs endast till i begäran när midgress-trafiken äger rum. I det här fallet är sidhuvudet inställt på 1 för att indikera att begäran har anropats via en ytterligare CDN-Server.| 1
[Värd](#host-request-header) | Identifierar värden och porten där det begärda innehållet kan hittas. | marketing.mydomain.com:80
[X-Gateway-lista](#x-gateway-list-request-header) | OCH: identifierar listan över växling vid fel för och gateway-servrar som tilldelats ett kund ursprung. <br />Ursprungs skärm: anger den uppsättning ursprungs Skölds servrar som tilldelats ett kund ursprung. | `icn1,hhp1,hnd1`
X-EC-_&lt; namn &gt;_ | Begärandehuvuden som börjar med *X-EG* (till exempel x-EG-tag, [x-EG-debug](cdn-http-debug-headers.md)) är reserverade för användning av CDN.| WAF – produktion

## <a name="via-request-header"></a>Via begär ande huvud
Det format som rubriken för `Via` begäran identifierar en pop-server i, anges med följande syntax:

`Via: Protocol from Platform (POP/ID)` 

Termerna som används i syntaxen definieras enligt följande:
- Protokoll: anger vilken version av protokollet (t. ex. HTTP/1.1) som används för att proxya begäran. 

- Plattform: anger den plattform där innehållet begärdes. Följande koder är giltiga för det här fältet: 

    Kod | Plattform
    -----|---------
    ECAcc | HTTP-stor
    ECS   | HTTP-liten
    ECD   | Program leverans nätverk (och)

- POP: anger den [pop](cdn-pop-abbreviations.md) som hanterade begäran. 

- ID: endast för internt bruk.

### <a name="example-via-request-header"></a>Exempel via begär ande huvud

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Rubrik för värd förfrågan
POP-servrarna kommer att skriva över `Host` sidhuvudet när båda följande villkor är uppfyllda:
- Källan för det begärda innehållet är en kund ursprungs Server.
- Alternativet för den motsvarande kundens källa för HTTP-värd är inte tomt.

`Host`Begär ande huvudet skrivs över för att återspegla det värde som definierats i alternativet HTTP-värd huvud.
Om alternativet för kundens källa för HTTP-värd är inställt på tomt `Host` vidarebefordras det begär ande huvudet som skickas av beställaren till kundens ursprungs Server.

## <a name="x-gateway-list-request-header"></a>X-Gateway – lista över begär ande huvud
En POP-server lägger till/skriver över begär ande rubriken "X-Gateway-List" när något av följande villkor uppfylls:
- Begäran hänvisar till och-plattformen.
- Begäran vidarebefordras till en kund ursprungs server som skyddas av funktionen för ursprungs sköld.

