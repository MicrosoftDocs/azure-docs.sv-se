---
title: Azure VMware-lösning genom CloudSimple – sträck ut ett Layer 2-nätverk lokalt till ett privat moln
description: Beskriver hur du konfigurerar ett Layer 2 VPN mellan NSX-T i ett CloudSimple-privat moln och en lokal fristående NSX Edge-klient
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06446b6c36e36466fe891d7327d8151603cdecd2
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899379"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrera arbetsbelastningar med hjälp av stretchade Layer 2-nätverk

I den här guiden får du lära dig hur du använder Layer 2 VPN (L2VPN) för att sträcka ut ett nätverk på nivå 2 från din lokala miljö till ditt CloudSimple-privata moln. Med den här lösningen kan du migrera arbets belastningar som körs i din lokala VMware-miljö till det privata molnet i Azure inom samma adress utrymme för under nätet utan att behöva IP-adresser för arbets belastningen.

L2VPN-baserad utsträckning av Layer 2-nätverk kan fungera med eller utan NSX nätverk i din lokala VMware-miljö. Om du inte har NSX-baserade nätverk för arbets belastningar lokalt kan du använda en fristående NSX Edge Services-Gateway.

> [!NOTE]
> Den här guiden beskriver scenariot där lokala och privata moln Data Center är anslutna via plats-till-plats-VPN.

## <a name="deployment-scenario"></a>Distributionsscenario

För att sträcka ut ditt lokala nätverk med hjälp av L2VPN, måste du konfigurera en L2VPN-Server (målet NSX-T Tier0 router) och en L2VPN-klient (fristående källa).  

I det här distributions scenariot är ditt privata moln anslutet till din lokala miljö via en plats-till-plats-VPN-tunnel som gör det möjligt för lokala hanterings-och vMotion-undernät att kommunicera med de privata moln hanterings-och vMotion-undernätet. Den här ordningen är nödvändig för Cross vCenter vMotion (xVC-vMotion). En NSX-T Tier0-router distribueras som en L2VPN-server i det privata molnet.

Fristående NSX-Edge distribueras i din lokala miljö som en L2VPN-klient och sedan kopplas till L2VPN-servern. En GRE-tunnel slut punkt skapas på varje sida och konfigureras för att sträcka ut det lokala nivå 2-nätverket till ditt privata moln. Den här konfigurationen illustreras i följande bild.

![Distributionsscenario](media/l2vpn-deployment-scenario.png)

Om du vill veta mer om migrering med hjälp av L2 VPN, se [virtuella privata nätverk](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) i VMware-dokumentationen.

## <a name="prerequisites-for-deploying-the-solution"></a>Krav för att distribuera lösningen

Kontrol lera att följande är på plats innan du distribuerar och konfigurerar lösningen:

* Den lokala vSphere-versionen är 6,7 U1 + eller 6.5 P03 +.
* Den lokala vSphere-licensen finns på Enterprise Plus-nivå (för vSphere-distribuerad växel).
* Identifiera arbets belastnings skikt 2-nätverket som ska sträckas till ditt privata moln.
* Identifiera ett Layer 2-nätverk i din lokala miljö för att distribuera L2VPN-klientprogramvaran.
* [Ett privat moln har redan skapats](create-private-cloud.md).
* Versionen av den fristående NSX-T Edge-enheten är kompatibel med NSX-T Manager-versionen (NSX-T 2.3.0) som används i din privata moln miljö.
* En segment grupp för trunkering har skapats i den lokala vCenteren med förfalskade överföringar aktiverade.
* En offentlig IP-adress har reserver ATS för att användas för NSX-T fristående klientens IP-adress för överordnad länk och 1:1 NAT finns för översättning mellan de två adresserna.
* DNS-vidarebefordran ställs in på lokala DNS-servrar för az.cloudsimple.io-domänen så att de pekar på de privata molnets DNS-servrar.
* Svars tids fördröjningen är mindre än eller lika med 150 ms, vilket krävs för att vMotion ska fungera på de två platserna.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

