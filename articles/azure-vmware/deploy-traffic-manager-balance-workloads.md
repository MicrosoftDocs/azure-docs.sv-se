---
title: Distribuera Traffic Manager för att balansera arbets belastningar för Azure VMware-lösningar
description: Lär dig att integrera Traffic Manager med Azure VMware-lösningen för att balansera program arbets belastningar över flera slut punkter i olika regioner.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988593"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Distribuera Traffic Manager för att balansera arbets belastningar för Azure VMware-lösningar

Den här artikeln beskriver steg för steg hur du integrerar [azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) med Azure VMware-lösningen. Integrationen balanserar program arbets belastningar över flera slut punkter. Den här artikeln beskriver också hur du konfigurerar Traffic Manager för att dirigera trafik mellan tre [Azure Application Gateway](../application-gateway/overview.md) som sträcker sig över flera Azure VMware-lösnings regioner. 

Gatewayerna har Azure VMware-lösning för virtuella datorer som kon figurer ATS som medlemmar i Server delen för att belastningsutjämna inkommande Layer 7-begäranden. Mer information finns i [använda Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösning](protect-azure-vmware-solution-with-application-gateway.md)

Diagrammet visar hur Traffic Manager tillhandahåller belastnings utjämning för program på DNS-nivå mellan regionala slut punkter. Gatewayerna har Server dels medlemmar konfigurerade som IIS-servrar och refereras till som Azure VMware-lösningar externa slut punkter. Anslutning över det virtuella nätverket mellan de två privata moln regionerna använder en ExpressRoute-Gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Arkitektur diagram för Traffic Manager-integrering med Azure VMware-lösning" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Innan du börjar måste du först granska [kraven](#prerequisites) och sedan gå igenom procedurerna för att:

> [!div class="checklist"]
> * Verifiera konfigurationen av dina programgatewayer och NSX-T-segmentet
> * Skapa din Traffic Manager-profil
> * Lägg till externa slut punkter i din Traffic Manager-profil

## <a name="prerequisites"></a>Förutsättningar

- Tre virtuella datorer som kon figurer ATS som Microsoft IIS-servrar som körs i olika Azure VMware-lösnings regioner: 
   - USA, västra
   - Europa, västra
   - USA, östra (lokalt) 

- En Programgateway med externa slut punkter i de Azure VMware-lösnings regioner som anges ovan.

- Värd med Internet anslutning för verifiering. 

- Ett [NSX-T-nätverks segment som skapats i Azure VMware-lösningen](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verifiera din Application Gateway-konfiguration

Följande steg kontrollerar konfigurationen av dina programgatewayer.

1. I Azure Portal väljer du **programgatewayer** för att visa en lista över aktuella programgatewayer:

   - AVS-GW-WUS
   - AVS-GW-EUS (lokalt)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Skärm bild av sidan Application Gateway som visar en lista över konfigurerade programgatewayer." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Välj en av dina tidigare distribuerade programgatewayer. 

   Ett fönster med information om programgatewayen visas. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Skärm bild av sidan Application Gateway som visar information om den valda programgatewayen." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Välj **backend-pooler** för att verifiera konfigurationen av en av backend-poolerna. Du ser en medlem i en virtuell dator i Server delen som är konfigurerad som en webb server med en IP-adress för 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Skärm bild av sidan Redigera server dels bassäng med mål-IP-adress markerad.":::

1. Verifiera konfigurationen av de andra programgatewayerna och medlemmar i Server delen. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Verifiera NSX-T-segmentets konfiguration

Följande steg kontrollerar konfigurationen av NSX-T-segmentet i Azure VMware-lösnings miljön.

1. Välj **segment** om du vill visa dina konfigurerade segment.  Du ser contoso-SEGMENT1 anslutna till contoso-T01 Gateway, en flexibel router på nivå 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Skärm bild som visar segment profiler i NSX-T-hanteraren." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Välj **nivå-1-gateways** om du vill visa en lista med nivå 1-gatewayer med antalet länkade segment. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Skärm bild som visar gateway-adressen för det valda segmentet.":::    

1. Välj det segment som är kopplat till contoso-T01. Ett fönster öppnas som visar det logiska gränssnitt som kon figurer ATS på nivån-01-routern. Den fungerar som en gateway till den virtuella datorns medlem i Server delen som är ansluten till segmentet.

1. I vSphere-klienten väljer du den virtuella datorn för att visa information om den. 

   >[!NOTE]
   >Dess IP-adress matchar den virtuella datorns Server dels grupp medlem som en webb server från föregående avsnitt: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Skärm bild som visar information om virtuella datorer i vSphere-klienten." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Välj den virtuella datorn och välj sedan **åtgärder > redigera inställningar** för att verifiera anslutningen till NSX-T-segmentet.

## <a name="create-your-traffic-manager-profile"></a>Skapa din Traffic Manager-profil

1. Logga in på [Azure-portalen](https://rc.portal.azure.com/#home). Under **Azure-tjänster > nätverk** väljer du **Traffic Manager profiler**.

2. Välj **+ Lägg** till för att skapa en ny Traffic Manager-profil.
 
3. Ange följande information och välj sedan **skapa**:

   - Profilnamn
   - Routningsmetod (Använd [viktat](../traffic-manager/traffic-manager-routing-methods.md)
   - Prenumeration
   - Resursgrupp

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Lägg till externa slut punkter i Traffic Manager profilen

1. Välj Traffic Manager profil i rutan Sök resultat, Välj **slut punkter** och sedan **+ Lägg till**.

1. Ange nödvändig information för var och en av de externa slut punkterna i de olika regionerna och välj sedan **Lägg till**: 
   - Typ
   - Name
   - Fullständigt kvalificerat domän namn (FQDN) eller IP
   - Vikt (tilldela en vikt på 1 till varje slut punkt). 

   När du har skapat alla tre visas i Traffic Manager profilen. Övervaknings status för alla tre måste vara **online**.

3. Välj **Översikt** och kopiera URL: en under **DNS-namn**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Skärm bild som visar en översikt över en Traffic Manager-slutpunkt med DNS-namn markerat." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Klistra in DNS-namn-URL: en i en webbläsare. Skärm bilden visar trafik som leder till regionen Europa, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Skärm bild av webbläsarfönstret som visar trafik som dirigeras till Västeuropa."::: 

5. Uppdatera webbläsaren. Skärm bilden visar trafik som dirigerar till en annan uppsättning medlemmar i Server delen i regionen USA, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Skärm bild av webbläsarfönstret som visar trafik som skickas till västra USA."::: 

6. Uppdatera webbläsaren igen. Skärm bilden visar trafik som dirigerar till den slutliga uppsättningen medlemmar i backend-poolen lokalt.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Skärm bild av webbläsarfönstret med trafik dirigerad till lokalt.":::

## <a name="next-steps"></a>Nästa steg

Nu när du har använt integreringen av Azure Traffic Manager med Azure VMware-lösningen kanske du vill lära dig mer om:

- [Använda Azure Application Gateway på Azure VMware-lösningen](protect-azure-vmware-solution-with-application-gateway.md).
- [Traffic Manager routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- [Kombinera tjänster för belastnings utjämning i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Mätning Traffic Manager prestanda](../traffic-manager/traffic-manager-performance-considerations.md).
