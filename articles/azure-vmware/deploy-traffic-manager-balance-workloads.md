---
title: Distribuera Traffic Manager för att balansera Azure VMware Solution arbetsbelastningar
description: Lär dig hur du integrerar Traffic Manager med Azure VMware Solution för att balansera programarbetsbelastningar över flera slutpunkter i olika regioner.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876930"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Distribuera Traffic Manager för att balansera Azure VMware Solution arbetsbelastningar

Den här artikeln beskriver steg för steg hur du [integrerar Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) med Azure VMware Solution. Integreringen balanserar programarbetsbelastningar över flera slutpunkter. Den här artikeln går också igenom stegen för hur du konfigurerar Traffic Manager för att dirigera trafik [mellan tre Azure Application Gateway](../application-gateway/overview.md) flera Azure VMware Solution regioner. 

Gatewayerna har Azure VMware Solution (VM) konfigurerade som medlemmar i serverpoolen för att belastningsutjämna inkommande Layer 7-begäranden. Mer information finns i Använda [Azure Application Gateway för att skydda dina webbappar på Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)

Diagrammet visar hur Traffic Manager ger belastningsutjämning för programmen på DNS-nivå mellan regionala slutpunkter. Gatewayerna har medlemmar i serverpoolen konfigurerade som IIS-servrar och refereras till Azure VMware Solution externa slutpunkter. Anslutningen över det virtuella nätverket mellan de två privata molnregionerna använder en ExpressRoute-gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Arkitekturdiagram över Traffic Manager integrering med Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Innan du börjar ska du först [läsa Igenom Kraven](#prerequisites) och sedan går vi igenom procedurerna för att:

> [!div class="checklist"]
> * Kontrollera konfigurationen av dina programgatewayer och NSX-T-segmentet
> * Skapa din Traffic Manager profil
> * Lägga till externa slutpunkter i din Traffic Manager profil

## <a name="prerequisites"></a>Förutsättningar

- Tre virtuella datorer konfigurerade som Microsoft IIS-servrar som körs i olika Azure VMware Solution regioner: 
   - USA, västra
   - Europa, västra
   - USA, östra (lokalt) 

- En programgateway med externa slutpunkter i de Azure VMware Solution som nämns ovan.

- Värd med Internetanslutning för verifiering. 

- Ett [NSX-T-nätverkssegment som skapats i Azure VMware Solution](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verifiera konfigurationen av programgatewayer

Följande steg kontrollerar konfigurationen av dina programgatewayer.

1. I Azure Portal du **Programgatewayer för** att visa en lista över dina aktuella programgatewayer:

   - AVS-GW-WUS
   - AVS-GW-EUS (lokalt)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Skärmbild av sidan Application Gateway med en lista över konfigurerade programgatewayer." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Välj en av dina tidigare distribuerade programgatewayer. 

   Ett fönster öppnas som visar olika typer av information om programgatewayen. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Skärmbild av sidan Programgateway med information om den valda programgatewayen." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Välj **Serverpooler för** att verifiera konfigurationen av en av serverpoolerna. Du ser en medlem i serverdelspoolen för virtuella datorer konfigurerad som en webbserver med IP-adressen 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Skärmbild av sidan Redigera backend-pool med mål-IP-adressen markerad.":::

1. Kontrollera konfigurationen för andra programgatewayer och medlemmar i serverpoolen. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Kontrollera segmentkonfigurationen för NSX-T

Följande steg kontrollerar konfigurationen av NSX-T-segmentet i Azure VMware Solution miljö.

1. Välj **Segment** för att visa dina konfigurerade segment.  Du ser Att Contoso-segment1 är anslutet till en contoso-T01-gateway, en flexibel router på nivå 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Skärmbild som visar segmentprofiler i NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Välj **Gateway på nivå 1 om** du vill se en lista över gatewayer på nivå 1 med antalet länkade segment. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Skärmbild som visar gatewayadressen för det valda segmentet.":::    

1. Välj det segment som är länkat till Contoso-T01. Ett fönster öppnas som visar det logiska gränssnittet som konfigurerats på nivå 01-routern. Den fungerar som en gateway till den virtuella datorn för medlemmar i backend-poolen som är ansluten till segmentet.

1. I vSphere-klienten väljer du den virtuella datorn för att visa dess information. 

   >[!NOTE]
   >Ip-adressen matchar medlem i serverdelspoolen för virtuella datorer som konfigurerats som en webbserver från föregående avsnitt: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Skärmbild som visar information om virtuella datorer i vSphere-klienten." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Välj den virtuella datorn och välj **sedan ÅTGÄRDER > Redigera inställningar för** att verifiera anslutningen till NSX-T-segmentet.

## <a name="create-your-traffic-manager-profile"></a>Skapa din Traffic Manager profil

1. Logga in på [Azure-portalen](https://rc.portal.azure.com/#home). Under **Azure Services > Networking (Nätverk)** väljer Traffic Manager **profiler**.

2. Välj **+ Lägg till** för att skapa en Traffic Manager profil.
 
3. Ange följande information och välj sedan **Skapa:**

   - Profilnamn
   - Routningsmetod (använd [viktad](../traffic-manager/traffic-manager-routing-methods.md)
   - Prenumeration
   - Resursgrupp

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Lägga till externa slutpunkter i Traffic Manager profilen

1. Välj Traffic Manager i sökresultatfönstret, välj Slutpunkter **och** sedan **+ Lägg till**.

1. För var och en av de externa slutpunkterna i de olika regionerna anger du nödvändig information och väljer sedan Lägg **till**: 
   - Typ
   - Name
   - Fullständigt kvalificerat domännamn (FQDN) eller IP
   - Vikt (tilldela en vikt på 1 till varje slutpunkt). 

   När de har skapats visas alla tre i Traffic Manager profilen. Övervakningsstatusen för alla tre måste vara **Online**.

3. Välj **Översikt** och kopiera URL:en under **DNS-namn.**

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Skärmbild som visar Traffic Manager slutpunktsöversikt med DNS-namnet markerat." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Klistra in DNS-namn-URL:en i en webbläsare. Skärmbilden visar trafik som dirigerar till regionen Europa, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Skärmbild av webbläsarfönstret som visar trafik som dirigeras till Europa, västra."::: 

5. Uppdatera webbläsaren. Skärmbilden visar trafik som dirigerar till en annan uppsättning medlemmar i backend-poolen i regionen USA, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Skärmbild av webbläsarfönstret som visar trafik som dirigeras till USA, västra."::: 

6. Uppdatera webbläsaren igen. Skärmbilden visar trafik som dirigerar till den slutliga uppsättningen medlemmar i backend-poolen lokalt.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Skärmbild av webbläsarfönstret som visar trafik som dirigeras till den lokala platsen.":::

## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på integrering Azure Traffic Manager med Azure VMware Solution kanske du vill lära dig mer om:

- [Använda Azure Application Gateway på Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager-dirigeringsmetoder](../traffic-manager/traffic-manager-routing-methods.md)
- [Kombinera tjänster för belastningsutjämning i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Mäta Traffic Manager prestanda](../traffic-manager/traffic-manager-performance-considerations.md)
