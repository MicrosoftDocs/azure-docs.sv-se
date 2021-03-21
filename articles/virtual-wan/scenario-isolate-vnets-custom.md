---
title: 'Scenario: anpassad isolering för virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – förhindra att valda virtuella nätverk kan komma åt varandra
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461829"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: anpassad isolering för virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I ett anpassat isolerings scenario för virtuella nätverk är målet att förhindra att en speciell uppsättning virtuella nätverk kan komma åt en annan speciell uppsättning virtuella nätverk. Virtuella nätverk krävs dock för att komma åt alla grenar (VPN/ER/användare VPN). Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

För att ta reda på hur många väg tabeller som behövs kan du bygga en anslutnings mat ris. I det här scenariot ser det ut ungefär så här, där varje cell visar om en källa (rad) kan kommunicera med ett mål (kolumn):

| Från | Till:| *Blå virtuella nätverk* | *Röd virtuella nätverk* | *Grenar*|
|---|---|---|---|---|
| **Blå virtuella nätverk** |   &#8594;|   Direct     |           |  Direct |
| **Röd virtuella nätverk**  |   &#8594;|              |   Direct  |  Direct |
| **Grenar**   |   &#8594;|   Direct     |   Direct  |  Direct |

Var och en av cellerna i föregående tabell beskriver om en virtuell WAN-anslutning ("från"-sidan i flödet, rad rubrikerna) kommunicerar med ett mål ("till"-sidan av flödet, kolumn rubrikerna i kursiv stil). I det här scenariot finns det inga brand väggar eller virtuella nätverks enheter, så kommunikationen flödar direkt över det virtuella WAN-nätverket (och därför ordet "Direct" i tabellen).

Antalet olika rad mönster är antalet väg tabeller som vi behöver i det här scenariot. I det här fallet kommer tre väg tabeller att anropa **RT_BLUE** och **RT_RED** för de virtuella nätverken och som **standard** för grenarna. Kom ihåg att grenar alltid måste kopplas till standard routningstabellen.

Grenarna måste lära sig prefixen från både rött och blått virtuella nätverk, så alla virtuella nätverk måste spridas till standard (till och med antingen **RT_BLUE** eller **RT_RED**). Blå och röd virtuella nätverk måste lära sig om grenarnas prefix, så grenar kommer att spridas till båda väg tabellerna **RT_BLUE** och **RT_RED** . Därför är detta den slutliga designen:

* Blå virtuella nätverk:
  * Associerad routningstabell: **RT_BLUE**
  * Sprider till routningstabeller: **RT_BLUE** och **standard**
* Röda virtuella nätverk:
  * Associerad routningstabell: **RT_RED**
  * Sprider till routningstabeller: **RT_RED** och **standard**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_BLUE**, **RT_RED** och **standard**

> [!NOTE]
> Eftersom alla grenar måste kopplas till standard väg tabellen, och för att spridas till samma uppsättning routningstabeller, kommer alla grenar att ha samma anslutnings profil. Med andra ord kan inte det röda/blå-konceptet för virtuella nätverk tillämpas på grenar.

> [!NOTE]
> Om ditt virtuella WAN-nätverk distribueras över flera regioner måste du skapa **RT_BLUE** och **RT_RED** routningstabeller i varje hubb, och vägar från varje VNET-anslutning måste spridas till routningstabeller i varje virtuellt nav som använder spridnings etiketter.

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Arbetsflöde

I **bild 1** finns det blå och röda VNet-anslutningar.

* Blue-anslutna virtuella nätverk kan komma åt varandra, samt komma åt alla grenar (VPN/ER/P2S)-anslutningar.
* Red virtuella nätverk kan komma åt varandra, samt komma åt alla grenar (VPN/ER/P2S)-anslutningar.

Överväg följande steg när du konfigurerar routning.

1. Skapa två anpassade väg tabeller i Azure Portal **RT_BLUE** och **RT_RED**.
2. För väg tabell **RT_BLUE** för följande inställningar:
   * **Association**: Markera alla blå virtuella nätverk.
   * **Spridning**: för grenar väljer du alternativet för grenar, vilket innebär att förgreningar (VPN/er/P2s)-anslutningar sprider vägar till den här routningstabellen.
3. Upprepa samma steg för **RT_RED** routningstabell för Red virtuella nätverk och grenar (VPN/er/P2s).

Detta leder till att konfigurations konfigurationen ändras så som visas i figuren nedan

**Bild 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="bild 1":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
