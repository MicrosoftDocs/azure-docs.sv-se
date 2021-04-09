---
title: Koncept – integrera en Azure VMware-lösning distribution i en hubb och eker-arkitektur
description: Lär dig mer om att integrera en Azure VMware-distribution i en hubb och eker-arkitektur på Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99062753"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrera Azure VMware-lösningen i en hubb och eker-arkitektur

Den här artikeln innehåller rekommendationer för att integrera en Azure VMware-distribution av VMware-lösningen i en befintlig eller ny [hubb och eker-arkitektur](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) på Azure. 


NAV-och eker-scenariot förutsätter en hybrid moln miljö med arbets belastningar på:

* Inbyggd Azure med IaaS-eller PaaS-tjänster
* Azure VMware Solution 
* vSphere lokalt

## <a name="architecture"></a>Arkitektur

*Hubben* är en Azure-Virtual Network som fungerar som en central punkt för anslutningen till ditt lokala och Azure VMware-lösningens privata moln. *Ekrarna* är virtuella nätverk som peer-kopplas med hubben för att aktivera kommunikation mellan virtuella nätverk.

Trafik mellan det lokala data centret, Azure VMware-lösningens privata moln och navet går via Azure ExpressRoute-anslutningar. Eker-virtuella nätverk innehåller vanligt vis IaaS-baserade arbets belastningar, men kan ha PaaS-tjänster som [App Service-miljön](../app-service/environment/intro.md), som har direkt integrering med Virtual Network eller andra PaaS-tjänster med [Azure Private Link](../private-link/index.yml) aktive rad.

>[!IMPORTANT]
>Du kan använda en befintlig ExpressRoute-Gateway för att ansluta till Azure VMware-lösningen så länge den inte överskrider gränsen på fyra ExpressRoute-kretsar per virtuellt nätverk.  Men för att få åtkomst till Azure VMware-lösningen från lokala platser via ExpressRoute måste du ha ExpressRoute Global Reach eftersom ExpressRoute-gatewayen inte tillhandahåller transitiv routning mellan dess anslutna kretsar.

Diagrammet visar ett exempel på en distribution av hubb och ekrar i Azure som är ansluten till en lokal och Azure VMware-lösning via ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware-lösning hubb och eker-integrering" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

Arkitekturen har följande huvud komponenter:

- **Lokal plats:** Kundens lokala data Center (er) anslutna till Azure via en ExpressRoute-anslutning.

- **Privat moln i Azure VMware-lösning:** Azure VMware-lösningen SDDC utformad av ett eller flera vSphere-kluster, var och en med högst 16 värdar.

- **ExpressRoute-Gateway:** Möjliggör kommunikationen mellan Azure VMware-lösningen privat moln, delade tjänster på hubb virtuellt nätverk och arbets belastningar som körs på ekrar virtuella nätverk.

- **ExpressRoute Global Reach:** Möjliggör anslutning mellan lokalt och privat moln i Azure VMware-lösningen. Anslutningen mellan Azure VMware-lösningen och Azure-infrastrukturen är via ExpressRoute endast Global Reach. Du kan inte välja något annat alternativ än ExpressRoute snabb väg.  ExpressRoute Direct stöds inte.


- **Överväganden för S2S VPN:** För produktions distributioner av Azure VMware-lösningar stöds inte Azure S2S VPN på grund av nätverks krav för VMware HCX. Du kan dock använda den för en PoC-distribution.


- **Hubb virtuellt nätverk:** Fungerar som central punkt för anslutningen till ditt lokala nätverk och Azure VMware-lösningens privata moln.

- **Virtuellt nätverk i eker**

    - **IaaS eker:** En IaaS eker är värd för Azure IaaS-baserade arbets belastningar, inklusive tillgänglighets uppsättningar för virtuella datorer och skalnings uppsättningar för virtuella datorer och motsvarande nätverks komponenter.

    - **PaaS eker:** En PaaS eker är värd för Azure PaaS-tjänster med hjälp av privat adressering tack vare [privat slut punkt](../private-link/private-endpoint-overview.md) och [privat länk](../private-link/private-link-overview.md).

- **Azure-brand vägg:** Fungerar som en central del för att segmentera trafik mellan ekrar och Azure VMware-lösningen.

- **Application Gateway:** Exponerar och skyddar webbappar som körs antingen på Azure IaaS/PaaS eller virtuella datorer i Azure VMware-lösningen. Den integreras med andra tjänster som API Management.

## <a name="network-and-security-considerations"></a>Nätverks-och säkerhets aspekter

ExpressRoute-anslutningar möjliggör trafik som flödar mellan lokala Azure VMware-lösningar och Azures nätverks infrastruktur resurs. Azure VMware-lösningen använder [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) för att implementera den här anslutningen.

Eftersom en ExpressRoute-Gateway inte tillhandahåller transitiv routning mellan sina anslutna kretsar, måste även lokal anslutning använda ExpressRoute Global Reach för att kommunicera mellan den lokala vSphere-miljön och Azure VMware-lösningen. 

