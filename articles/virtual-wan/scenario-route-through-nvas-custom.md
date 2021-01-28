---
title: Dirigera trafik via NVA med anpassade inställningar
titleSuffix: Azure Virtual WAN
description: Det här scenariot hjälper dig att dirigera trafik via NVA med hjälp av en annan NVA för Internet-baserad trafik.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938576"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scenario: dirigera trafik genom NVA med anpassade inställningar

När du arbetar med Azure Virtual WAN-routning för virtuella WAN-nätverk har du ett antal alternativ som är tillgängliga för dig. Fokus för den här artikeln är när du vill dirigera trafik via en virtuell nätverks installation (NVA) för kommunikation mellan virtuella nätverk och grenar och använda en annan NVA för Internet-baserad trafik. Mer information finns i [om routning av virtuell hubb](about-virtual-hub-routing.md).

## <a name="design"></a>Design

* **Ekrar** för virtuella nätverk som är anslutna till den virtuella hubben. (Till exempel VNet 1, VNet 2 och VNet 3 i diagrammet senare i den här artikeln.)
* **Service VNet** för virtuella nätverk där användare har distribuerat en NVA för att inspektera trafik som inte är Internet och eventuellt med gemensamma tjänster som används av ekrar. (Till exempel VNet 4 i diagrammet längre fram i den här artikeln.) 
* **Perimeter-VNet** för virtuella nätverk där användare har distribuerat en NVA som ska användas för att kontrol lera Internet-bundit trafik. (Till exempel VNet 5 i diagrammet längre fram i den här artikeln.)
* **Hubbar** för virtuella WAN-nav som hanteras av Microsoft.

I följande tabell sammanfattas de anslutningar som stöds i det här scenariot:

| Från          | Om du vill|Spokes (ekrar)|Service VNet|Grenar|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes (ekrar)**| ->| rakt |rakt | via tjänstens VNet |via perimeter-VNet |
| **Service VNet**| ->| rakt |saknas| rakt | |
| **Grenar** | ->| via tjänstens VNet |rakt| rakt |  |

Var och en av cellerna i anslutnings matrisen beskriver om anslutningen flödar direkt över ett virtuellt WAN eller över ett av de virtuella nätverken med en NVA. 

Observera följande information:
* Ekrar
  * Ekrar kommer att komma åt andra ekrar direkt över virtuella WAN-hubbar.
  * Ekrar får anslutning till grenar via en statisk väg som pekar på tjänstens VNet. De kan inte lära sig vissa prefix från grenarna eftersom de är mer exakta och åsidosätter sammanfattningen.
  * Ekrar kommer att skicka Internet trafik till perimeternätverket via en direkt VNet-peering.
* Grenar får ekrar via en statisk routning som pekar på tjänstens VNet. De lär sig inte vissa prefix från de virtuella nätverk som åsidosätter den sammanfattade statiska vägen.
* Tjänstens VNet kommer att likna ett VNet för delade tjänster som behöver kunna bli tillgängligt från alla virtuella nätverk och varje gren.
* Perimeternätverket för perimeter behöver inte ha någon anslutning via det virtuella WAN-nätverket, eftersom den enda trafik som stöds kommer att komma över direkta peer-anslutningar för virtuella nätverk. För att förenkla konfigurationen använder du dock samma anslutnings modell som för perimeternätverket.

Det finns tre separata anslutnings mönster, som översätts till tre väg tabeller. Associationerna till de olika virtuella nätverken är:

* Ekrar
  * Associerad routningstabell: **RT_V2B**
  * Sprider till routningstabeller: **RT_V2B** och **RT_SHARED**
* NVA virtuella nätverk (service VNet och DMZ VNet):
  * Associerad routningstabell: **RT_SHARED**
  * Sprider till routningstabeller: **RT_SHARED**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_SHARED** och **standard**

> [!NOTE] 
> Kontrol lera att eker-virtuella nätverk inte sprids till standard etiketten. Detta säkerställer trafik från grenar till ekrar som virtuella nätverk kommer att vidarebefordras till NVA.

Dessa statiska vägar säkerställer att trafik till och från det virtuella nätverket och grenen går genom NVA i tjänstens VNet (VNet 4):

