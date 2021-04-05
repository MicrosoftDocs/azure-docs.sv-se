---
title: 'IPsec/IKE-princip för S2S VPN & VNet-till-VNet-anslutningar: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Konfigurera IPsec/IKE-princip för S2S-eller VNet-till-VNet-anslutningar med Azure VPN-gatewayer med hjälp av Azure Resource Manager och Azure Portal.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878552"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Konfigurera IPsec/IKE-princip för S2S VPN-anslutningar och VNet-till-VNet-anslutningar: Azure Portal

Den här artikeln vägleder dig genom stegen för att konfigurera IPsec/IKE-princip för VPN Gateway plats-till-plats-VPN eller VNet-till-VNet-anslutningar med hjälp av Azure Portal. I följande avsnitt får du hjälp med att skapa och konfigurera en IPsec/IKE-princip och tillämpa principen på en ny eller befintlig anslutning.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Om IPsec-och IKE-principinställningar

IPsec-och IKE-protokoll standarden stöder en mängd olika krypteringsalgoritmer i olika kombinationer. Läs mer [om kryptografiska krav och Azure VPN-gatewayer](vpn-gateway-about-compliance-crypto.md) för att se hur detta kan hjälpa till att garantera anslutningar mellan lokala nätverk och VNET-till-VNet för att uppfylla dina krav på efterlevnad eller säkerhet.

Den här artikeln innehåller anvisningar för att skapa och konfigurera en IPsec/IKE-princip och tillämpa den på en ny eller befintlig VPN Gateway anslutning.

### <a name="considerations"></a>Överväganden

* IPsec/IKE-principen fungerar bara på följande Gateway-SKU: er:
  * ***VpnGw1 ~ 5 och VpnGw1AZ ~ 5AZ***
  * ***Standard** _ och _ *_HighPerformance_**
* Du kan bara ange ***en*** principkombination för en viss anslutning.
* Du måste ange alla algoritmer och parametrar för både IKE (huvud läge) och IPsec (snabb läge). Partiell principspecifikationen tillåts inte.
* Kontakta din VPN-enhets specifikationer för att se till att principen stöds på dina lokala VPN-enheter. S2S-eller VNet-till-VNet-anslutningar kan inte upprätta om principerna är inkompatibla.

## <a name="workflow"></a><a name ="workflow"></a>Arbetsflöde

Det här avsnittet beskriver arbets flödet för att skapa och uppdatera IPsec/IKE-princip på en S2S VPN-eller VNet-till-VNet-anslutning:

1. Skapa ett virtuellt nätverk och en VPN-gateway.
2. Skapa en lokal nätverksgateway för anslutning mellan platser eller ett annat virtuellt nätverk och en gateway för VNet-till-VNet-anslutning.
3. Skapa en anslutning (IPsec eller VNet2VNet).
4. Konfigurera/uppdatera/ta bort IPsec/IKE-principen på anslutnings resurserna.

Anvisningarna i den här artikeln hjälper dig att konfigurera och konfigurera IPsec/IKE-principer som visas i diagrammet:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-princip diagram" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Kryptografiska algoritmer som stöds & nyckel längder

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmer och nycklar

I följande tabell visas de kryptografiska algoritmer som stöds och viktiga fördelar som kan konfigureras av kunderna:

| **IPsec/IKE**    | **Alternativ**    |
| ---              | ---            |
| IKE-kryptering   | AES256, AES192, AES128, DES3, DES                  |
| IKE-integritet    | SHA384, SHA256, SHA1, MD5                          |
| DH-grupp         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, ingen |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Integrity  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-grupp        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None   |
| QM SA-livstid   | (**Valfritt**: standardvärden används om inget anges)<br>Sekunder (heltal. **min. 300**/standard 27 000 sekunder)<br>Kilobyte (heltal. **min. 1024**/standard 102400000 kilobyte)    |
| Trafikväljare | UsePolicyBasedTrafficSelectors * * ($True/$False; **Valfritt**, standard $FALSE om inget anges)    |
| DPD-timeout      | Sekunder (heltal: min. 9/Max. 3600; standard 45 sekunder) |
|  |  |

#### <a name="important-requirements"></a>Viktiga krav

