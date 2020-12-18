---
title: Visa trafik data på Android-kartor | Microsoft Azure Maps
description: I den här artikeln lär du dig hur du visar trafik data på en karta med hjälp av Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680430"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Visa trafik data på kartan (Android SDK)

Flödes data och incident data är de två typer av trafik data som kan visas på kartan. Den här guiden visar hur du visar båda typerna av trafik data. Incident data består av punkt-och linjebaserade data för sådant som konstruktioner, väg stängningar och olyckor. Flow-data visar mått för trafik flödet på resan.

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Det finns två typer av trafik data tillgängliga i Azure Maps:

- Incident data – består av punkt-och linjebaserade data för sådant som konstruktion, väg stängningar och olyckor.
- Flow-data – innehåller mått för trafik flödet på vägarna. Ofta används trafikflödes data för att färga vägarna. Färgerna baseras på hur mycket trafik som saktar ned flödet, i förhållande till hastighets gränsen eller något annat mått. Det finns fyra värden som kan överföras till `flow` kartans trafik alternativ.

    |Flödes värde | Beskrivning|
    | :-- | :-- |
    | TrafficFlow. NONE | Visar inte trafik data på kartan |
    | TrafficFlow. relativ | Visar trafik data som är relativa till vägens fria flödes hastighet |
    | TrafficFlow.RELATIVE_DELAY | Visar områden som är långsammare än det genomsnittliga förväntad fördröjning |
    | TrafficFlow. absolut | Visar den absoluta hastigheten för alla fordon på väg |

Följande kod visar hur du visar trafik data på kartan.

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

Följande skärm bild visar ovanstående kod Rending information om real tids trafik på kartan.

![Karta som visar trafik information i real tid](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Hämta incident information för trafik

Information om en trafik incident är tillgänglig i egenskaperna för den funktion som används för att Visa incidenten på kartan. Trafik incidenter läggs till i kartan med hjälp av tjänsten Azure Maps Traffic incident Vector panel. Formatet på data i dessa vektor paneler om det [dokumenteras här](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Följande kod lägger till en klick händelse till kartan och hämtar den trafik incident funktion som klickade och visar ett popup-meddelande med några av detaljerna.

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

Följande skärm bild visar ovanstående kod Rending information om real tids trafik på kartan med ett popup-meddelande som visar incident information.

![Karta som visar trafik information i real tid med ett popup-meddelande som visar incident information](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Nästa steg

Se följande guider för att lära dig hur du lägger till mer data i kartan:

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](how-to-add-tile-layer-android-map.md)
