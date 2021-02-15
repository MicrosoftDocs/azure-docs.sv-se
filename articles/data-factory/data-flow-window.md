---
title: Fönster omvandling i data flöde för mappning
description: Transformering av Azure Data Factory mappning av data flödes fönster
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/16/2020
ms.openlocfilehash: 56024fd0aac2f9fbefb7fe919eef2481550e573f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367834"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Fönster omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Fönster omvandlingen är den plats där du ska definiera Window-baserade agg regeringar för kolumner i dina data strömmar. I uttrycks verktyget kan du definiera olika typer av agg regeringar som baseras på data eller tidsfönster (SQL över-sats) som LEAD, fördröjning, NTILE, CUMEDIST, rang osv.). Ett nytt fält skapas i utdata som innehåller dessa agg regeringar. Du kan även ta med valfria grupp fält.

![Skärm bild som visar de fönster som valts från menyn.](media/data-flow/windows1.png "Windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Gentemot
Ange partitionering för kolumn data för fönster omvandlingen. SQL-motsvarigheten är ```Partition By``` i över-satsen i SQL. Om du vill skapa en beräkning eller skapa ett uttryck som ska användas för partitionering kan du göra det genom att hovra över kolumn namnet och välja "beräknad kolumn".

![Skärm bild som visar fönster inställningar med fliken över valt.](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Sortera
En annan del av över-satsen är att ange ```Order By``` . Detta anger sorterings ordningen för data. Du kan också skapa ett uttryck för att beräkna värdet i det här kolumn fältet för sortering.

![Skärm bild som visar fönster inställningar med fliken Sortera markerad.](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Intervall av
Ange sedan fönster ramen som obunden eller gräns. Om du vill ange en obunden fönster ram anger du skjutreglaget till obundet i båda ändar. Om du väljer en inställning mellan en obunden och aktuell rad måste du ange start-och slut värden för offset. Båda värdena är positiva heltal. Du kan använda antingen relativa tal eller värden från dina data.

Skjutreglaget fönster har två värden att ange: värdena före den aktuella raden och värdena efter den aktuella raden. Start-och slut förskjutningen matchar de två markeringarna i skjutreglaget.

![Skärm bild som visar fönster inställningar med fliken intervall efter vald.](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Fönster kolumner
Använd slutligen uttrycks verktyget för att definiera de agg regeringar som du vill använda med data Fönstren som rang, COUNT, MIN, MAX, tätare rang, LEAD, fördröjning osv.

![Skärm bild som visar resultatet av fönster åtgärden.](media/data-flow/windows7.png "Windows 7")

En fullständig lista över agg regerings-och analys funktioner som du kan använda i uttrycks språket för ADF-dataflödet via uttrycks verktyget finns här: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>Nästa steg

Om du letar efter en enkel samlings grupp, använder du den [sammanställda omvandlingen](data-flow-aggregate.md)
