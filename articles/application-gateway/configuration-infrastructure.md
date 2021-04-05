---
title: Konfiguration av Azure Application Gateway-infrastruktur
description: I den här artikeln beskrivs hur du konfigurerar infrastrukturen för Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397628"
---
# <a name="application-gateway-infrastructure-configuration"></a>Application Gateway infrastruktur konfiguration

Infrastrukturen för programgatewayen innehåller det virtuella nätverket, undernät, nätverks säkerhets grupper och användardefinierade vägar.

## <a name="virtual-network-and-dedicated-subnet"></a>Virtuellt nätverk och dedikerat undernät

En Application Gateway är en dedikerad distribution i det virtuella nätverket. I det virtuella nätverket krävs ett dedikerat undernät för Application Gateway. Du kan ha flera instanser av en specifik Programgateway-distribution i ett undernät. Du kan också distribuera andra programgatewayer i under nätet. Men du kan inte distribuera någon annan resurs i Application Gateway-undernätet. Du kan inte mixa Standard_v2 och standard Azure Application Gateway i samma undernät.

> [!NOTE]
> [Slut punkts principer för virtuella nätverk](../virtual-network/virtual-network-service-endpoint-policies-overview.md) stöds för närvarande inte i ett Application Gateway-undernät.

### <a name="size-of-the-subnet"></a>Storlek på under nätet

Application Gateway använder en privat IP-adress per instans, plus en annan privat IP-adress om en privat klient-IP-adress har kon figurer ATS.

Azure reserverar också fem IP-adresser i varje undernät för internt bruk: de första fyra och sista IP-adresserna. Överväg till exempel 15 Application Gateway-instanser utan privat frontend-IP. Du behöver minst 20 IP-adresser för det här under nätet: fem för intern användning och 15 för Application Gateway-instanserna.

Överväg ett undernät som har 27 Application Gateway-instanser och en IP-adress för en privat klient dels-IP. I det här fallet behöver du 33 IP-adresser: 27 för Application Gateway-instanserna, en för den privata klient delen och fem för internt bruk.

Application Gateway-SKU: n (standard eller WAF) har stöd för upp till 32 instanser (32 instans-IP-adresser + 1 privat frontend IP + 5 Azure reserverad) – så en minsta under näts storlek på/26 rekommenderas

Application Gateway (Standard_v2 eller WAF_v2 SKU) har stöd för upp till 125 instanser (125 instans-IP-adresser + 1 privat frontend IP + 5 Azure reserverad) – så en minsta under näts storlek på/24 rekommenderas

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Nätverks säkerhets grupper (NSG: er) stöds på Application Gateway. Men det finns vissa begränsningar:

- Du måste tillåta inkommande Internet trafik på TCP-portarna 65503-65534 för Application Gateway v1 SKU och TCP-portarna 65200-65535 för v2-SKU: n med mål under nätet som **valfri** och källa som **GatewayManager** service tag. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter, inklusive kunder av dessa gateways, kan inte kommunicera med dessa slut punkter.

- Det går inte att blockera utgående Internet anslutning. Standard regler för utgående trafik i NSG tillåter Internet anslutning. Vi rekommenderar att du gör följande:

  - Ta inte bort standard reglerna för utgående trafik.
  - Skapa inte andra utgående regler som nekar utgående anslutningar.

- Trafik från **AzureLoadBalancer** -taggen med mål under **nätet måste vara** tillåten.

### <a name="allow-access-to-a-few-source-ips"></a>Tillåt åtkomst till några käll-IP: er

I det här scenariot använder du NSG: er i under nätet Application Gateway. Lägg till följande begränsningar i under nätet i prioritetsordning:

1. Tillåt inkommande trafik från en käll-IP-adress eller ett IP-intervall med målet som hela Application Gateway under nätets adress intervall och målport som din port för inkommande åtkomst, till exempel port 80 för HTTP-åtkomst.
2. Tillåt inkommande begär Anden från källan som **GatewayManager** service tag och destination som **alla** och mål portar som 65503-65534 för Application Gateway v1 sku och portarna 65200-65535 för v2 SKU för [kommunikation med backend-hälsostatus](./application-gateway-diagnostics.md). Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter kan inte initiera ändringar på dessa slut punkter utan lämpliga certifikat på plats.
3. Tillåt inkommande Azure Load Balancer-avsökningar (*AzureLoadBalancer* -tagg) och inkommande virtuell nätverks trafik (*VirtualNetwork* -tagg) i [nätverks säkerhets gruppen](../virtual-network/network-security-groups-overview.md).
4. Blockera all annan inkommande trafik med hjälp av en regel för neka-alla.
5. Tillåt utgående trafik till Internet för alla destinationer.

