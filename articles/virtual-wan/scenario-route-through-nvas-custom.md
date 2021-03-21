---
title: Dirigera trafik via NVA med anpassade inställningar
titleSuffix: Azure Virtual WAN
description: Det här scenariot hjälper dig att dirigera trafik via NVA med hjälp av en annan NVA för Internet-baserad trafik.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6291964aff7c215df7f738ab103947dcb9b1b1d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036899"
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
|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes (ekrar)**| ->| rakt |rakt | via tjänstens VNet |via perimeter-VNet |
| **Service VNet**| ->| rakt |saknas| rakt | |
| **Grenar** | ->| via tjänstens VNet |rakt| rakt |  |

Var och en av cellerna i anslutnings matrisen beskriver om anslutningen flödar direkt över ett virtuellt WAN eller över ett av de virtuella nätverken med en NVA.

Observera följande information:

* Ekrar
  * Ekrar kommer att komma åt andra ekrar direkt över virtuella WAN-hubbar.
  * Ekrar får anslutning till grenar via en statisk väg som pekar på tjänstens VNet. De kan inte lära sig vissa prefix från grenarna eftersom dessa prefix är mer exakta och åsidosätter sammanfattningen.
  * Ekrar kommer att skicka Internet trafik till perimeternätverket via en direkt VNet-peering.
* Grenar får ekrar via en statisk routning som pekar på tjänstens VNet. De lär sig inte vissa prefix från de virtuella nätverk som åsidosätter den sammanfattade statiska vägen.
* Tjänstens VNet kommer att likna ett VNet för delade tjänster som behöver kunna bli tillgängligt från alla virtuella nätverk och varje gren.
* Perimeternätverket för perimeter behöver inte ha någon anslutning via det virtuella WAN-nätverket, eftersom den enda trafik som stöds kommer att komma över direkta peer-anslutningar för virtuella nätverk. För att förenkla konfigurationen använder du dock samma anslutnings modell som för perimeternätverket.

Det finns tre olika anslutnings mönster, som översätts till tre väg tabeller. Associationerna till de olika virtuella nätverken är:

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
> Kontrol lera att eker-virtuella nätverk inte sprids till standard etiketten. Detta säkerställer att trafik från grenar till ekrar virtuella nätverk vidarebefordras till NVA.

Dessa statiska vägar säkerställer att trafik till och från det virtuella nätverket och grenen går genom NVA i tjänstens VNet (VNet 4):

| Beskrivning | Routningstabell | Statisk väg              |
| ----------- | ----------- | ------------------------- |
| Grenar    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA pinnar  | Standardvärde     | 10.1.0.0/16-> vnet4conn  |

Nu kan du använda Virtual WAN för att välja rätt anslutning för att skicka paketen till. Du måste också använda Virtual WAN för att välja rätt åtgärd som ska vidtas när du tar emot dessa paket. Du använder tabellerna för anslutnings väg för detta, enligt följande:

| Beskrivning | Anslutning | Statisk väg            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Mer information finns i [om routning av virtuell hubb](about-virtual-hub-routing.md).

## <a name="architecture"></a>Arkitektur

Följande diagram visar arkitekturen som beskrivs tidigare i artikeln.

Det finns ett nav i diagrammet. **Hubb 1**.

* **Hubb 1** är direkt ansluten till NVA virtuella nätverk **VNet 4** och **VNet 5**.

* Trafik mellan virtuella nätverk 1, 2, 3 och gren förväntas gå via **VNet 4 NVA** -10.4.0.5.

* All Internet bindnings trafik från virtuella nätverk 1, 2 och 3 förväntas gå via **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagram över nätverks arkitektur.":::

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagram över arbets flöde." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Överväg följande steg för att konfigurera routning via NVA:

