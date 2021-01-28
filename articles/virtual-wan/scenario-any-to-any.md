---
title: 'Scenario: alla-till-alla'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – alla-till-alla
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: a866c21e067293481a52dd563873892de8b5444c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955283"
---
# <a name="scenario-any-to-any"></a>Scenario: alla-till-alla

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I ett valfritt scenario kan alla ekrar komma åt en annan eker. När det finns flera hubbar är hubb-till-hubb-routning (även kallat mellan hubb) aktiverat som standard i standard Virtual WAN. Du kan skapa den här konfigurationen med en mängd olika metoder, till exempel Azure Portal eller en [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways/). Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md). 

## <a name="design"></a><a name="design"></a>Design

För att ta reda på hur många väg tabeller som behövs i ett virtuellt WAN-scenario kan du bygga en anslutnings mat ris där varje cell representerar om en källa (rad) kan kommunicera med ett mål (kolumn).

| Från |   Om du vill |  *Virtuella nätverk* | *Grenar* |
| -------------- | -------- | ---------- | ---|
| Virtuella nätverk     | &#8594;| Direct | Direct |
| Grenar   | &#8594;| Direct  | Direct |

Var och en av cellerna i föregående tabell beskriver om en virtuell WAN-anslutning ("från"-sidan i flödet, rad rubrikerna) kommunicerar med ett måltema ("till"-sidan i flödet, kolumn rubrikerna i kursiv stil). I det här scenariot finns det inga brand väggar eller virtuella nätverks enheter, så kommunikationen flödar direkt över det virtuella WAN-nätverket (och därför ordet "Direct" i tabellen).

Eftersom alla anslutningar från både virtuella nätverk och grenar (VPN, ExpressRoute och User VPN) har samma anslutnings krav krävs en enda väg tabell. Därför kommer alla anslutningar att associeras och spridas till samma routningstabell, standard väg tabellen:

* Virtuella nätverk:
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

I **bild 1** kan alla virtuella nätverk och grenar (VPN, EXPRESSROUTE, P2s) komma åt varandra. I en virtuell hubb fungerar anslutningarna på följande sätt:

* En VPN-anslutning ansluter en VPN-plats till en VPN-gateway.
* En virtuell nätverks anslutning ansluter ett virtuellt nätverk till en virtuell hubb. Den virtuella hubbens router tillhandahåller överförings funktionerna mellan virtuella nätverk.
* En ExpressRoute-anslutning ansluter en ExpressRoute-krets till en ExpressRoute-Gateway.

Dessa anslutningar (som standard när de skapas) är kopplade till standard väg tabellen, om du inte ställer in Dirigerings konfigurationen för anslutningen till **ingen**, eller en anpassad väg tabell. Dessa anslutningar sprider också vägar som standard till standard väg tabellen. Det här gör det möjligt för ett scenario där alla ekrar (VNet, VPN, ER, P2S) kan komma åt varandra.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="bild 1":::

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Det här scenariot är aktiverat som standard för virtuellt WAN-nätverk. Om inställningarna för gren-till-gren är inaktiverade i WAN-konfigurationen, så tillåter inte anslutning mellan Branch ekrar. VPN/ExpressRoute/User VPN betraktas som Branch ekrar i virtuellt WAN

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
