---
title: Rensning av cacheminne i Azure frontend-standard/Premium (för hands version)
description: Den här artikeln hjälper dig att förstå hur du rensar cachen i en Azure-dörr standard/Premium.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099816"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Rensning av cacheminne i Azure frontend-standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azure front dörr standard/Premium cachelagrar till gångar tills till gångens TTL (Time-to-Live) går ut. När en klient begär en till gång med utgånget TTL hämtar Azures frontend-miljö en ny uppdaterad kopia av till gången för att betjäna begäran och lagrar sedan den uppdaterade cachen.

Bästa praxis är att se till att användarna alltid får den senaste kopian av dina till gångar. Sättet att göra det är att version till till gångar för varje uppdatering och publicera dem som nya URL: er. Azures front dörr standard/Premium hämtar omedelbart de nya till gångarna för nästa klient begär Anden. Ibland kanske du vill rensa cachelagrat innehåll från alla Edge-noder och tvinga dem att hämta nya, uppdaterade till gångar. Anledningen till att du vill rensa cachelagrat innehåll är att du har gjort nya uppdateringar av programmet eller vill uppdatera till gångar som innehåller felaktig information.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Läs mer om hur cachelagring fungerar i [Azures cache för frontend-dörr](concept-caching.md) .

## <a name="configure-cache-purge"></a>Konfigurera rensning av cache

1. Gå till sidan Översikt i Azures frontend-profil med de till gångar som du vill rensa och välj sedan **Rensa cache**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Skärm bild av cache-rensning på översikts sidan.":::

1. Välj den slut punkt och domän som du vill rensa från Edge-noderna. *(Du kan välja fler än en domän)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Skärm bild av sidan rensning av cache.":::

1. Om du vill rensa alla till gångar väljer du **Rensa alla till gångar för de valda domänerna**. Annars anger du sökvägen till varje till gång som du vill rensa i **sökvägar**.

Dessa format stöds i listor med sökvägar som ska rensas:

* **Enkel sökväg rensa**: rensa enskilda till gångar genom att ange den fullständiga sökvägen till till gången (utan protokollet och domänen), med fil namns tillägget, till exempel/Pictures/strasbourg.png.
* **Rot domän rensning**: Rensa slut punktens rot med "/*" i sökvägen.

Rensningar av cacheminnen på standard-/preium för Azures främre dörr är inte Skift läges känsliga. Dessutom är de frågesträngen oberoende, vilket innebär att en URL rensas och alla frågesträngar tas bort. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
