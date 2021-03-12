---
title: Visa och konfigurera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion)
description: Den här artikeln förklarar hur du visar och konfigurerar Azure IoT Connector för FHIR (för hands version) mått.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020631"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Visa och konfigurera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion) 

I den här artikeln får du lära dig hur du visar och konfigurerar Azure IoT Connector för snabba hälso-och sjukvårds resurser (FHIR&#174;) * mått.

> [!TIP]
> Om du vill lära dig hur du ställer in export av mått data följer du rikt linjerna i [Exportera Azure IoT Connector för FHIR (för hands version) mått via diagnostikinställningar](iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Visa mått för Azure IoT Connector för FHIR (för hands version)

1. Logga in på Azure Portal och välj sedan din Azure API for FHIR-tjänst. 

2. Välj **mått** i det vänstra fönstret. 

3. Välj fliken **IoT-koppling** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Skärm bild av fönstret &quot;IoT Connector&quot;, med linje diagram som visar antalet inkommande och normaliserade meddelanden." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Välj en IoT-koppling för att visa dess mått. Det finns till exempel fyra IoT-kopplingar (*anslutnings 1*, *koppling 2* och så vidare) som är kopplade till den här Azure API för FHIR-tjänsten.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Skärm bild av fönstret &quot;IoT Connector&quot;, som visar IoT Connector-flikarna 1, 2, 3 och 4." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Välj tids period (till exempel **1 timme**, **24 timmar**, **7 dagar** eller **anpassad**) för de IoT-kopplings mått som du vill visa. Genom att välja fliken **anpassad** kan du skapa angivna tids-och datum kombinationer för att Visa IoT Connector-mått.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Skärm bild av fönstret &quot;IoT Connector&quot; som visar ett linje diagram för tids perioder på 1 timme för koppling 1." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Mått typer för Azure IoT Connector för FHIR (för hands version) 

> [!TIP]
> Om du vill lära dig mer om data flödet i Azure IoT Connector för FHIR kan du läsa mer om fel meddelanden och korrigeringar i Azure IoT Connector för [FHIR (för hands version) data flöde](iot-data-flow.md) och [Azure IoT Connector för FHIR (för hands version)](iot-troubleshoot-guide.md) .

De IoT-kopplings mått som du kan visa visas i följande tabell:

|Mått typ|Mått syfte| 
|-----------|--------------|
|Antal inkommande meddelanden|Visar antalet mottagna rå inkommande meddelanden (till exempel enhets händelser).|
|Antal normaliserade meddelanden|Visar antalet normaliserade meddelanden.|
|Antal meddelande grupper|Visar antalet grupper som har sammanställt meddelanden i den angivna tids perioden.|
|Genomsnittlig normaliserad fas fördröjning|Visar genomsnitts fördröjningen för den normaliserade fasen. Den normaliserade fasen utför normalisering på rå inkommande meddelanden.|
|Genomsnittlig svars tid för grupp fas|Visar den genomsnittliga svars tiden för grupp stadiet. Grupp fasen utför buffring, agg regering och gruppering av normaliserade meddelanden.| 
|Totalt antal fel|Visar det totala antalet fel.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Fokusera på och konfigurera Azure IoT Connector för FHIR (för hands version) mått

I det här exemplet ska vi fokusera på **antalet inkommande meddelande** mått.

1. Välj en tidpunkt som du vill fokusera på.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Skärm bild av mått fönstret &quot;antal inkommande meddelanden&quot;, som markerar en enskild tidpunkt i linje diagrammet." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. I fönstret **antal inkommande meddelanden** kan du ytterligare anpassa måttet genom att välja **Lägg till mått**, **Lägg till filter** eller **tillämpa delning**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Skärm bild av fönstret &quot;antal inkommande meddelanden&quot;, som markerar knapparna Lägg till mått, Lägg till filter och Använd delning." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Slutsats 
Att ha åtkomst till data Plans mått är viktigt för övervakning och fel sökning. Azure IoT Connector för FHIR hjälper dig med dessa åtgärder via mått. 

## <a name="next-steps"></a>Nästa steg

Få svar på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7. 
