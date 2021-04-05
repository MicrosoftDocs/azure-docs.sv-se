---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010831"
---
|**Generering av VPN <br> Gateway <br>** |**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2S <br> IKEv2/OpenVPN-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** | **BGP** | **Zon – redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | Max. 10    | Max. 128  | Stöds inte  | 100 Mbit/s  | Stöds inte| Inga |
|**Generation1**|**VpnGw1**  | Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Inga |
|**Generation1**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Inga |
|**Generation1**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Inga |
|**Generation1**|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Ja |
|**Generation1**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Ja |
|**Generation1**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Ja |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbit/s | Stöds | Inga |
|**Generation2**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbit/s  | Stöds | Inga |
|**Generation2**|**VpnGw4**  | Max. 30*   | Max. 128  | Max. 5000      | 5 Gbit/s    | Stöds | Inga |
|**Generation2**|**VpnGw5**  | Max. 30*   | Max. 128  | Max. 10000      | 10 Gbit/s   | Stöds | Inga |
|**Generation2**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbit/s | Stöds | Ja |
|**Generation2**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbit/s  | Stöds | Ja |
|**Generation2**|**VpnGw4AZ**| Max. 30*   | Max. 128  | Max. 5000      | 5 Gbit/s    | Stöds | Ja |
|**Generation2**|**VpnGw5AZ**| Max. 30*   | Max. 128  | Max. 10000      | 10 Gbit/s   | Stöds | Ja |

(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Storleks ändringen av VpnGw-SKU: er tillåts inom samma generation, förutom att ändra storlek på den grundläggande SKU: n. Den grundläggande SKU: n är en äldre SKU och har funktions begränsningar. För att kunna flytta från Basic till en annan VpnGw SKU måste du ta bort Basic SKU VPN-gatewayen och skapa en ny Gateway med önskad kombination av generation och SKU-storlek.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* I en enda tunnel kan högst 1 Gbit/s genom strömning uppnås. Benchmark för sammanställd data flöde i tabellen ovan baseras på mätningar av flera tunnlar som sammanställs via en enda Gateway. Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamåttet för aggregerat datagenomflöde inte garanterat genomströmning på grund av villkor för Internet-trafik och dina program.

För att hjälpa våra kunder att förstå de relativa prestandan för SKU: er som använder olika algoritmer använde vi allmänt tillgängliga iPerf-och CTSTraffic-verktyg för att mäta prestanda. Tabellen nedan visar resultatet av prestandatester för generation 1, VpnGw SKU: er. Som du kan se erhålls bästa prestanda när vi använde GCMAES256-algoritm för kryptering och integritet i både IPsec och integritet. Vi fick genomsnitts prestanda när du använder AES256 för IPsec-kryptering och SHA256 för integritet. Vi fick lägsta prestanda när vi använde DES3 för IPsec-kryptering och SHA256 för integritet.

|**Generation**|**SKU**   | **Använda algoritmer <br>** | **Data flöde som <br> observerats** | **Paket per sekund som <br> observerats** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbit/s<br>120 Mbit/s   | 58 000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbit/s<br>120 Mbit/s | 90 000<br>80 000<br>55 000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105 000<br>90 000<br>60 000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbit/s<br>120 Mbit/s   | 58 000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbit/s<br>120 Mbit/s | 90 000<br>80 000<br>55 000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105 000<br>90 000<br>60 000|
