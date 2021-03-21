---
title: Text visualiseringar för Azure Monitor arbets bok
description: Lär dig mer om alla text visualiseringar för Azure Monitor boken.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100622241"
---
# <a name="text-visualizations"></a>Textvisualiseringar

Med arbets böcker kan författarna innehålla textblock i sina arbets böcker. Texten kan vara mänsklig analys av telemetri, information som hjälper användarna att tolka dina data, avsnitts rubriker osv.

[![Skärm bild av Apdex text tabell](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Text läggs till via en markdown-kontroll, som ger fullständig formatering. Detta inkluderar olika rubrik-och teckensnitts format, hyperlänkar, tabeller osv.

Redigerings läge:

![Skärm bild av ett text steg i redigerings läge.](./media/workbooks-text-visualizations/text-edit-mode.png)

Förhands gransknings läge:

![Skärm bild av ett text steg i redigerings läge på fliken förhands granskning.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Lägg till en text kontroll

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till text** för att lägga till en text kontroll i arbets boken.
3. Lägg till markdown i fältet redaktör.
4. Använd alternativet *text format* för att växla mellan vanliga markdown och markdown omslutna med Azure Portal standard information/varning/lyckad/fel format.
5. Använd fliken **förhands granskning** för att se hur ditt innehåll kommer att se ut. Under redigeringen visar förhands granskningen innehållet i ett rullnings List utrymme för att begränsa dess storlek. men vid körning expanderas markdown-innehållet så att det fyller allt utrymme som krävs, utan rullnings lister.
6. Klicka på knappen **klar redigering** för att slutföra redigeringen av steget.

> [!TIP]
> Använd det här [markdown lathund-bladet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) för att lära dig om olika formateringsalternativ.

## <a name="text-styles"></a>Text format

Följande text format är tillgängliga för text steg:

| Format     | Förklaring                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Ingen ytterligare formatering används.                                                      |
| `info`    | Portalens "informations"-format, med en  `ℹ` eller liknande ikon och vanligt vis blå bakgrund.      |
| `error`   | Portalens "fel"-format, med en `❌` eller liknande ikon och i allmänhet röd bakgrund.     |
| `success` | Portalens "framgångar"-format, med en `✔` eller liknande ikon och i allmänhet grön bakgrund.  |
| `upsell`  | Portalens "merförsäljning"-format, med en `🚀` eller liknande ikon och vanligt vis lila bakgrund. |
| `warning` | Portalens "varning"-format, med en `⚠` eller liknande ikon och vanligt vis blå bakgrund.   |

I stället för att välja en speciell formatmall kan du också välja en text parameter som källa för formatet. Parametervärdet måste vara ett av ovanstående text värden. Avsaknad av ett värde eller ett okänt värde kommer att behandlas som `plain` format.

Exempel på informations format:

![Skärm bild av vad informations formatet ser ut.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Exempel på varnings format:

![Skärm bild av det som varnings formatet ser ut.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar ett [diagram i arbets böcker](workbooks-chart-visualizations.md).
* Lär dig hur du skapar ett [rutnät i arbets böcker](workbooks-grid-visualizations.md).
