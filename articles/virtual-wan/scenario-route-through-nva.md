---
title: 'Scenario: dirigera trafik via en virtuell nätverks installation (NVA)'
titleSuffix: Azure Virtual WAN
description: Dirigera trafik via NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519804"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: Dirigera trafik via NVA

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här NVA-scenariot är målet att dirigera trafik via en NVA (virtuell nätverks installation) för gren till VNet och VNet till gren. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

> [!NOTE]
> Om du redan har en konfiguration med vägar som är tidigare än de nya funktionerna som krävs för [att konfigurera virtuell hubb](how-to-virtual-hub-routing.md) blir tillgänglig, kan du följa stegen i dessa versioner av artiklarna:
>* [Azure Portal artikel](virtual-wan-route-table-nva-portal.md)
>* [PowerShell-artikel](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Design

I det här scenariot använder vi namngivnings konventionen:

* "NVA virtuella nätverk" för virtuella nätverk där användare har distribuerat en NVA och har anslutit andra virtuella nätverk som ekrar (VNet 2 och VNet 4 i **bild 2** längre ned i artikeln).
* "NVA ekrar" för virtuella nätverk som är anslutna till ett NVA VNet (VNet 5, VNet 6, VNet 7 och VNet 8 i **bild 2** ).
* "Icke-NVA virtuella nätverk" för virtuella nätverk som är anslutna till ett virtuellt WAN-nätverk som inte har en NVA eller andra virtuella nätverk som peer-kopplas med dem (VNet 1 och VNet 3 i **figur 2** längre ned i artikeln).
* "Hubbar" för Microsoft-hanterade virtuella WAN-nav, där NVA virtuella nätverk är anslutna till. NVA eker-virtuella nätverk behöver inte vara anslutna till virtuella WAN-nav, endast till NVA virtuella nätverk.

Följande anslutnings mat ris sammanfattar de flöden som stöds i det här scenariot:

**Anslutnings mat ris**

| Från             | Till:|   *NVA pinnar*|*NVA virtuella nätverk*|*Icke-NVA virtuella nätverk*|*Grenar*|
|---|---|---|---|---|---|
| **NVA pinnar**   | &#8594; | Över NVA VNet | Peering | Över NVA VNet | Över NVA VNet |
| **NVA virtuella nätverk**    | &#8594; | Peering | Direct | Direct | Direct |
| **Icke-NVA virtuella nätverk**| &#8594; | Över NVA VNet | Direct | Direct | Direct |
| **Grenar**     | &#8594; | Över NVA VNet | Direct | Direct | Direct |

Var och en av cellerna i anslutnings matrisen beskriver hur ett VNet eller en gren ("från"-sidan i flödet, rad rubrikerna i tabellen) kommunicerar med ett virtuellt nätverk eller en förgrening ("till"-sidan i flödet, kolumn rubrikerna i kursiv stil i tabellen). "Direct" innebär att anslutningen tillhandahålls internt av Virtual WAN, "peering" innebär att anslutningen tillhandahålls av en User-Defined väg i VNet, "över NVA VNet" innebär att anslutningen passerar NVA som distribueras i NVA VNet. Tänk också på följande:

* NVA ekrar hanteras inte av virtuella WAN-nätverk. Det innebär att de mekanismer som de kommer att kommunicera med andra virtuella nätverk eller grenar bevaras av användaren. Anslutning till NVA VNet tillhandahålls av en VNet-peering och en standard väg till 0.0.0.0/0 som pekar på NVA som nästa hopp bör gälla anslutning till Internet, till andra ekrar och till grenar
* NVA-virtuella nätverk kommer att veta om sina egna NVA ekrar, men inte om NVA ekrar kopplade till andra NVA virtuella nätverk. Till exempel i bild 2 finns mer detaljerat i den här artikeln, VNet 2 vet om VNet 5 och VNet 6, men inte andra ekrar som VNet 7 och VNet 8. En statisk väg krävs för att mata in andra ekrars prefix i NVA virtuella nätverk
* På samma sätt vet inte grenar och icke-NVA virtuella nätverk om några NVA ekrar, eftersom NVA ekrar inte är anslutna till virtuella WAN-hubbar. Det innebär att statiska vägar också behövs här.

Med hänsyn till att NVA ekrar inte hanteras av virtuella WAN-nätverk visas samma anslutnings mönster i alla andra rader. Därför kommer en enda routningstabell (standard en) att göra:

* Virtuella nätverk (icke-hubb virtuella nätverk och virtuella nätverk för användar NAV):
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**

I det här scenariot behöver vi dock tänka på vilka statiska vägar som ska konfigureras. Varje statisk väg har två komponenter, en del i den virtuella WAN-hubben som talar om vilka virtuella WAN-komponenter som anslutningen ska använda för varje eker, och en annan i den aktuella anslutningen som pekar på den konkreta IP-adress som tilldelats till NVA (eller till en belastningsutjämnare framför flera NVA), som **bild 1** visar:

**Bild 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Bild 1":::

Med det här alternativet är de statiska vägar som vi behöver i standard tabellen för att skicka trafik till NVA ekrar bakom NVA VNet följande:

| Beskrivning | Routningstabell | Statisk väg              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Standardvärde     | 10.2.0.0/16-> eastusconn |
| VNet 4       | Standardvärde     | 10.4.0.0/16-> weconn     |

Nu vet Virtual WAN vilken anslutning som ska skicka paketen till, men anslutningen måste veta vad du ska göra när du tar emot dessa paket: det är här som anslutnings väg tabellerna används. Här kommer vi att använda de kortare prefixen (/24 i stället för längre/16) för att se till att dessa vägar har prioritet över vägar som importeras från NVA-virtuella nätverk (VNet 2 och VNet 4):

| Beskrivning | Anslutning | Statisk väg            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24 – > 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24 – > 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24 – > 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 – > 10.4.0.5 |

Nu NVA virtuella nätverk, icke-NVA virtuella nätverk och grenar vet hur man når alla NVA ekrar. Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

I **bild 2** finns det två hubbar. **Hub1** och **Hub2**.

* **Hub1** och **Hub2** är direkt anslutna till NVA virtuella nätverk **VNet 2** och **VNet 4**.

* **VNet 5** och **VNet 6** är peer-datorer med **VNet 2**.

* **VNet 7** och **VNet 8** är peer-datorer med **VNet 4**.

* **Virtuella nätverk 5, 6, 7, 8** är indirekta ekrar, inte direkt anslutna till en virtuell hubb.

**Bild 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Bild 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

Om du vill konfigurera routning via NVA följer du stegen nedan:

1. Identifiera NVA eker VNet-anslutningen. I **bild 2** är det **VNet 2-anslutning (Eastusconn)** och **VNet 4-anslutning (weconn)**.

   Se till att UDR har kon figurer ATS:
   * Från VNet 5 och VNet 6 till VNet 2 NVA IP
   * Från VNet 7 och VNet 8 till VNet 4 NVA IP 
   
   Du behöver inte ansluta virtuella nätverk 5, 6, 7, 8 till de virtuella hubbarna direkt. Se till att NSG: er i virtuella nätverk 5, 6, 7, 8 tillåter trafik för grenen (VPN/ER/P2S) eller virtuella nätverk som är anslutna till sin fjärran sluten virtuella nätverk. Till exempel måste virtuella nätverk 5, 6 säkerställa att NSG: er tillåter trafik för lokala adressprefix och virtuella nätverk 7, 8 som är anslutna till fjärrhubben 2.

Det virtuella WAN-nätverket har inte stöd för ett scenario där virtuella nätverk 5, 6 ansluter till en virtuell hubb och kommunicerar via VNet 2 NVA IP; Därför måste du ansluta virtuella nätverk 5, 6 till VNet2 och på samma sätt som VNet 7, 8 till VNet 4.

2. Lägg till en sammanställd statisk väg post för virtuella nätverk 2, 5, 6 till hubb 1 standard väg tabell.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exempel":::

3. Konfigurera en statisk väg för virtuella nätverk 5, 6 i VNet 2: s virtuella nätverks anslutning. Information om hur du konfigurerar konfigurering av routning för en virtuell nätverks anslutning finns i [routning av virtuell hubb](how-to-virtual-hub-routing.md#routing-configuration).

4. Lägg till en sammanställd statisk väg post för virtuella nätverk 4, 7, 8 till hubb 1 standard väg tabell.

5. Upprepa steg 2, 3 och 4 för standard väg tabellen i hubb 2.

Detta leder till konfigurations ändringar för routning, som visas i **bild 3** nedan.

**Bild 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Bild 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