## <a name="supported-user-defined-routes"></a>Användardefinierade vägar som stöds 

> [!IMPORTANT]
> Om du använder UDR på Application Gateway under nätet kan hälso tillståndet i [hälso läget för Server delen](./application-gateway-diagnostics.md#back-end-health) visas som **Okänt**. Det kan också leda till att Application Gateway loggar och mät värden Miss fungerar. Vi rekommenderar att du inte använder UDR på Application Gateway under nätet så att du kan visa backend-hälsa, loggar och mått.

- **v1**

   För v1-SKU: n (UDR) stöds de användardefinierade vägarna i Application Gateway under nätet, så länge de inte ändrar svars-/svars kommunikation från slut punkt till slut punkt. Du kan till exempel konfigurera en UDR i Application Gateway under nätet så att den pekar på en brand Väggs enhet för paket granskning. Men du måste se till att paketet når sitt avsedda mål efter inspektion. Om du inte gör det kan det leda till felaktig hälso avsökning eller trafik-routning. Detta inkluderar inlärda vägar eller standard 0.0.0.0/0-vägar som sprids av Azure ExpressRoute eller VPN-gatewayer i det virtuella nätverket. Alla scenarier där 0.0.0.0/0 måste omdirigeras lokalt (Tvingad tunnel trafik) stöds inte för v1.

- **2**

   För v2-SKU: n finns det stöd för och scenarier som inte stöds:

   **scenarier som stöds i v2**
   > [!WARNING]
   > En felaktig konfiguration av väg tabellen kan resultera i asymmetrisk routning i Application Gateway v2. Se till att all hanterings-och kontroll Plans trafik skickas direkt till Internet och inte via en virtuell installation. Loggning och mått kan också påverkas.


  **Scenario 1**: UDR för att inaktivera Border Gateway Protocol (BGP)-spridning av vägar till Application Gateway-undernätet

   Ibland annonseras standard-gateway-vägen (0.0.0.0/0) via de ExpressRoute eller VPN-gatewayer som är associerade med Application Gateway virtuella nätverket. Detta bryter hanterings Plans trafik, som kräver en direkt sökväg till Internet. I sådana fall kan en UDR användas för att inaktivera spridning av BGP-vägar. 

   Använd följande steg för att inaktivera instabilitetsdämpning av BGP-vägar:

   1. Skapa en väg tabell resurs i Azure.
   2. Inaktivera parametern för **väg spridning av virtuell nätverks-Gateway** . 
   3. Koppla routningstabellen till lämpligt undernät. 

   Om du aktiverar UDR för det här scenariot bör du inte bryta eventuella befintliga inställningar.

  **Scenario 2**: UDR till Direct 0.0.0.0/0 till Internet

   Du kan skapa en UDR för att skicka 0.0.0.0/0-trafik direkt till Internet. 

  **Scenario 3**: UDR för Azure Kubernetes service med Kubernetes

  Om du använder Kubernetes med Azure Kubernetes service (AKS) och Application Gateway ingress (AGIC) behöver du en routningstabell för att tillåta trafik som skickas till poddar från Application Gateway att dirigeras till rätt nod. Detta behövs inte om du använder Azure-CNI. 

  Följ stegen nedan om du vill använda routningstabellen för att Kubernetes ska fungera:

  1. Gå till resurs gruppen som skapats av AKS (namnet på resurs gruppen måste börja med "MC_")
  2. Hitta routningstabellen som skapats av AKS i den resurs gruppen. Routningstabellen ska vara ifylld med följande information:
     - Adressprefixet ska vara IP-intervallet för de poddar som du vill uppnå i AKS. 
     - Nästa hopp typ ska vara virtuell installation. 
     - Nästa hopp adress ska vara IP-adressen för noden som är värd för poddar.
  3. Koppla den här routningstabellen till Application Gateway under nätet. 
    
  **scenarier som inte stöds i v2**

  **Scenario 1**: UDR för virtuella enheter

  Alla scenarier där 0.0.0.0/0 behöver omdirigeras via en virtuell installation, ett virtuellt nätverk för hubb/eker eller lokalt (Tvingad tunnel trafik) stöds inte för v2.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om konfiguration av klient delens IP-adress](configuration-front-end-ip.md).