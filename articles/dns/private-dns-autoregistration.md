---
title: Vad är funktionen för autoregistrering i Azure DNS privata zoner
description: Översikt över funktionen för autoregistrering i Azure DNS privata zoner
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 2e5f41c0e149c99b5524c439c59e72afe554c776
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783905"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Vad är funktionen för autoregistrering i Azure DNS privata zoner

Funktionen för automatisk registrering i Azure DNS privata zoner tar hand om hantering av DNS-poster för virtuella datorer som distribueras i ett virtuellt nätverk. När du [länkar ett virtuellt nätverk](./private-dns-virtual-network-links.md) med en privat DNS-zon och aktiverar automatisk registrering för alla virtuella datorer, skapas DNS-posterna för de virtuella datorer som distribueras i det virtuella nätverket automatiskt i den privata DNS-zonen. Förutom att vidarebefordra Sök poster (A-poster) skapas också poster för omvänd sökning (PTR-poster) automatiskt för de virtuella datorerna.
Om du lägger till fler virtuella datorer i det virtuella nätverket skapas även DNS-poster för de virtuella datorerna automatiskt i den länkade privata DNS-zonen.

När du tar bort en virtuell dator raderas DNS-posterna för den virtuella datorn automatiskt från den privata DNS-zonen.

Du kan aktivera automatisk registrering genom att välja alternativet Aktivera automatisk registrering när du skapar en länk till ett virtuellt nätverk.

![Aktivera automatisk registrering](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Begränsningar

* Autoregistrering fungerar bara för virtuella datorer. För alla andra resurser som interna belastningsutjämnare osv. kan du skapa DNS-poster manuellt i den privata DNS-zon som är länkad till det virtuella nätverket.
* DNS-poster skapas automatiskt för den primära virtuella datorns nätverkskort. Om dina virtuella datorer har mer än ett nätverkskort kan du manuellt skapa DNS-poster för andra nätverks gränssnitt.
* DNS-poster skapas automatiskt endast om den primära virtuella datorns nätverkskort använder DHCP. Om statiska IP-adresser har kon figurer ATS (till exempel för att använda [flera IP-adresser i Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)) skapas inga poster för den virtuella datorn med autoregistrering.
* Autoregistrering för IPv6 (AAAA-poster) stöds inte.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika funktioner som kan förväntas för vissa typer av åtgärder, finns i [privat DNS vanliga frågor](./dns-faq-private.md)och svar.