* **Trafik flöde för lokala Azure VMware-lösningar**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Trafik flöde för lokala Azure VMware-lösningar" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware-lösning till hubben för VNET-trafikflöde**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware-lösning för hubb av virtuellt nätverks trafikflöde" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Mer information om nätverks-och anslutnings koncept i Azure VMware-lösningar finns i [produkt dokumentationen för Azure VMware-lösningen](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Trafik segmentering

[Azure-brandväggen](../firewall/index.yml) är den centrala delen av hubb och eker-topologin som distribueras på det virtuella hubb nätverket. Använd Azure-brandväggen eller en annan virtuell Azure-installation som stöds av virtuella nätverk för att upprätta trafik regler och segmentera kommunikationen mellan olika ekrar och arbets belastningar för Azure VMware-lösningar.

Skapa routningstabeller för att dirigera trafiken till Azure-brandväggen.  För de virtuella eker-nätverken skapar du en väg som anger standard vägen till det interna gränssnittet i Azure-brandväggen. På så sätt kan brand väggen, när en arbets belastning i Virtual Network behöver komma åt Azure VMware-lösningens adress utrymme, utvärdera det och tillämpa motsvarande trafik regel för att antingen tillåta eller neka den.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Skapa routningstabeller för att dirigera trafik till Azure-brandväggen" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> En väg med adressprefixet 0.0.0.0/0 i **GatewaySubnet** -inställningen stöds inte.

Ange vägar för vissa nätverk i motsvarande routningstabell. Till exempel kan vägar för att uppnå Azure VMware lösnings hantering och arbets belastningar IP-prefix från eker-arbetsbelastningar och tvärtom.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Ange vägar för vissa nätverk i motsvarande routningstabell" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

En andra nivå av trafik segmentering som använder nätverks säkerhets grupper i ekrar och hubben för att skapa en mer detaljerad trafik princip.

> [!NOTE]
> **Trafik från lokal till Azure VMware-lösning:** Trafik mellan lokala arbets belastningar, antingen vSphere-baserade eller andra, aktive ras av Global Reach, men trafiken går inte via Azure-brandväggen på hubben. I det här scenariot måste du implementera mekanismer för trafik segmentering, antingen lokalt eller i Azure VMware-lösning.

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway v1 och v2 har testats med webbappar som körs på virtuella datorer i Azure VMware-lösningen som en backend-pool. Application Gateway är för närvarande den enda metoden som stöds för att exponera webbappar som körs på virtuella datorer i Azure VMware-lösningen till Internet. Det kan också exponera appar till interna användare på ett säkert sätt.

Mer information finns i den Azure VMware Solution-/regionsspecifika artikeln på [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md).

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Sekundär nivå för trafik segmentering med nätverks säkerhets grupper" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Hopp Box och Azure-skydds

Få åtkomst till Azure VMwares lösnings miljö med en hopp ruta, som är en virtuell Windows 10-eller Windows Server-dator som har distribuerats i det delade tjänst under nätet i det virtuella hubb nätverket.

>[!IMPORTANT]
>Azure skydds är den tjänst som rekommenderas för att ansluta till hopp rutan för att förhindra att Azure VMware-lösningen exponeras för Internet. Du kan inte använda Azure-skydds för att ansluta till virtuella Azure VMware-lösningar eftersom de inte är Azure IaaS-objekt.  

Av säkerhets skäl bör du distribuera [Microsoft Azure skydds](../bastion/index.yml) -tjänsten i det virtuella hubb nätverket. Azure skydds ger sömlös RDP-och SSH-åtkomst till virtuella datorer som distribueras i Azure utan att behöva etablera offentliga IP-adresser till dessa resurser. När du har etablerat Azure skydds-tjänsten kan du komma åt den valda virtuella datorn från Azure Portal. När du har upprättat anslutningen öppnas en ny flik som visar rutan Skriv bord och du kan komma åt Azure VMware-lösningens privata moln hanterings plan.

> [!IMPORTANT]
> Ge inte en offentlig IP-adress till den virtuella hopp rutan eller exponera port 3389/TCP på det offentliga Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtuellt nätverk för Azure skydds Hub" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS lösnings överväganden

För Azure DNS upplösning finns det två tillgängliga alternativ:

-   Använd domän kontrol Lanterna som distribueras på hubben (beskrivs i [identitets överväganden](#identity-considerations)) som namnservrar.

-   Distribuera och konfigurera en Azure DNS privat zon.

Det bästa sättet är att kombinera båda för att tillhandahålla tillförlitlig namn matchning för Azure VMware-lösning, lokalt och Azure.

Som en allmän rekommendation använder du den befintliga Azure DNS-infrastrukturen (i det här fallet Active Directory-integrerad DNS) som distribueras till minst två virtuella Azure-datorer som distribuerats i det virtuella Hubbs nätverket och konfigureras i de virtuella eker-nätverken för att använda dessa Azure DNS-servrar i DNS-inställningarna.

Du kan använda Azure Privat DNS, där Azure Privat DNS Zone länkar till det virtuella nätverket.  DNS-servrarna används som hybrid lösningar med villkorlig vidarebefordran till lokala eller Azure VMware-lösningar som kör DNS med hjälp av kund Azure Privat DNS-infrastruktur. 

Aktivera automatisk registrering för att automatiskt hantera DNS-posternas livs cykel för de virtuella datorer som distribueras i de virtuella nätverken i ekrar. När det är aktiverat är det maximala antalet privata DNS-zoner bara en. Om den är inaktive rad är det maximala antalet 1000.

Lokala och Azure VMware Solution-servrar kan konfigureras med villkorliga vidarebefordrare för att matcha virtuella datorer i Azure för Azure Privat DNS-zonen.

## <a name="identity-considerations"></a>Identitets överväganden

För identitets syfte är det bästa sättet att distribuera minst en domänkontrollant på hubben. Använd två undernät för delade tjänster i zon-distribuerad mode eller en tillgänglighets uppsättning för virtuella datorer. Mer information om hur du utökar din lokala Active Directory-domän (AD) till Azure finns i [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Dessutom kan du distribuera en annan domänkontrollant på Azure VMware-lösningen för att agera som identitet och DNS-källa i vSphere-miljön.

Som rekommenderad praxis integrerar du [AD-domänen med Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
