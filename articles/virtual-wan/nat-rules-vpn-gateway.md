---
title: Konfigurera VPN NAT-regler för din gateway
titleSuffix: Azure Virtual WAN
description: Lär dig hur du konfigurerar NAT-regler för din VWAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746932"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Konfigurera NAT-regler för din virtuella WAN-Gateway – för hands version

> [!IMPORTANT]
> NAT-regler finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan konfigurera din virtuella WAN-Gateway med statiska en-till-ett NAT-regler. En NAT-regel ger en mekanism för att ställa in en-till-en-översättning av IP-adresser. NAT kan användas för att koppla samman två IP-nätverk som har inkompatibla eller överlappande IP-adresser. Ett typiskt scenario är grenar med överlappande IP-adresser som vill komma åt Azure VNet-resurser.

Den här konfigurationen använder en Flow-tabell för att dirigera trafik från en extern (värd) IP-adress till en intern IP-adress som är kopplad till en slut punkt i ett virtuellt nätverk (virtuell dator, dator, behållare osv.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram som visar arkitektur.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Konfigurera och Visa regler

Du kan konfigurera och Visa NAT-regler på dina VPN Gateway-inställningar när som helst.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Skärm bild som visar redigerings regler.":::

1. Navigera till din virtuella hubb.
1. Välj **VPN (plats till plats)**.
1. Välj **NAT-regler (redigera)**.
1. På sidan **Redigera NAT-regel** kan du **lägga till/redigera/ta bort** en NAT-regel med följande värden:

   * **Namn:** Ett unikt namn för din NAT-regel.
   * **Typ:** Oföränderlig. Statisk en-till-en NAT upprättar en 1-till-1-relation mellan en intern adress och en extern adress.
   * **Läge:** IngressSnat eller EgressSnat.  
      * IngressSnat-läget (även kallat ingångs källans NAT) är tillämpligt för trafik som anger Azure Hub-VPN-gatewayen för plats-till-plats.
      * EgressSnat-läget (även kallat utgående käll-NAT) är tillämpligt för trafik som lämnar Azure Hub: s VPN-gateway för plats-till-plats.
   * **InternalMapping:** Ett adressprefix som är ett intervall av käll-IP: er på det inre nätverket som ska mappas till en uppsättning externa IP-adresser. Med andra ord är ditt adressprefix för före NAT.
   * **ExternalMapping:** Ett adressprefix som är ett mål-IP-adressintervall för det externa nätverket som Källans IP-adresser mappas till. Med andra ord är ditt adressprefix för efter NAT.
   * **Länk anslutning:** Anslutnings resurs som i stort sett ansluter en VPN-plats till Azure Hub-VPN-gatewayen för plats-till-plats.

### <a name="configuration-considerations"></a>Konfigurations överväganden

* Under näts storleken för både intern och extern mappning måste vara samma för statisk 1-till-en NAT.
* Se till att redigera VPN-platsen i Azure Portal för att lägga till **ExternalMapping** -prefix i fältet "privat adress utrymme". För närvarande måste platser som har BGP aktiverat se till att den lokala BGP-tillkännageren (enhetens BGP-inställningar) innehåller en post för de externa mappnings-prefixen.

## <a name="next-steps"></a>Nästa steg

Mer information om plats-till-plats-konfigurationer finns i [Konfigurera en virtuell WAN-plats-till-plats-anslutning](virtual-wan-site-to-site-portal.md).
