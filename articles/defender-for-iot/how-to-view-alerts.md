---
title: Visa aviseringar
description: Visa aviseringar enligt olika kategorier och Använd Sök funktioner för att få hjälp att hitta aviseringar om intresse.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bce83e11a0d4567d37e78388445c108385ca9a61
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509085"
---
# <a name="view-alerts"></a>Visa aviseringar

Den här artikeln beskriver hur du visar aviseringar som har utlösts av sensorn och hanterar dem med aviserings verktyg.

Du kan visa aviseringar baserat på olika kategorier, till exempel aviseringar som har arkiverats eller fästs. Du kan också söka efter aviseringar av intresse, till exempel aviseringar baserat på en IP-eller MAC-adress.  

Du kan också visa aviseringar från sensorns instrument panel.

För att visa aviseringar:

- Välj **aviseringar** på menyn på sidan. **Aviserings fönstret visar** de aviseringar som sensorn har identifierat.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Vy över aviserings skärmen.":::

## <a name="view-alerts-by-category"></a>Visa aviseringar per kategori

Du kan visa aviseringar efter olika kategorier från huvudvyerna **aviseringar** . Välj en avisering för att granska informationen och hantera händelsen.

| Sortera efter typ | Description |
|--|--|
| **Viktiga aviseringar** | Aviseringar sorterade efter prioritet. |
| **Fästa aviseringar** | Aviseringar som användaren har fäst för ytterligare undersökning. Fästa aviseringar arkiveras inte och lagras i 14 dagar i den fästa mappen. |
| **Senaste aviseringar** | Aviseringar sorterade efter tid. |
| **Accepterade aviseringar** | Aviseringar som har godkänts och inte hanterats, eller som har stängts av och stängts av. |
| **Arkiverade aviseringar** | Varnar att systemet har arkiverats automatiskt. Endast administratörs användaren kan komma åt dem. |

## <a name="search-for-alerts-of-interest"></a>Sök efter aviseringar av intresse

Huvud vyn **aviseringar** innehåller olika Sök funktioner som hjälper dig att hitta aviseringar om intresse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Skärm bild för varningar inlärning.":::

### <a name="text-search"></a>Texts ökning 

Använd alternativet **kostnads fri sökning** för att söka efter aviseringar efter text, siffror eller tecken.

Att söka:

- Skriv den text som krävs i det **kostnads fria Sök** fältet och tryck på RETUR på tangent bordet.

Rensa sökningen:

- Ta bort texten i det **kostnads fria Sök** fältet och tryck på RETUR på tangent bordet.

### <a name="device-group-or-device-ip-address-search"></a>Sök efter enhets grupp eller enhets-IP-adress

Sök efter aviseringar som refererar till vissa IP-adresser eller enhets grupper. Enhets grupper skapas i enhets kartan.

Använda avancerade filter:

1. Välj **Avancerade filter** från huvud vyn **aviseringar** .

2. Välj en enhets grupp eller en enhet.

3. Välj **Bekräfta**.

4. Om du vill rensa sökningen väljer du **Rensa alla**.

### <a name="security-versus-operational-alert-search"></a>Säkerhet jämfört med drifts aviserings sökning

Växla mellan att Visa drift-och säkerhets aviseringar:

- **Säkerhets** aviseringar representerar potentiell skadlig kod, nätverks avvikelser, princip överträdelser och protokoll överträdelser.

- **Drift** aviseringar representerar nätverks avvikelser, princip överträdelser och protokoll överträdelser.

När inget av alternativen är markerat visas alla aviseringar.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Säkerhet på skärmen aviseringar.":::

## <a name="alert-page-options"></a>Alternativ för aviserings sida

Aviserings meddelanden innehåller följande åtgärder:

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: att bekräfta en avisering.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: om du vill bekräfta en avisering.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: om du vill fästa en avisering.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: om du vill ta bort en avisering.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: att bekräfta alla aviseringar.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: om du vill lära dig och bekräfta alla aviseringar.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: att exportera aviserings listan till en CSV-fil och välj alternativet Exportera. Välj **Exportera avisering** för det vanliga alternativet Exportera till CSV. Eller Välj **utökad varnings export** för möjligheten att lägga till separata rader för ytterligare information om en AVISERING i CSV-filen.

## <a name="alert-pop-up-window-options"></a>Alternativ för popup-fönstret avisering

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ikonen för att ladda ned en varnings rapport som en PDF-fil.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ikonen för att ladda ned pcap-filen. Filen är synlig med wireshark, det kostnads fria Network Protocol Analyzer.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: det här alternativet om du vill ladda ned en filtrerad pcap-fil som bara innehåller relevanta aviserings paket, vilket minskar storleken på utdatafilen och ger en mer fokuserad Du kan visa filen genom att använda wireshark.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ikonen för att visa aviseringen i händelse tids linjen.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ikonen för att fästa aviseringen.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ikonen för att ta bort aviseringen.

- Välj :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: att godkänna trafiken (endast säkerhetsanalytiker och administratörer).

- Välj en enhet för att visa den i enhets kartan.

## <a name="next-steps"></a>Nästa steg

[Hantera aviseringshändelsen](how-to-manage-the-alert-event.md)

[Påskynda aviserings arbets flöden](how-to-accelerate-alert-incident-response.md)
