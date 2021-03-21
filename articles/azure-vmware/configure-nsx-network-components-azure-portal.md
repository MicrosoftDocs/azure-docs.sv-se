---
title: Konfigurera NSX Network-komponenter i Azure VMware-lösningen
description: Lär dig hur du konfigurerar NSX-T-nätverksanslutningar med hjälp av Azure VMware Solution-konsolen.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716996"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Konfigurera NSX Network-komponenter i Azure VMware-lösningen

Ett privat moln i Azure VMware-lösningen levereras med NSX-T som en SDDC (Software Defined Network) som standard. Det levereras i förväg med en NSX-T-nivå-0-gateway i aktivt/aktivt läge och en standard-T--nivå – 1-gateway i aktivt/vänte läge.  Med dessa gatewayer kan du ansluta segmenten (logiska växlar) och ange East-West och North-South anslutning. 

När det privata molnet för Azure VMware-lösningen har distribuerats kan du konfigurera de nödvändiga NSX-T-objekten från Azure VMware Solution-konsolen under **arbets belastnings nätverk**.  Konsolen visar den förenklade vyn av NSX-T-åtgärder som en VMware-administratör behöver dagligen och riktas mot användare som inte är bekanta med NSX-T.  

Du har fyra alternativ för att konfigurera NSX-T-komponenter i Azure VMware-lösnings konsolen:
- **Segment** – skapa segment som visas i NSX-hanteraren och vCenter.
- **DHCP** – skapa en DHCP-server eller DHCP-relä om du planerar att använda DHCP.
- **Port spegling** – skapa port spegling för att hjälpa till med fel sökning av nätverks problem.
- **DNS** – skapa en DNS-vidarebefordrare för att skicka DNS-begäranden till en angiven DNS-server för matchning.  

>[!NOTE]
>Du har fortfarande åtkomst till konsolen för NSX-T-hanteraren där du kan använda de avancerade inställningar som nämns och andra NSX-T-funktioner. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Skärm bild som visar fyra alternativ i Azure VMware Solution-konsolen för att konfigurera NSX-T.":::

## <a name="prerequisites"></a>Förutsättningar
Virtuella datorer som skapas eller migreras till Azures privata moln i VMware-lösningen måste kopplas till ett segment. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Skapa ett NSX-T-segment i Azure Portal
Du kan skapa och konfigurera ett NSX-T-segment från Azure VMware Solution-konsolen i Azure Portal.  De här segmenten är anslutna till standard nivå 1-gatewayen och arbets belastningarna på dessa segment får East-West och North-South anslutning. När du har skapat segmentet visas det i NSX-hanteraren och vCenter.

>[!NOTE]
>Om du planerar att använda DHCP måste du [Konfigurera en DHCP-server eller DHCP-relä](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) innan du kan skapa och konfigurera ett NSX-T-segment.

1. I ditt privata moln i Azure VMware-lösningen under **arbets belastnings nätverk** väljer du **segment**  >  **Lägg till**. Ange information om det nya logiska segmentet och välj **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Skärm bild som visar hur du lägger till ett nytt segment.":::
   
   - **Segment namn** – namnet på den logiska växel som är synlig i vCenter.
   - **Subnet Gateway** – Gateway-IP-adress för den logiska växelns undernät med en under nät mask. Virtuella datorer är anslutna till en logisk växel och alla virtuella datorer som ansluter till den här växeln tillhör samma undernät.  Alla virtuella datorer som är kopplade till det här logiska segmentet måste också ha en IP-adress från samma segment.
   - **DHCP** (valfritt) – DHCP-intervall för ett logiskt segment. En [DHCP-server eller DHCP-relä](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) måste konfigureras för att använda DHCP i segment.  
   - **Ansluten Gateway**  -  *Markerad som standard och skrivskyddad.*  Nivå-1-gateway och typ av segment information. 
      - **T1** -namnet på nivån-1 gateway i NSX-T Manager. Ett privat moln i Azure VMware-lösningen levereras med en NSX-T-nivå-0-gateway i aktivt/aktivt läge och en standard NSX-T-nivå-1-gateway i aktivt/vänte läge.  Segment som skapats via Azure VMware-lösningen ansluter bara till standard nivå 1-gatewayen och arbets belastningarna för dessa segment får East-West och North-South anslutning. Du kan bara skapa fler nivå 1-gatewayer via NSX-T-hanteraren. Nivå 1-gatewayer som skapats från NSX-T Manager-konsolen visas inte i Azure VMware-lösnings konsolen. 
      - **Typ** – överläggs segment som stöds av Azure VMware-lösningen.

   Segmentet visas nu i Azure VMware-lösnings konsolen, NSX-T-hanteraren och vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Skapa en DHCP-server eller DHCP-relä i Azure Portal