| Description | Routningstabell | Statisk väg              |
| ----------- | ----------- | ------------------------- |
| Grenar    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA pinnar  | Standardvärde     | 10.1.0.0/16-> vnet4conn  |

Nu kan du använda Virtual WAN för att välja rätt anslutning för att skicka paketen till. Du måste också använda Virtual WAN för att välja rätt åtgärd som ska vidtas när du tar emot dessa paket. Du använder tabellerna för anslutnings väg för detta, enligt följande:

| Description | Anslutning | Statisk väg            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Mer information finns i [om routning av virtuell hubb](about-virtual-hub-routing.md).

## <a name="architecture"></a>Arkitektur

Här är ett diagram över arkitekturen som beskrivs tidigare i artikeln.

Det finns ett nav som kallas **hubb 1**.

* **Hubb 1** är direkt ansluten till NVA virtuella nätverk **VNet 4** och **VNet 5**.

* Trafik mellan virtuella nätverk 1, 2 och 3 och grenar förväntas gå via **VNet 4 NVA** -10.4.0.5.

* All Internet bindnings trafik från virtuella nätverk 1, 2 och 3 förväntas gå via **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagram över nätverks arkitektur.":::

## <a name="workflow"></a>Arbetsflöde

Om du vill konfigurera routning via NVA följer du stegen nedan:

1. För att Internet-bundit trafik ska gå via VNet 5 behöver du virtuella nätverk 1, 2 och 3 för att ansluta direkt via det virtuella nätverkets peering till VNet 5. Du behöver också en användardefinierad väg uppsättning i de virtuella nätverken för 0.0.0.0/0 och nästa hopp 10.5.0.5. För närvarande tillåter inte Virtual WAN en nästa hopp-NVA i den virtuella hubben för 0.0.0.0/0.

1. I Azure Portal går du till din virtuella hubb och skapar en anpassad routningstabell som heter **RT_Shared**. Den här tabellen lär sig vägar via spridning från alla virtuella nätverk och förgrenings anslutningar. Du kan se den tomma tabellen i följande diagram.

   * **Vägar:** Du behöver inte lägga till några statiska vägar.

   * **Association:** Välj virtuella nätverk 4 och 5, vilket innebär att anslutningarna för de här virtuella nätverken associeras med routningstabellen **RT_Shared**.

   * **Spridning:** Eftersom du vill att alla grenar och virtuella nätverks anslutningar ska sprida sina vägar dynamiskt till den här routningstabellen väljer du grenar och alla virtuella nätverk.

1. Skapa en anpassad routningstabell med namnet **RT_V2B** för att dirigera trafik från virtuella nätverk 1, 2 och 3 till grenar.

   * **Vägar:** Lägg till en sammanställd statisk väg post för grenar, med nästa hopp som VNet 4-anslutningen. Konfigurera en statisk väg i VNet 4-anslutning för förgrenings-prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Välj alla virtuella nätverk 1, 2 och 3. Detta innebär att VNet-anslutningarna 1, 2 och 3 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:** Anslutningar sprider vägar till routningstabeller. Om du väljer virtuella nätverk 1, 2 och 3 aktiverar du spridning av vägar från virtuella nätverk 1, 2 och 3 till den här väg tabellen. Det finns inget behov av att sprida vägar från förgrenings anslutningar till **RT_V2B**, eftersom trafiken i grenen för virtuella nätverk går via NVA i VNet 4.
  
1. Redigera standard väg tabellen, **DefaultRouteTable**.

   Alla VPN-, Azure-ExpressRoute och användares VPN-anslutningar är kopplade till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

   * **Vägar:** Lägg till en sammanställd statisk väg post för virtuella nätverk 1, 2 och 3, med nästa hopp som VNet 4-anslutningen. Konfigurera en statisk väg i VNet 4-anslutning för VNet 1, 2 och 3 aggregerade prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, vilket säkerställer att lokala förgrenings anslutningar är kopplade till standard väg tabellen.

   * **Spridningen från:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala anslutningar sprider vägar till standard väg tabellen.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagram över arbets flöde." lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
