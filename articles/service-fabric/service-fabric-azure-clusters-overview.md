---
title: Skapa kluster på Windows Server och Linux
description: Service Fabric kluster som körs på Windows Server och Linux. Du kan distribuera och vara värd för Service Fabric program överallt där du kan köra Windows Server eller Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: bbfdc0a30aa673e8602ec9233fde4236c99ef5aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97882219"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Översikt över Service Fabric kluster i Azure
Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, Service Fabric balanseringen av tjänste partitionens repliker och instanser över det ökade antalet noder. Övergripande program prestanda förbättras och konkurrens för åtkomst till minnes minskningar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric åter balanserar partitionens repliker och instanser över det minskade antalet noder för att bättre kunna använda maskin varan på varje nod.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning noder (virtuella datorer) i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen. 

## <a name="cluster-components-and-resources"></a>Kluster komponenter och resurser
Ett Service Fabric kluster i Azure är en Azure-resurs som använder och interagerar med andra Azure-resurser:
* Virtuella datorer och virtuella nätverkskort
* skalningsuppsättningar för virtuella datorer
* virtuella nätverk
* lastbalanserare
* lagrings konton
* offentliga IP-adresser

![Service Fabric kluster][Image]

### <a name="virtual-machine"></a>Virtuell dator
En [virtuell dator](../virtual-machines/index.yml) som ingår i ett kluster kallas för en nod, som är tekniskt, en klusternod är en Service Fabric körnings process. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel [placerings egenskaper](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Varje dator eller virtuell dator har en tjänst för automatisk start, *FabricHost.exe*, som börjar köras vid start och sedan startar två körbara filer, *Fabric.exe* och *FabricGateway.exe*, som utgör noden. En produktions distribution är en nod per fysisk eller virtuell dator. För testnings scenarier kan du vara värd för flera noder på en dator eller en virtuell dator genom att köra flera instanser av *Fabric.exe* och *FabricGateway.exe*.

Varje virtuell dator är associerad med ett virtuellt nätverks gränssnitts kort (NIC) och varje nätverkskort tilldelas en privat IP-adress.  En virtuell dator tilldelas ett virtuellt nätverk och en lokal balans via NÄTVERKSKORTet.

Alla virtuella datorer i ett kluster placeras i ett virtuellt nätverk.  Alla noder i samma Node-typ/skalnings uppsättning placeras i samma undernät i det virtuella nätverket.  Dessa noder har bara privata IP-adresser och är inte direkt adresser bara utanför det virtuella nätverket.  Klienter har åtkomst till tjänster på noderna via Azure Load Balancer.

### <a name="scale-setnode-type"></a>Skalnings uppsättning/nodtyp
När du skapar ett kluster definierar du en eller flera nodtyper.  Noderna eller de virtuella datorerna i en nodtyp har samma storlek och egenskaper som antalet processorer, minne, antal diskar och disk-I/O.  Till exempel kan en nodtyp vara för små, klient delsbaserade virtuella datorer med portar öppna på Internet medan en annan nodtyp kan vara för stora virtuella datorer med backend-data som bearbetar data. I Azure-kluster mappas varje nodtyp till en [skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/index.yml).

Du kan använda skalnings uppsättningar för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skalnings uppsättning. Service Fabric runtime är startat på varje virtuell dator i skalnings uppsättningen med hjälp av Azure VM-tillägg. Du kan skala upp eller ned varje nodtyp separat, ändra OS-SKU: n som körs på varje klusternod, ha olika portar öppna och använda olika kapacitets mått. En skalnings uppsättning har fem [uppgraderings domäner](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) och fem [fel domäner](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) och kan ha upp till 100 virtuella datorer.  Du skapar kluster av fler än 100 noder genom att skapa flera skalnings uppsättningar/Node-typer.

> [!IMPORTANT]
> Att välja antalet nodtyper för klustret och egenskaperna för varje nodtyp (storlek, primär, Internet som är riktad, antal virtuella datorer osv.) är en viktig uppgift.  Mer information finns i [överväganden vid planering av kluster kapacitet](service-fabric-cluster-capacity.md).

Mer information finns i [Service Fabric nodtyper och skalnings uppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Virtuella dator instanser är anslutna bakom en [Azure-belastningsutjämnare](../load-balancer/load-balancer-overview.md), som är associerad med en [offentlig IP-adress](../virtual-network/public-ip-addresses.md) och en DNS-etikett.  När du etablerar ett kluster *&lt; med &gt; kluster* namn, DNS-namnet, *&lt; kluster namn &gt; . &lt; Location &gt; . cloudapp.Azure.com* är DNS-etiketten som är associerad med belastningsutjämnaren framför skalnings uppsättningen.

Virtuella datorer i ett kluster har bara [privata IP-adresser](../virtual-network/private-ip-addresses.md).  Hanterings trafik och tjänst trafik dirigeras via den offentliga belastningsutjämnaren.  Nätverks trafik dirigeras till dessa datorer via NAT-regler (klienter ansluter till vissa noder/instanser) eller regler för belastnings utjämning (trafik går till resursallokeringar med resursallokering).  En belastningsutjämnare har en associerad offentlig IP-adress med ett DNS-namn i formatet: *&lt; kluster namn &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  En offentlig IP-adress är en annan Azure-resurs i resurs gruppen.  Om du definierar flera nodtyper i ett kluster skapas en belastningsutjämnare för varje nodtyp/skalnings uppsättning. Eller så kan du konfigurera en enskild belastningsutjämnare för flera nodtyper.  Den primära nodtypen har DNS-etiketten *&lt; kluster namn &gt; . &lt; Location &gt; . cloudapp.Azure.com*, andra nodtyper har DNS-etiketten *&lt; kluster- &gt; - &lt; NodeType &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Lagringskonton
Varje typ av klusternod stöds av ett [Azure Storage-konto](../storage/common/storage-introduction.md) och hanterade diskar.

## <a name="cluster-security"></a>Klustersäkerhet
Ett Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster så att obehöriga användare kan ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktions arbets belastningar i klustret. 

### <a name="node-to-node-security"></a>Säkerhet från nod till nod
Säkerhet från nod till nod skyddar kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhets scenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdbaserade program och tjänster i klustret. Service Fabric använder X. 509-certifikat för att skydda ett kluster och tillhandahålla funktioner för program säkerhet.  Ett kluster certifikat krävs för att skydda kluster trafik och tillhandahålla kluster-och serverautentisering.  Självsignerade certifikat kan användas för test kluster, men ett certifikat från en betrodd certifikat utfärdare bör användas för att skydda produktions kluster.

Mer information finns i [nod-till-nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Säkerhet från klient till nod
Säkerhet mellan klienter autentiserar klienter och skyddar kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienterna identifieras unikt genom antingen deras X. 509-certifikat säkerhets uppgifter. Valfritt antal valfria klient certifikat kan användas för att autentisera administratörs-eller användar klienter med klustret.

Förutom klient certifikat kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns i [klient-till-nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du tilldela detaljerade åtkomst kontroller på Azure-resurser.  Du kan tilldela olika åtkomst regler till prenumerationer, resurs grupper och resurser.  Azure RBAC-regler ärvs längs resurs-hierarkin om den inte åsidosätts på en lägre nivå.  Du kan tilldela alla användar grupper eller användar grupper i AAD med Azure RBAC-regler så att angivna användare och grupper kan ändra klustret.  Mer information finns i [Översikt över Azure RBAC](../role-based-access-control/overview.md).

Service Fabric stöder också åtkomst kontroll för att begränsa åtkomsten till vissa kluster åtgärder för olika användar grupper. Detta gör klustret säkrare. Två åtkomst kontroll typer stöds för klienter som ansluter till ett kluster: administratörs roll och användar roll.  

Mer information finns i [Service Fabric rollbaserad åtkomst kontroll](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper 
Nätverks säkerhets grupper (NSG: er) kontrollerar inkommande och utgående trafik för ett undernät, en virtuell dator eller ett särskilt nätverkskort.  När flera virtuella datorer placeras i samma virtuella nätverk kan de kommunicera med varandra via vilken port som helst.  Om du vill begränsa kommunikationen mellan datorerna kan du definiera NSG: er för att segmentera nätverket eller isolera virtuella datorer från varandra.  Om du har flera nodtyper i ett kluster kan du tillämpa NSG: er på undernät för att förhindra att datorer som tillhör olika nodtyper från att kommunicera med varandra.  

Mer information finns i om [säkerhets grupper](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Skalning

Program krav ändras med tiden. Du kan behöva öka kluster resurserna för att möta ökad program belastning eller nätverks trafik eller minska kluster resurserna när efter frågan går vidare. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurserna för noderna). Du kan skala klustret när som helst, även när arbets belastningar körs på klustret. När klustret skalas, skalas programmen automatiskt.

Mer information finns i [skala Azure-kluster](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uppgradera
Ett Azure Service Fabric-kluster är en resurs som du äger, men som hanteras delvis av Microsoft. Microsoft ansvarar för att korrigera det underliggande operativ systemet och utföra Service Fabric körnings uppgraderingar på klustret. Du kan ange att klustret ska ta emot automatiska körnings uppgraderingar, när Microsoft släpper en ny version eller väljer att välja en version som stöds för körning. Förutom körnings uppgraderingar kan du också uppdatera kluster konfigurationen, till exempel certifikat eller program portar.

Mer information finns i [uppgradera kluster](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Du kan skapa kluster på virtuella datorer som kör dessa operativ system:

| Operativsystem | Tidigaste Service Fabric version som stöds | Senaste Service Fabric-version som stöds |
| --- | --- | --- | 
| Windows Server 2019 | 6.4.654.9590 | Ej tillämpligt |
| Windows Server 2016 | Alla versioner | Ej tillämpligt |
| Windows Server-20H2 | 7.2.445.9590 | Ej tillämpligt |
| Windows Server 1809 | 6.4.654.9590 | 7.2.445.9590 |
| Windows Server 1803 | 6.4 | 7.2.445.9590 |
| Windows Server 1709 | 6.0 | 7.2.445.9590 |
| Windows Server 2012 | Alla versioner | Ej tillämpligt | 
| Linux Ubuntu 16,04 | 6.0 | Ej tillämpligt |
| Linux Ubuntu 18,04 | 7.1 | Ej tillämpligt |

Mer information finns i [kluster versioner som stöds i Azure](./service-fabric-versions.md#supported-operating-systems)

> [!NOTE]
> Om du bestämmer dig för att distribuera Service Fabric på Windows Server 1709, Observera att (1) det är inte en långsiktig service gren, så du kan behöva flytta versioner i framtiden och (2) om du distribuerar behållare kommer behållare som skapats på Windows Server 2016 inte att fungera på Windows Server 1709 och vice versa (du måste återskapa dem för att kunna distribuera dem).
>


## <a name="next-steps"></a>Nästa steg
Läs mer om att [skydda](service-fabric-cluster-security.md), [skala](service-fabric-cluster-scaling.md)och [Uppgradera](service-fabric-cluster-upgrade.md) Azure-kluster.

Läs mer om [Service Fabric support alternativ](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG