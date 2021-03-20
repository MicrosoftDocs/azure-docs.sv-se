---
title: Azure VMware-lösning av CloudSimple – konfigurera hög tillgänglighet från lokal plats till CloudSimple VPN-gateway
description: Beskriver hur du konfigurerar en anslutning med hög tillgänglighet från din lokala miljö till en CloudSimple VPN-gateway som är aktive rad för hög tillgänglighet
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895707"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurera en anslutning med hög tillgänglighet från lokal plats till CloudSimple VPN-gateway

Nätverks administratörer kan konfigurera en IPsec-plats-till-plats-VPN-anslutning med hög tillgänglighet från sin lokala miljö till en CloudSimple VPN-gateway.

Den här guiden beskriver steg för att konfigurera en lokal brand vägg för en IPsec-anslutning med VPN för plats-till-plats-anslutning med hög tillgänglighet. De detaljerade stegen är särskilda för typen av lokal brand vägg. Som exempel visar den här guiden steg för två typer av brand väggar: Cisco ASA och Palo-nätverk.

## <a name="before-you-begin"></a>Innan du börjar

Utför följande åtgärder innan du konfigurerar den lokala brand väggen.

1. Kontrol lera att din organisation har [allokerat](create-nodes.md) de nödvändiga noderna och skapat minst ett CloudSimple privat moln.
2. [Konfigurera en plats-till-plats-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mellan ditt lokala nätverk och ditt CloudSimple privata moln.

Se [Översikt över VPN-gatewayer](cloudsimple-vpn-gateways.md) för förslag på fas 1 och fas 2.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurera lokal Cisco ASA-brandvägg

Anvisningarna i det här avsnittet gäller Cisco ASA version 8,4 och senare. I konfigurations exemplet distribueras Cisco adaptiv Security-program version 9,10 och konfigureras i IKEv1-läge.

För att VPN för plats-till-plats ska fungera måste du tillåta UDP 500/4500 och ESP (IP-protokoll 50) från den primära och den sekundära offentliga IP-CloudSimple (peer-IP) på det externa gränssnittet för den lokala Cisco ASA VPN-gatewayen.

### <a name="1-configure-phase-1-ikev1"></a>1. Konfigurera fas 1 (IKEv1)

Om du vill aktivera fas 1 (IKEv1) i gränssnittet utanför kan du ange följande CLI-kommando i Cisco ASA-brandväggen.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. skapa en IKEv1-princip

Skapa en IKEv1-princip som definierar algoritmer och metoder som ska användas för hashing, autentisering, Diffie-Hellman grupp, livs längd och kryptering.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. skapa en tunnel grupp

Skapa en tunnel grupp under IPsec-attributen. Konfigurera peer-IP-adressen och den i förväg delade tunneln som du angav när du [konfigurerade VPN-gatewayen för plats-till-plats](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurera fas 2 (IPsec)

Om du vill konfigurera fas 2 (IPsec) skapar du en åtkomst kontrol lista (ACL) som definierar den trafik som ska krypteras och tunnlas. I följande exempel är trafiken av ränta från den tunnel som har sitt ursprung i det lokala lokala under nätet (10.16.1.0/24) till det privata molnets fjärrundernät (192.168.0.0/24). ACL: en kan innehålla flera poster om det finns flera undernät mellan platserna.

I Cisco ASA version 8,4 och senare kan objekt eller objekt grupper skapas som fungerar som behållare för nätverk, undernät, värd-IP-adresser eller flera objekt. Skapa ett objekt för det lokala objektet och ett objekt för de fjärranslutna under näten och Använd dem för krypterings-ACL och NAT-uttryck.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definiera ett lokalt lokalt undernät som ett objekt

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definiera CloudSimple-fjärrundernätet som ett objekt

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurera en åtkomst lista för trafik av intresse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Konfigurera Transformations uppsättningen

Konfigurera transformerings uppsättningen (TS), som måste omfatta nyckelordet ```ikev1``` . De krypterings-och hash-attribut som anges i TS måste matcha de parametrar som anges i [standard konfigurationen för CloudSimple VPN-gatewayer](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurera krypterings mappningen

Konfigurera kryptografi kartan, som innehåller följande komponenter:

* Peer-IP-adress
* Definierad ACL som innehåller trafik av intresse
* Transformera uppsättning

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Använd krypterings mappningen

Tillämpa krypterings mappningen på gränssnittet utanför:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Bekräfta tillämpliga NAT-regler

Följande är den NAT-regel som används. Se till att VPN-trafiken inte omfattas av någon annan NAT-regel.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Exempel på IPsec plats-till-plats-VPN upprättade utdata från Cisco ASA

Fas 1-utdata:

![Fas 1-utdata för Cisco ASA-brandvägg](media/ha-vpn-connection-cisco-phase1.png)

Fas 2-utdata:

![Fas 2-utdata för Cisco ASA-brandvägg](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurera lokal Palo-nätverks brand vägg

Anvisningarna i det här avsnittet gäller Palo-nätverk version 7,1 och senare. I det här konfigurations exemplet distribueras Palo-nätverk VM-Series program varu version 8.1.0 och konfigureras i IKEv1-läge.

För att plats-till-plats-VPN ska fungera måste du tillåta UDP 500/4500 och ESP (IP-protokoll 50) från den primära och den sekundära offentliga IP-CloudSimple (peer-IP) i det externa gränssnittet för den lokala Palo-nätverks-gatewayen.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. skapa primära och sekundära tunnel gränssnitt

Logga in på Palo-brand väggen, Välj **nätverks**  >  **gränssnitt**  >  **tunnel**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

* Gränssnitts namn. Det första fältet fylls i automatiskt med nyckelordet tunnel. I det intilliggande fältet anger du ett värde mellan 1 och 9999. Det här gränssnittet används som ett primärt tunnel gränssnitt för plats-till-plats-trafik mellan det lokala data centret och det privata molnet.
* Kommentar. Ange kommentarer för enkel identifiering av syftet med tunneln
* Netflow-profil. Låt standardvärdet vara kvar.
* Konfigurationsfil. Tilldela gränssnittet till: virtuell router: Välj **standard**. 
        Säkerhets zon: Välj zonen för betrodd LAN-trafik. I det här exemplet är namnet på zonen för LAN-trafik "förtroende".
* IPv6. Klicka på **Lägg till** och Lägg till en icke-överlappande oanvänd/32-IP-adress i din miljö som ska tilldelas till det primära tunnel gränssnittet och som ska användas för att övervaka tunnlarna (förklaras senare).

Eftersom den här konfigurationen gäller för VPN med hög tillgänglighet krävs två tunnel gränssnitt: en primär och en sekundär. Upprepa föregående steg för att skapa det sekundära tunnel gränssnittet. Välj ett annat tunnel-ID och en annan oanvänd/32-IP-adress.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Konfigurera statiska vägar för privata moln under nät som ska nås via plats-till-plats-VPN

Vägar är nödvändiga för lokala undernät för att uppnå CloudSimple privata moln under nät.

Välj   >  **virtuella nätverks routrar**  >  *standard*  >  **statiska vägar**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange ett namn för att enkelt identifiera syftet med vägen.
* Mål. Ange de CloudSimple-undernät för privata moln som ska nås via S2S tunnel Interfaces från lokala platser
* Gränssnitt. Välj det primära tunnel gränssnittet som skapades i steg-1 (avsnitt 2) i list rutan. I det här exemplet är det tunnel. 20.
* Nästa hopp. Välj **Ingen**.
* Administratörens avstånd. Låt standardvärdet vara kvar.
* Kostnads. Ange ett värde mellan 1 och 65535. Nyckeln är att ange lägre mått för den väg som motsvarar det primära tunnel gränssnittet jämfört med den väg som motsvarar det sekundära tunnel gränssnittet som gör den tidigare vägen önskad. Om tunnel. 20 har Metric-värdet 20 till skillnad från ett Metric-värde på 30 för tunnel. 30, föredras tunnel. 20.
* Routningstabell. Låt standardvärdet vara kvar.
* BFD-profil. Låt standardvärdet vara kvar.
* Sök vägs övervakning. Lämna alternativet omarkerat.

Upprepa föregående steg för att skapa en annan väg för privata moln under nät som ska användas som sekundär-/säkerhets kopierings väg via sekundärt tunnel gränssnitt. Den här gången väljer du ett annat tunnel-ID och ett högre mått än för den primära vägen.

### <a name="3-define-the-cryptographic-profile"></a>3. definiera den kryptografiska profilen

Definiera en kryptografisk profil som anger protokoll och algoritmer för identifiering, autentisering och kryptering som ska användas för att konfigurera VPN-tunnlar i IKEv1 fas 1.

Välj **nätverk**  >  **expandera nätverks profiler**  >  **IKE-krypto**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange ett valfritt namn på IKE-kryptografi profilen.
* DH-grupp. Klicka på **Lägg till** och välj lämplig DH-grupp.
* Kryptering. Klicka på **Lägg till** och välj lämplig krypterings metod.
* Autentisering. Klicka på **Lägg till** och välj lämplig autentiseringsmetod.
* Livstid för nyckel. Låt standardvärdet vara kvar.
* IKEv2-autentisering flera. Låt standardvärdet vara kvar.

### <a name="4-define-ike-gateways"></a>4. definiera IKE-gatewayer

Definiera IKE-gatewayer för att upprätta kommunikation mellan peer-datorer i varje ände av VPN-tunneln.

Välj **nätverk**  >  **expandera nätverks profiler**  >  **IKE-gatewayer**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

Fliken Allmänt:

* Namn. Ange namnet på IKE-gatewayen som ska peer-kopplas till den primära CloudSimple VPN-peer.
* 2.0.1. Välj **läget endast ikev1**.
* Adress typ. Välj **IPv4**.
* Gränssnitt. Välj det offentliga gränssnittet eller utsidan.
* Lokal IP-adress. Låt standardvärdet vara kvar.
* Typ av peer-IP-adress. Välj **IP**.
* Peer-adress. Ange IP-adressen för den primära CloudSimple VPN-peer.
* Autentisering. Välj i **förväg delad nyckel**.
* I förväg delad nyckel/bekräfta i förväg delad nyckel. Ange den i förväg delade nyckeln som matchar CloudSimple VPN gateway-nyckeln.
* Lokal identifiering. Ange den offentliga IP-adressen för den lokala Palo-brand väggen.
* Peer-identifiering. Ange IP-adressen för den primära CloudSimple VPN-peer.

Fliken Avancerade alternativ:

* Aktivera passivt läge. Lämna alternativet omarkerat.
* Aktivera NAT-överträdelse. Lämna omarkerad om den lokala Palo-brand väggen inte finns bakom någon NAT-enhet. Annars markerar du kryss rutan.

Ikev1

* Exchange-läge. Välj **huvud**.
* IKE-kryptografi profil. Välj den IKE-kryptografi profil som du skapade tidigare. Lämna rutan Aktivera fragmentering avmarkerad.
* Utebliven peer-identifiering. Lämna rutan omarkerad.

Upprepa föregående steg för att skapa den sekundära IKE-gatewayen.

### <a name="5-define-ipsec-crypto-profiles"></a>5. definiera IPSEC-kryptografi profiler

Välj **nätverk**  >  **expandera nätverks profiler**  >  **IPSec-kryptografi**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange ett namn för IPsec-kryptografi profilen.
* IPsec-protokoll. Välj **ESP**.
* Kryptering. Klicka på **Lägg till** och välj lämplig krypterings metod.
* Autentisering. Klicka på **Lägg till** och välj lämplig autentiseringsmetod.
* DH-grupp. Välj **no-PFS**.
* Livslängd. Ange som 30 minuter.
* Aktivera. Lämna rutan omarkerad.

Upprepa föregående steg för att skapa en annan IPsec-kryptografi profil som ska användas som sekundär CloudSimple VPN-peer. Samma IPSEC-kryptografi profil kan också användas både för de primära och sekundära IPsec-tunnlarna (se följande procedur).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. definiera övervaknings profiler för tunnel övervakning

Välj **nätverk**  >  **expandera nätverks profiler**  >  **övervaka**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange ett namn på den övervaknings profil som ska användas för tunnel övervakning för proaktiv reaktion på problemet.
* Tgärd. Välj **redundansväxla**.
* Intervall. Ange värdet **3**.
* Fastställd. Ange värdet **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Konfigurera primära och sekundära IPsec-tunnlar.

Välj **nätverk**  >  **IPSec-tunnlar**  >  **Lägg till**, konfigurera följande fält och klicka på **OK**.

Fliken Allmänt:

* Namn. Ange ett namn för den primära IPSEC-tunneln som ska peer-kopplas med primär CloudSimple VPN-peer.
* Tunnel gränssnitt. Välj det primära tunnel gränssnittet.
* Bastyp. Låt standardvärdet vara kvar.
* Adress typ. Välj **IPv4**.
* IKE-Gateway. Välj den primära IKE-gatewayen.
* IPsec-kryptografi profil. Välj den primära IPsec-profilen. Välj **Visa avancerade alternativ**.
* Aktivera uppspelnings skydd. Låt standardvärdet vara kvar.
* Kopiera TOS-rubrik. Lämna rutan omarkerad.
* Tunnel övervakare. Markera kryss rutan.
* Mål-IP. Ange en IP-adress som tillhör det CloudSimple privata moln under nätet som tillåts via plats-till-plats-anslutningen. Se till att tunnel gränssnitten (t. ex. tunnel. 20-10.64.5.2/32 och tunnel. 30-10.64.6.2/32) på Palo-kan komma åt den CloudSimple privata molnets IP-adress via VPN för plats till plats. Se följande konfiguration för proxy-ID: n.
* Upphandlarprofil. Välj Monitor-profilen.

Fliken Proxy-ID: Klicka på **IPv4**  >  **Lägg till** och konfigurera följande:

* Proxy-ID. Ange ett namn för den intressanta trafiken. Det kan finnas flera proxy-ID i en IPsec-tunnel.
* Inställningar. Ange lokala lokala undernät som tillåts att kommunicera med privata moln under nät över plats-till-plats-VPN.
* Fjärrserveradministrationsverktyg. Ange de privata moln-fjärrundernät som tillåts kommunicera med de lokala under näten.
* Protokollhanterare. Välj **valfri**.

Upprepa föregående steg för att skapa en annan IPsec-tunnel som ska användas för den sekundära CloudSimple VPN-peer.

## <a name="references"></a>Referenser

Konfigurera NAT på Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Konfigurations guide för Cisco ASA 5500-serien</a>

IKEv1-och IKEv2-attribut som stöds på Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Konfigurations guide för Cisco ASA Series CLI</a>

Konfigurera IPsec plats-till-plats-VPN i Cisco ASA med version 8,4 och senare:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurera IKEv1 IPsec plats-till-plats-tunnlar med ASDM eller CLI på ASA</a>

Konfigurera Cisco adaptiv Security-installation (ASAv) på Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Snabb starts guide för Cisco adaptiv Security (ASAv)</a>

Konfigurera VPN för plats-till-plats med proxy-ID: n på Palo,

[Konfigurera VPN för plats-till-plats](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Konfigurera tunnel Övervakare:

[Konfigurera tunnel övervakning](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE-gateway eller IPsec-tunnel åtgärder:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Aktivera/inaktivera, uppdatera eller starta om en IKE-gateway eller IPsec-tunnel</a>
