---
title: Azure CDN från Verizon Premium-regel motor matchnings villkor
description: Referens dokumentation för Azure Content Delivery Network från Verizon Premium Rules-regler för motor matchnings villkor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84323322"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN från Verizon Premium-regel motor matchnings villkor

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchnings villkor för Azure-Content Delivery Network (CDN) från Verizon Premium [Rules Engine](cdn-verizon-premium-rules-engine.md).

Den andra delen av en regel är matchnings villkoret. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka en uppsättning funktioner ska utföras.

Du kan till exempel använda ett matchnings villkor för att:

- Filtrera begär ande innehåll på en viss plats.
- Filtrera förfrågningar som genereras från en viss IP-adress eller land/region.
- Filtrera förfrågningar efter rubrik information.

## <a name="match-conditions"></a><a name="top"></a>Matchnings villkor

* [Alltid](#always)
* [Enhet](#device)
* [Plats](#location)
* [Kommer](#origin)
* [Förfrågan](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Always

[Villkoret Always match](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) är utformat för att tillämpa en standard uppsättning funktioner för alla begär Anden.

### <a name="device"></a><a name="device"></a>Enhet

Dessa matchnings villkor är utformade för att identifiera begär Anden baserat på klientens användar agent.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| Märkes namn | Identifierar förfrågningar efter om enhetens märkes namn matchar en: <br> **-** Angivet värde ([märkes namn literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Reguljärt uttryck ([märkes namn regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Ett speciellt mönster ([jokertecken för märkes namn](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Enhetens operativ system | Identifierar förfrågningar efter om enhetens operativ system matchar en: <br> **-** Angivet värde ([enhetens OS-litteral](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Reguljärt uttryck ([enhets-OS-regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för enhetens operativ system](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Enhetens operativsystemversion | Identifierar förfrågningar efter om enhetens operativ system version matchar en: <br> **-** Angivet värde ([enhetens OS-version literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Reguljärt uttryck ([enhetens operativ system version regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för enhetens operativ system version](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Dubbel orientering?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifierar förfrågningar efter om enheten stöder dubbel orientering. |
| HTML-föredragen DTD | Identifierar förfrågningar efter om enhetens HTML-önskade DTD matchar en: <br> **-** Angivet värde ([HTML-prioriterad DTD-literal](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Reguljärt uttryck ([HTML föredra DTD regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Speciellt mönster ([HTML-föredraget DTD-jokertecken](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Vill du ha en bild?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifierar förfrågningar efter om enheten stöder base64-kodade avbildningar. |
| [Är Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifierar förfrågningar efter om enheten använder Android-OS. |
| [Är app?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifierar förfrågningar efter om ett ursprungligt program begär innehåll. |
| [Är full skriv bord?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifierar förfrågningar efter om enheten ger en fullständig Skriv bords miljö. |
| [Är iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifierar förfrågningar efter om enheten använder iOS. |
| [Är robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifierar begär Anden efter om enheten anses vara en automatiserad HTTP-klient (t. ex. en robot-Crawler). |
| [Är smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifierar förfrågningar efter om enheten är en smart TV. |
| [Är smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifierar förfrågningar efter om enheten är en smartphone.
| [Är surfplatta?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifierar förfrågningar efter om enheten är en surfplatta. |
| [Är pekskärm?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifierar förfrågningar efter om enhetens primära pekdon är en pekskärm. |
| [Är Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifierar förfrågningar efter om enheten är en Windows Mobile 6.5/Windows Phone 7 eller högre. |
| [Är trådlös enhet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifierar förfrågningar efter om enheten är trådlös. 
| Marknadsförings namn | Identifierar förfrågningar efter om enhetens marknadsförings namn matchar en: <br> **-** Speciellt värde ([namn på marknadsförings namn](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Reguljärt uttryck ([marknadsförings namn, regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för marknadsförings namn](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Mobil webbläsare | Identifierar förfrågningar efter om enhetens webbläsare matchar en: <br> **-** Angivet värde ([mobil webbläsarens litteraler](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Reguljärt uttryck ([Mobile Browser regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för mobil webbläsare](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Mobil webbläsarens version | Identifierar förfrågningar efter om enhetens webbläsare version matchar en: <br> **-** Angivet värde ([mobil webbläsarens versions sträng](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Reguljärt uttryck ([mobil webbläsare version regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för mobil webbläsare version](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Modell namn | Identifierar förfrågningar efter om enhetens modell namn matchar en: <br> **-** Speciellt värde ([modell namn sträng](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Reguljärt uttryck ([modell namns regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för modell namn](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Progressiv nedladdning?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifierar förfrågningar efter om enheten har stöd för progressiv nedladdning. |
| Utgivningsdatum | Identifierar förfrågningar efter om enhetens utgivnings datum matchar ett: <br> **-** Visst värde ([exakt versions datum](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Reguljärt uttryck ([versions datum regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Visst mönster ([jokertecken för versions datum](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Lösnings höjd](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifierar förfrågningar efter enhetens höjd. |
| [Lösnings bredd](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifierar förfrågningar efter enhetens bredd. |

**[Tillbaka till början](#top)**

### <a name="location"></a><a name="location"></a>Location

Dessa matchnings villkor är utformade för att identifiera begär Anden utifrån beställarens plats.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [SOM nummer](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifierar begär Anden som kommer från ett visst nätverk. |
| Orts namn | Identifierar begär Anden efter om de härstammar från en stad vars namn matchar en: <br> **-** Angivet värde ([Orts namn literal](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Reguljärt uttryck ([Orts namn regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Kontinent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifierar begär Anden som kommer från de angivna kontinenterna. |
| [Land](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifierar begär Anden som kommer från de angivna länderna. |
| [DMA-kod](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifierar begär Anden som kommer från angiven Metros (utsedda marknads områden). |
| [Latitud](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifierar begär Anden som kommer från angivna latituder. |
| [Long](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifierar begär Anden som kommer från angivna longituder. |
| [Tunnelbane kod](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifierar begär Anden som kommer från angiven Metros (utsedda marknads områden). |
| [Postnummer](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifierar begär Anden som kommer från angivna post nummer. |
| [Regions kod](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifierar begär Anden som kommer från de angivna regionerna. |

> [!NOTE]
> **Ska jag använda tunnel Bane kod eller DMA-kod?** <br>
Båda dessa matchnings villkor ger samma funktion. Vi rekommenderar dock att du använder villkors matchnings villkoret för Metro-koden för att identifiera begär Anden via DMA.

**[Tillbaka till början](#top)**

### <a name="origin"></a><a name="origin"></a>Kommer

Dessa matchnings villkor är utformade för att identifiera begär Anden som pekar på CDN-lagring eller kund ursprungs Server.

| Name       | Syfte                                                           |
|------------|-------------------------------------------------------------------|
| [CDN-ursprung](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifierar begär Anden om innehåll som lagras på CDN-lagring. |
| [Kund ursprung](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifierar begär Anden om innehåll som lagras på en specifik kund ursprungs Server. |

**[Tillbaka till början](#top)**

### <a name="request"></a><a name="request"></a>Förfrågan

Dessa matchnings villkor är utformade för att identifiera förfrågningar baserat på deras egenskaper.

| Name              | Syfte                                                                |
|-------------------|------------------------------------------------------------------------|
| [Klientens IP-adress](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifierar begär Anden som kommer från en viss IP-adress. |
| Cookie-parameter  | Identifierar en begäran av om den innehåller en cookie som matchar en: <br> **-** Speciellt värde ([cookie-parameter literal](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Reguljärt uttryck ([cookie-parameter regex](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Speciellt mönster ([jokertecken för cookie-parameter](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifierar begär Anden som pekar på en viss Edge CNAME. |
| Refererande domän | Identifierar en begäran av om den refereras till av ett värdnamn som matchar en: <br> **-** Speciellt värde ([refererad domän-literal](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Speciellt mönster ([refererad domän-jokertecken](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Begärandehuvud | Identifierar en begäran av om den innehåller ett huvud som matchar en: <br> **-** Speciellt värde ([tecken på begär ande rubrik](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Reguljärt uttryck ([begär huvudets regex](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för begär ande rubrik](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Metod för begäran](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifierar förfrågningar per HTTP-metod. |
| [Begär ande schema](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifierar begär Anden via HTTP-protokollet. |

**[Tillbaka till början](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | Syfte                                                                |
|-------------------|------------------------------------------------------------------------|
| URL-sökvägen | Identifierar förfrågningar efter om deras relativa sökväg, inklusive fil namn, matchar en: <br> **-** Angivet värde ([URL Path literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Reguljärt uttryck ([URL-sökväg regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Ett speciellt mönster ([jokertecken för URL-sökväg](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Katalog för URL-sökväg | Identifierar förfrågningar efter om deras relativa sökväg matchar en: <br> **-** Angivet värde ([URL Path Directory literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Speciellt mönster ([URL Path Directory-jokertecken](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| URL Path-tillägg | Identifierar begär Anden efter om fil namns tillägget matchar ett: <br> **-** Angivet värde ([URL Path Extension literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Speciellt mönster ([jokertecken för URL-sökvägar](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| URL-sökväg filename | Identifierar begär Anden efter om deras fil namn matchar ett: <br> **-** Angivet värde ([URL sökväg filename literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Ett speciellt mönster ([jokertecken för URL-sökväg filename](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| URL-fråga | Identifierar förfrågningar baserat på om deras frågesträng matchar en: <br> **-** Speciellt värde ([URL-frågans litteral](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Reguljärt uttryck ([URL-fråga regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Speciellt mönster ([jokertecken för URL-frågor](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parameter för URL-fråga | Identifierar begär Anden efter om de innehåller en frågesträngparametern som har angetts till ett värde som matchar en: <br> **-** Angivet värde ([URL för frågeparameter](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Speciellt mönster ([jokertecken för URL-fråga](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Tillbaka till början](#top)**

De senaste matchnings villkoren finns i dokumentationen om [Verizon Rules Engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Regel motor referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standard-HTTP-beteendet med hjälp av regel motorn](cdn-verizon-premium-rules-engine.md)