I följande tabell visas vilka vSphere-versioner och typer av nätverks anpassning som stöds.  

| vSphere-version | Typ av vSwitch-källa | Virtuell NIC-drivrutin | Typ av mål vSwitch | Stöds? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Alla | DVS | Alla | DVS | Yes |
| vSphere 6,7 UI eller högre, 6,5 P03 eller högre | DVS | VMXNET3 | N-VDS | Yes |
| vSphere 6,7 UI eller högre, 6,5 P03 eller högre | DVS | E1000 | N-VDS | [Stöds inte per VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6,7 UI eller 6.5 P03, NSX-V eller versioner under NSX-T 2.2, 6.5 P03 eller senare | Alla | Alla | N-VDS | [Stöds inte per VWware](https://kb.vmware.com/s/article/56991) |

Från och med VMware NSX-T 2,3-versionen:

* Den logiska växeln på den privata moln sidan som sträcker sig över till lokal över L2VPN kan inte dirigeras samtidigt. Den utsträckta logiska växeln kan inte anslutas till en logisk router.
* L2VPN-och Route-baserade IPSEC-VPN: er kan bara konfigureras med API-anrop.

Mer information finns i [virtuella privata nätverk](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) i VMware-dokumentationen.

### <a name="sample-l2-vpn-deployment-addressing"></a>Exempel adressering för L2 VPN-distribution

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Lokalt nätverk där den fristående ESG (L2 VPN-klienten) har distribuerats

| **Objekt** | **Värde** |
|------------|-----------------|
| Nätverksnamn | MGMT_NET_VLAN469 |
| LOKALT | 469 |
| CIDR| 10.250.0.0/24 |
| IP-adress för fristående Edge-utrustning | 10.250.0.111 |
| NAT IP-adress för fristående Edge-utrustning | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Det lokala nätverket ska sträckas ut

| **Objekt** | **Värde** |
|------------|-----------------|
| LOKALT | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>IP-schema för privata moln för NSX-T Tier0-routern (L2-VPN fungerar)

| **Objekt** | **Värde** |
|------------|-----------------|
| Loopback-gränssnitt | 192.168.254.254/32 |
| Tunnel gränssnitt | 5.5.5.1/29 |
| Logisk växel (utsträckt) | Stretch_LS |
| Loopback-gränssnitt (NAT IP-adress) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Ett privat moln nätverk som ska mappas till det ambitiösa nätverket

| **Objekt** | **Värde** |
|------------|-----------------|
| LOKALT | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Hämta det logiska router-ID som krävs för L2VPN

Följande steg visar hur du hämtar det logiska router-ID: t för Tier0 DR Logical router-instansen för IPsec-och L2VPN-tjänsterna. Det logiska router-ID: t krävs senare när du implementerar L2VPN.

1. Logga in på NSX-T-hanteraren `https://*nsx-t-manager-ip-address*` och välj Provider för **nätverks**  >  **routrar**  >  **– LR-**  >  **Översikt**. I **läget för hög tillgänglighet** väljer du **aktivt-standby**. Den här åtgärden öppnar ett popup-fönster som visar den virtuella Edge-dator där Tier0-routern för närvarande är aktiv.

    ![Välj Aktiv-standby](media/l2vpn-fetch01.png)

2. Välj kanter för **Fabric**-  >  **noder**  >  . Anteckna hanterings-IP-adressen för den aktiva virtuella Edge-datorn (Edge-VM1) som identifierades i föregående steg.

    ![Anmärkning för hantering av IP](media/l2vpn-fetch02.png)

3. Öppna en SSH-session med hanterings-IP-adressen för den virtuella Edge-datorn. Kör ```get logical-router``` kommandot med användar namnet **admin** och lösen ord **CloudSimple 123!**.

    ![Skärm bild som visar en öppen SSH-session.](media/l2vpn-fetch03.png)

4. Om du inte ser posten "DR-Provider-LR" utför du följande steg.

5. Skapa två överläggna logiska växlar. En logisk växel sträcks ut till lokal plats där de migrerade arbets belastningarna finns. En annan logisk växel är en dummy-växel. Instruktioner finns i [skapa en logisk växel](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) i VMware-dokumentationen.

    ![Skapa logisk växel](media/l2vpn-fetch04.png)

6. Anslut provdockans växel till 1-routern med en länk lokal IP-adress eller ett undernät som inte överlappar från lokalt eller i ditt privata moln. Se [lägga till en Downlink-port på en logisk nivå 1-router](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) i VMware-dokumentationen.

    ![Bifoga dummy-växel](media/l2vpn-fetch05.png)

7. Kör `get logical-router` kommandot igen på SSH-sessionen för den virtuella Edge-sessionen. UUID: t för den logiska routern "DR-Provider-LR" visas. Anteckna UUID, vilket krävs när du konfigurerar L2VPN.

    ![Skärm bild som visar UUID för den logiska routern.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Hämta det ID för logisk växel som krävs för L2VPN

1. Logga in på NSX-T Manager ( `https://nsx-t-manager-ip-address` ).
2. Välj **nätverks**  >  **växlings**  >  **växlar**  >  **< \logical \> switch**  >  **Overview**.
3. Anteckna UUID för den utsträckta logiska växeln, vilket krävs när du konfigurerar L2VPN.

    ![Hämta utdata för logiska routrar](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Routning och säkerhets överväganden för L2VPN

För att upprätta en IPsec Route-baserad VPN mellan NSX-T Tier0-routern och den fristående NSX Edge-klienten, måste loopback-gränssnittet för NSX-T Tier0-routern kunna kommunicera med den offentliga IP-adressen för NSX fristående klient lokalt via UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Tillåt UDP 500/4500 för IPsec

1. [Skapa en offentlig IP-adress](public-ips.md) för loopback-gränssnittet NSX-T Tier0 på CloudSimple-portalen.

2. [Skapa en brand Väggs tabell](firewall.md) med tillstånds känsliga regler som tillåter UDP 500/4500 inkommande trafik och koppla brand Väggs tabellen till NSX-T HostTransport-undernätet.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Annonsera IP-adressen för loopback-gränssnittet till Underlay-nätverket

1. Skapa en null-väg för loopback-användargränssnittet. Logga in på NSX-T-hanteraren och välj providers för routrar för **nätverks**  >  **routning**  >    >  **– LR**  >  **routning**  >  **statiska vägar**. Klicka på **Lägg till**. För **nätverk** anger du IP-adressen för loopback-gränssnittet. För **nästa hopp** klickar du på **Lägg till**, anger NULL för nästa hopp och behåller standardvärdet 1 för administratörs avstånd.

    ![Lägg till statisk väg](media/l2vpn-routing-security01.png)

2. Skapa en lista med IP-prefix. Logga in på NSX-T-hanteraren och välj Provider för routrar för **nätverks**  >  **routning**  >    >  **– LR**  >    >  **IP-prefix**. Klicka på **Lägg till**. Ange ett namn för att identifiera listan. För **prefix** klickar du på **Lägg till** två gånger. På den första raden anger du ' 0.0.0.0/0 ' för **nätverk** och ' neka ' för **åtgärd**. På den andra raden väljer du **ett** för **nätverk** och **Åtgärds** **tillstånd** .
3. Koppla listan IP-prefix till både BGP-grannar (TOR). Om du kopplar listan IP-prefix till BGP-grannar förhindrar det att standard vägen annonseras i BGP till TOR-växlarna. En annan väg som inkluderar null-vägen annonserar dock IP-adressen för loopback-gränssnittet till TOR-växlarna.

    ![Skapa lista över IP-prefix](media/l2vpn-routing-security02.png)

4. Logga in på NSX-T-hanteraren och välj Provider för routrar för **nätverks**  >  **routning**  >    >  **-LR**  >  **routning**  >  **BGP**-  >  **grannar**. Välj den första grannen. Klicka på **Redigera**  >  **adress familjer**. För IPv4-serien redigerar du kolumnen **ut filter** och väljer listan IP-prefix som du har skapat. Klicka på **Spara**. Upprepa det här steget för den andra grannen.

    ![Koppla IP-prefix lista 1 ](media/l2vpn-routing-security03.png) ![ koppla IP-prefixlängd lista 2](media/l2vpn-routing-security04.png)

5. Distribuera om den statiska väg som är null i BGP. Om du vill annonsera loopback-Underlay till måste du distribuera om den null-statiska vägen till BGP. Logga in på NSX-T-hanteraren och välj Provider för routrar för **nätverks**  >  **routning**  >    >  **-LR**  >  **routning**  >  **omdistribution** av  >  **grannar**. Välj **Provider-LR-Route_Redistribution** och klicka på **Redigera**. Markera kryss rutan **statisk** och klicka på **Spara**.

    ![Distribuera om en null-statisk väg till BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurera en Route-baserad VPN på NSX-T Tier0-routern

Använd följande mall för att fylla i all information om hur du konfigurerar en routad VPN-anslutning på NSX-T Tier0-routern. UUID: n i varje POST-anrop krävs i efterföljande anrop. IP-adresserna för loopback-och tunnel gränssnitten för L2VPN måste vara unika och inte överlappa varandra i nätverk för lokala eller privata moln.

De IP-adresser som valts för loopback-och tunnel gränssnitt som används för L2VPN måste vara unika och får inte överlappa varandra. Loopback-gränssnittets nätverk måste alltid vara/32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

För alla följande API-anrop ersätter du IP-adressen med din NSX-T Manager-IP-adress. Du kan köra alla dessa API-anrop från POSTman-klienten eller med hjälp av `curl` kommandon.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Aktivera IPSec VPN-tjänsten på den logiska routern

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Skapa profiler: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Skapa profiler: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Skapa profiler: tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Skapa en lokal slut punkt

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Skapa en peer-slutpunkt

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Skapa en Route-baserad VPN-session

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurera L2VPN på NSX-T Tier0-router

Fyll i följande information efter varje POST-anrop. ID: n krävs i efterföljande POST-anrop.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Skapa L2VPN-tjänsten

Utdata från följande GET-kommando kommer att vara tomt eftersom konfigurationen inte har slutförts ännu.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

För följande POST-kommando är det logiska router-ID: t det UUID som Tier0 DR-logisk router hämtade tidigare.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Skapa L2VPN-sessionen

För följande POST-kommando är L2VPN-tjänst-ID: t det ID som du precis hämtade och IPsec-sessions-ID: t för IPsec är det ID som hämtades i föregående avsnitt.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Dessa anrop skapar en GRE-tunnel slut punkt. Kör följande kommando för att kontrol lera statusen.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Skapa logisk port med angivet tunnel-ID

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Hämta peer-koden för L2VPN på NSX-T-Sidan

Hämta peer-koden för NSX-T-slutpunkten. Peer-koden krävs när du konfigurerar Fjärrslutpunkten. > för L2VPN <-session-ID kan hämtas från föregående avsnitt. Mer information finns i [API-guiden för NSX-T 2,3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Distribuera NSX-T fristående klient (lokal)

Innan du distribuerar bör du kontrol lera att dina lokala brand Väggs regler tillåter inkommande och utgående UDP 500/4500-trafik från/till den offentliga IP-CloudSimple som reserver ATS tidigare för loopback-gränssnittet NSX-T t0 router. 

1. [Hämta den fristående NSX Edge-klienten](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF och extrahera filerna från det hämtade paketet till en mapp.

    ![Hämta fristående NSX Edge-klient](media/l2vpn-deploy-client01.png)

2. Gå till mappen med alla extraherade filer. Välj alla VMDK: er (NSX-l2t-client-Large. MF och NSX-l2t-client-large. OVF för stor installations storlek eller NSX-l2t-client-XLarge. MF och NSX-l2t-client-Xlarge. OVF för extra stor storleks storlek). Klicka på **Nästa**.

    ![Välj mall- ](media/l2vpn-deploy-client02.png) ![ skärm bild som visar de valda VMDK: er-filerna.](media/l2vpn-deploy-client03.png)

3. Ange ett namn för NSX-T fristående klienten och klicka på **Nästa**.

    ![Ange Mallnamn](media/l2vpn-deploy-client04.png)

4. Klicka på **Nästa** för att komma till data lagrings inställningarna. Välj lämpligt data lager för NSX-T fristående klient och klicka på **Nästa**.

    ![Välj data lager](media/l2vpn-deploy-client06.png)

5. Välj rätt port grupper för trunkering (trunk PG), offentlig (PG PG) och HA-gränssnitt (överordnad) för NSX-T fristående klienten. Klicka på **Nästa**.

    ![Välj Port grupper](media/l2vpn-deploy-client07.png)

6. Fyll i följande information på skärmen **Anpassa mall** och klicka på **Nästa**:

    Expandera L2T:

    * **Peer-adress**. Ange IP-adressen som reserver ATS på Azure CloudSimple-portalen för NSX-T Tier0 loopback-gränssnitt.
    * **Peer-kod**. Klistra in peer-koden som hämtades från det sista steget i L2VPN Server-distribution.
    * **Under gränssnitt VLAN (tunnel-ID)**. Ange det VLAN-ID som ska sträckas ut. I parenteser () anger du det tunnel-ID som tidigare har kon figurer ATS.

    Expandera gränssnitt för överordnad länk:

    * **DNS-IP-adress**. Ange den lokala DNS-IP-adressen.
    * **Standardgateway.**  Ange standardgateway för det VLAN som ska fungera som standard-gateway för den här klienten.
    * **IP-adress**. Ange den fristående klientens IP-adress för överordnad länk.
    * **Prefixlängd**. Ange prefixlängden för LAN/undernät för överordnad länk.
    * **CLI-administratör/aktivera/rot användar lösen ord**. Ange lösen ordet för administratörs kontot/Enable/root.

      ![Anpassa mall ](media/l2vpn-deploy-client08.png)
       ![ – Anpassa mall – mer](media/l2vpn-deploy-client09.png)

7. Granska inställningarna och klicka på **Slutför**.

    ![Slutför konfiguration](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurera en lokal mottagar port

Om en av VPN-platserna inte har NSX distribuerat kan du konfigurera en L2-VPN genom att distribuera en fristående NSX på den platsen. En fristående NSX-Edge distribueras med hjälp av en OVF-fil på en värd som inte hanteras av NSX. Detta distribuerar en NSX Edge Services Gateway-enhet för att fungera som en L2 VPN-klient.

Om en sammanställd trunk-vNIC är ansluten till en vSphere-distribuerad växel, krävs antingen icke-filtrerat läge eller en mottagar port för funktionen L2 VPN. Användning av filtrerat läge kan orsaka dubbla pingar och dubbla svar. Därför använder du Port läget Sink i konfigurationen för fristående L2 VPN-NSX Edge. Se [Konfigurera en mottagar port](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) i VMware-dokumentationen.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN-och L2VPN-verifiering

Använd följande kommandon för att kontrol lera IPsec-och L2VPN-sessioner från fristående NSX-T-Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Använd följande kommandon för att kontrol lera IPsec-och L2VPN-sessioner från NSX-T Tier0-routern.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Använd följande kommandon för att verifiera mottagar porten på den ESXi-värd där NSX-T fristående klientens VM finns i den lokala miljön.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
