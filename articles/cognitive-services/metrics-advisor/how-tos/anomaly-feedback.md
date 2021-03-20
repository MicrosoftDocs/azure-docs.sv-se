---
title: Ge avvikande feedback till Metrics Advisor-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skickar feedback om avvikelser som upptäckts av din Metric Advisor-instans och justera resultaten.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185019"
---
# <a name="provide-anomaly-feedback"></a>Ge avvikande feedback

Feedback från användare är en av de viktigaste metoderna för att identifiera fel i systemet med avvikelse identifiering. Här är ett sätt för användarna att markera Felaktiga identifierings resultat direkt i en tids serie och sedan använda feedbacken direkt. På så sätt kan en användare lära sig avvikelse identifierings systemet hur man utför avvikelse identifiering för en speciell tids serie via aktiva interaktioner. 

> [!NOTE]
> För närvarande påverkar för närvarande inte avvikelse identifierings resultat vid **Smart identifiering** , men inte **tröskelvärdet för maskin** vara och **ändrings tröskel**.

## <a name="how-to-give-time-series-feedback"></a>Så här ger du feedback om Time Series

Du kan ge feedback från sidan med mått information i alla serier. Välj bara en punkt så visas dialog rutan nedan. Den visar dimensionerna i serien som du har valt. Du kan välja dimensions värden igen eller ta bort några av dem för att få en batch över tids serie data. När du har valt tids serie väljer du knappen **Lägg** till för att lägga till feedback, det finns fyra typer av feedback som du kan ge. Om du vill lägga till flera feedback-objekt väljer du knappen **Spara** när du är klar med dina kommentarer.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Graf med tids serie data med en blå linje och röda punkter vid olika punkter. Röd ruta som omger en punkt med texten: Välj valfri punkt":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Dialog rutan Lägg till feedback med två dimensioner och alternativet för att välja eller ta bort dimensioner och lägga till feedback.":::

### <a name="mark-the-anomaly-point-type"></a>Markera typen avvikelse punkt

Som du ser i bilden nedan fyller dialog rutan feedback automatiskt tills den valda punkten är tidsstämpel, men du kan redigera det här värdet. Sedan väljer du om du vill identifiera det här objektet som en `Anomaly` , `NotAnomaly` eller `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Nedrullningsbar meny med val av avvikelser, NotAnomaly och identifiera automatiskt":::

Valet kommer att tillämpa din feedback på den framtida avvikelse identifieringen av samma serie. De bearbetade punkterna kommer inte att beräknas om. Det innebär att om du har markerat en avvikelse som NotAnomaly, ignorerar vi liknande avvikelser i framtiden, och om du har markerat en `NotAnomaly` tidpunkt som kommer `Anomaly` vi att upptäcka liknande punkter som `Anomaly` i framtiden. Om väljs `AutoDetect` kommer all tidigare feedback på samma plats att ignoreras i framtiden.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Ge feedback för flera kontinuerliga punkter 

Om du vill ge avvikande feedback för flera kontinuerliga punkter samtidigt, markerar du den grupp av punkter som du vill kommentera. Du ser det valda tidsintervallet som fylls i automatiskt när du ger avvikande feedback.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Avvikande feedback-menyn med avvikelse markerat och ett särskilt tidsintervall":::

Om du vill se om en enskild punkt påverkas av din avvikande feedback, när du bläddrar i en tids serie, väljer du en enda punkt. Om resultatet av avvikelse identifieringen har ändrats via feedback visas knapp beskrivningen **påverkad av feedback: sant**. Om det visar sig **påverka feedback: falskt** innebär det att en avvikelse beräkning utfördes för den här punkten, men resultatet av avvikelse identifieringen bör inte ändras.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Tooltip visas med orden: påverkas av feedback: sant, markerat i rött":::

Det finns vissa situationer där vi inte föreslår feedback:

- Avvikelsen orsakas av en helgdag. Vi rekommenderar att du använder en förinställd händelse för att lösa den här typen av falsk larm, eftersom det blir mer exakt.
- Avvikelsen orsakas av en känd ändring av data källan. Till exempel inträffade en överordnad system ändring vid denna tidpunkt. I den här situationen förväntas vi ge en avvikelse varning eftersom systemet inte vet vad som orsakade ändringen och när liknande värde ändringar kommer att ske igen. Därför rekommenderar vi inte att du kommenterar den här typen av problem som `NotAnomaly` .

## <a name="change-points"></a>Ändra punkter

Ibland påverkar trend ändringar i data avvikelse identifierings resultat. När ett beslut fattas om en punkt är avvikande eller inte, kommer det senaste fönstret med historik data att beaktas. När din tids serie har en trend förändring kan du markera den exakta ändrings punkten, så att den hjälper vår avvikelse detektor i framtida analys.

Som bilden nedan visar kan du välja `ChangePoint` för feedback-typen och välja `ChangePoint` , `NotChangePoint` eller `AutoDetect` från List rutan.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menyn ändra punkt med list rutor som innehåller alternativ för ChangePoint, NotChangePoint och identifiera automatiskt":::

> [!NOTE]
> Om dina data ändras behöver du bara markera en punkt som en `ChangePoint` , så om du har markerat en `timerange` , fyller vi den sista punktens tidsstämpel och tid automatiskt. I det här fallet påverkar anteckningen bara avvikelse identifierings resultat efter 12 punkter.

## <a name="seasonality"></a>Säsongsberoende

Vid säsongs data är ett steg att uppskatta perioden (säsongs beroende) för tids serien när vi utför avvikelse identifiering och tillämpa den på fasen för avvikelse identifiering. Ibland är det svårt att identifiera en exakt period och perioden kan också ändras. En felaktigt definierad period kan ha sido effekter på dina avvikelse identifierings resultat. Du kan hitta den aktuella perioden från en knapp beskrivning. namnet är `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Knapp beskrivnings överlägg med ord perioden och siffran sju markerat i rött.":::

