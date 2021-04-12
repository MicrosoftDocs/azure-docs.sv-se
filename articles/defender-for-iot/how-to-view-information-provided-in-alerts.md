---
title: Om aviserings meddelanden
description: Välj en avisering från fönstret aviseringar för att granska informationen.
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781491"
---
# <a name="about-alert-messages"></a>Om aviserings meddelanden

Välj en avisering från fönstret **aviseringar** för att granska aviserings informationen. Informationen innehåller följande information:

- Aviserings-metadata

- Information om trafik, enheter och händelsen

- Länkar till anslutna enheter i enhets kartan

- Kommentarer som definierats av säkerhetsanalytiker och administratörer

- Rekommendationer för att undersöka evenemanget

## <a name="alert-metadata"></a>Aviserings-metadata

Aviserings informationen innehåller följande metadata för avisering:

  - Aviserings-ID

  - Princip motor som utlöste aviseringen

  - Datum och tid då aviseringen utlöstes

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Obehörig Internet anslutning har identifierats.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Information om enheter, trafik och händelsen

Aviserings meddelandet innehåller information om:

  - Identifierade enheter.

  - Trafiken identifieras mellan enheterna, till exempel protokoll och funktions koder.

  - Insikter om händelsens följder.

Du kan använda den här informationen när du bestämmer dig för att hantera aviserings händelsen.

## <a name="links-to-connected-devices-in-the-device-map"></a>Länkar till anslutna enheter i enhets kartan

Om du vill veta mer om enheter som är anslutna till de identifierade enheterna kan du välja en enhets avbildning i aviseringen och Visa anslutna enheter på kartan.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP-åtgärden misslyckades.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Skärm bild av enheter.":::

Kartan filtrerar till den enhet som du har valt och andra enheter som är anslutna till den. Kartan visar också dialog rutan **snabb egenskaper** för enheterna som identifierats i aviseringarna.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Kommentarer som definierats av säkerhetsanalytiker och administratörer 

Aviseringar kan innehålla en lista över fördefinierade kommentarer. Till exempel kan kommentarer vara instruktioner för att undvika åtgärder som ska vidtas eller namn på personer som ska kontaktas om evenemanget.

När du hanterar en varnings händelse kan du välja den eller de kommentarer som bäst motsvarar händelse statusen eller de steg som du har vidtagit för att undersöka aviseringen.

De valda kommentarerna sparas i aviserings meddelandet. Om du arbetar med kommentarer förbättras kommunikationen mellan personer och grupper under undersökningen av en varnings händelse. Det innebär att kommentarer kan påskynda incident svars tiden.

En administratör eller säkerhetsanalytiker fördefinierar kommentarer. De valda kommentarerna vidarebefordras inte till de partner system som definieras i reglerna för vidarebefordran.

När du har granskat informationen i en avisering kan du utföra olika kriminal tekniska-steg som hjälper dig att hantera aviserings händelsen. Exempel:

- Analysera den senaste enhets aktiviteten (data utvinnings rapport). 

- Analysera andra händelser som inträffade samtidigt (tids linje för händelse). 

- Analysera omfattande händelse trafik (PCAP-fil).

## <a name="pcap-files"></a>PCAP-filer

I vissa fall kan du komma åt en PCAP-fil från aviserings meddelandet. Detta kan vara användbart om du vill ha mer detaljerad information om den associerade nätverks trafiken.

Om du vill ladda ned en PCAP-fil väljer du :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Hämta ikon."::: längst upp till höger i dialog rutan **aviserings information** .

## <a name="recommendations-for-investigating-an-event"></a>Rekommendationer för att undersöka en händelse 

I **rekommendations** delen av en avisering visas information som kan hjälpa dig att förstå en händelse bättre. Granska den här informationen innan du hanterar aviserings händelsen eller vidtar åtgärder på enheten eller nätverket.

## <a name="see-also"></a>Se även

[Påskynda aviserings arbets flöden](how-to-accelerate-alert-incident-response.md)

[Hantera aviseringshändelsen](how-to-manage-the-alert-event.md)