Du kan skapa en DHCP-server eller vidarebefordra direkt från Azure VMware Solution-konsolen i Azure Portal. DHCP-servern eller reläet ansluter till nivån-1 Gateway, som skapas när du distribuerar en Azure VMware-lösning. Alla segment där du gav DHCP-intervall kommer att ingå i DHCP.  När du har skapat en DHCP-server eller DHCP-relä måste du definiera ett undernät eller ett intervall på segment nivå för att använda det.

1. I ditt privata moln i Azure VMware-lösningen väljer du **DHCP** Lägg till under **arbets belastnings nätverk**  >  .

2. Välj antingen **DHCP-server** eller **DHCP-relä** och ange sedan ett namn för servern eller RELÄET och tre IP-adresser. 

   >[!NOTE]
   >För DHCP-relä krävs bara en IP-adress för en lyckad konfiguration.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Skärm bild som visar hur du lägger till en DHCP-server eller DHCP-relä i Azure VMware-lösningar.":::

4. Slutför DHCP-konfigurationen genom [att tillhandahålla DHCP-intervall i de logiska segmenten](#create-an-nsx-t-segment-in-the-azure-portal) och välj sedan **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Konfigurera port spegling i Azure Portal
Du kan konfigurera port spegling för att övervaka nätverks trafik som omfattar att vidarebefordra en kopia av varje paket från en nätverks växel port till en annan. Med det här alternativet placeras en protokoll analys på porten som tar emot speglade data. Den analyserar trafik från en källa, en virtuell dator eller en grupp med virtuella datorer och skickas sedan till ett definierat mål. 

Om du vill konfigurera port spegling i Azure VMware Solution-konsolen kan du:

* [Steg 1. Skapa virtuella käll-och mål datorer eller VM-grupper](#step-1-create-source-and-destination-vms-or-vm-groups) – käll gruppen har en enda virtuell dator eller flera virtuella datorer där trafiken speglas.

* [Steg 2. Skapa en port speglings profil](#step-2-create-a-port-mirroring-profile) – du ska definiera trafik riktningen för de virtuella käll-och mål dator grupperna.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Steg 1. Skapa virtuella käll-och mål datorer eller VM-grupper

I det här steget ska du skapa en virtuell käll dator grupp och en VM-grupp för en virtuell dator.

1. I ditt privata moln i Azure VMware-lösningen, under **arbets belastnings nätverk** väljer du **port spegling**  >  **VM-grupper**  >  **Lägg till**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Skärm bild som visar hur du skapar en VM-grupp för port spegling.":::

1. Ange ett namn för den nya VM-gruppen, Välj önskade virtuella datorer i listan och klicka sedan på **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Skärm bild som visar listan över virtuella datorer som ska läggas till i VM-gruppen.":::

1. Upprepa de här stegen för att skapa den virtuella mål gruppen.

### <a name="step-2-create-a-port-mirroring-profile"></a>Steg 2. Skapa en port speglings profil

I det här steget ska du definiera en profil för trafik riktningen för käll-och mål dator grupper.

>[!NOTE]
>Kontrol lera att du har skapat både käll-och mål gruppen för virtuella datorer.

1. Välj **port spegling**  >  **Lägg till** och ange sedan:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Skärm bild som visar den information som krävs för port speglings profilen.":::

   - **Namn på port speglings namn** – beskrivande namn för profilen.
   - **Riktning** – Välj mellan ingångs-, utgående eller dubbelriktade.
   - **Källa** – Välj den virtuella käll gruppen.
   - **Mål** – Välj den virtuella mål dator gruppen.
   - **Beskrivning** – ange en beskrivning av port speglingen.

1. Klicka på **OK** för att slutföra profilen. 

   Profil-och VM-grupperna visas i Azure VMware-lösnings konsolen.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Konfigurera en DNS-vidarebefordrare i Azure Portal
Du konfigurerar en DNS-vidarebefordrare där särskilda DNS-begäranden vidarebefordras till en angiven DNS-server för matchning.  En DNS-vidarebefordrare associeras med en **standard-DNS-zon** och upp till tre **FQDN-zoner**.

>[!TIP]
>Du kan också använda [NSX-T Manager-konsolen för att konfigurera en DNS-vidarebefordrare](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Om du vill konfigurera en DNS-vidarebefordrare i Azure VMware-lösnings konsolen kan du:

* [Steg 1. Konfigurera en DNS-standardzon och en FQDN-zon](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – när en DNS-fråga tas emot jämför en DNS-vidarebefordrare domän namnet med domän namnen i DNS-zonen med fullständigt domän namn. 

* [Steg 2. Konfigurera DNS-tjänsten](#step-2-configure-dns-service) – du konfigurerar DNS forwarder-tjänsten.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Steg 1. Konfigurera en standard-DNS-zon och FQDN-zon
Du konfigurerar en standard-DNS-zon och en FQDN-zon för att skicka DNS-frågor till den överordnade servern.  När en DNS-fråga tas emot jämför DNS-vidarebefordraren domän namnet i frågan med de FQDN DNS-zonernas domän namn. Om en matchning hittas vidarebefordras frågan till de DNS-servrar som anges i DNS-zonen med fullständigt domän namn. Om ingen matchning hittas vidarebefordras frågan till de DNS-servrar som anges i standard-DNS-zonen.

>[!NOTE]
>Du måste definiera en standard-DNS-zon innan du konfigurerar en FQDN-zon. 

1. I ditt privata moln i Azure VMware-lösningen under **arbets belastnings nätverk** väljer du **DNS**  >  **-zoner**  >  **Lägg till**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Skärm bild som visar hur du lägger till DNS-zoner och en DNS-tjänst.":::

1. Välj **standard-DNS-zon** och ange:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Skärm bild som visar hur du lägger till en standard-DNS-zon.":::

   1. Ett namn för DNS-zonen.

   1. Upp till tre IP-adresser för DNS-servrar i formatet **8.8.8.8**.

1. Välj **FQDN-zon** och ange:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Skärm bild som visar hur du lägger till en FQDN-zon. ":::

   1. Ett namn för DNS-zonen.

   1. FQDN-domänen.

   1. Upp till tre IP-adresser för DNS-servrar i formatet **8.8.8.8**.

1. Välj **OK** för att lägga till standard-DNS-zonen och DNS-tjänsten.

### <a name="step-2-configure-dns-service"></a>Steg 2. Konfigurera DNS-tjänsten

1. Välj fliken **DNS-tjänst** och välj **Lägg till**. Ange informationen och välj **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Skärm bild som visar den information som krävs för DNS-tjänsten.":::

   >[!TIP]
   >**Nivån-1 Gateway** är markerad som standard och återspeglar den gateway som skapades när du distribuerar Azure VMware-lösningen.

   DNS-tjänsten har lagts till.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Skärm bild som visar att DNS-tjänsten har lagts till.":::

