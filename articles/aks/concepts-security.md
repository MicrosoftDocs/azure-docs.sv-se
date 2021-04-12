---
title: Koncept – säkerhet i Azure Kubernetes Services (AKS)
description: Lär dig mer om säkerhet i Azure Kubernetes service (AKS), inklusive Master-och Node-kommunikation, nätverks principer och Kubernetes hemligheter.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105314"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)

Kluster säkerhet skyddar dina kund uppgifter när du kör program arbets belastningar i Azure Kubernetes service (AKS). 

Kubernetes innehåller säkerhets komponenter, till exempel *nätverks principer* och *hemligheter*. Samtidigt innehåller Azure komponenter som nätverks säkerhets grupper och dirigerade kluster uppgraderingar. AKS kombinerar dessa säkerhets komponenter för att:
* Behåll ditt AKS-kluster med de senaste säkerhets uppdateringarna och Kubernetes-versionerna av operativ systemet.
* Tillhandahålla säker Pod-trafik och åtkomst till känsliga autentiseringsuppgifter.

Den här artikeln beskriver de viktigaste begreppen som skyddar dina program i AKS:

- [Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Huvud säkerhet](#master-security)
  - [Nods säkerhet](#node-security)
    - [Beräknings isolering](#compute-isolation)
  - [Kluster uppgraderingar](#cluster-upgrades)
    - [Cordon och dränering](#cordon-and-drain)
  - [Nätverkssäkerhet](#network-security)
    - [Azure-nätverkssäkerhetsgrupper](#azure-network-security-groups)
  - [Kubernetes hemligheter](#kubernetes-secrets)
  - [Nästa steg](#next-steps)

## <a name="master-security"></a>Huvud säkerhet

I AKS är Kubernetes Master-komponenterna en del av den hanterade tjänsten som tillhandahålls, hanteras och underhålls av Microsoft. Varje AKS-kluster har sin egen, dedikerad Kubernetes-huvudhanterare för att tillhandahålla API-servern, Scheduler osv.

Som standard använder Kubernetes-API-servern en offentlig IP-adress och ett fullständigt kvalificerat domän namn (FQDN). Du kan begränsa åtkomsten till API-serverns slut punkt med hjälp av [auktoriserade IP-intervall][authorized-ip-ranges]. Du kan också skapa ett helt [privat kluster][private-clusters] om du vill begränsa åtkomsten till API-servern till ditt virtuella nätverk.

Du kan styra åtkomsten till API-servern med hjälp av Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) och Azure RBAC. Mer information finns i [Azure AD-integrering med AKS][aks-aad].

## <a name="node-security"></a>Nods säkerhet

AKS-noder är virtuella Azure-datorer (VM) som du hanterar och underhåller. 
* Linux-noder kör en optimerad Ubuntu-distribution med hjälp av `containerd` eller Moby container Runtime. 
* Windows Server-noder kör en optimerad Windows Server 2019-version med hjälp av `containerd` eller Moby container Runtime. 

När ett AKS-kluster skapas eller skalas, distribueras noderna automatiskt med de senaste säkerhets uppdateringarna och konfigurationerna för operativ systemet.

> [!NOTE]
> AKS-kluster med hjälp av:
> * Kubernetes-version 1,19 och större användning `containerd` som container Runtime. 
> * Kubernetes före v 1.19 Node Pools använder [Moby](https://mobyproject.org/) (överordnad Docker) som dess behållar körning.

### <a name="node-security-patches"></a>Säkerhets korrigeringar för nod

#### <a name="linux-nodes"></a>Linux-noder
Azure-plattformen tillämpar automatiskt OS-säkerhetskorrigeringsfiler på Linux-noder på en natt basis. Om en säkerhets uppdatering för Linux-operativsystem kräver en omstart av datorn, kommer den inte att startas om automatiskt. Du kan antingen:
* Starta om Linux-noderna manuellt.
* Använd [Kured][kured], en daemon för omstart av öppen källkod för Kubernetes. Kured körs som en [DaemonSet][aks-daemonsets] och övervakar varje nod för en fil som anger att en omstart krävs. 

Omstarter hanteras i klustret med samma [Cordon och tömnings processen](#cordon-and-drain) som en kluster uppgradering.

#### <a name="windows-server-nodes"></a>Windows Server-noder

För Windows Server-noder körs Windows Update inte automatiskt och använder de senaste uppdateringarna. Schemalägg Windows Server Node pool-uppgraderingar i ditt AKS-kluster runt den vanliga Windows Update lanserings cykeln och din egen verifierings process. Den här uppgraderings processen skapar noder som kör den senaste Windows Server-avbildningen och uppdateringar och tar sedan bort de äldre noderna. Mer information om den här processen finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Node-distribution
Noder distribueras till ett privat virtuellt nätverk under nät, utan att några offentliga IP-adresser tilldelats. För fel sökning och hanterings syfte är SSH aktiverat som standard och endast tillgängligt med den interna IP-adressen.

### <a name="node-storage"></a>Node Storage
Noderna använder Azure Managed Disks för att tillhandahålla lagring. För de flesta VM-storlekar är Azure Managed Disks Premium diskar som backas upp av SSD med höga prestanda. Data som lagras på hanterade diskar krypteras automatiskt i vila på Azure-plattformen. För att förbättra redundans replikeras Azure Managed Disks säkert i Azure-datacentret.

### <a name="hostile-multi-tenant-workloads"></a>Farliga arbets belastningar för flera klienter

Kubernetes-miljöer är för närvarande inte säkra för att skydda användningen av flera klienter. Extra säkerhetsfunktioner, t. ex. *Pod säkerhets principer* eller Kubernetes RBAC för noder, kan effektivt blockera sårbarheter. För verklig säkerhet vid körning av skydds arbets belastningar med flera klienter, är det bara att lita på en hypervisor. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. 

För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster. Mer information om hur du isolerar arbets belastningar finns i [metod tips för kluster isolering i AKS][cluster-isolation].

### <a name="compute-isolation"></a>Beräknings isolering

På grund av efterlevnads-eller reglerings krav kan vissa arbets belastningar kräva en hög grad av isolering från andra kund arbets belastningar. För dessa arbets belastningar tillhandahåller Azure [isolerade virtuella datorer](../virtual-machines/isolation.md) som ska användas som agent-noder i ett AKS-kluster. De här virtuella datorerna är isolerade till en viss maskin varu typ och är dedikerad till en enda kund. 

Välj [en av de isolerade VM-storlekarna](../virtual-machines/isolation.md) som **Node-storlek** när du skapar ett AKS-kluster eller lägger till en Node-pool.

## <a name="cluster-upgrades"></a>Kluster uppgraderingar

Azure tillhandahåller uppgradering av Orchestration-verktyg för att uppgradera ett AKS-kluster och-komponenter, upprätthålla säkerhet och efterlevnad och få till gång till de senaste funktionerna. Den här uppgraderings dirigeringen omfattar både Kubernetes huvud-och agent komponenter. 

Starta uppgraderings processen genom att ange en av de [listade tillgängliga Kubernetes-versionerna](supported-kubernetes-versions.md). Azure cordons och tömmer sedan varje AKS-nod och uppgraderingar.

### <a name="cordon-and-drain"></a>Cordon och dränering

Under uppgraderings processen är AKS-noder individuellt avspärrade från klustret för att förhindra att nya poddar schemaläggs på dem. Noderna töms sedan och uppgraderas enligt följande:

1.  En ny nod distribueras till Node-poolen. 
    * Den här noden kör den senaste OS-avbildningen och korrigeringarna.
1. En av de befintliga noderna identifieras för uppgradering. 
1. Poddar på den identifierade noden avslutas och schemaläggs på andra noder i Node-poolen.
1. Den tömda noden tas bort från AKS-klustret.
1. Steg 1-4 upprepas tills alla noder har bytts ut som en del av uppgraderings processen.

Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade-cluster].

## <a name="network-security"></a>Nätverkssäkerhet

För anslutning och säkerhet med lokala nätverk kan du distribuera ditt AKS-kluster till befintliga Azure Virtual Network-undernät. Dessa virtuella nätverk ansluter tillbaka till ditt lokala nätverk med Azure plats-till-plats-VPN eller Express Route. Definiera Kubernetes ingress-kontrollanter med privata, interna IP-adresser för att begränsa åtkomsten till den interna nätverks anslutningen.

### <a name="azure-network-security-groups"></a>Azure-nätverkssäkerhetsgrupper

Azure använder regler för nätverks säkerhets grupper för att filtrera trafik flödet för det virtuella nätverket. Dessa regler definierar käll-och mål-IP-intervall, portar och protokoll som tillåts eller nekas åtkomst till resurser. Standard regler skapas för att tillåta TLS-trafik till Kubernetes-API-servern. Du skapar tjänster med belastningsutjämnare, Port mappningar eller inkommande vägar. AKS ändrar automatiskt nätverks säkerhets gruppen för trafik flöde.

Om du anger ett eget undernät för ditt AKS-kluster **ska du inte** ändra nätverks säkerhets gruppen för under nätet som hanteras av AKS. Skapa i stället fler nätverks säkerhets grupper på under näts nivå för att ändra trafik flödet. Se till att de inte stör nödvändig trafik hantering av klustret, till exempel åtkomst till belastningsutjämnare, kommunikation med kontroll planet och [utgående][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Kubernetes nätverks princip

För att begränsa nätverks trafiken mellan poddar i klustret, erbjuder AKS stöd för [Kubernetes nätverks principer][network-policy]. Med nätverks principer kan du tillåta eller neka vissa nätverks Sök vägar i klustret baserat på namn områden och etikett väljare.

## <a name="kubernetes-secrets"></a>Kubernetes-hemligheter

Med en Kubernetes- *hemlighet* kan du mata in känsliga data i poddar, till exempel autentiseringsuppgifter eller nycklar för åtkomst. 
1. Skapa en hemlighet med Kubernetes-API: et. 
1. Definiera din POD eller distribution och begär en speciell hemlighet. 
    * Hemligheter ges endast till noder med en schemalagd Pod som kräver dem.
    * Hemligheten lagras i *tmpfs*, som inte skrivs till disk. 
1. När du tar bort den sista Pod på en nod som kräver en hemlighet, tas hemligheten bort från nodens tmpfs. 
   * Hemligheter lagras inom ett angivet namn område och kan endast nås av poddar inom samma namnrymd.

Att använda hemligheter minskar känslig information som definieras i manifestet POD eller service YAML. I stället begär du hemligheten som lagras i Kubernetes API-servern som en del av ditt YAML-manifest. Den här metoden ger endast den speciella Pod åtkomst till hemligheten. 

> [!NOTE]
> Manifest filen för RAW Secret innehåller hemliga data i base64-format (se den [officiella dokumentationen][secret-risks] för mer information). Behandla filerna som känslig information och genomför dem aldrig på käll kontroll.

Kubernetes hemligheter lagras i etcd, ett distribuerat nyckel värdes lager. Etcd-butiken hanteras fullständigt av AKS och [data krypteras i vila på Azure-plattformen][encryption-atrest]. 

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med att skydda dina AKS-kluster finns i [uppgradera ett AKS-kluster][aks-upgrade-cluster].

För associerade bästa metoder, se [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security] och [metod tips för Pod-säkerhet i AKS][developer-best-practices-pod-security].

Mer information om kärn Kubernetes-och AKS-koncept finns i:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identitet][aks-concepts-identity]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
