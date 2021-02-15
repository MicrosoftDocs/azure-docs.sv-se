---
title: Spåra sensoraktivitet
description: Händelsens tids linje visar en tids linje för aktivitet som identifierats i nätverket, inklusive aviseringar och aviserings hanterings åtgärder, nätverks händelser och användar åtgärder som användar inloggning och borttagning av användare.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: b8a7679521096cf3d98e8f55748eb0398fdc4ab8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523608"
---
# <a name="track-sensor-activity"></a>Spåra sensoraktivitet

## <a name="event-timeline"></a>Händelse tids linje

Händelsens tids linje visar en tids linje för aktivitet som sensorn har identifierat. Exempel:

  - Aviseringar och aviserings hanterings åtgärder

  - Nätverks händelser

  - Användar åtgärder som användar inloggning och användar borttagning

Händelsens tids linje innehåller en kronologisk vy över händelser som hände i nätverket. Händelse tids linjen gör det möjligt att förstå och analysera händelse kedjan som föregås och följde en attack eller incident, som hjälper dig i undersökningen och data utredning.

> [!NOTE]
> *Administratörer* och *säkerhets analyser* kan utföra de procedurer som beskrivs i det här avsnittet.

Så här visar du händelse loggarna:

- Välj **händelse tids linje** på menyn på sidan.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Visa dina händelser på händelsens tids linje.":::

Förutom att visa de händelser som sensorn har identifierat kan du manuellt lägga till händelser i tids linjen. Den här processen är användbar om händelsen inträffade i ett externt system men påverkar nätverket, och det är viktigt att registrera händelsen och presentera den som en del av tids linjen.

Så här lägger du till händelser manuellt:

- Välj **Skapa händelse**.

Så här exporterar du händelse logg information till en CSV-fil:

- Välj **Exportera**.

## <a name="filter-the-event-timeline"></a>Filtrera händelsens tids linje

Filtrera tids linjen för att visa enheter och händelser som intresserar dig.

Så här filtrerar du tids linjen:

1. Välj **Avancerade filter**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Använd fönstret avancerade filter för händelser för att filtrera dina händelser.":::

2. Ange händelse filter enligt följande:

   - **Ta med adress**: Visa händelser för vissa enheter.

   - **Undanta adress**: Dölj händelser för vissa enheter.

   - **Inkludera händelse typer**: Visa vissa händelse typer.

   - **Exkludera händelse typer**: Dölj vissa händelse typer.

   - **Enhets grupp**: Välj en enhets grupp, som den definierades i enhets kartan. Endast händelser från den här gruppen visas.

3. Välj **Rensa alla** om du vill rensa alla markerade filter.

4. Sök **enbart efter aviseringar**, **aviseringar och meddelanden**, eller **alla händelser**.

5. Välj **Välj datum** om du vill välja ett visst datum. Välj dag, timme och minut. Händelser från den valda tids ramen visas.

6.  Välj **användar åtgärder** för att inkludera eller exkludera användar åtgärds händelser.

7.  Välj pilen (**V**) om du vill visa mer information om händelsen:

    - Välj de relaterade aviseringarna (om det finns några) om du vill visa en detaljerad beskrivning av aviseringen.

    - Välj enheten för att visa enheten på kartan.

    - Välj **Filtrera händelser efter relaterade enheter** om du vill filtrera efter relaterade enheter.

    - Välj **pcap-fil** för att ladda ned pcap-filen (om den finns) som innehåller en paket fångst av hela nätverket vid en angiven tidpunkt. 
    
      PCAP-filen innehåller teknisk information som kan hjälpa nätverks ingenjörer att fastställa de exakta parametrarna för händelsen. Du kan analysera PCAP-filen med en analys av nätverks protokoll som Wireshark, ett program med öppen källkod.

## <a name="next-steps"></a>Nästa steg

[Visa aviseringar](how-to-view-alerts.md)
