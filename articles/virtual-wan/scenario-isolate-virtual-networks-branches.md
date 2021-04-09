---
title: 'Scenario: anpassad isolering för virtuella nätverk och grenar'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – förhindra att valda virtuella nätverk och grenar kan komma åt varandra
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99408500"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scenario: anpassad isolering för virtuella nätverk och grenar

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I ett anpassat isolerings scenario för både virtuella nätverk (virtuella nätverk) och grenar är målet att förhindra att en speciell uppsättning virtuella nätverk når en annan uppsättning virtuella nätverk. På samma sätt tillåts inte grenar (VPN/ER/användare VPN) att uppnå vissa uppsättningar av virtuella nätverk.

Vi introducerar också det ytterligare kravet att Azure-brandväggen bör inspektera grenen till VNet och gren till VNet-trafik, men **inte**  VNet till VNet-trafik.  

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

För att ta reda på hur många väg tabeller som behövs kan du bygga en anslutnings mat ris. I det här scenariot ser det ut ungefär så här, där varje cell visar om en källa (rad) kan kommunicera med ett mål (kolumn):

| Från | Till:| *Blå virtuella nätverk* | *Röd virtuella nätverk* | *Röda grenar*| *Blå grenar*| 
|---|---|---|---|---|---|
| **Blå virtuella nätverk** |   &#8594;|   Direct     |           |   |  AzFW|
| **Röd virtuella nätverk**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Röda grenar**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Blå grenar**| &#8594;| AzFW  |   |Direct   | Direct

Var och en av cellerna i föregående tabell beskriver om en virtuell WAN-anslutning ("från"-sidan i flödet, rad rubrikerna) kommunicerar med ett mål ("till"-sidan av flödet, kolumn rubrikerna i kursiv stil). **Direkt** innebär att trafiken flödar direkt via det virtuella WAN-nätverket medan **AzFW** innebär att trafiken inspekteras av Azure-brandväggen innan den vidarebefordras till målet. En tom post innebär att flödet blockeras i installationen.

I det här fallet krävs de två väg tabellerna för virtuella nätverk för att uppnå målet för VNet-isolering utan Azure-brandvägg i sökvägen. Vi kommer att anropa tabellerna **RT_BLUE** och **RT_RED**.

Dessutom måste grenar alltid vara kopplade till  **standard** väg tabellen. För att säkerställa att trafiken till och från grenarna inspekteras av Azure-brandväggen lägger vi till statiska vägar i **standard**, **RT_RED** och **RT_BLUE** väg tabeller som pekar trafik till Azure-brandväggen och konfigurerar nätverks regler för att tillåta önskad trafik. Vi ser också till att grenarna **inte** sprids till **RT_BLUE** och **RT_RED**.

Därför är detta den slutliga designen:

* Blå virtuella nätverk:
  * Associerad routningstabell: **RT_BLUE**
  * Sprider till routningstabeller: **RT_BLUE**
* Röda virtuella nätverk:
  * Associerad routningstabell: **RT_RED**
  * Sprider till routningstabeller: **RT_RED** 
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**
* Statiska vägar:
    * **Standard väg tabell**: Virtual Network adress utrymmen med nästa hopp Azure-brandvägg
    * **RT_RED**: 0.0.0.0/0 med nästa hopp Azure-brandvägg
    * **RT_BLUE**: 0.0.0.0/0 med nästa hopp Azure-brandvägg
* Nätverks regler för brand vägg:
    * **Tillåt regel** **källans prefix**: blå förgrenings adress prefix **destination**: blå VNet-prefix 
    * **Tillåt regel**  **källans prefix**: röda förgrenings adress prefix **mål prefix**: röda VNet-prefix

> [!NOTE]
> Eftersom alla grenar måste kopplas till standard väg tabellen, och för att spridas till samma uppsättning routningstabeller, kommer alla grenar att ha samma anslutnings profil. Med andra ord kan inte det röda/blå-konceptet för virtuella nätverk tillämpas på grenar. Vi kan dock vidarebefordra trafik från grenarna till Azure-brandväggen för att uppnå anpassad routning för grenar.

> [!NOTE]
> Azure Firewall som standard nekar trafik i en modell med noll förtroende. Om det inte finns någon explicit **tillåten** regel som matchar det kontrollerade paketet släpper Azure-brandväggen paketet.

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Arbetsflöde

I **bild 1** finns det blå och röda virtuella nätverk samt grenar som kan komma åt antingen blå eller röd virtuella nätverk.

* Blue-anslutna virtuella nätverk kan komma åt varandra och kan komma åt alla blå grenar (VPN/ER/P2S)-anslutningar. I diagrammet är den blå grenen plats-till-plats-VPN-platsen.
* Röda anslutna virtuella nätverk kan komma åt varandra och kan komma åt alla röda grenar (VPN/ER/P2S)-anslutningar. I diagrammet är den röda grenen punkt-till-plats-VPN-användare.

Överväg följande steg när du konfigurerar routning.

1. Skapa två anpassade routningstabeller i Azure Portal, **RT_BLUE** och **RT_RED** för att anpassa trafiken till dessa virtuella nätverk.
2. För väg tabell **RT_BLUE** använder du följande inställningar för att se till att blå virtuella nätverk lär sig om adressprefix för alla andra blå virtuella nätverk.:
   * **Association**: Markera alla blå virtuella nätverk.
   * **Spridning**: Välj alla blå virtuella nätverk.
3. Upprepa samma steg för **RT_RED** routningstabell för röd virtuella nätverk.
4. Etablera en Azure-brandvägg i Virtual WAN. Mer information om Azure-brandväggen i den virtuella WAN-hubben finns i [Konfigurera Azure-brandväggen i Virtual WAN Hub](howto-firewall.md).
5. Lägg till en statisk väg i **standard** väg tabellen för den virtuella hubben som dirigerar all trafik till VNet-adress utrymmen (både blå och röd) till Azure-brandväggen. Det här steget ser till att paket från dina grenar skickas till Azure-brandväggen för granskning.
    * Exempel: **målets prefix**: 10.0.0.0/24 **nästa hopp**: Azure-brandvägg
    >[!NOTE]
    > Det här steget kan också göras via brand Väggs hanteraren genom att välja alternativet "säker privat trafik". Detta lägger till en väg för alla RFC1918 privata IP-adresser som är tillämpliga på virtuella nätverk och grenar. Du måste manuellt lägga till i alla grenar eller virtuella nätverk som inte är kompatibla med RFC1918. 

6. Lägg till en statisk väg till **RT_RED** och **RT_BLUE** dirigera all trafik till Azure-brandväggen. Det här steget säkerställer att virtuella nätverk inte kommer åt grenar direkt. Det här steget kan inte utföras via brand Väggs hanteraren eftersom de virtuella nätverken inte är associerade med standard väg tabellen.
    * Exempel: **målets prefix**: 0.0.0.0/0 **nästa hopp**: Azure-brandvägg

    > [!NOTE]
    > Routning utförs med den längsta prefix matchningen (LPM). Det innebär att de statiska vägarna för 0.0.0.0/0 **inte** prioriteras för de exakta prefix som finns i **BLUE_RT** och **RED_RT**. Det innebär att trafiken inom VNet inte kontrol leras av Azure-brandväggen.

Detta leder till att konfigurations ändringarna i routningen visas i bilden nedan.

**Bild 1** 
 [ ![ Bild 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
