---
title: ta med fil
description: ta med fil
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919083"
---
Det här avsnittet beskriver rikt linjer och krav för att allokera klient adress utrymmen där det valda virtuella WAN Hub-VPN Gateway skalnings enhet är större än eller lika med 40.

### <a name="background"></a>Bakgrund

Punkt-till-plats-VPN-gatewayer i den virtuella WAN-hubben distribueras med flera instanser. Varje instans av en punkt-till-plats-VPN-gateway har stöd för upp till 10 000 samtidiga punkt-till-plats-användar anslutningar. Som ett resultat för skalnings enheter som är större än 40 måste det virtuella WAN-nätverket distribuera extra kapacitet, vilket kräver ett minsta antal adresspooler som har allokerats för olika skalnings enheter.

Om till exempel en skalnings enhet på 100 väljs, distribueras 5 instanser för punkt-till-plats-VPN Gateway i virtuell hubb. Den här distributionen kan stödja 50 000 samtidiga anslutningar och **minst** 5 distinkta adresspooler.

**Tillgängliga skalnings enheter**

| Skalnings enhet | Högsta antal klienter som stöds | Minsta antal adresspooler |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Ange adresspooler

Nedan följer några rikt linjer för att välja adresspooler. Observera att tilldelningar av VPN-adresspoolen för punkt-till-plats görs automatiskt av Virtual WAN.

1. En gateway-instans tillåter högst 10 000 samtidiga anslutningar. Därför bör varje adresspool innehålla minst 10 000 unika RFC1918-IP-adresser.
1. Flera intervall för adresspoolen kombineras automatiskt och tilldelas till en **enda** Gateway-instans. Den här processen görs i ett Round-Robin-sätt för alla gateway-instanser som har färre än 10 000 IP-adresser. En pool med 5 000-adresser kan till exempel kombineras automatiskt av virtuella WAN-nätverk med en annan pool med 8 000 adresser och tilldelas till en enda Gateway-instans.
1. En enda adresspool tilldelas bara till en enda Gateway-instans av virtuellt WAN.
1. Adresspooler måste vara distinkta. Det får inte finnas några överlappningar mellan adresspooler.

> [!NOTE] 
> Om en adresspool är kopplad till en gateway-instans som underhålls av underhållet, kan inte adresspoolen tilldelas en annan instans igen.

### <a name="example"></a>Exempel 

I följande exempel beskrivs en situation där 60 skalnings enheter har stöd för upp till 30 000 anslutningar men allokerade adresspooler resulterar i färre än 30 000 samtidiga anslutningar.

Det totala antalet samtidiga anslutningar som stöds i den här installationen är 28 192. Den första Gateway-instansen stöder 10 000-adresser, den andra instansen 8 192-anslutningar och den tredje instansen stöder också 10 000-adresser.

| Nummer för adresspool | Adresspool | Anslutningar som stöds |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**Rekommendation #1: se till att adresspoolen #2 har minst 10 000 distinkta IP-adresser. (exempel: 10.13.0.0/15)**

**Rekommendations #2: Lägg till ytterligare en adresspool. (exempel: adresspoolen #4 10.15.0.0/21 med 2048 adresser). Adresspooler 2 och 4 kombineras automatiskt och gör att Gateway-instansen stöder 10 000 samtidiga anslutningar.**
