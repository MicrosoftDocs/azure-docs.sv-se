---
title: Metod tips för AKS affärs kontinuitet och haveri beredskap
description: Lär dig mer om en kluster operatörs bästa praxis för att uppnå maximal drift tid för dina program, vilket ger hög tillgänglighet och förbereder för haveri beredskap i Azure Kubernetes service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105093"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Metod tips för verksamhets kontinuitet och haveri beredskap i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) blir programmets drift tid viktig. Som standard ger AKS hög tillgänglighet genom att använda flera noder i en [skalnings uppsättning för virtuella datorer (VMSS)](../virtual-machine-scale-sets/overview.md). Men dessa flera noder skyddar inte systemet från ett regions haveri. Du kan maximera drift tiden genom att planera i förväg för att upprätthålla affärs kontinuiteten och förbereda för haveri beredskap.

Den här artikeln fokuserar på hur du planerar för verksamhets kontinuitet och haveri beredskap i AKS. Lär dig att:

> [!div class="checklist"]
> * Planera för AKS-kluster i flera regioner.
> * Dirigera trafik över flera kluster med hjälp av Azure Traffic Manager.
> * Använd geo-replikering för dina register för behållar avbildningar.
> * Planera för program tillstånd över flera kluster.
> * Replikera lagringen över flera regioner.

## <a name="plan-for-multiregion-deployment"></a>Planera för distribution i flera regioner

> **Bästa praxis**
>
> När du distribuerar flera AKS-kluster väljer du regioner där AKS är tillgängligt. Använd kopplade regioner.

Ett AKS-kluster distribueras till en enda region. Om du vill skydda systemet från regions fel kan du distribuera programmet till flera AKS-kluster i olika regioner. När du planerar var du ska distribuera ditt AKS-kluster bör du tänka på följande:

