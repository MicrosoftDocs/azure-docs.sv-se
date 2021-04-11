---
title: ta med fil
description: ta med fil
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 2262290486047c225cec8e8f6bb73242df7258fa
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106271511"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Nätverks gränser – Azure Resource Manager
Följande begränsningar gäller endast för nätverks resurser som hanteras via **Azure Resource Manager** per region per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vi har nyligen ökat alla standard gränser till sina maximala gränser. Om det inte finns någon övre gräns kolumn har inte resursen några justerbara gränser. Om du hade de här gränserna ökat med stöd tidigare och inte ser uppdaterade gränser i följande tabeller, öppnar du [en support förfrågan online för kunder utan kostnad](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Resurs | Gräns | 
| --- | --- |
| Virtuella nätverk |1 000 |
| Undernät per virtuellt nätverk |3 000 |
| Peering för virtuella nätverk per virtuellt nätverk |500 |
| [Virtuella nätverksgateway (VPN-gatewayer) per virtuellt nätverk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuella nätverksgateway (ExpressRoute-gatewayer) per virtuellt nätverk](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-servrar per virtuellt nätverk |20 |
| Privata IP-adresser per virtuellt nätverk |65 536 |
| Privata IP-adresser per nätverks gränssnitt |256 |
| Privata IP-adresser per virtuell dator |256 |
| Offentliga IP-adresser per nätverks gränssnitt |256 |
| Offentliga IP-adresser per virtuell dator |256 |
| [Samtidiga TCP-eller UDP-flöden per nätverkskort för en virtuell dator eller roll instans](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500 000 |
| Nätverks gränssnitts kort |65 536 |
| Nätverkssäkerhetsgrupper |5 000 |
| NSG-regler per NSG |1 000 |
| IP-adresser och intervall som har angetts för källa eller mål i en säkerhets grupp |4 000 |
| Programsäkerhetsgrupper |3 000 |
| Program säkerhets grupper per IP-konfiguration, per nätverkskort |20 |
| IP-konfigurationer per program säkerhets grupp |4 000 |
| Program säkerhets grupper som kan anges i alla säkerhets regler för en nätverks säkerhets grupp |100 |
| Användardefinierade routningstabeller |200 |
| Användardefinierade vägar per routningstabell |400 |
| Punkt-till-plats-rot certifikat per Azure-VPN Gateway |20 |
| Kranar för virtuellt nätverk |100 |
| Nätverks gränssnitt för att trycka på konfigurationer per virtuellt nätverk |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Begränsningar för offentliga IP-adresser
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser<sup>1</sup> | 10 för Basic. | Kontakta supporten. |
| Statiska offentliga IP-adresser<sup>1</sup> | 10 för Basic. | Kontakta supporten. |
| Offentliga standard IP-adresser<sup>1</sup> | 10 | Kontakta supporten. |
| [Offentliga IP-adresser per resurs grupp](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Kontakta supporten. | 
| Prefix till offentliga IP-adresser | begränsas av antalet offentliga standard-IP: er i en prenumeration | Kontakta supporten. |
| Prefixlängd för offentlig IP | /28 | Kontakta supporten. |

<sup>1</sup> Standard gränser för offentliga IP-adresser varierar beroende på kategori typ, till exempel kostnads fri utvärdering, betala per användning, CSP. Standardvärdet för Enterprise-avtal-prenumerationer är till exempel 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Gränser för belastnings utjämning
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Resurs                                | Gräns         |
|-----------------------------------------|-------------------------------|
| Lastbalanserare                          | 1 000                         |
| Regler (Load Balancer + inkommande NAT) per resurs                      | 1500                         |
| Regler per nätverkskort (över alla IP-adresser på ett nätverkskort) | 300                           |
| IP-konfigurationer för klient del              | 600                           |
| Storlek på serverdelspool                       | 1 000 IP-konfigurationer, enskilt virtuellt nätverk |
| Server dels resurser per Load Balancer <sup> 1<sup> | 1 200                   |
| Portar med hög tillgänglighet                 | 1 per intern klient del       |
| Utgående regler per Load Balancer        | 600                           |
| Belastnings utjämning per virtuell dator                   | 2 (1 offentlig och 1 intern)   |

<sup>1</sup> gränsen är upp till 1 200 resurser, i valfri kombination av fristående virtuella dator resurser, tillgänglighets uppsättnings resurser och placerings grupper för virtuella datorers skal uppsättning.

**Grundläggande Load Balancer**

| Resurs                                | Gräns        |
|-----------------------------------------|------------------------------|
| Lastbalanserare                          | 1 000                        |
| Regler per resurs                      | 250                          |
| Regler per nätverkskort (över alla IP-adresser på ett nätverkskort) | 300                          |
| IP-konfigurationer för klient del <sup> 2<sup>  | 200                          |
| Storlek på serverdelspool                       | 300 IP-konfigurationer, enskild tillgänglighets uppsättning |
| Tillgänglighets uppsättningar per Load Balancer     | 1                            |
| Belastnings utjämning per virtuell dator                   | 2 (1 offentlig och 1 intern)  |

<sup>2</sup> gränsen för en enskild, diskret resurs i en backend-pool (fristående virtuell dator, tillgänglighets uppsättning eller en placerings grupp för virtuell dators skal uppsättning) måste ha upp till 250 IP-konfigurationer för klient delen i en enskild grundläggande Load Balancer och basic Internal Load Balancer.

<a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverks resurser som hanteras via den **klassiska** distributions modellen per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |100 |100 |
| Lokala nätverksplatser |20 |50 |
| DNS-servrar per virtuellt nätverk |20 |20 |
| Privata IP-adresser per virtuellt nätverk |4 096 |4 096 |
| Samtidiga TCP-eller UDP-flöden per nätverkskort för en virtuell dator eller roll instans |500 000, upp till 1 000 000 för två eller flera nätverkskort. |500 000, upp till 1 000 000 för två eller flera nätverkskort. |
| Nätverks säkerhets grupper (NSG: er) |200 |200 |
| NSG-regler per NSG |200 |1 000 |
| Användardefinierade routningstabeller |200 |200 |
| Användardefinierade vägar per routningstabell |400 |400 |
| Offentliga IP-adresser (dynamiska) |500 |500 |
| Reserverade offentliga IP-adresser |500 |500 |
| Offentlig IP-adress per distribution |5 |Kontakta supporten |
| Privat IP (intern belastnings utjämning) per distribution |1 |1 |
| Åtkomst kontrol listor för slut punkt (ACL) |50 |50 |
