---
title: Arbeta med aviseringar på sensorn
description: Arbeta med aviseringar så att du kan förbättra säkerheten och driften i nätverket.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523557"
---
# <a name="work-with-alerts-on-your-sensor"></a>Arbeta med aviseringar på sensorn

Arbeta med aviseringar så att du kan förbättra säkerheten och driften i nätverket. Aviseringar ger information om:

- Avvikelser från tillåten nätverks aktivitet

- Protokoll och drift avvikelser

- Misstänkt skadlig kod

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Identifiera adress genomsökning.":::

Med alternativ för aviserings hantering kan användarna:

- Instruera sensorer att lära sig att identifiera aktiviteter som auktoriserad trafik.

- Bekräfta att du har granskat aviseringen.

- Instruera sensorer att stänga av händelser som identifierats med identiska enheter och jämförbar trafik.

Det finns ytterligare verktyg som hjälper dig att förbättra och påskynda aviserings undersökningen. Exempel:

  - Lägg till instruktions kommentarer för aviserings granskare.

  - Skapa aviserings grupper för visning i SOC-lösningar. 

  - Sök efter vissa aviseringar. granska relaterade PCAP-filer; Visa identifierade enheter och andra anslutna enheter i enhets kartan eller skicka aviserings information till partner system.

  - Vidarebefordra aviseringar till partner leverantörer: SIEM-system, MSSP-system och mycket annat.

## <a name="alerts-and-engines"></a>Aviseringar och motorer

Aviseringar utlöses när sensor motorer identifierar förändringar i nätverks trafik och beteende som kräver din uppmärksamhet. Den här artikeln beskriver vilken typ av aviseringar som varje motor utlöser.

| Aviseringstyp | Beskrivning |
|-|-|
| Aviseringar om princip överträdelser | Utlöses när motorn för policy överträdelse identifierar en avvikelse från trafik som tidigare har lärts. Exempel: <br /> -En ny enhet har identifierats.  <br /> – En ny konfiguration identifieras på en enhet. <br /> – En enhet som inte har definierats som en programmerings enhet utför en program ändring. <br /> -En version av inbyggd program vara har ändrats. |
| Varningar om protokoll fel | Utlöses när motorn för protokoll överträdelse identifierar paket strukturer eller fält värden som inte uppfyller protokoll specifikationen. | 
| Drift aviseringar | Utlöses när drift motorn identifierar problem med nätverks drift eller en enhet som inte fungerar. Till exempel stoppades en nätverks enhet genom ett Stop PLC-kommando, eller ett gränssnitt på en sensor som slutade övervaka trafiken. |
| Varningar om skadlig kod | Utlöses när motorn för skadlig kod identifierar skadlig nätverks aktivitet. Till exempel identifierar-motorn ett känt angrepp som Conficker. |
| Avvikelse aviseringar | Utlöses när avvikelse motorn identifierar en avvikelse. En enhet utför exempelvis nätverks genomsökningar men är inte definierad som en skannings enhet. |

Det finns verktyg för att aktivera och inaktivera sensor motorer. Aviseringar utlöses inte från motorer som är inaktiverade. Se [kontrol lera vilken trafik som övervakas](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Aviseringar och sensor rapportering

Aktivitet som återges i aviseringar kan beräknas när du genererar data utvinning, riskbedömning och angrepps vektor rapporter. När du hanterar dessa händelser uppdaterar sensorn rapporterna enligt detta.

Exempel:

  - Obehörig anslutning mellan en enhet i ett definierat undernät och enheter som finns utanför under nätet (offentlig) visas i rapporten Data utvinning *Internet aktivitet* och avsnittet *Internet-anslutningar* för riskbedömning. När enheterna har auktoriserats (lärt sig) beräknas de när de genererar dessa rapporter.

  - Händelser för skadlig kod som identifierats på nätverks enheter rapporteras i riskbedömnings rapporter. När aviseringar om händelser för skadlig kod är *avstängda*, kommer berörda enheter inte att beräknas i rapporten om riskbedömning.

## <a name="see-also"></a>Se även

- [Lägen för inlärning och smart IT-utbildning](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [Visa informationen som tillhandahålls i aviseringar](how-to-view-information-provided-in-alerts.md)
- [Hantera aviseringshändelsen](how-to-manage-the-alert-event.md)
- [Påskynda aviserings arbets flöden](how-to-accelerate-alert-incident-response.md)
