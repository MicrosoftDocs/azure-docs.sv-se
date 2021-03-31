---
title: Anpassade rapporter från Verizon | Microsoft Docs
description: 'Du kan visa användnings mönster för CDN med hjälp av följande rapporter: bandbredd, överförda data, träffar, cache-status, antal träffar i cachen, IPV4/IPV6-data överförs.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84888715"
---
# <a name="custom-reports-from-verizon"></a>Anpassade rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Genom att använda anpassade Verizon-rapporter via hanterings portalen för Verizon-profiler kan du definiera vilken typ av data som ska samlas in för Edge CNAME-rapporter.


## <a name="accessing-verizon-custom-reports"></a>Åtkomst till Verizon-anpassade rapporter
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera CDN-profil](./media/cdn-reports/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **analys** och hovra sedan över de **anpassade rapporter** som utfälls. Klicka på **Edge CNAME**.
   
    ![Hanterings Portal för CDN – menyn anpassade rapporter](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Edge CNAME-anpassad rapport
Den anpassade rapporten Edge CNAMEs innehåller information om träffar och data som överförs för Edge CNAME där anpassad rapport loggning har Aktiver ATS. Edge-CNAME består av Azure CDN slut punkts värdnamn och eventuella associerade anpassade domän namn. 

Loggning av anpassade rapport data påbörjas en timme efter att du har aktiverat en Edge CNAME-anpassad rapporterings funktion. Du kan Visa rapport data genom att generera en Edge CNAME-rapport för en speciell plattform eller för alla plattformar. Täckningen för den här rapporten är begränsad till de CNAME-CNAME för vilka anpassade rapport data samlades in under den angivna tids perioden. Rapporten Edge CNAME består av en graf och data tabell för de översta 10 kant-CNAMEna enligt måttet som definierats i mått alternativet. 

Skapa en anpassad rapport genom att definiera följande rapport alternativ:

- Mått: följande alternativ stöds:

   - Träffar: anger det totala antalet begär Anden som dirigeras till en Edge CNAME som den anpassade rapporterings funktionen är aktive rad för. Det här måttet inkluderar inte den status kod som returneras till klienten.

   - Överförda data: anger den totala mängden data som överförs från gräns servrarna till HTTP-klienter (till exempel webbläsare) för begär Anden som dirigeras till en Edge CNAME där den anpassade rapporterings funktionen är aktive rad. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden i svars texten. Det innebär att mängden data som överförs för varje till gång är större än den faktiska fil storleken.

- Grupperingar: anger vilken typ av statistik som visas under stapeldiagrammet. Följande alternativ stöds:

   - HTTP-svars koder: sorterar statistik efter HTTP-svarskod (till exempel 200, 403 osv.) som returneras till klienten. 

   - Status för cache: sorterar statistik efter cache-status.


Om du vill ange datum intervallet för rapporten kan du antingen välja ett fördefinierat datum intervall, till exempel **idag** eller **den här veckan**, i list rutan eller så kan du välja **anpassad** och manuellt ange ett datum intervall genom att klicka på kalender ikonerna. 

När du har valt datum intervallet klickar du på **gå** för att generera rapporten.

Du kan exportera data i Excel-format genom att klicka på Excel-symbolen till höger om knappen **gå** .

![CNAME-rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME-anpassade rapport fält

| Fält                     | Beskrivning   |
|---------------------------|---------------|
| 2xx                       | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i en HTTP-statuskod för 2xx (till exempel 200 OK). |
| 3xx                       | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i en HTTP-statuskod för 3xx (till exempel 302 eller 304 som inte har ändrats). |
| 4xx                       | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i en HTTP-statuskod för 4xx (till exempel 400 Felaktig begäran, 403 förbjuden eller 404 hittades inte). |
| 5xx                       | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i en HTTP-statuskod för 5xx (till exempel 500 internt Server fel eller 502 Felaktig gateway). |
| Cacheträffar%               | Anger procent andelen cacheable-begäranden som betjänades direkt från cache till beställaren. |
| Cacheträffar                | Anger det totala antalet begär Anden eller överförda data (MB) för den Edge CNAME som resulterar i en cacheträff (till exempel TCP_EXPIRED_HIT, TCP_HIT eller TCP_PARTIAL_HIT). En cacheträff inträffar när en cachelagrad version av det begärda innehållet hittas. |
| Överförda data (MB)     | Anger den totala mängden data som överförs (MB) från gräns servrarna till HTTP-klienter (webbläsare) för Edge CNAME. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden i svars texten. Det innebär att mängden data som överförs för varje till gång är större än den faktiska fil storleken. |
| Beskrivning               | Identifierar en Edge CNAME med dess värdnamn |
| Träffar                      | Anger det totala antalet begär anden till Edge CNAME |
| Missar                    | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i ett cache-missar (till exempel TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS eller TCP_MISS). Ett cacheminne saknas när det begärda innehållet inte cachelagrades på gräns servern som löste begäran. | 
| Ingen cache                  | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME som resulterar i en CONFIG_NOCACHE cache-statuskod.  |
| Annat                     | Anger det totala antalet begär Anden eller överförda data (MB) för Edge CNAME, vilket resulterar i en HTTP-statuskod som faller utanför 2xx-5xx-intervallet. |
| Plattform                  | Anger den plattform som hanterar Edge CNAME-trafiken. |
| Ej tilldelad               | Anger det totala antalet begär Anden eller överförda data (MB) för den kant-CNAME för vilken cache status kod eller HTTP-statuskod inte loggades.  |
| Det går inte att cachelagra               | Anger det totala antalet begär Anden eller överförda data (MB) för den Edge CNAME som resulterar i en cache-statuskod.  |


## <a name="considerations"></a>Överväganden
Rapporter kan bara skapas under de senaste 18 månaderna.

