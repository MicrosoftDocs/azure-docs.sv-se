---
title: Använda dekoration markörer för att markera text – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder text dekorationer och träff markeringar i dina Sök resultat med hjälp av API för webbsökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351886"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Använda dekoration markörer för att markera text

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing stöder träff markering, som markerar sökord (eller andra termer som Bing hittar relevanta) i visnings strängarna för vissa svar. Till exempel kan en webb sidas resultat `name` -, `displayUrl` -och- `snippet` fält innehålla markerade sökord. 

Som standard innehåller Bing inte markerings markörer i visnings strängar. Om du vill aktivera markörerna lägger du till `textDecorations` frågeparametern i din begäran och anger den `true` .

## <a name="hit-highlighting-example"></a>Exempel på träff markering

I följande exempel visas ett webb resultat för `Sailing Dinghy` . Bing har markerat början och slutet av frågetermen med hjälp av Unicode-tecknen E000 och E001.
  
![Träff markering](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Innan du visar resultatet i användar gränssnittet ersätter du Unicode-tecknen med dem som passar ditt visnings format.

## <a name="marker-formatting"></a>Markör format

Bing ger möjlighet att använda antingen Unicode-tecken eller HTML-taggar som markörer. Om du vill ange vilka markörer som ska användas [inkluderar du Frågeparametern](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) : 

| Värde             | Vänsterindrag                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-tecken (standard) |
| `textFormat=HTML` | HTML-tecken              |

## <a name="additional-text-decorations"></a>Ytterligare dekorationer för text

Bing kan returnera flera olika text dekorationer. Ett `Computation` svar kan till exempel innehålla nedsänkta markörer för frågetermn `log(2)` i `expression` fältet.

![beräknings markörer](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Om begäran inte angav dekorationer `expression` skulle fältet innehålla `log10(2)` . 

Om `textDecorations` är `true` , kan Bing innehålla följande markörer i svars visnings strängar. Om det inte finns någon motsvarande HTML-tagg är tabell cellen tom.

|Unicode|HTML|Beskrivning
|-|-|-
|U + E000|\<b>|Markerar början på frågetermen (träff markering)
|U + E001|\</b>|Markerar slutet av frågeterm
|U + E002|\<i>|Markerar början på kursivt innehåll 
|U + E003|\</i>|Markerar slutet på kursivt innehåll
|U + E004|\<br/>|Markerar en rad brytning
|U + E005||Markerar början av ett telefonnummer
|U + E006||Markerar slutet på ett telefonnummer
|U + E007||Markerar början av en adress
|U + E008||Markerar slutet på en adress
|U + E009|\&nbsp;|Markerar ett hårt blank steg
|U + E00C|\<strong>|Markerar början på fet innehålls
|U + E00D|\</strong>|Markerar slutet på fet innehålls
|U + E00E||Markerar början av innehåll vars bakgrund ska vara ljusare än den omgivande bakgrunden
|U + E00F||Markerar slutet på innehållet vars bakgrund ska vara ljusare än den omgivande bakgrunden
|U + E010||Markerar början av innehåll vars bakgrund ska vara mörkare än den omgivande bakgrunden
|U + e011||Markerar slutet på innehållet vars bakgrund ska vara mörkare än den omgivande bakgrunden
|U + E012|\<del>|Markerar början på det innehåll som ska placeras genom
|U + E013|\</del>|Markerar slutet på det innehåll som ska placeras genom
|U + E016|\<sub>|Markerar början av nedsänktt innehåll
|U + E017|\</sub>|Markerar slutet på nedsänktt innehåll
|U + E018|\<sup>|Markerar början av upphöjda skript innehåll
|U + E019|\</sup>|Markerar slutet på upphöjda skript innehåll

## <a name="next-steps"></a>Nästa steg

* [Vad är API för webbsökning i Bing?](overview.md) 
* [Ändra storlek på och beskär miniatyrer](resize-and-crop-thumbnails.md)