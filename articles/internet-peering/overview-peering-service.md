---
title: Internet-peering jämfört med Peering Service
titleSuffix: Azure
description: Internet-peering jämfört med Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023453"
---
# <a name="internet-peering-vs-peering-service"></a>Internet-peering jämfört med Peering Service

Internet-peering avser en sammanlänkning mellan Microsofts globala nätverk (AS8075) och bärare eller tjänst leverantörs nätverk. En tjänst leverantör kan bli en peering service-partner genom att implementera samarbets kraven för peering service som beskrivs nedan för att tillhandahålla tillförlitliga och högpresterande offentliga anslutningar med optimal routning från kunden till Microsoft-nätverket.

## <a name="about-peering-service"></a>Om Peering Service
Peering-tjänsten är ett partner program med viktiga tjänste leverantörer för att tillhandahålla den bästa offentliga Internet anslutningen till sina företags användare. Partner som är en del av programmet kommer att ha direkta, geo-redundanta anslutningar med hög tillgänglighet och optimerad routning till Microsoft. Peering-tjänsten är ett tillägg till Microsoft-anslutnings portföljen:
*   ExpressRoute för privat anslutning till IaaS-eller PaaS-resurser (stöd för privat IP-utrymme)
    *   Partner baserad anslutning
    *   Direct 100G-anslutning till Microsoft
*   IPSEC via Internet för VPN-anslutning till molnet
*   SD-WAN-anslutning till Azure via virtuellt WAN

Mål segmentet för peering-tjänsten är SaaS-anslutning, SD-WAN-kunder är villiga att göra Internet-grupp i grenen och kunder som har en med dubbel strategi och Internet på företags nivå.

Det primära målet vid anslutning till Microsoft Cloud bör vara att minimera svars tiden genom att minska tiden för tur och retur från en användar webbplats till Microsofts globala nätverk, som är Microsofts offentliga nätverks stamnät som sammanbinder alla Microsofts Data Center och flera start punkter för moln program. Se [få bästa möjliga anslutning och prestanda i Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Bild av distribuerad åtkomst](./media/distributed-access.png)

I bilden ovan ansluter varje avdelnings kontor i ett globalt företag till den närmaste möjliga Microsoft Edge-plats som är möjlig via partnerns nätverk.

**Kund förmåner för peering service:**
* Bästa offentliga routning via Internet till Microsoft Cloud tjänster för optimala prestanda och tillförlitlighet.
* Möjlighet att välja önskad tjänst leverantör att ansluta till Microsoft Cloud.
* Trafik insikter, till exempel latens rapportering, och prefix övervakning.
* Optimala nätverks hopp (som humle) från Microsoft Cloud.
* Route Analytics och statistik-händelser för Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) väg avvikelser (läckage/kapning) och för optimal routning.

## <a name="peering-service-partnership-requirements"></a>Partner krav för peering service
* Anslutning till Microsoft Cloud på en plats närmast kunden. En partner tjänst leverantör kommer att dirigera användar trafik till Microsoft Edge närmast användaren. På samma sätt dirigerar Microsoft trafik (med BGP-tagg) till den gräns plats som är närmast användaren och tjänst leverantören att leverera trafiken till användaren på samma sätt som för användaren.
* Partnern upprätthåller hög tillgänglighet, högt data flöde och Geo-redundant anslutning med Microsofts globala nätverk.
* Partner kan använda sin befintliga peering för att stödja peering-tjänsten om den uppfyller kravet.

## <a name="faq"></a>Vanliga frågor
Vanliga frågor och svar om vanliga frågor finns i [peering service – Vanliga frågor och svar](service-faqs.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om kund förmåner med [peering-tjänsten](../peering-service/index.yml).
* Lär dig mer om steg för att aktivera en direkt peering för peering-tjänsten för peering [service partner genom gång](walkthrough-peering-service-all.md).