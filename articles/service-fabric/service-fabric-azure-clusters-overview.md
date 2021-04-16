---
title: Skapa kluster på Windows Server och Linux
description: Service Fabric kluster körs på Windows Server och Linux. Du kan distribuera och vara värd Service Fabric program var du än kan köra Windows Server eller Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520164"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Översikt över Service Fabric i Azure
Ett Service Fabric är en nätverksansluten uppsättning virtuella eller fysiska datorer där dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i Service Fabric balanserar om tjänstpartitionsreplikerna och instanserna över det ökade antalet noder. Den övergripande programprestandan förbättras och du får mindre åtkomst till minnet. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsreplikerna och instanserna över det minskade antalet noder för att bättre använda maskinvaran på varje nod.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning noder (virtuella datorer) i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen. 

## <a name="cluster-components-and-resources"></a>Klusterkomponenter och resurser
Ett Service Fabric i Azure är en Azure-resurs som använder och interagerar med andra Azure-resurser:
* Virtuella datorer och virtuella nätverkskort
* skalningsuppsättningar för virtuella datorer
* virtuella nätverk
* lastbalanserare
* lagringskonton
* offentliga IP-adresser

![Service Fabric kluster][Image]

### <a name="virtual-machine"></a>Virtuell dator
En [virtuell dator](../virtual-machines/index.yml) som ingår i ett kluster kallas dock för en nod, tekniskt sett är en klusternod en Service Fabric körningsprocess. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel [placeringsegenskaper](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Varje dator eller virtuell dator har en tjänst för automatisk *start,FabricHost.exe*, som börjar köras vid start och startar sedan två körbara filer, *Fabric.exe* och *FabricGateway.exe*, som utgör noden. En produktionsdistribution är en nod per fysisk eller virtuell dator. I testscenarier kan du vara värd för flera noder på en enskild dator eller virtuell dator genom *att köra flera instanser avFabric.exe* och *FabricGateway.exe*.

Varje virtuell dator är associerad med ett virtuellt nätverkskort (NIC) och varje nätverkskort tilldelas en privat IP-adress.  En virtuell dator tilldelas till ett virtuellt nätverk och en lokal balanserare via nätverkskortet.

Alla virtuella datorer i ett kluster placeras i ett virtuellt nätverk.  Alla noder i samma nodtyp/skalningsuppsättning placeras i samma undernät i det virtuella nätverket.  Dessa noder har bara privata IP-adresser och kan inte adresseras direkt utanför det virtuella nätverket.  Klienter kan komma åt tjänster på noderna via Azure Load Balancer.

### <a name="scale-setnode-type"></a>Skalningsuppsättning/nodtyp
När du skapar ett kluster definierar du en eller flera nodtyper.  Noderna, eller de virtuella datorerna, i en nodtyp har samma storlek och egenskaper, till exempel antal processorer, minne, antal diskar och disk-I/O.  En nodtyp kan till exempel vara för små virtuella datorer på frontend-sidan med portar öppna till Internet, medan en annan nodtyp kan vara för stora virtuella datorer i backend-datorer som bearbetar data. I Azure-kluster mappas varje nodtyp till en [VM-skalningsuppsättning.](../virtual-machine-scale-sets/index.yml)

Du kan använda skalningsuppsättningar för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster uppsättningar en separat skalningsuppsättning. Den Service Fabric körningen startas på varje virtuell dator i skalningsuppsättningen med hjälp av Azure VM-tillägg. Du kan oberoende skala upp eller ned varje nodtyp, ändra os-SKU:n som körs på varje klusternod, ha olika uppsättningar portar öppna och använda olika kapacitetsmått. En skalningsuppsättning har [fem uppgraderingsdomäner](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) [och fem feldomäner](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) och kan ha upp till 100 virtuella datorer.  Du skapar kluster med fler än 100 noder genom att skapa flera skalningsuppsättningar/nodtyper.

> [!IMPORTANT]
> Att välja antalet nodtyper för klustret och egenskaperna för varje nodtyp (storlek, primär, Internetuppriktad, antal virtuella datorer osv.) är en viktig uppgift.  Mer information finns i Överväganden [vid kapacitetsplanering för kluster.](service-fabric-cluster-capacity.md)

Mer information finns i [Service Fabric-nodtyper och VM-skalningsuppsättningar.](service-fabric-cluster-nodetypes.md)

### <a name="azure-load-balancer"></a>Azure Load Balancer
VIRTUELLA datorinstanser är kopplade bakom en [Azure-lastbalanserare](../load-balancer/load-balancer-overview.md), som är associerad med en offentlig [IP-adress](../virtual-network/public-ip-addresses.md) och DNS-etikett.  När du etablerar ett kluster *&lt; med klusternamn &gt;*, DNS-namnet, *&lt; klusternamnet &gt; . &lt; location &gt; .cloudapp.azure.com* är den DNS-etikett som är associerad med lastbalanseraren framför skalningsuppsättningen.

Virtuella datorer i ett kluster har bara [privata IP-adresser.](../virtual-network/private-ip-addresses.md)  Hanteringstrafik och tjänsttrafik dirigeras via den offentliga lastbalanseraren.  Nätverkstrafik dirigeras till dessa datorer via NAT-regler (klienter ansluter till specifika noder/instanser) eller belastningsutjämningsregler (trafiken går till virtuella datorer resursallokering).  En lastbalanserare har en associerad offentlig IP-adress med ett DNS-namn i formatet: *&lt; clustername &gt; . &lt; location &gt; .cloudapp.azure.com*.  En offentlig IP-adress är en annan Azure-resurs i resursgruppen.  Om du definierar flera nodtyper i ett kluster skapas en lastbalanserare för varje nodtyp/skalningsuppsättning. Du kan också konfigurera en enda lastbalanserare för flera nodtyper.  Den primära nodtypen har DNS-etiketten *&lt; clustername &gt; . &lt; location &gt; .cloudapp.azure.com*, andra nodtyper har DNS-etiketten *&lt; clustername &gt; - &lt; nodetype &gt; . &lt; location &gt; .cloudapp.azure.com*.

### <a name="storage-accounts"></a>Lagringskonton
Varje klusternodtyp stöds av ett [Azure Storage-konto](../storage/common/storage-introduction.md) och hanterade diskar.

## <a name="cluster-security"></a>Klustersäkerhet
Ett Service Fabric kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare ansluter till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret. 

### <a name="node-to-node-security"></a>Nod-till-nod-säkerhet
Nod-till-nod-säkerhet skyddar kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhetsscenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i att vara värd för program och tjänster i klustret. Service Fabric använder X.509-certifikat för att skydda ett kluster och tillhandahålla programsäkerhetsfunktioner.  Ett klustercertifikat krävs för att skydda klustertrafik och tillhandahålla kluster- och serverautentisering.  Själv signerade certifikat kan användas för testkluster, men ett certifikat från en betrodd certifikatutfärdare bör användas för att skydda produktionskluster.

Mer information finns i [Nod-till-nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Säkerhet från klient till nod
Säkerhet från klient till nod autentiserar klienter och hjälper till att skydda kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet säkerställer att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienter identifieras unikt via sina X.509-certifikatsäkerhetsuppgifter. Valfritt antal valfria klientcertifikat kan användas för att autentisera administratörs- eller användarklienter med klustret.

Förutom klientcertifikat kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns i [Säkerhet från klient till nod](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du tilldela mer begränsade åtkomstkontroller för Azure-resurser.  Du kan tilldela olika åtkomstregler till prenumerationer, resursgrupper och resurser.  Azure RBAC-regler ärvs längs resurshierarkin om de inte åsidosätts på en lägre nivå.  Du kan tilldela valfria användargrupper i AAD med Azure RBAC-regler så att angivna användare och grupper kan ändra klustret.  Mer information finns i Översikt över [Azure RBAC.](../role-based-access-control/overview.md)

Service Fabric också stöd för åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Detta hjälper till att göra klustret säkrare. Två typer av åtkomstkontroll stöds för klienter som ansluter till ett kluster: administratörsroll och användarroll.  

Mer information finns i [Service Fabric rollbaserad åtkomstkontroll.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper 
Nätverkssäkerhetsgrupper (NSG: er) styr inkommande och utgående trafik för ett undernät, en virtuell dator eller ett specifikt nätverkskort.  När flera virtuella datorer läggs på samma virtuella nätverk kan de som standard kommunicera med varandra via valfri port.  Om du vill begränsa kommunikationen mellan datorerna kan du definiera NSG:er för att segmentera nätverket eller isolera virtuella datorer från varandra.  Om du har flera nodtyper i ett kluster kan du använda NSG:er för undernät för att förhindra att datorer som hör till olika nodtyper kommunicerar med varandra.  

Mer information finns i om [säkerhetsgrupper](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Skalning

Programkrav ändras med tiden. Du kan behöva öka klusterresurserna för att möta ökad programarbetsbelastning eller nätverkstrafik eller minska klusterresurser när efterfrågan sjunker. När du har Service Fabric ett kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurserna för noderna). Du kan skala klustret när som helst, även när arbetsbelastningarna körs i klustret. När klustret skalas skalas även dina program automatiskt.

Mer information finns i [Skala Azure-kluster.](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Uppgradera
Ett Azure Service Fabric kluster är en resurs som du äger, men som delvis hanteras av Microsoft. Microsoft ansvarar för att korrigera det underliggande operativsystemet och utföra Service Fabric körningsuppgraderingar i klustret. Du kan ange att klustret ska ta emot automatiska körningsuppgraderingar när Microsoft släpper en ny version eller välja en körningsversion som stöds. Förutom körningsuppgraderingar kan du även uppdatera klusterkonfigurationen, till exempel certifikat eller programportar.

Mer information finns i [Uppgradera kluster.](service-fabric-cluster-upgrade.md)

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Mer information [finns i Versioner som stöds](./service-fabric-versions.md) i Azure


## <a name="next-steps"></a>Nästa steg
Läs mer om [att skydda,](service-fabric-cluster-security.md) [skala](service-fabric-cluster-scaling.md)och [uppgradera](service-fabric-cluster-upgrade.md) Azure-kluster.

Läs mer [Service Fabric om supportalternativen](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG