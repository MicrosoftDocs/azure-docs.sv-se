---
title: Azure VMware-lösning genom CloudSimple – konfigurera en VPN-gateway
description: Beskriver hur du konfigurerar plats-till-plats-VPN-gateway och plats-till-plats-VPN-gateway och skapar anslutningar mellan ditt lokala nätverk och ditt CloudSimple privata moln
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 78f78bfbf885cbae7708a75cb54ce2d41e7b526c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899107"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Konfigurera VPN-gatewayer i CloudSimple-nätverk

Med VPN-gatewayer kan du ansluta till CloudSimple-nätverk från ditt lokala nätverk och från en klient dator via en fjärr anslutning. En VPN-anslutning mellan ditt lokala nätverk och ditt CloudSimple-nätverk ger åtkomst till vCenter och arbets belastningar i ditt privata moln. CloudSimple stöder både plats-till-plats-VPN och punkt-till-plats-VPN-gatewayer.

## <a name="vpn-gateway-types"></a>VPN gateway-typer

* Med **plats-till-plats-VPN-** anslutning kan du konfigurera dina privata moln arbets belastningar för att komma åt lokala tjänster. Du kan också använda lokala Active Directory som identitets källa för att autentisera till ditt privata moln vCenter.  För närvarande stöds endast **Principbaserad VPN-** typ.
* **Punkt-till-plats-VPN-** anslutning är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd punkt-till-plats-VPN-anslutning för att fjärrans luta till det privata molnet. Information om hur du installerar en-klient för en punkt-till-plats-VPN-anslutning finns i [Konfigurera en VPN-anslutning till ditt privata moln](set-up-vpn.md).

I en region kan du skapa en plats-till-plats-VPN-gateway och en plats-till-plats-VPN-gateway.

## <a name="automatic-addition-of-vlansubnets"></a>Automatiskt tillägg av VLAN/undernät

CloudSimple VPN-gatewayer innehåller principer för att lägga till VLAN/undernät till VPN-gatewayer.  Med principer kan du ange olika regler för hantering av VLAN/undernät och användardefinierade VLAN/undernät.  Regler för hantering av VLAN/undernät gäller för alla nya privata moln som du skapar.  Regler för användardefinierade VLAN/undernät gör att du automatiskt kan lägga till nya VLAN/undernät i befintliga eller nya privata moln för en plats-till-plats-VPN-gateway. du definierar principen för varje anslutning.

Principerna för att lägga till VLAN/undernät till VPN-gatewayer gäller för både plats-till-plats-VPN och punkt-till-plats-VPN-gatewayer.

## <a name="automatic-addition-of-users"></a>Automatiskt tillägg av användare

