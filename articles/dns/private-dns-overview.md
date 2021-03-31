---
title: Vad är Azure Privat DNS?
description: I den här artikeln kom igång med en översikt över den privata DNS-värdservern på Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87985639"
---
# <a name="what-is-azure-private-dns"></a>Vad är Azure Privat DNS?

Domain Name System eller DNS ansvarar för översättning (eller matchning av) ett tjänst namn till dess IP-adress.  Azure DNS är en värd tjänst för DNS-domäner som ger namn matchning med hjälp av Microsoft Azure-infrastrukturen. Förutom att stöda DNS-domäner med stöd för Internet, stöder Azure DNS även privata DNS-zoner.

Azure Privat DNS ger en tillförlitlig och säker DNS-tjänst för att hantera och lösa domän namn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Genom att använda privata DNS-zoner kan du använda dina egna anpassade domän namn istället för de namn som tillhandahålls av Azure som är tillgängliga idag. Med hjälp av anpassade domän namn kan du skräddarsy din virtuella nätverks arkitektur så att den passar din organisations behov. Den ger namn matchning för virtuella datorer i ett virtuellt nätverk och mellan virtuella nätverk. Dessutom kan du konfigurera zonernas namn med vyn delat visnings horisont, vilket gör att en privat och en offentlig DNS-zon kan dela namnet.

Om du vill matcha poster i en privat DNS-zon från det virtuella nätverket måste du länka det virtuella nätverket till zonen. Länkade virtuella nätverk har fullständig åtkomst och kan matcha alla DNS-poster som publiceras i den privata zonen. Dessutom kan du aktivera autoregistrering på en länk till ett virtuellt nätverk. Om du aktiverar autoregistrering på en virtuell nätverks länk registreras DNS-posterna för de virtuella datorerna i det virtuella nätverket i den privata zonen. När autoregistrering har Aktiver ATS uppdaterar Azure DNS även zon posterna när en virtuell dator skapas, ändrar dess IP-adress eller raderas.

![DNS-översikt](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Vi rekommenderar att du inte använder en *. lokal* domän för din privata DNS-zon. Det är inte alla operativ system som har stöd för detta.

## <a name="benefits"></a>Fördelar

Azure Privat DNS ger följande fördelar:

* **Tar bort behovet av anpassade DNS-lösningar**. Tidigare skapade många kunder anpassade DNS-lösningar för att hantera DNS-zoner i sina virtuella nätverk. Nu kan du hantera DNS-zoner med hjälp av den interna Azure-infrastrukturen, vilket innebär att du slipper skapa och hantera anpassade DNS-lösningar.

* **Använd alla vanliga typer av DNS-poster**. Azure DNS stöder posterna A, AAAA, CNAME, MX, PTR, SOA, SRV och TXT.

* **Automatisk hantering av värdnamn poster**. Tillsammans med dina anpassade DNS-poster upprätthåller Azure automatiskt värd namns poster för de virtuella datorerna i de angivna virtuella nätverken. I det här scenariot kan du optimera de domän namn som du använder utan att behöva skapa anpassade DNS-lösningar eller ändra program.

* **Matchning av värdnamn mellan virtuella nätverk**. Till skillnad från Azure-tillhandahållna värdnamn kan privata DNS-zoner delas mellan virtuella nätverk. Den här funktionen fören klar scenarier mellan nätverk och tjänst identifiering, till exempel virtuell nätverks-peering.

* **Välbekanta verktyg och användar upplevelser**. För att minska inlärnings kurvan använder tjänsten väletablerad Azure DNS verktyg (Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager mallar och REST API).

* **DNS-support med delad horisont**. Med Azure DNS kan du skapa zoner med samma namn som matchar olika svar inifrån ett virtuellt nätverk och från det offentliga Internet. Ett typiskt scenario för DNS med delad horisont är att tillhandahålla en dedikerad version av en tjänst som kan användas i det virtuella nätverket.

* **Tillgängligt i alla Azure-regioner**. Funktionen Azure DNS privata zoner är tillgänglig i alla Azure-regioner i det offentliga Azure-molnet.

## <a name="capabilities"></a>Funktioner

Azure DNS tillhandahåller följande funktioner:

* **Automatisk registrering av virtuella datorer från ett virtuellt nätverk som är länkat till en privat zon med automatisk registrering aktive rad**. De virtuella datorerna registreras (läggs till) i den privata zonen som en post som pekar på deras privata IP-adresser. När en virtuell dator i en virtuell nätverks länk med automatisk registrering aktive rad tas bort, så tar Azure DNS också automatiskt bort motsvarande DNS-post från den länkade privata zonen.

* **Vidarebefordran av DNS-matchning stöds i virtuella nätverk som är länkade till den privata zonen**. För DNS-matchning över virtuella nätverk finns det inget uttryckligt beroende, så att de virtuella nätverken är peer-kopplade med varandra. Du kanske vill använda peer virtuella nätverk för andra scenarier (till exempel HTTP-trafik).

* **Omvänd DNS-sökning stöds inom det virtuella nätverkets omfång**. Omvänd DNS-sökning för en privat IP-adress i det virtuella nätverk som är kopplat till en privat zon returnerar det fullständiga domän namnet som innehåller värd/postnamn och zonnamn som suffix.

## <a name="other-considerations"></a>Ytterligare överväganden

Azure DNS har följande begränsningar:

* Ett enskilt virtuellt nätverk kan bara länkas till en privat zon om automatisk registrering av VM DNS-poster är aktiverat. Du kan dock länka flera virtuella nätverk till en enda DNS-zon.
* Omvänd DNS fungerar endast för privat IP-utrymme i det länkade virtuella nätverket
* Omvänd DNS för en privat IP-adress för ett länkat virtuellt nätverk returnerar *Internal.cloudapp.net* som standard suffix för den virtuella datorn. För virtuella nätverk som är länkade till en privat zon med autoregistrering aktive rad returnerar omvänd DNS för en privat IP-adress två FQDN: en med standard suffixet *Internal.cloudapp.net* och ett annat med suffixet för den privata zonen.
* Villkorlig vidarebefordran stöds inte för närvarande. Information om hur du aktiverar matchning mellan Azure och lokala nätverk finns i [namn matchning för virtuella datorer och roll instanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Priser

Information om priser finns i [Azure DNS priser](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika funktioner som kan förväntas för vissa typer av åtgärder, finns i [privat DNS vanliga frågor](./dns-faq-private.md)och svar.

* Lär dig mer om DNS-zoner och poster genom att gå till [Översikt över DNS-zoner och-poster](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
