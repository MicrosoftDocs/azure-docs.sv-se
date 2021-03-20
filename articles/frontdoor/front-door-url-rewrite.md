---
title: Azure-front dörr – URL-omskrivning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure-frontend gör att URL skrivs om för dina vägar, om den har kon figurer ATS.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91445482"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-omskrivning (anpassad sökväg för vidarebefordran)
Azures front dörr stöder URL-omskrivning genom att konfigurera en valfri **anpassad vidarebefordrings Sök väg** som ska användas för att skapa begäran om att vidarebefordra den till Server delen. Om en anpassad vidarebefordrings Sök väg inte anges kopieras den inkommande URL-sökvägen till den URL som används i den vidarebefordrade begäran som standard. Värdhuvudet som används i den vidarebefordrade begäran ser ut så som det konfigurerats för den valda serverdelen. Läs [värd rubriken för Server delen](front-door-backend-pool.md#hostheader) för att lära dig vad det gör och hur du kan konfigurera den.

Den kraftfulla delen av URL-omskrivning är att den anpassade vidarebefordrande sökvägen kommer att kopiera någon del av den inkommande sökvägen som matchar en sökväg med jokertecken till den vidarebefordrade sökvägen (dessa Sök vägs segment är de **gröna** segmenten i exemplet nedan):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="URL-omskrivning i Azures frontend-dörr":::

## <a name="url-rewrite-example"></a>Exempel på URL-omskrivning
Överväg en regel för routning med följande kombination av klient dels värdar och konfigurerade sökvägar:

| Värdar      | Sökvägar       |
|------------|-------------|
| www- \. contoso.com | /\*   |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

Den första kolumnen i tabellen nedan visar exempel på inkommande begär Anden och den andra kolumnen visar vad som skulle vara den "mest aktuella" matchande väg Sök väg ".  De tredje och välliggande kolumnerna i tabellen är exempel på konfigurerade **anpassade vidarebefordrings Sök vägar**.

Om vi t. ex. läser på den andra raden, säger vi att för inkommande begäran `www.contoso.com/sub` , om den anpassade sökvägen för vidarebefordran var `/` , så skulle den vidarebefordrande sökvägen vara `/sub` . Om den anpassade vidarebefordrande sökvägen var `/fwd/` , är den vidarebefordrade sökvägen `/fwd/sub` . Och så vidare, för de återstående kolumnerna. De **betonade** delarna av Sök vägarna nedan representerar de delar som är en del av matchningen av jokertecken.

| Inkommande begäran       | Mest speciell matchnings Sök väg | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www- \. contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| www- \. contoso.com/**Sub**     | /\*                      | /**Build**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www \. **-contoso.com/a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www- \. contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| www- \. contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| www \. contoso.com/foo/-**stapel** | foo\*                  | /**Valle**   | /FWD/-**fält**   | /foo/-**fält**   | /foo/bar/-**fält**   |

## <a name="optional-settings"></a>Valfria inställningar
Det finns ytterligare valfria inställningar som du kan ange för alla inställningar för routningsregler:

* **Cache-konfiguration** – om det är inaktiverat eller ej angivet försöker begär Anden som matchar den här regeln inte använda cachelagrat innehåll. i stället hämtas de alltid från Server delen. Läs mer om [cachelagring med front dörren](front-door-caching.md).

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