Med en punkt-till-plats-VPN-gateway kan du definiera en princip för automatisk tillägg för nya användare. Som standard har alla ägare och deltagare i prenumerationen åtkomst till CloudSimple-portalen.  Användare skapas endast när CloudSimple-portalen startas för första gången.  Om du väljer **Lägg till regler automatiskt** kan alla nya användare få åtkomst till CloudSimple-nätverket med punkt-till-plats-VPN-anslutning.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Konfigurera en plats-till-plats-VPN-gateway

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **nätverk**.
2. Välj **VPN gateway**.
3. Klicka på **ny VPN gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration** anger du följande inställningar och klickar på **Nästa**.

    * Välj **VPN för plats-till-plats** som gateway-typ.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst har distribuerats.
    * Du kan också aktivera hög tillgänglighet.

    ![Skapa Gateway för plats-till-plats-VPN](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Att aktivera hög tillgänglighet kräver att din lokala VPN-enhet stöder anslutning till två IP-adresser. Det här alternativet kan inte inaktive ras när VPN-gatewayen har distribuerats.

5. Skapa den första anslutningen från ditt lokala nätverk och klicka på **Nästa**.

    * Ange ett namn för att identifiera anslutningen.
    * För peer-IP anger du din lokala VPN-gateways offentliga IP-adress.
    * Ange peer-ID för din lokala VPN-gateway.  Peer-identifieraren är vanligt vis den offentliga IP-adressen för din lokala VPN-gateway.  Ange identifieraren om du har konfigurerat en unik identifierare på din gateway.
    * Kopiera den delade nyckel som ska användas för anslutning från din lokala VPN-gateway.  Om du vill ändra den delade standard nyckeln och ange en ny som standard klickar du på ikonen Redigera.
    * För **lokala prefix** anger du de lokala CIDR-prefix som kommer att ha åtkomst till CloudSimple-nätverket.  Du kan lägga till flera CIDR-prefix när du skapar anslutningen.

    ![Skapa Gateway-anslutning för plats-till-plats-VPN](media/create-vpn-gateway-s2s-connection.png)

6. Aktivera VLAN/undernät i ditt privata moln nätverk som kommer att nås från det lokala nätverket och klicka på **Nästa**.

    * Om du vill lägga till ett hanterings-VLAN/undernät aktiverar du **Lägg till hanterings-VLAN/undernät för privata moln**.  Hanterings under nätet krävs för vMotion-och virtuellt San-undernät.
    * Om du vill lägga till vMotion-undernät aktiverar du **Lägg till vMotion nätverk av privata moln**.
    * Om du vill lägga till virtuellt San-undernät aktiverar du **Lägg till virtuellt San-undernät för privata moln**.
    * Markera eller avmarkera vissa virtuella lokala nätverk.

    ![Skapa anslutning](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Granska inställningarna och klicka på **Skicka**.

    ![Granska och skapa VPN gateway för plats-till-plats](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Skapa punkt-till-plats-VPN-gateway

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **nätverk**.
2. Välj **VPN gateway**.
3. Klicka på **ny VPN gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration** anger du följande inställningar och klickar på **Nästa**.

    * Välj **punkt-till-plats-VPN** som gateway-typ.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst har distribuerats.
    * Ange klient under nätet för punkt-till-plats-Gateway.  DHCP-adresser tilldelas från klient under nätet när du ansluter.

5. För **anslutning/användare** anger du följande inställningar och klickar på **Nästa**.

    * Om du vill att alla aktuella och framtida användare automatiskt ska kunna komma åt det privata molnet via punkt-till-plats-Gateway väljer du **Lägg automatiskt till alla användare**. När du väljer alternativet väljs alla användare i användar listan automatiskt. Du kan åsidosätta det automatiska alternativet genom att avmarkera enskilda användare i listan.
    * Om du vill välja enskilda användare klickar du på kryss rutorna i användar listan.

6. I avsnittet VLAN/undernät kan du ange hanterings-och användar-VLAN/undernät för gatewayen och anslutningarna.

    * Alternativen **Lägg till automatiskt** anger den globala principen för gatewayen. Inställningarna gäller för den aktuella gatewayen. Inställningarna kan åsidosättas i **Select** -ytan.
    * Välj **Lägg till hanterings-VLAN/undernät för privata moln**. 
    * Om du vill lägga till alla användardefinierade VLAN/undernät klickar du på  **Lägg till användardefinierade VLAN/undernät**.
    * **Välj** inställningar åsidosätter de globala inställningarna under **Lägg till automatiskt**.

7. Klicka på **Nästa** för att granska inställningarna. Klicka på Redigera ikoner för att göra ändringar.
8. Klicka på **skapa** för att skapa VPN-gatewayen.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Klient under nät och protokoll för punkt-till-plats-VPN-gateway

Punkt-till-plats-VPN-gatewayen tillåter TCP-och UDP-anslutningar.  Välj det protokoll som ska användas när du ansluter från datorn genom att välja TCP-eller UDP-konfigurationen.

Det konfigurerade klient under nätet används för både TCP-och UDP-klienter.  CIDR-prefixet är uppdelat i två undernät, ett för TCP och ett för UDP-klienter. Välj prefixlängd baserat på antalet VPN-användare som kommer att ansluta samtidigt.  

I följande tabell visas antalet samtidiga klient anslutningar för prefixvärde.

| Prefixlängd | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Antal samtidiga TCP-anslutningar | 124 | 60 | 28 | 12 | 4 |
| Antal samtidiga UDP-anslutningar | 124 | 60 | 28 | 12 | 4 |

Om du vill ansluta med punkt-till-plats-VPN kan du läsa [Anslut till CloudSimple med punkt-till-plats-VPN](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