* [**Tillgänglighet för AKS-regionen**](./quotas-skus-regions.md#region-availability)
    * Välj regioner som är nära dina användare. 
    * AKS utökas kontinuerligt till nya regioner.
* [**Azure-kopplade regioner**](../best-practices-availability-paired-regions.md)
    * För ditt geografiska område väljer du två regioner kopplade samman.
    * Kopplade regioner koordinerar plattforms uppdateringar och prioriterar återställnings ansträngningar vid behov.
* **Tjänst tillgänglighet**
    * Bestäm om dina kopplade regioner ska vara varm/frekvent, varm/varm eller varm/kall.
    * Vill du köra båda regionerna samtidigt, och en region är *redo* att börja betjäna trafik? Eller:
    * Vill du ge en region tid för att bli redo att betjäna trafiken?

AKS regions tillgänglighet och kopplade regioner är ett gemensamt övervägande. Distribuera dina AKS-kluster i kopplade regioner som är utformade för att hantera Disaster Recovery för regioner tillsammans. Till exempel är AKS tillgängligt i USA, östra och västra USA. Dessa regioner är kopplade till varandra. Välj dessa två regioner när du skapar en AKS BC/DR-strategi.

När du distribuerar ditt program lägger du till ett annat steg till din CI/CD-pipeline för att distribuera till dessa flera AKS-kluster. Uppdatering av pipelines för distribution förhindrar att program distribueras till bara en av dina regioner och AKS-kluster. I det scenariot får inte kund trafik som är riktad mot en sekundär region de senaste kod uppdateringarna.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använd Azure Traffic Manager för att dirigera trafik

> **Bästa praxis**
>
> Azure Traffic Manager kan dirigera dig till närmaste AKS-kluster och program instans. För bästa prestanda och redundans ska du dirigera all program trafik genom Traffic Manager innan den går till ditt AKS-kluster.

Om du har flera AKS-kluster i olika regioner använder du Traffic Manager för att styra trafikflödet till de program som körs i varje kluster. [Azure Traffic Manager](../traffic-manager/index.yml) är en DNS-baserad trafikbelastnings utjämning som kan distribuera nätverks trafik över flera regioner. Använd Traffic Manager för att dirigera användare baserat på kluster svars tid eller baserat på geografi.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Om du har ett enda AKS-kluster ansluter du normalt till tjänstens IP-adress eller DNS-namn för ett visst program. I en distribution med flera kluster bör du ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna på varje AKS-kluster. Definiera de här tjänsterna genom att använda Traffic Manager-slutpunkter. Varje slut punkt är *IP för tjänst belastnings utjämning*. Använd den här konfigurationen för att dirigera nätverks trafik från Traffic Manager slut punkt i en region till slut punkten i en annan region.

![Geografisk routning genom Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager utför DNS-sökningar och returnerar din lämpligaste slut punkt. Kapslade profiler kan prioritera en primär plats. Du bör till exempel ansluta till deras närmaste geografiska region. Om den här regionen har ett problem, Traffic Manager dirigerar dig till en sekundär region. Den här metoden säkerställer att du kan ansluta till en program instans även om den närmaste geografiska regionen inte är tillgänglig.

Information om hur du konfigurerar slut punkter och routning finns i [Konfigurera principen för geografisk trafik cirkulation med hjälp av Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Application routing med Azure-tjänsten för front dörr

Med hjälp av delat TCP-baserat anycast-protokoll ansluter [Azure frontend-tjänsten](../frontdoor/front-door-overview.md) till dina slutanvändare till närmaste pop-dörr (punkt för närvaro). Fler funktioner i Azures tjänst för front dörr:
* TLS-terminering
* Anpassad domän
* Brandvägg för webbaserade program
* URL-omskrivning
* Sessionstillhörighet 

Granska behoven hos din program trafik för att förstå vilken lösning som passar bäst.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interconnect-regioner med global peering för virtuella nätverk

Anslut båda virtuella nätverken till varandra via [peering av virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) för att aktivera kommunikation mellan kluster. Peering för virtuella nätverk kopplar samman virtuella nätverk, vilket ger hög bandbredd i Microsofts stamnät nätverk – även i olika geografiska regioner.

Innan du peer-koppla virtuella nätverk med AKS kluster använder du standard Load Balancer i ditt AKS-kluster. Den här förutsättningen gör att Kubernetes-tjänster kan uppnås i det virtuella nätverkets peering.

## <a name="enable-geo-replication-for-container-images"></a>Aktivera geo-replikering för behållar avbildningar

> **Bästa praxis**
> 
> Lagra behållar avbildningarna i Azure Container Registry och geo-replikera registret till varje AKS region.

Om du vill distribuera och köra dina program i AKS behöver du ett sätt att lagra och hämta behållar avbildningarna. Container Registry integreras med AKS så att det säkert kan lagra behållar avbildningar eller Helm-diagram. Container Registry stöder geo-replikering med flera huvud servrar för att automatiskt replikera dina avbildningar till Azure-regioner runtom i världen. 

Förbättra prestanda och tillgänglighet:
1. Använd Container Registry geo-replikering för att skapa ett register i varje region där du har ett AKS-kluster. 
1. Varje AKS-kluster hämtar sedan behållar avbildningar från det lokala behållar registret i samma region:

![Container Registry geo-replikering för behållar avbildningar](media/operator-best-practices-bc-dr/acr-geo-replication.png)

När du använder Container Registry geo-replikering för att hämta avbildningar från samma region, är resultaten:

* **Snabbare**: Hämta bilder från höghastighets nätverks anslutningar med låg latens inom samma Azure-region.
* **Mer tillförlitligt**: om en region inte är tillgänglig hämtar ditt AKS-kluster avbildningarna från ett tillgängligt behållar register.
* **Billigare**: ingen utgående nätverks belastning mellan data Center.

Geo-replikering är en *Premium* -funktion för SKU-behållare. Information om hur du konfigurerar geo-replikering finns [container Registry geo-replikering](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Ta bort tjänst tillstånd från behållare i behållare

> **Bästa praxis**
> 
> Undvik att lagra tjänst tillstånd i behållaren. Använd i stället en Azure-plattform som en tjänst (PaaS) som stöder replikering i flera regioner.

*Tjänst tillstånd* syftar på indata-eller disk data som krävs av en tjänst för att fungera. Status omfattar de data strukturer och medlemsvariabler som tjänsten läser och skriver. Beroende på hur tjänsten är konstruerad kan tillstånd även innehålla filer eller andra resurser som lagras på disken. Till exempel kan tillstånd innehålla de filer som en databas använder för att lagra data och transaktions loggar.

Status kan antingen vara extern eller samordnad med den kod som ändrar tillstånd. Normalt Externalize du tillstånd genom att använda en databas eller ett annat data lager som körs på olika datorer över nätverket eller som tar slut på samma dator.

Behållare och mikrotjänster är mest elastiska när processer som körs inuti dem inte behåller sitt tillstånd. Eftersom program nästan alltid innehåller status, använder du en PaaS-lösning, till exempel:
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL Database

Information om hur du skapar bärbara program finns i följande rikt linjer:

* [Den 12-Factor app-metoden](https://12factor.net/)
* [Köra ett webb program i flera Azure-regioner](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Skapa en plan för lagringsmigrering

> **Bästa praxis**
>
> Om du använder Azure Storage förbereder du och testar hur du migrerar lagringen från den primära regionen till säkerhets kopierings regionen.

Dina program kan använda Azure Storage för sina data. I så fall sprids programmen över flera AKS-kluster i olika regioner. Du måste hålla lagringen synkroniserad. Här följer två vanliga sätt att replikera lagring:

* Infrastruktur-baserad asynkron replikering
* Programbaserad asynkron replikering

### <a name="infrastructure-based-asynchronous-replication"></a>Infrastruktur-baserad asynkron replikering

Dina program kan kräva beständig lagring även efter att en pod har tagits bort. I Kubernetes kan du använda beständiga volymer för att bevara data lagring. Beständiga volymer monteras till en virtuell nod och exponeras sedan för poddar. Beständiga volymer följer poddar även om poddar flyttas till en annan nod i samma kluster.

Vilken replikeringsprincip du använder beror på din lagrings lösning. Följande vanliga lagrings lösningar ger sin egen vägledning om haveri beredskap och replikering:
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Normalt anger du en gemensam lagrings plats där program skriver sina data. Dessa data replikeras sedan mellan regioner och används lokalt.

![Infrastruktur-baserad asynkron replikering](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Om du använder Azure Managed Disks kan du använda [Velero på Azure][velero] och [kasten][kasten] för att hantera replikering och haveri beredskap. De här alternativen säkerhetskopierar lösningar som är inbyggda i, men som inte stöds av Kubernetes.

### <a name="application-based-asynchronous-replication"></a>Programbaserad asynkron replikering

Kubernetes tillhandahåller för närvarande ingen inbyggd implementering för programbaserad asynkron replikering. Eftersom behållare och Kubernetes är löst kopplade, bör alla traditionella program-och språk metoder fungera. Programmen replikerar vanligt vis lagrings begär Anden, som sedan skrivs till varje klusters underliggande data lagring.

![Programbaserad asynkron replikering](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på verksamhets kontinuitet och haveri beredskap för AKS-kluster. Mer information om kluster åtgärder i AKS finns i följande artiklar om metod tips:

* [Flera innehavare och kluster isolering][aks-best-practices-cluster-isolation]
* [Basic Kubernetes Scheduler-funktioner][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/