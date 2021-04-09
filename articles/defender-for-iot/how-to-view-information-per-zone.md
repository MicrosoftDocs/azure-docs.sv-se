---
title: Läs mer om enheter i vissa zoner
description: Använd den lokala hanterings konsolen för att få en omfattande visnings information per viss zon
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776335"
---
# <a name="view-information-per-zone"></a>Visa information per zon


## <a name="view-a-device-map-for-a-zone"></a>Visa en enhets karta för en zon

Visa en enhets karta för en vald zon på en sensor. I den här vyn visas alla nätverks element som är relaterade till den valda zonen, inklusive sensorer, enheter som är anslutna till dem och annan information.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Skärm bild av zon kartan.":::


- I fönstret **plats hantering** väljer du **Visa zon karta** från fältet som innehåller zonens namn.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Standard region till standard affär senhet.":::

Fönstret **enhets karta** visas. Följande verktyg är tillgängliga för visning av enheter och enhets information från kartan. Mer information om dessa funktioner finns i *användar handboken för Defender för IoT Platform*.

- **Kart zoomnings** lägena: förenklad vy, vyn anslutningar och detaljerad vy. Den visade kart visningen varierar beroende på kartans zoomnings nivå. Du växlar mellan kart visning genom att justera zoomnings nivåerna.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Kart söknings-och layoutverktyg**: verktyg som används för att Visa varierande nätverks segment, enheter, enhets grupper eller lager.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Skärm bild av vyn sökverktyg och layoutverktyg.":::

- **Etiketter och indikatorer på enheter:** Till exempel antalet enheter som grupperas i ett undernät i ett IT-nätverk. I det här exemplet är det 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Skärm bild av etiketter och indikatorer.":::

- **Visa egenskaper för enhet**: till exempel sensorn som övervakar enhetens och grundläggande enhets egenskaper. Högerklicka på enheten för att Visa enhets egenskaperna.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Skärm bild av vyn över enhets egenskaper.":::

- **Avisering som är associerad med en enhet:** Högerklicka på enheten för att visa relaterade aviseringar.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Skärm bild av vyn Visa aviseringar.":::

## <a name="view-alerts-associated-with-a-zone"></a>Visa aviseringar som är associerade med en zon

Visa aviseringar som är associerade med en speciell zon:

- Välj aviserings ikonen formulär fönstret **zon** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Standard vyn för affär senheten med exempel.":::

Mer information finns i [Översikt: arbeta med aviseringar](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Visa enhets inventeringen för en zon

Så här visar du enhets inventeringen som är associerad med en speciell zon:

- Välj **Visa enhets inventering** i fönstret **zon** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Skärmen enhets inventering visas.":::

Mer information finns i [undersöka alla identifieringar av företags sensor i en enhets inventering](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Visa ytterligare zon information

Följande ytterligare zon information är tillgänglig:

- **Zon information**: Visa antalet enheter, aviseringar och sensorer som är kopplade till zonen.

- **Sensor information**: Visa namn, IP-adress och version för varje sensor som har tilldelats zonen.

- **Anslutnings status**: om en sensor är frånkopplad ansluter du från sensorn. Se [ansluta sensorer till den lokala hanterings konsolen](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Uppdaterings förlopp**: om den anslutna sensorn håller på att uppgraderas visas uppgraderings status. Under uppgraderingen tar inte den lokala hanterings konsolen emot enhets information från sensorn.

## <a name="next-steps"></a>Nästa steg

[Få insikter om globala, regionala och lokala hot](how-to-gain-insight-into-global-regional-and-local-threats.md)
