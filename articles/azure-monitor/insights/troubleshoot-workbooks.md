---
title: Felsöka Azure Monitor arbetsböcker-baserade insikter
description: Innehåller fel söknings vägledning för Azure Monitor arbets boksbaserade insikter för tjänster som Azure Key Vault, Azure CosmosDB, Azure Storage och Azure cache för Redis.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3a2765e9dcf3b0e7bf28191e6ecbb069f26c42f5
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208530"
---
# <a name="troubleshooting-workbook-based-insights"></a>Felsöka arbetsbaserade insikter

Den här artikeln hjälper dig med diagnos och fel sökning av några vanliga problem som kan uppstå när du använder Azure Monitor arbets boksbaserade insikter.


## <a name="why-can-i-only-see-200-resources"></a>Varför kan jag bara se 200-resurser

Antalet valda resurser har en gräns på 200, oavsett hur många prenumerationer som har valts.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Vad händer när jag klickar på en nyligen fäst panel på instrument panelen

* Om du klickar någonstans på panelen kommer du till fliken där panelen har fästs från. Om du till exempel fäster en graf på fliken "Översikt" och sedan klickar på den panelen på instrument panelen öppnas den som standardvy, men om du fäster ett diagram från din egna sparade kopia öppnas vyn Sparad kopia.
* Filter ikonen längst upp till vänster i rubriken öppnar fliken "Konfigurera panel inställningar".
* Ellips-ikonen längst upp till höger ger dig alternativen "anpassa rubrik data", "anpassa", "uppdatera" och "ta bort från instrument panelen".

## <a name="what-happens-when-i-save-a-workbook"></a>Vad händer när jag sparar en arbets bok

* När du sparar en arbets bok kan du skapa en ny kopia av arbets boken med dina ändringar och ändra rubriken. Om du sparar skrivs inte arbets boken över, den aktuella arbets boken är alltid standardvy.
* En **osparad** arbets bok är bara standardvyn.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Varför visas inte alla mina prenumerationer i portalen

Portalen visar endast data för de valda prenumerationerna på Portal lansering. Om du vill ändra vilka prenumerationer som är markerade går du till det övre högra hörnet och klickar på antecknings boken med en filter ikon. Med det här alternativet visas fliken **katalog + prenumerationer** .

![Katalog + prenumeration](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Vad är tidsintervall

Tidsintervallet visar data från en viss tidsram. Om tidsintervallet exempelvis är 24 timmar visar det data från de senaste 24 timmarna.

## <a name="what-is-time-granularity-time-grain"></a>Vad är tids kornig het (tids kornig het)

Tids kornig het är tids skillnaden mellan två data punkter. Om tids kornigheten till exempel är en sekund som innebär att mått samlas in varje sekund.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Vad är tids kornig het när vi fäster någon del av arbets böckerna på en instrument panel

Standard tids kornig het är inställd på automatisk, den går för närvarande inte att ändra just nu.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hur gör jag för att ändra tidsintervall/tidsintervall för arbets bokens steg på min instrument panel

Som standard är TimeSpan/tidsintervallet på instrument panelen inställd på 24 timmar. om du vill ändra detta klickar du på ellipserna längst upp till höger. Välj **Anpassa panel data**, markera kryss rutan Åsidosätt inställningarna för instrument panelen på rubrik nivå och välj sedan ett TimeSpan med hjälp av list menyn.  

![Välj ellipserna i det högra hörnet av panelen och välj anpassa dessa data](./media/storage-insights-overview/fqa-data-settings.png)

![I Konfigurera panel inställningar väljer du List rutan TimeSpan för att ändra tidsintervall/tidsintervall](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hur gör jag för att ändra titeln på arbets boken eller ett arbets boks steg som jag fästa på en instrument panel

Rubriken på arbets bokens eller arbets bokens steg som fästs på en instrument panel behåller samma namn som den hade i arbets boken. Om du vill ändra titeln måste du spara din egen kopia av arbets boken. Sedan kan du namnge arbets boken innan du trycker på Spara.

![Välj Spara längst upp för att spara en kopia av arbets boken och ändra namnet på den](./media/storage-insights-overview/fqa-change-workbook-name.png)

Om du vill ändra namnet på ett steg i din sparade arbets bok väljer du redigera under steget och väljer sedan kugg hjulet längst ned i inställningarna.

![Välj Redigera längst ned i ett arbets boks steg för att öppna inställningarna ](./media/storage-insights-overview/fqa-edit.png)
 ![ i Inställningar Välj växeln längst ned för att kunna ändra steg namnet](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om arbets böckerna för scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../platform/workbooks-overview.md).
