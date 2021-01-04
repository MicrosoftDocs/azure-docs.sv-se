---
title: Hantera DHCP för Azure VMware-lösning
description: Lär dig hur du skapar och hanterar DHCP för ditt privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708559"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Hantera DHCP för Azure VMware-lösning

Program och arbets belastningar som körs i en privat moln miljö kräver DHCP-tjänster för IP-adress tilldelningar.  Den här artikeln visar hur du skapar och hanterar DHCP i Azure VMware-lösningen på två sätt:

- Om du använder NSX-T för att vara värd för DHCP-servern måste du [skapa en DHCP-server](#create-a-dhcp-server) och [vidarebefordra till den servern](#create-dhcp-relay-service). När du skapar DHCP-servern lägger du också till ett nätverks segment och anger IP-adressintervallet för DHCP.   

- Om du använder en extern DHCP-server från tredje part i nätverket måste du [skapa DHCP Relay service](#create-dhcp-relay-service). När du skapar ett relä till en DHCP-server, oavsett om du använder NSX-T eller en tredje part som värd för din DHCP-server, måste du ange IP-adressintervallet för DHCP.

>[!IMPORTANT]
>DHCP fungerar inte för virtuella datorer (VM) i VMware HCX L2-utsträcknings nätverk när DHCP-servern finns i det lokala data centret.  NSX blockerar som standard alla DHCP-begäranden från att passera L2-utsträckningen. Information om lösningen finns i [Skicka DHCP-begäranden till den lokala DHCP-server](#send-dhcp-requests-to-the-on-premises-dhcp-server) proceduren.


## <a name="create-a-dhcp-server"></a>Skapa en DHCP-server

Om du vill använda NSX-T som värd för DHCP-servern skapar du en DHCP-server. Sedan lägger du till ett nätverks segment och anger IP-adressintervallet för DHCP.

1. I NSX-T Manager väljer du **nätverks**  >  **-DHCP** och väljer sedan **Lägg till Server**.

1. Välj **DHCP** som **Server typ**, ange Server namn och IP-adress och välj sedan **Spara**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj **nivå 1-gatewayer**, Välj den lodräta ellipsen på nivå 1-gatewayen och välj sedan **Redigera**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Välj den gateway som ska användas" border="true":::

1. Välj **ingen IP-allokerad uppsättning** för att lägga till ett undernät.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Lägg till ett undernät" border="true":::

1. I **typ** väljer du **DHCP lokal server**. 
   
1. För **DHCP-servern** väljer du **standard-DHCP** och väljer sedan **Spara**.

1. Välj **Spara** igen och välj sedan **Stäng redigering**.

### <a name="add-a-network-segment"></a>Lägg till ett nätverks segment

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Skapa DHCP Relay service

Om du vill använda en extern DHCP-server från tredje part måste du skapa en DHCP Relay-tjänst. Du anger också DHCP IP-adressintervallet i NSX-T-hanteraren. 

1. I NSX-T Manager väljer du **nätverks**  >  **-DHCP** och väljer sedan **Lägg till Server**.

1. Välj **DHCP-relä** för **Server typ**, ange Server namn och IP-adress och välj sedan **Spara**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Skapa DHCP Relay service" border="true":::

1. Välj **nivå 1-gatewayer**, Välj den lodräta ellipsen på nivå 1-gatewayen och välj sedan **Redigera**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="redigera nivå 1-Gateway" border="true":::

1. Välj **ingen IP-allokerad uppsättning** för att definiera IP-adressallokering.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Redigera IP-adressallokering" border="true":::

1. I **typ** väljer du **DHCP-server**. 
   
1. För **DHCP-servern** väljer du **DHCP Relay** och väljer sedan **Spara**.

1. Välj **Spara** igen och välj sedan **Stäng redigering**.


## <a name="specify-the-dhcp-ip-address-range"></a>Ange IP-adressintervall för DHCP

1. I NSX-T Manager väljer du **nätverks**  >  **segment**. 
   
1. Välj den lodräta ellipsen i segment namnet och välj **Redigera**.
   
1. Välj **Ange undernät** för att ange DHCP IP-adress för under nätet. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="nätverks segment" border="true":::
      
1. Ändra IP-adressen för gatewayen om det behövs och ange IP-adressen för DHCP-intervallet. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="redigera undernät" border="true":::
      
1. Välj **Verkställ** och sedan **Spara**. Segmentet tilldelas en DHCP-serverpoolen.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP-serverpoolen tilldelad till segment" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Skicka DHCP-begäranden till den lokala DHCP-servern

Om du vill skicka DHCP-begäranden från dina virtuella datorer i Azure VMware-lösningen i det utökade L2-segmentet till den lokala DHCP-servern skapar du en säkerhets segment profil. 

1. Logga in på din lokala vCenter och välj **HCX** under **Start**.

1. Välj **nätverks tillägg** under **tjänster**.

1. Välj det nätverks tillägg som du vill ge stöd för DHCP-begäranden från Azure VMware-lösningen till lokalt. 

1. Anteckna mål nätverkets namn.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Skärm bild av ett nätverks tillägg i VMware vSphere-klienten" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. I Azure VMware-lösningen NSX-T Manager väljer du **nätverks**  >  **segment**  >  **segment profiler**. 

1. Välj **Lägg till segment profil** och sedan **segment säkerhet**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Skärm bild av hur du lägger till en segment profil i NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Ange ett namn och en tagg och ange sedan att **BPDU-filtret** ska växla till på och att alla DHCP växlar till av.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Skärm bild som visar BPDU-filtret aktiverat och DHCP växlar" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Ta bort alla MAC-adresser, om de finns, under **listan över tillåtna BPDU-filter**.  Välj sedan **Spara**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Skärm bild som visar MAC-adresser i listan över tillåtna BPDU-filter":::

1. Under **nätverks**  >  **segment**  >  **segment**, i Sök fältet, anger du definitions nätverks namnet.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Skärm bild av filter fältet nätverks > segment":::

1. Välj den lodräta ellipsen i segment namnet och välj **Redigera**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Skärm bild av knappen Redigera för segmentet" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Ändra **segment säkerheten** till segment profilen som du skapade tidigare.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Skärm bild av fältet segment säkerhet" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Nästa steg

Läs mer om [värd underhåll och livs cykel hantering](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).