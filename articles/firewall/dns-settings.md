---
title: DNS-inställningar för Azure-brandväggen
description: Du kan konfigurera Azure-brandväggen med inställningar för DNS-server och DNS-proxy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/04/2021
ms.author: victorh
ms.openlocfilehash: 855c06b610fb8166f6f2dfcf37af34efb3713ffe
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883234"
---
# <a name="azure-firewall-dns-settings"></a>DNS-inställningar för Azure-brandväggen

Du kan konfigurera en anpassad DNS-server och aktivera DNS-proxy för Azure-brandvägg. Konfigurera de här inställningarna när du distribuerar brand väggen eller konfigurera dem senare på sidan **DNS-inställningar** .

## <a name="dns-servers"></a>DNS-servrar

En DNS-Server upprätthåller och löser domän namn till IP-adresser. Som standard använder Azure-brandväggen Azure DNS för namn matchning. Med **DNS-serverns** inställning kan du konfigurera dina egna DNS-servrar för namn matchning av Azure-brandvägg. Du kan konfigurera en enskild server eller flera servrar.

> [!NOTE]
> För instanser av Azure-brandväggen som hanteras med hjälp av Azure Firewall Manager konfigureras DNS-inställningarna i den associerade Azure Firewall-principen.

### <a name="configure-custom-dns-servers---azure-portal"></a>Konfigurera anpassade DNS-servrar – Azure Portal

1. Under **Inställningar** för Azure-brandvägg väljer du **DNS-inställningar**.
2. Under **DNS-servrar** kan du skriva eller lägga till befintliga DNS-servrar som tidigare har angetts i det virtuella nätverket.
3. Välj **Spara**.

Brand väggen dirigerar nu DNS-trafik till de angivna DNS-servrarna för namn matchning.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Skärm bild som visar inställningar för D N S-servrar.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Konfigurera anpassade DNS-servrar – Azure CLI

I följande exempel uppdateras Azure-brandväggen med anpassade DNS-servrar med hjälp av Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Kommandot `az network firewall` kräver att Azure CLI-tillägget `azure-firewall` är installerat. Du kan installera det med hjälp av kommandot `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Konfigurera anpassade DNS-servrar – Azure PowerShell

I följande exempel uppdateras Azure-brandväggen med anpassade DNS-servrar med hjälp av Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS-proxy

Du kan konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy. En DNS-proxy är en mellanhand för DNS-begäranden från klientens virtuella datorer till en DNS-server. Om du konfigurerar en anpassad DNS-server aktiverar du DNS-proxyn för att undvika matchning av DNS-matchning och aktiverar FQDN (fullständigt kvalificerat domän namn) i nätverks reglerna.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="D N S proxy-konfiguration med hjälp av en anpassad D N S-server.":::


Om du inte aktiverar DNS-proxy kan DNS-begäranden från klienten skickas till en DNS-Server vid en annan tidpunkt eller returnera ett annat svar jämfört med den i brand väggen. DNS-proxy placerar Azure-brandväggen i sökvägen för klient begär Anden för att undvika inkonsekvens.

När Azure-brandväggen är en DNS-proxy är två typer av caching-funktioner möjliga:

- **Positivt cacheminne**: DNS-matchningen har slutförts. Brand väggen använder TTL-värdet (Time to Live) för paketet eller objektet. 

- **Negativ cache**: DNS-matchning resulterar inte i något svar eller ingen upplösning. Brand väggen cachelagrar den här informationen i en timme.

DNS-proxyn lagrar alla matchade IP-adresser från FQDN: er i nätverks regler. Vi rekommenderar att du använder FQDN: er som matchar en IP-adress.  

### <a name="dns-proxy-configuration"></a>Konfiguration av DNS-proxy

Konfiguration av DNS-proxy kräver tre steg:
1. Aktivera DNS-proxyn i Azure Firewall DNS-inställningar.
2. Du kan också konfigurera en anpassad DNS-server eller använda standard alternativet.
3. Konfigurera Azure firewalls privata IP-adress som en anpassad DNS-adress i inställningarna för DNS-servern för det virtuella nätverket. Den här inställningen säkerställer att DNS-trafik dirigeras till Azure-brandväggen.

#### <a name="configure-dns-proxy---azure-portal"></a>Konfigurera DNS-proxy – Azure Portal

Om du vill konfigurera DNS-proxy måste du konfigurera inställningen för DNS-servrar för virtuellt nätverk så att den använder brand väggens privata IP-adress. Aktivera DNS-proxyn i Azure-brand väggens **DNS-inställningar**.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar 

1. Välj det virtuella nätverk där DNS-trafiken ska dirigeras via Azure Firewall-instansen.
2. Under **Inställningar** väljer du **DNS-servrar**.
3. Under **DNS-servrar** väljer du **anpassad**.
4. Ange brand väggens privata IP-adress.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket så att de är kopplade till de nya DNS-serverinställningarna. De virtuella datorerna fortsätter att använda sina aktuella DNS-inställningar tills de har startats om.

##### <a name="enable-dns-proxy"></a>Aktivera DNS-proxy

1. Välj din Azure Firewall-instans.
2. Under **Inställningar** väljer du **DNS-inställningar**.
3. Som standard är **DNS-proxy** inaktive rad. När den här inställningen är aktive rad, lyssnar brand väggen på port 53 och vidarebefordrar DNS-begäranden till de konfigurerade DNS-servrarna.
4. Granska konfigurationen av **DNS-servrarna** för att se till att inställningarna är lämpliga för din miljö.
5. Välj **Spara**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Skärm bild som visar inställningarna för D N S proxy.":::

#### <a name="configure-dns-proxy---azure-cli"></a>Konfigurera DNS-proxy – Azure CLI

Du kan använda Azure CLI för att konfigurera DNS-proxyinställningar i Azure-brandväggen. Du kan också använda den för att uppdatera virtuella nätverk för att använda Azure-brandväggen som DNS-server.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

I följande exempel konfigureras det virtuella nätverket för att använda Azure-brandväggen som DNS-server.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Aktivera DNS-proxy

I följande exempel aktive ras funktionen DNS-proxy i Azure-brandväggen.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Konfigurera DNS-proxy – Azure PowerShell

Du kan använda Azure PowerShell för att konfigurera inställningar för DNS-proxy i Azure-brandväggen. Du kan också använda den för att uppdatera virtuella nätverk för att använda Azure-brandväggen som DNS-server.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurera virtuella nätverks-DNS-servrar

I följande exempel konfigureras det virtuella nätverket för att använda Azure-brandväggen som en DNS-server.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Aktivera DNS-proxy

I följande exempel aktive ras funktionen DNS-proxy i Azure-brandväggen.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Nästa steg

[FQDN-filtrering i nätverks regler](fqdn-filtering-network-rules.md)