* Din lokala konfiguration för VPN-enheten måste stämma överens med eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principen:
  * IKE-krypteringsalgoritm (huvud läge/fas 1)
  * IKE-integritetsalgoritm (huvud läge/fas 1)
  * DH-grupp (huvud läge/fas 1)
  * IPsec-krypteringsalgoritm (snabb läge/fas 2)
  * IPsec integritetsalgoritm (snabb läge/fas 2)
  * PFS-grupp (snabb läge/fas 2) > * Traffic Selector (om UsePolicyBasedTrafficSelectors används)
  * SA-livstider är lokala specifikationer och behöver inte matcha.

* Om GCMAES används som för IPsec-krypteringsalgoritm måste du välja samma GCMAES-algoritm och nyckel längd för IPsec-integriteten. Du kan till exempel använda GCMAES128 för båda.

* I [tabellen algoritmer och nycklar](#table1) ovan:
  * IKE motsvarar huvud läget eller fas 1
  * IPsec motsvarar snabb läge eller fas 2
  * DH-grupp anger Diffie-Hellmen grupp som används i huvud läge eller fas 1
  * PFS-gruppen angav Diffie-Hellmen gruppen som användes i snabb läge eller fas 2

* IKE-livstid för huvud läge i huvud läge fastställs till 28 800 sekunder på Azure VPN-gatewayer.

* Om du ställer in **UsePolicyBasedTrafficSelectors** till $true på en anslutning konfigureras Azure VPN-gatewayen för att ansluta till principbaserad VPN-brandvägg lokalt. Om du aktiverar PolicyBasedTrafficSelectors måste du se till att VPN-enheten har de matchande trafik väljare som definierats med alla kombinationer av ditt lokala nätverk (lokal nätverksgateway) prefix till/från de virtuella Azure-nätverks prefixen i stället för alla-till-alla. Om ditt prefix för det lokala nätverket är 10.1.0.0/16 och 10.2.0.0/16 och ditt prefix för det virtuella nätverket är 192.168.0.0/16 och 172.16.0.0/16, måste du ange följande trafik väljare:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Mer information om principbaserad trafik väljare finns i [ansluta flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD-timeout – standardvärdet är 45 sekunder på Azure VPN-gatewayer. Om du ställer in tids gränsen på kortare perioder blir IKE-nycklar mer aggressiva, vilket gör att anslutningen verkar vara frånkopplad i vissa instanser. Detta kanske inte är önskvärt om dina lokala platser ligger längre bort från Azure-regionen där VPN-gatewayen finns, eller om det fysiska länk tillståndet kan innebära paket förlust. Den allmänna rekommendationen är att ange tids gränsen mellan **30 och 45** sekunder.

### <a name="diffie-hellman-groups"></a>Diffie-Hellman grupper

I följande tabell visas motsvarande Diffie-Hellman grupper som stöds av den anpassade principen:

| **Diffie-Hellman-grupp**  | **DHGroup**              | **PFSGroup** | **Nyckellängd** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bitars MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bitars MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bitars MODP  |
| 19                        | ECP256                   | ECP256       | 256-bitars ECP    |
| 20                        | ECP384                   | ECP384       | 384-bitars ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bitars MODP  |

Se [RFC3526](https://tools.ietf.org/html/rfc3526) och [RFC5114](https://tools.ietf.org/html/rfc5114) för mer information.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>S2S VPN med IPsec/IKE-princip

Det här avsnittet vägleder dig genom stegen för att skapa en VPN-anslutning från plats till plats med en IPsec/IKE-princip. Följande steg skapar anslutningen som visas i följande diagram:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Plats-till-plats-princip" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Steg 1 – skapa det virtuella nätverket, VPN-gatewayen och den lokala Nätverksgatewayen

Skapa följande resurser, som du ser i skärm bilderna nedan. Anvisningar finns i [skapa en plats-till-plats-VPN-anslutning](./tutorial-site-to-site-portal.md).

* **Virtuellt nätverk:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN-Gateway:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Gateway":::

* **Lokal nätverksgateway:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Webbplats":::

* **Anslutning:** VNet1 till Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Anslutning":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Steg 2 – konfigurera IPsec/IKE-princip på S2S VPN-anslutningen

I det här avsnittet konfigurerar du en IPsec/IKE-princip med följande algoritmer och parametrar:

* IKE: AES256, SHA384, DHGroup24, DPD timeout 45 sekunder
* IPsec: AES256, SHA256, PFS none, SA livstid 30000 sekunder och 102400000KB

1. Navigera till anslutnings resursen **VNet1toSite6** i Azure Portal. Välj **konfigurations** sida och välj **anpassad** IPSec/IKE-princip för att visa alla konfigurations alternativ. Skärm bilden nedan visar konfigurationen enligt listan:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Plats 6":::

1. Om du använder GCMAES för IPsec måste du använda samma GCMAES-algoritm och nyckel längd för både IPsec-kryptering och integritet. Skärm bilden nedan anger till exempel GCMAES128 för både IPsec-kryptering och IPsec-integritet:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES för IPsec":::

1. Du kan också välja **Aktivera** för alternativet **Använd principbaserade trafik väljare** för att aktivera Azure VPN-gateway för att ansluta till principbaserade VPN-enheter lokalt, enligt beskrivningen ovan.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Princip beroende trafik väljare":::

1. När alla alternativ är markerade väljer du **Spara** för att genomföra ändringarna i anslutnings resursen. Principen kommer att verkställas om en minut.

> [!IMPORTANT]
>
> * När en IPsec/IKE-princip anges i en anslutning skickas eller accepteras bara IPsec/IKE-förslaget med angivna kryptografiska algoritmer och nyckel längder för den aktuella anslutningen i Azure VPN-gatewayen. Kontrol lera att den lokala VPN-enheten för anslutningen använder eller accepterar den exakta princip kombinationen, annars upprättas inte S2S VPN-tunneln.
>
> * **Principbaserade trafik väljare** och **DPD timeout** -alternativ kan anges med **standard** principen, utan den anpassade IPSec/IKE-principen som visas i skärm bilden ovan.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet-till-VNet med IPsec/IKE-princip

Stegen för att skapa en VNet-till-VNet-anslutning med en IPsec/IKE-princip liknar den för en S2S VPN-anslutning.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Princip diagram för VNet-till-VNet" border="false":::

1. Använd stegen i artikeln [skapa en VNet-till-VNET-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) för att skapa en VNet-till-VNET-anslutning.

2. När du har slutfört stegen visas två VNet-till-VNet-anslutningar som visas i skärm bilden nedan från VNet2GW-resursen:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Anslutningar mellan virtuella nätverk":::

3. Navigera till anslutnings resursen och gå till sidan **konfiguration** i portalen. Välj **anpassad** på **IPSec/IKE-principen** för att visa alternativ för anpassad princip. Välj de kryptografiska algoritmerna med motsvarande nyckel längd.

   Skärm bilden visar en annan IPsec/IKE-princip med följande algoritmer och parametrar:
   * IKE: AES128, SHA1, DHGroup14, DPD timeout 45 sekunder
   * IPsec: GCMAES128, GCMAES128, PFS14, SA-livstid 14400 sekunder & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Anslutnings princip":::

4. Välj **Spara** för att tillämpa princip ändringarna på anslutnings resursen.

5. Tillämpa samma princip för den andra anslutnings resursen, VNet2toVNet1. Om du inte gör det kommer IPsec/IKE VPN-tunneln inte att ansluta på grund av policy matchnings fel.

   > [!IMPORTANT]
   > När en IPsec/IKE-princip anges i en anslutning skickas eller accepteras bara IPsec/IKE-förslaget med angivna kryptografiska algoritmer och nyckel längder för den aktuella anslutningen i Azure VPN-gatewayen. Kontrol lera att IPsec-principerna för båda anslutningarna är desamma, annars upprättas inte VNet-till-VNet-anslutningen.

6. När du har slutfört de här stegen upprättas anslutningen på några minuter och du får följande nätverkstopologi:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-princip diagram" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Ta bort anpassad IPsec/IKE-princip från en anslutning

1. Om du vill ta bort en anpassad princip från en anslutning navigerar du till anslutnings resursen och går till **konfigurations** sidan för att se den aktuella principen.

2. Välj **standard** på alternativet **IPSec/IKE-princip** . Detta tar bort alla anpassade principer som tidigare angavs i anslutningen och återställer standard-IPsec/IKE-inställningarna för den här anslutningen:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Ta bort princip":::

3. Välj **Spara** för att ta bort den anpassade principen och återställa standard-IPSec/IKE-inställningarna på anslutningen.

## <a name="next-steps"></a>Nästa steg

Se [Anslut flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information om principbaserad trafik väljare.