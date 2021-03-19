---
title: Omdirigera översikt för Azure Application Gateway
description: Lär dig mer om omdirigerings funktionen i Azure Application Gateway för att dirigera om trafik som tas emot från en lyssnare till en annan lyssnare eller till en extern plats.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fb01d5a4923410f693b682d66be8d5d09f9019d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90561586"
---
# <a name="application-gateway-redirect-overview"></a>Översikt över Application Gateway omdirigering

Du kan använda Application Gateway för att omdirigera trafik.  Den har en allmän omdirigeringsmekanism som gör det möjligt att omdirigera trafik som tas emot i en lyssnare till en annan lyssnare eller till en webbadress till en extern webbplats. Detta fören klar program konfigurationen, optimerar resursanvändningen och har stöd för nya omdirigerings scenarier, inklusive global och Sök vägs baserad omdirigering.

Ett vanligt scenario för omdirigering för många webb program är att stödja automatisk HTTP-till-HTTPS-omdirigering för att säkerställa all kommunikation mellan program och dess användare sker över en krypterad sökväg. Tidigare har kunder använt tekniker som att skapa en dedikerad backend-pool vars enda syfte är att omdirigera begär Anden som tas emot på HTTP till HTTPS. Med stöd för omdirigering i Application Gateway kan du göra det genom att lägga till en ny omdirigerings konfiguration till en regel för Routning och ange en annan lyssnare med HTTPS-protokollet som mål lyssnare.

Följande typer av omdirigering stöds:

- 301 permanent omdirigering
- 302 hittades
- 303 Se andra
- 307 tillfällig omdirigering

Stöd för Application Gateway-stöd har följande funktioner:

-  **Global omdirigering**

   Omdirigerar från en lyssnare till en annan lyssnare på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- **Sökväg baserad omdirigering**

   Den här typen av omdirigering möjliggör HTTP till HTTPS-omdirigering endast på ett speciellt plats område, till exempel ett shopping vagns område som betecknas av/Cart/*.
- **Omdirigera till extern plats**

![Diagrammet visar användare och en app-gateway och anslutningar mellan de två, inklusive en nedlåst H T T P-röd pil, en inte tillåten 301 direkt röd pil och en låst H T T P S en grön pil.](./media/redirect-overview/redirect.png)

Med den här ändringen måste kunderna skapa ett nytt konfigurations objekt för omdirigering som anger den mål lyssnare eller extern plats som omdirigering önskas till. Konfigurations elementet stöder också alternativ för att lägga till URI-sökvägen och frågesträngen till den omdirigerade URL: en. Du kan också välja typ av omdirigering. När den här omdirigeringen har skapats kopplas den till käll lyssnaren via en ny regel. När du använder en grundläggande regel associeras omdirigerings konfigurationen med en käll lyssnare och är en global omdirigering. När en sökväg-baserad regel används, definieras omdirigerings konfigurationen i sökvägen till URL-sökvägen. Detta gäller endast för det speciella Sök vägs området på en plats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera URL-omdirigering på en Application Gateway](tutorial-url-redirect-powershell.md)
