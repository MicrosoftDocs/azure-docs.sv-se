---
title: 'Snabb start: skapa ett privat moln'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Lär dig hur du skapar och konfigurerar ett privat moln med Azure VMware-lösningar från CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073917"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Snabb start – konfigurera en privat moln miljö

I den här artikeln lär du dig hur du skapar ett privat CloudSimple-moln och konfigurerar din privata moln miljö.

## <a name="before-you-begin"></a>Innan du börjar

Granska [nätverks krav](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ett privat moln är en isolerad VMware-stack som stöder ESXi-värdar, vCenter, virtuellt San och NSX.

Privata moln hanteras via CloudSimple-portalen. De har sina egna vCenter-servrar i sin egen hanterings domän. Stacken körs på dedikerade noder och isolerade Bare Metal-maskinvarukonfigurationer.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Från **Översikt** klickar du på **skapa privat moln** för att öppna en ny flik i webbläsaren för CloudSimple-portalen.  Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.  

    ![Skapa ett privat moln från Azure](media/create-private-cloud-from-azure.png)

5. I CloudSimple-portalen anger du ett namn för ditt privata moln.
6. Välj **plats** för ditt privata moln.
7. Välj **nodtyp**, konsekvent med det du etablerade i Azure.
8. Ange **antal noder**.  Minst tre noder krävs för att skapa ett privat moln.

    ![Skapa privat moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: avancerade alternativ**.
10. Ange CIDR-intervallet för vSphere/virtuellt San-undernät. Se till att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med Gateway-undernätet.

    **Alternativ för CIDR-intervall:** /24,/23,/22 eller/21. Ett/24 CIDR-intervall stöder upp till 26 noder, ett/23 CIDR-intervall stöder upp till 58 noder och ett/22-och/21 CIDR-intervall stöder 64-noder (det maximala antalet noder i ett privat moln).  Mer information och VLAN och undernät finns i [Översikt över VLAN och undernät](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > IP-adresser i CIDR-intervallet vSphere/virtuellt San är reserverade för användning av den privata moln infrastrukturen.  Använd inte IP-adressen i det här intervallet på någon virtuell dator.

11. Klicka på **Nästa: granska och skapa**.
12. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **föregående**.
13. Klicka på **Skapa**.

Etablerings processen för privata moln startar.  Det kan ta upp till två timmar för det privata molnet att tillhandahållas.

## <a name="launch-cloudsimple-portal"></a>Starta CloudSimple-portalen

Du kan komma åt CloudSimple-portalen från Azure Portal.  CloudSimple-portalen startas med dina Azure-inloggnings uppgifter med hjälp av enkel Sign-On (SSO).  För att få åtkomst till CloudSimple-portalen måste du auktorisera programmet för **CloudSimple-auktorisering** .  Mer information om hur du beviljar behörigheter finns i [medgivande to CloudSimple service Authorization Application](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Från Översikt klickar **du på gå till CloudSimple-portalen** för att öppna en ny flik i webbläsaren för CloudSimple-portalen.  Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Skapa punkt-till-plats-VPN

En punkt-till-plats-VPN-anslutning är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd punkt-till-plats-VPN-anslutning om du ansluter till det privata molnet via en fjärr anslutning.  Följ stegen nedan för snabb åtkomst till ditt privata moln.  Åtkomst till CloudSimple-regionen från ditt lokala nätverk kan göras med [plats-till-plats-VPN](vpn-gateway.md) eller [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Skapa Gateway

1. Starta CloudSimple-portalen och välj **nätverk**.
2. Välj **VPN gateway**.
3. Klicka på **ny VPN gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration** anger du följande inställningar och klickar på **Nästa**.

    * Välj **punkt-till-plats-VPN** som gateway-typ.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst har distribuerats.
    * Ange klient under nätet för punkt-till-plats-Gateway.  DHCP-adresser tilldelas från det här under nätet när du ansluter.

5. För **anslutning/användare** anger du följande inställningar och klickar på **Nästa**.

    * Om du automatiskt vill tillåta alla aktuella och framtida användare åtkomst till det privata molnet via den här punkt-till-plats-gatewayen väljer du **Lägg automatiskt till alla användare**. När du väljer det här alternativet markeras alla användare i användar listan automatiskt. Du kan åsidosätta det automatiska alternativet genom att avmarkera enskilda användare i listan.
    * Om du bara vill välja enskilda användare klickar du på kryss rutorna i användar listan.

6. I avsnittet VLAN/undernät kan du ange hanterings-och användar-VLAN/undernät för gatewayen och anslutningarna.

    * Alternativen **Lägg till automatiskt** anger den globala principen för den här gatewayen. Inställningarna gäller för den aktuella gatewayen. Inställningarna kan åsidosättas i **Select** -ytan.
    * Välj **Lägg till hanterings-VLAN/undernät för privata moln**.
    * Om du vill lägga till alla användardefinierade VLAN/undernät klickar du på  **Lägg till användardefinierade VLAN/undernät**.
    * **Välj** inställningar åsidosätter de globala inställningarna under **Lägg till automatiskt**.

7. Klicka på **Nästa** för att granska inställningarna. Klicka på Redigera ikoner för att göra ändringar.
8. Klicka på **skapa** för att skapa VPN-gatewayen.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ansluta till CloudSimple med punkt-till-plats-VPN

VPN-klienten krävs för att ansluta till CloudSimple från din dator.  Ladda ned [OpenVPN-klienten](https://openvpn.net/community-downloads/) för Windows eller [viskositet](https://www.sparklabs.com/viscosity/download/) för MacOS och OS X.

1. Starta CloudSimple-portalen och välj **nätverk**.
2. Välj **VPN gateway**.
3. I listan med VPN-gatewayer klickar du på punkt-till-plats-VPN-gatewayen.
4. Välj **Användare**.
5. Klicka på **Hämta min VPN-konfiguration**.

    ![Ladda ned VPN-konfiguration](media/download-p2s-vpn-configuration.png)

6. Importera konfigurationen på VPN-klienten.

    * Instruktioner för att [importera konfigurationen på Windows-klienten](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruktioner för att [importera konfigurationen på MacOS eller OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Anslut till CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Skapa ett virtuellt lokalt nätverk för dina virtuella arbets belastningar

När du har skapat ett privat moln skapar du ett VLAN där du distribuerar dina virtuella datorer/program.

1. I CloudSimple-portalen väljer du **nätverk**.
2. Klicka på **VLAN/undernät**.
3. Klicka på **skapa VLAN/undernät**.

    ![Skapa VLAN/undernät](media/create-new-vlan-subnet.png)

4. Välj det **privata molnet** för det nya VLAN/undernät.
5. Välj ett VLAN-ID i listan.  
6. Ange ett under näts namn för att identifiera under nätet.
7. Ange CIDR-intervall och mask för undernät.  Intervallet får inte överlappa några befintliga undernät.
8. Klicka på **Skicka**.

    ![Information om skapa VLAN/undernät](media/create-new-vlan-subnet-details.png)

VLAN/undernät kommer att skapas.  Du kan nu använda detta VLAN-ID för att skapa en distribuerad port grupp i ditt privata moln vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ansluta din miljö till ett virtuellt Azure-nätverk

CloudSimple tillhandahåller en ExpressRoute-krets för ditt privata moln. Du kan ansluta ditt virtuella nätverk på Azure till ExpressRoute-kretsen. Om du vill ha fullständig information om hur du konfigurerar anslutningen följer du stegen i [Azure Virtual Network-anslutning med ExpressRoute](./cloudsimple-azure-network-connection.md).

## <a name="sign-in-to-vcenter"></a>Logga in på vCenter

Nu kan du logga in på vCenter för att konfigurera virtuella datorer och principer.

1. Kom åt vCenter genom att starta från CloudSimple-portalen. På Start sidan, under **vanliga uppgifter**, klickar du på **Starta vSphere-klient**.  Välj det privata molnet och klicka sedan på **Starta vSphere-klient** i det privata molnet.

    ![Starta vSphere-klienten](media/launch-vcenter-from-cloudsimple-portal.png)

2. Välj önskad vSphere-klient för att komma åt vCenter och logga in med ditt användar namn och lösen ord.  Standardvärdena är:
    * Användar namn: `CloudOwner@cloudsimple.local`
    * Lösenord: `CloudSimple123!`

VCenter-skärmarna i nästa procedur är från vSphere-klienten (HTML5).

## <a name="change-your-vcenter-password"></a>Ändra ditt vCenter-lösenord

CloudSimple rekommenderar att du ändrar ditt lösen ord första gången du loggar in på vCenter.  
Det lösen ord som du anger måste uppfylla följande krav:

* Högsta livstid: lösen ordet måste ändras var 365: e dag
* Begränsa åter användning: användarna kan inte återanvända något av de föregående fem lösen orden
* Längd: 8-20 tecken
* Specialtecken: minst ett specialtecken
* Alfabetiska tecken: minst ett versal tecken, A-Z och minst ett gement tecken, a-z
* Siffror: minst ett numeriskt tecken, 0-9
* Maximalt antal identiska intilliggande tecken: tre

    Exempel: CC eller kopia är acceptabelt som en del av lösen ordet, men CCCC är inte.

Om du anger ett lösen ord som inte uppfyller kraven:

* Om du använder vSphere Flash-klienten rapporterar den ett fel
* Om du använder HTML5-klienten rapporteras inget fel. Klienten accepterar inte ändringen och det gamla lösen ordet fortsätter att fungera.

## <a name="access-nsx-manager"></a>Åtkomst till NSX Manager

NSX Manager distribueras med ett standard lösen ord. 

* Användar namn: **admin**
* Lösen ord: **CloudSimple123!**

Du kan hitta det fullständigt kvalificerade domän namnet (FQDN) och IP-adressen för NSX Manager på CloudSimple-portalen.

1. Starta CloudSimple-portalen och välj **resurser**.
2. Klicka på det privata moln som du vill använda.
3. Välj **vSphere Management Network**
4. Använd FQDN eller IP-adressen för **NSX Manager** och Anslut med en webbläsare.

    ![Hitta NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Skapa en port grupp

Skapa en distribuerad port grupp i vSphere:

1. Följ instruktionerna i "Lägg till en distribuerad port grupp" i [vSphere Networking guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. När du konfigurerar den distribuerade port gruppen anger du det VLAN-ID som skapades i [skapa ett virtuellt lokalt nätverk för dina virtuella arbets belastningar](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](on-premises-connection.md)
* [Konfigurera VPN för plats-till-plats från en lokal plats](vpn-gateway.md)