1. För att Internet-bundit trafik ska gå via VNet 5 behöver du virtuella nätverk 1, 2 och 3 för att ansluta direkt via det virtuella nätverkets peering till VNet 5. Du behöver också en användardefinierad väg uppsättning i de virtuella nätverken för 0.0.0.0/0 och nästa hopp 10.5.0.5.

   Om du inte vill ansluta virtuella nätverk 1, 2 och 3 till VNet 5 och istället bara använda NVA i VNet 4 för att dirigera 0.0.0.0/0-trafik från grenar (lokala VPN-eller ExpressRoute-anslutningar) går du till det [alternativa arbets flödet](#alternate).

   Men om du vill att VNet-till-VNet-trafik ska överföras via NVA måste du koppla från VNet 1, 2, 3 från den virtuella hubben och ansluta den eller stacka den ovanför NVA eker-VNet4. I virtuella WAN-trafik överföring mellan virtuella nätverk via den virtuella WAN-hubben eller en virtuell WAN-Hubbs Azure-brandvägg (säker hubb). Om virtuella nätverk-peer direkt använder VNet-peering kan de kommunicera direkt över överföringen via den virtuella hubben.

1. I Azure Portal går du till din virtuella hubb och skapar en anpassad routningstabell som heter **RT_Shared**. Den här tabellen lär sig vägar via spridning från alla virtuella nätverk och förgrenings anslutningar. Du kan se den tomma tabellen i följande diagram.

   * **Vägar:** Du behöver inte lägga till några statiska vägar.

   * **Association:** Välj virtuella nätverk 4 och 5, vilket innebär att anslutningarna för de här virtuella nätverken associeras med routningstabellen **RT_Shared**.

   * **Spridning:** Eftersom du vill att alla grenar och virtuella nätverks anslutningar ska sprida sina vägar dynamiskt till den här routningstabellen väljer du grenar och alla virtuella nätverk.

1. Skapa en anpassad routningstabell med namnet **RT_V2B** för att dirigera trafik från virtuella nätverk 1, 2 och 3 till grenar.

   * **Vägar:** Lägg till en sammanställd statisk väg post för grenar, med nästa hopp som VNet 4-anslutningen. Konfigurera en statisk väg i VNet 4-anslutning för förgrenings-prefix. Ange nästa hopp som den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Välj alla **virtuella nätverk 1, 2 och 3**. Detta innebär att VNet-anslutningarna 1, 2 och 3 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:** Anslutningar sprider vägar till routningstabeller. Att välja virtuella nätverk 1, 2 och 3 aktiverar spridning av vägar från virtuella nätverk 1, 2 och 3 till den här routningstabellen. Det finns inget behov av att sprida vägar från förgrenings anslutningar till **RT_V2B**, eftersom trafiken i grenen för virtuella nätverk går via NVA i VNet 4.
  
1. Redigera standard väg tabellen, **DefaultRouteTable**.

   Alla VPN-, Azure-ExpressRoute och användares VPN-anslutningar är kopplade till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

   * **Vägar:** Lägg till en sammanställd statisk väg post för virtuella nätverk 1, 2 och 3, med nästa hopp som VNet 4-anslutningen. Konfigurera en statisk väg i VNet 4-anslutning för VNet 1, 2 och 3 aggregerade prefix. Ange nästa hopp som den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, vilket säkerställer att lokala förgrenings anslutningar är kopplade till standard väg tabellen.

   * **Spridningen från:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala anslutningar sprider vägar till standard väg tabellen.

## <a name="alternate-workflow"></a><a name="alternate"></a>Alternativt arbets flöde

I det här arbets flödet ansluter du inte virtuella nätverk 1, 2 och 3 till VNet 5. I stället använder du NVA i VNet 4 för att dirigera 0.0.0.0/0-trafik från grenar (lokala VPN-eller ExpressRoute-anslutningar).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagram över alternativt arbets flöde." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Överväg följande steg för att konfigurera routning via NVA:

1. I Azure Portal går du till din virtuella hubb och skapar en anpassad routningstabell som heter **RT_NVA** för att dirigera trafik via NVA-10.4.0.5  

   * **Vägar:**   Ingen åtgärd krävs.

   * **Association:**   Välj **VNet4**. Detta innebär att VNet-anslutning 4 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:**   Anslutningar sprider vägar till routningstabeller. Att välja virtuella nätverk 1, 2 och 3 aktiverar spridning av vägar från virtuella nätverk 1, 2 och 3 till den här routningstabellen. Om du väljer grenar (VPN/ER/P2S) kan du sprida vägar från grenar/lokala anslutningar till den här routningstabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

1. Skapa en anpassad routningstabell med namnet **RT_VNET** för att dirigera trafik från virtuella nätverk 1, 2 och 3 till grenar eller Internet (0.0.0.0/0) via VNET4-NVA. VNet-till-VNet-trafik kommer att dirigeras och inte via VNet 4-NVA. Om du vill att trafiken ska gå via NVA, kopplar du från VNet 1, 2 och 3 och ansluter dem med VNet-peering till VNet4.

   * **Cirkulera**  

     * Lägg till en aggregerad väg "10.2.0.0/16" med nästa hopp som VNet 4-anslutningen för trafik från virtuella nätverk 1, 2 och 3 till grenar. I VNet4-anslutningen konfigurerar du en väg för "10.2.0.0/16" och anger nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

     * Lägg till en väg (0.0.0.0/0) med nästa hopp som VNet 4-anslutningen. 0.0.0.0/0 har lagts till för att innebära att trafik skickas till Internet. Det innebär inte några speciella adressprefix som rör virtuella nätverk eller grenar. I VNet4-anslutningen konfigurerar du en väg för ' 0.0.0.0/0 ' och anger nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

   * **Association:**   Välj alla **virtuella nätverk 1, 2 och 3**. Detta innebär att VNet-anslutningarna 1, 2 och 3 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:**   Anslutningar sprider vägar till routningstabeller. Om du väljer virtuella nätverk 1, 2 och 3 aktiverar du spridning av vägar från virtuella nätverk 1, 2 och 3 till den här väg tabellen. Se till att alternativet för grenar (VPN/ER/P2S) inte är markerat. Detta säkerställer att lokala anslutningar inte kan komma åt virtuella nätverk 1, 2 och 3 direkt.

1. Redigera standard väg tabellen, **DefaultRouteTable**.

   Alla VPN-, Azure-ExpressRoute och användares VPN-anslutningar är kopplade till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

   * **Cirkulera**  

     * Lägg till en sammanställd väg "10.1.0.0/16" för **virtuella nätverk 1, 2 och 3** med nästa hopp som **VNet 4-anslutningen**.

     * Lägg till en väg (0.0.0.0/0) med nästa hopp som **VNet 4-anslutningen**. 0.0.0.0/0 har lagts till för att innebära att trafik skickas till Internet. Det innebär inte några speciella adressprefix som rör virtuella nätverk eller grenar. I det föregående steget för VNet4-anslutningen hade du redan konfigurerat en väg för "0.0.0.0/0", där nästa hopp är den speciella IP-adressen för NVA i VNet 4.

   * **Association:**   Se till att alternativet för grenar **(VPN/er/P2s)** är markerat. Detta säkerställer att lokala förgrenings anslutningar är kopplade till standard väg tabellen. Alla VPN-, Azure-ExpressRoute och användares VPN-anslutningar är bara kopplade till standard väg tabellen.

   * **Spridningen från:**   Se till att alternativet för grenar **(VPN/er/P2s)** är markerat. Detta säkerställer att lokala anslutningar sprider vägar till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning väg tabeller.

   >[!Note]
   >
   > * Portal användare måste aktivera "Sprid till standard väg" på anslutningar (VPN/ER/P2S/VNet) för att vägen 0.0.0.0/0 ska börja gälla.
   > * PS/CLI/REST-användare måste ange flaggan ' enableinternetsecurity ' till true för att vägen 0.0.0.0/0 ska börja gälla.
   > * Virtual Network anslutningen har inte stöd för nästa hopp-IP för nästa hopp till samma virtuella nätverks program i ett eker-VNet om "en av vägarna med nästa hopp-IP anges som offentlig IP-adress eller 0.0.0.0/0 (Internet)

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