Du kan ge feedback om perioden för att åtgärda den här typen av avvikelse identifierings fel. När bilden visas kan du ange ett period värde. Enheten `interval` innebär en kornig het. Noll intervaller innebär att data inte är säsongsbaserade. Du kan också välja `AutoDetect` om du vill avbryta tidigare feedback och låta pipelinen identifiera perioden automatiskt. 
 
> [!NOTE]
> När du anger period som du inte behöver tilldela en tidsstämpel eller timerange, kommer perioden att påverka framtida avvikelse identifieringar på hela timeseries från det ögonblick du ger feedback.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Meny med automatisk identifiering av period angiven till 28 och intervall inställt på noll som anger att det inte är säsongs.":::

## <a name="provide-comment-feedback"></a>Lämna kommentarer om kommentarer

Du kan också lägga till kommentarer för att kommentera och tillhandahålla sammanhang till dina data. Om du vill lägga till kommentarer väljer du ett tidsintervall och lägger till texten för din kommentar.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Meny med möjlighet att ange ett tidsintervall och en ruta för att lägga till en text-baserad kommentar":::

## <a name="time-series-batch-feedback"></a>Tid serie batch-feedback

Som tidigare har beskrivits kan du använda den modala återkopplingen för att välja eller ta bort dimensions värden för att få en batch över tids serier som definieras av ett dimensions filter. Du kan också öppna den här modala genom att klicka på knappen "+" för feedback i den vänstra panelen och välja dimensioner och dimensions värden.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Meny med ett blått plus tecken markerat i rött bredvid Word-feedback":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Menyn Lägg till feedback med två dimensioner som anges av Dim1 och Dim2":::

## <a name="how-to-view-feedback-history"></a>Visa feedback historik

Det finns två sätt att Visa feedback historik. Du kan välja knappen feedback historik i den vänstra panelen och en lista över återkopplings listor visas. Den visar en lista över alla synpunkter som du har gett innan du för enskilda serier eller dimensions filter.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menyn feedback-lista '":::

Ett annat sätt att Visa feedback historiken är från en serie. Flera knappar visas i det övre högra hörnet i varje serie. Välj knappen Visa feedback så växlar linjen från att Visa avvikelse punkter för att Visa feedback-poster. Den gröna flaggan representerar en ändrings punkt och de blå punkterna är andra återkopplings punkter. Du kan också välja dem och få en lista över återkopplings listor som visar information om den feedback som anges för den här punkten.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Diagram över feedback-historik":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menyn feedback-lista med två dimensioner":::

> [!NOTE]
> Alla som har åtkomst till måttet tillåts ge feedback, så att du kan se feedback från andra datafeed-ägare. Om du redigerar samma punkt som någon annan kommer din feedback skriva över den tidigare feedback-posten.       

## <a name="next-steps"></a>Nästa steg
- [Diagnostisera en incident](diagnose-incident.md).
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
- [Konfigurera aviseringar och få meddelanden via en hook](../how-tos/alerts.md)