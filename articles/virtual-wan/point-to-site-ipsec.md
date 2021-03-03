---
title: IPsec-principer för virtuella WAN-platser
titleSuffix: Azure Virtual WAN
description: Lär dig mer om anslutnings principer för virtuella WAN-platser i Azure.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746985"
---
# <a name="point-to-site-ipsec-policies"></a>Punkt-till-plats-IPsec-principer

Den här artikeln visar de kombinationer av IPsec-principer som stöds för punkt-till-plats-VPN-anslutning i Azure Virtual WAN.

## <a name="default-ipsec-policies"></a>Standard-IPsec-principer

I följande tabell visas standard-IPsec-parametrarna för punkt-till-plats-VPN-anslutningar. Dessa parametrar väljs automatiskt av VPN-gatewayen för virtuella WAN-platser när en punkt-till-plats-profil skapas.

| Inställning | Parametrar |
|--- |--- |
| Fas 1 IKE-kryptering | AES256 |
| Fas 1 IKE-integritet |  SHA256 |
| DH-grupp | DHGroup24 |
| Fas 2 IPsec-kryptering | GCMAES256|
| Fas 2 IPsec-integritet | GCMAES25 |
| PFS-grupp |PFS24|

## <a name="custom-ipsec-policies"></a>Anpassade IPsec-principer

Tänk på följande när du arbetar med anpassade IPsec-principer:

* **IKE** – för fas 1 IKE kan du välja vilken parameter som helst från IKE-kryptering, plus vilken parameter som helst från IKE-integritet, samt vilken parameter som helst från DH-gruppen.
* **IPSec** – för fas 2 IPSec kan du välja vilken parameter som helst från IPSec-kryptering, plus vilken parameter som helst från IPSec-integritet, plus PFS. Om någon av parametrarna för IPsec-kryptering eller IPsec-integritet är GCM måste både IPsec-kryptering och integritet använda samma algoritm. Om GCMAES128 t. ex. är valt för IPsec-kryptering måste GCMAES128 också väljas för IPsec-integritet.  

I följande tabell visas de tillgängliga IPsec-parametrarna för punkt-till-plats-VPN-anslutningar.

| Inställning | Parametrar |
|--- |--- |
| Fas 1 IKE-kryptering | AES128, AES256, GCMAES128, GCMAES256 |
| Fas 1 IKE-integritet |  SHA256, SHA384 |
| DH-grupp | DHGroup14, DHGroup24, ECP256, ECP384 |
| Fas 2 IPsec-kryptering | GCMAES128, GCMAES256, SHA256|
| Fas 2 IPsec-integritet | GCMAES128, GCMAES256 |
| PFS-grupp |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Nästa steg

Se [så här skapar du en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md)

Mer information om virtuellt WAN finns i [vanliga frågor och svar om virtuella](virtual-wan-faq.md)WAN.
