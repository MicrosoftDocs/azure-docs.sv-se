---
title: Hantera aviserings händelser
description: Hantera aviserings händelser som identifierats i nätverket.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 866ff4cad74b7092dda11a20f8f7bcadf91233ae
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526909"
---
# <a name="manage-alert-events"></a>Hantera aviserings händelser

Följande alternativ är tillgängliga för hantering av aviserings händelser:

 | Åtgärd | Beskrivning |
 |--|--|
 | **Learn** | Auktorisera den identifierade händelsen. Mer information finns i [om att lära och ta reda på händelser](#about-learning-and-unlearning-events). |
 | **Bekräfta** | Dölj aviseringen en gång för den identifierade händelsen. Aviseringen utlöses igen om händelsen upptäcks igen. Mer information finns i avsnittet [om att bekräfta och bekräfta händelser](#about-acknowledging-and-unacknowledging-events). |
 | **Mikrofon** | Ignorera kontinuerlig aktivitet med identiska enheter och jämförbar trafik. Mer information finns i om att stänga av och stänga av [händelser](#about-muting-and-unmuting-events). |
 
Du kan också exportera aviserings information.
## <a name="about-learning-and-unlearning-events"></a>Om att lära och ta reda på händelser

Händelser som indikerar att avvikelser i det inlärda nätverket kan avspegla giltiga nätverks ändringar. Exempel kan innehålla en ny auktoriserad enhet som anslöt till nätverket eller en godkänd inbyggd program uppdatering.

När du väljer **lära** anser sensorn att trafik, konfigurationer eller aktivitet är giltig. Det innebär att aviseringar inte längre utlöses för händelsen. Det innebär också att händelsen inte kommer att beräknas när sensorn genererar riskbedömning, angrepps vektor och andra rapporter.

Du får till exempel en avisering som indikerar aktivitet för adress genomsökning på en enhet som nätverks skannern inte tidigare har definierat. Om den här enheten har lagts till i nätverket för genomsökning kan du instruera sensorn att lära enheten som en skannings enhet.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Adress identifierad söknings period.":::

Inlärda händelser kan vara inlärt. När sensorn tar reda på händelser återaktiverar den aviseringar som är relaterade till den här händelsen.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Om att bekräfta och bekräfta händelser

I vissa situationer kanske du inte vill att en sensor ska lära sig en identifierad händelse, eller så kanske alternativet inte är tillgängligt. Incidenten kan i stället kräva en minskning. Exempel:

- **Minimera en nätverks konfiguration eller enhet**: du får en avisering som anger att en ny enhet har identifierats i nätverket. När du undersöker upptäcker du att enheten är en oauktoriserad nätverks enhet. Du hanterar incidenten genom att koppla bort enheten från nätverket.
- **Uppdatera en sensor konfiguration**: du får en avisering som anger att en server har initierat ett högt antal fjärr anslutningar. Den här aviseringen utlöstes på grund av att sensor avvikelse trösklar definierades för att utlösa aviseringar över ett visst antal sessioner inom en minut. Du hanterar incidenten genom att uppdatera tröskelvärdena.

När du har gjort en minskning eller undersökning kan du instruera sensorn att dölja aviseringen genom att välja **Bekräfta**. Om händelsen identifieras igen återutlöss aviseringen.

Så här tar du bort aviseringen:

  - Välj **Bekräfta**.

Så här visar du aviseringen igen:

  - Få åtkomst till aviseringen och välj sedan **Bekräfta**.

Avbekräftelser aviseringar om ytterligare undersökning krävs.

## <a name="about-muting-and-unmuting-events"></a>Om avstängnings-och avstängnings händelser

Under vissa omständigheter kanske du vill instruera sensorn att ignorera ett visst scenario i nätverket. Exempel:

  - **Avvikelse** motorn utlöser en varning vid en ökning i bandbredd mellan två enheter, men insamling är giltig för dessa enheter.

  - **Protokoll överträdelsen** utlöser en avisering på en protokoll avvikelse som identifieras mellan två enheter, men avvikelsen är giltig mellan enheterna.

I dessa situationer är inlärning inte tillgängligt. När inlärningen inte kan utföras och du vill ignorera aviseringen och ta bort enheten när du beräknar risker och angrepps vektorer kan du stänga av varnings händelsen i stället.

> [!NOTE] 
> Du kan inte stänga av händelser där en Internet enhet definieras som källa eller mål.

### <a name="what-alert-activity-is-muted"></a>Vilken aviserings aktivitet är avstängd?

Ett inmutet scenario omfattar nätverks enheter och trafik som identifierats för en händelse. Aviserings rubriken beskriver den trafik som är avstängd.

Enheten eller enheterna som är avstängda visas som en avbildning i aviseringen. Om två enheter visas stängs den aktuella varnings trafiken mellan dem.

**Exempel 1**

När en händelse är avstängd, ignoreras det när den primära (källan) skickar den sekundära (målet) en ogiltig funktions kod som definieras av leverantören.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="En sekundär enhet togs emot.":::

**Exempel 2**

När en händelse är avstängd ignoreras den varje gång källan skickar ett HTTP-huvud med otillåtet innehåll, oavsett mål.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Skärm bild av ogiltigt innehåll i HTTP-huvudet.":::

**När en händelse är avstängd:**

- Aviseringen är tillgänglig i den **bekräftade** aviserings vyn tills den har slagits på.

- Åtgärden ljud av visas i **händelse tids linjen**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Händelsen upptäcktes och är avstängd.":::

- Sensorn beräknar om enheter när den genererar riskbedömning, angrepps vektor och andra rapporter. Om du till exempel avstängde en avisering som identifierade skadlig trafik på en enhet, kommer enheten inte att beräknas i rapporten om riskbedömning.

**För att stänga av och stänga av en avisering:**

- Välj ikonen **ljud** av på aviseringen.

**Visa avstängda aviseringar:**

1. Välj alternativet **bekräftat** som utgör huvud skärmen **aviseringar** .

2. Hovra över en avisering för att se om den är avstängd.  

## <a name="export-alert-information"></a>Exportera aviserings information

Exportera aviserings information till en. csv-fil. Du kan exportera information om alla aviseringar som identifierats eller exportera information baserat på den filtrerade vyn. Följande information exporteras:

- Käll adress
- Mål adress
- Aviseringsrubrik
- Allvarlighetsgrad för avisering
- Aviserings meddelande
- Ytterligare information
- Bekräftad status
- PCAP tillgänglighet

Att exportera:

1. Välj aviseringar på menyn på sidan.
1. Välj Exportera.
1. Välj Exportera utökade aviseringar för att exportera aviserings information i separata rader för varje avisering som täcker flera enheter. När du väljer Exportera utökade aviseringar, skapar CSV-filen en dubblett av aviserings händelsen med de unika objekten på varje rad. Med det här alternativet blir det enklare att undersöka exporterade aviserings händelser.

## <a name="see-also"></a>Se även

[Styr vilken trafik som övervakas](how-to-control-what-traffic-is-monitored.md)
