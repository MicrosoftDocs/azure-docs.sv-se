---
title: DNS-inställningar för Azure Firewall policy
description: Du kan konfigurera Azure Firewall-principer med DNS-server och DNS-proxyinställningar.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633656"
---
# <a name="azure-firewall-policy-dns-settings"></a>DNS-inställningar för Azure Firewall policy

Du kan konfigurera en anpassad DNS-server och aktivera DNS-proxy för Azure brand Väggs principer. Du kan konfigurera de här inställningarna när du distribuerar brand väggen eller senare från sidan **DNS-inställningar** .

## <a name="dns-servers"></a>DNS-servrar

En DNS-Server upprätthåller och löser domän namn till IP-adresser. Som standard använder Azure-brandväggen Azure DNS för namn matchning. Med **DNS-serverns** inställning kan du konfigurera dina egna DNS-servrar för namn matchning av Azure-brandvägg. Du kan konfigurera en enda eller flera servrar.

### <a name="configure-custom-dns-servers"></a>Konfigurera anpassade DNS-servrar

1. Välj brand Väggs princip.
2. Under **Inställningar** väljer du **DNS-inställningar**.
3. Under **DNS-servrar** kan du skriva eller lägga till befintliga DNS-servrar som tidigare har angetts i Virtual Network.
4. Välj **Spara**.
5. Brand väggen dirigerar nu DNS-trafik till de angivna DNS-servrarna för namn matchning.

## <a name="dns-proxy"></a>DNS-proxy

Du kan konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy. En DNS-proxy fungerar som en mellanhand för DNS-begäranden från klientens virtuella datorer till en DNS-server. Om du konfigurerar en anpassad DNS-server bör du aktivera DNS-proxy för att undvika matchning av DNS-matchning och aktivera FQDN-filtrering i nätverks regler.

Om du inte aktiverar DNS-proxy kan DNS-begäranden från klienten skickas till en DNS-Server vid en annan tidpunkt eller returnera ett annat svar jämfört med den i brand väggen. DNS-proxy placerar Azure-brandväggen i sökvägen för klient begär Anden för att undvika inkonsekvens.

Konfiguration av DNS-proxy kräver tre steg:

1. Aktivera DNS-proxy i Azure Firewall DNS-inställningar.
2. Om du vill kan du konfigurera din anpassade DNS-server eller använda det angivna standardvärdet.
3. Slutligen måste du konfigurera Azure firewalls privata IP-adress som en anpassad DNS-adress i DNS-serverinställningar för det virtuella nätverket. Detta säkerställer att DNS-trafik dirigeras till Azure-brandväggen.

### <a name="configure-dns-proxy"></a>Konfigurera DNS-proxy

Om du vill konfigurera DNS-proxy måste du konfigurera inställningen för DNS-servrar för virtuellt nätverk så att den använder brand väggens privata IP-adress. Aktivera DNS-proxy i Azure brand Väggs princip **DNS-inställningar**.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

1. Välj det virtuella nätverk där DNS-trafiken ska dirigeras via Azure-brandväggen.
2. Under **Inställningar** väljer du **DNS-servrar**.
3. Välj **anpassad** under **DNS-servrar**.
4. Ange brand väggens privata IP-adress.
5. Välj **Spara**.

#### <a name="enable-dns-proxy"></a>Aktivera DNS-proxy

1. Välj din Azure Firewall-princip.
2. Under **Inställningar** väljer du **DNS-inställningar**.
3. Som standard är **DNS-proxy** inaktive rad. När den är aktive rad lyssnar brand väggen på port 53 och vidarebefordrar DNS-begäranden till de konfigurerade DNS-servrarna.
4. Granska konfigurationen av **DNS-servrarna** för att se till att inställningarna är lämpliga för din miljö.
5. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[FQDN-filtrering i nätverks regler](fqdn-filtering-network-rules.md)