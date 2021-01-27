---
title: Förbättra port- och VLAN-namnmatchning
description: Anpassa port-och VLAN-namn på sensorer för att utöka enhets matchningen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c976671bccb420ae24d8def7a6574098d86ce6d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98803582"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>Förbättra port- och VLAN-namnmatchning

Du kan anpassa port-och VLAN-namn på sensorer för att utöka enhets matchningen.

## <a name="customize-port-names"></a>Anpassa port namn

Azure Defender for IoT tilldelar automatiskt namn till de flesta universellt reserverade portar, till exempel DHCP eller HTTP. Du kan anpassa port namn för andra portar som Defender för IoT identifierar. Tilldela till exempel ett namn till en icke-reserverad port eftersom porten visar ovanligt hög aktivitet.

Dessa namn visas när:

  - Du väljer **enhets grupper** från enhets kartan.

  - Du skapar widgetar som tillhandahåller portinformation.

### <a name="view-custom-port-names-in-the-device-map"></a>Visa anpassade port namn i enhets kartan

Portar som innehåller ett namn som definieras av användarna visas i enhets kartan i gruppen **kända program** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Skärm bild av enhets kartan som visar den kända program gruppen.":::

### <a name="view-custom-port-names-in-widgets"></a>Visa anpassade port namn i widgetar

Port namn som du har definierat visas i widgetarna som avser trafik efter port.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Behandlar trafik.":::

Definiera anpassade port namn:

1. Välj **Systeminställningar** och välj sedan **standard-alias**.

2. Välj **Lägg till port Ali Aset**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Lägg till port Ali Aset.":::

3. Ange port numret, Välj **TCP/UDP** eller Välj **både** och Lägg till namnet.

4. Välj **Spara**.

## <a name="configure-vlan-names"></a>Konfigurera VLAN-namn

Du kan utöka enhets inventerings data med enhets-VLAN-nummer och-taggar. Förutom data berikning kan du Visa antalet enheter per VLAN och Visa bandbredd per VLAN-widgetar.

Stöd för VLAN baseras på 802.1 q (upp till VLAN-ID 4094).

Det finns två metoder för att hämta VLAN-information:

- **Automatiskt identifierad**: sensorn identifierar automatiskt VLAN som standard. VLAN som identifieras med trafik visas på skärmen VLAN-konfiguration, i rapporter om Data utvinning och i andra rapporter som innehåller VLAN-information. Oanvända VLAN visas inte. Du kan inte redigera eller ta bort dessa VLAN. 

  Du bör lägga till ett unikt namn för varje VLAN. Om du inte lägger till ett namn visas VLAN-numret på alla platser där VLAN rapporteras.

- **Manuellt tillagd**: du kan lägga till virtuella lokala nätverk manuellt. Du måste lägga till ett unikt namn för varje VLAN som har lagts till manuellt och du kan redigera eller ta bort dessa VLAN.

VLAN-namn kan innehålla upp till 50 ASCII-tecken.

> [!NOTE]
> VLAN-namn synkroniseras inte mellan sensorn och hanterings konsolen. Du måste även definiera namnet på hanterings konsolen.  
För Cisco-växlar lägger du till följande rad i span-konfigurationen: `monitor session 1 destination interface XX/XX encapsulation dot1q` . I det kommandot är *XX/XX* namnet och numret på porten.

Konfigurera virtuella lokala nätverk:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **Systeminställningar** väljer du **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Använd systeminställningarna för att redigera dina VLAN.":::

3. Lägg till ett unikt namn bredvid varje VLAN-ID.

## <a name="next-steps"></a>Nästa steg

Visa utförlig enhets information i olika rapporter:

- [Undersök identifieringar av sensorer i en enhetsinventering](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Sensor trender och statistik rapporter](how-to-create-trends-and-statistics-reports.md)
- [Frågor om sensor Data utvinning](how-to-create-data-mining-queries.md)
