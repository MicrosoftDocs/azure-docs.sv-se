---
title: Introduktion till Azure Kubernetes Service
description: Läs mer om funktionerna och fördelarna med att använda Azure Kubernetes Service för att distribuera och hantera containerbaserade program i Azure.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 1505366d9a91eac596b21804f93abb8245a84605
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590005"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes service (AKS) fören klar distributionen av ett hanterat Kubernetes-kluster i Azure genom att avlasta mycket av komplexiteten och drifts kostnaderna för Azure. Som en värdbaserad Kubernetes-tjänst hanterar Azure viktiga uppgifter åt dig, till exempel hälso övervakning och underhåll.  

Eftersom Kubernetes-huvudmasters hanteras av Azure kan du bara hantera och underhålla agent-noderna. Därför är AKS gratis som en hanterad Kubernetes-tjänst. du betalar bara för agent-noderna i klustren, inte för huvud servrar.  

Du kan skapa ett AKS-kluster med hjälp av Azure Portal, Azure CLI, Azure PowerShell eller genom att använda mall drivna distributions alternativ, till exempel Resource Manager-mallar och terraform. När du distribuerar ett AKS-kluster styr Kubernetes och alla noder distribueras och konfigureras för dig. Ytterligare funktioner, till exempel avancerade nätverk, Azure Active Directory-integrering och övervakning kan också konfigureras under distributionsprocessen. Windows Server-behållare stöds i AKS.

Mer information om grunderna i Kubernetes finns i [Kubernetes Core Concepts for AKS][concepts-clusters-workloads].

Kom igång genom att slutföra snabb starten av AKS [i Azure Portal][aks-portal] eller [med Azure CLI][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Åtkomst, säkerhet och övervakning

För förbättrad säkerhet och hantering kan AKS integreras med Azure Active Directory (Azure AD) och:
* Använda Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC). 
* Övervaka hälso tillståndet för klustret och resurserna.

### <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

För att begränsa åtkomsten till kluster resurser stöder AKS [KUBERNETES RBAC][kubernetes-rbac]. Med Kubernetes RBAC kan du styra åtkomst och behörigheter till Kubernetes-resurser och namn områden.  

Du kan också konfigurera ett AKS-kluster att integrera med Azure AD. Med Azure AD-integrering kan du konfigurera Kubernetes-åtkomst baserat på befintliga identiteter och grupp medlemskap. Dina befintliga Azure AD-användare och-grupper kan tillhandahålla en integrerad inloggnings upplevelse och åtkomst till AKS-resurser.  

Mer information om identitet finns i [åtkomst-och identitets alternativ för AKS][concepts-identity].

Mer information om att säkra AKS-kluster finns i [integrera Azure Active Directory med AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrerad loggning och övervakning

Azure Monitor för container Health samlar in minnes-och processor prestanda mått från behållare, noder och kontrollanter i ditt AKS-kluster och distribuerade program. Du kan granska både behållar loggarna och [Kubernetes huvud loggar][aks-master-logs]. Dessa övervaknings data lagras på en Azure Log Analytics-arbetsyta och är tillgänglig via Azure Portal, Azure CLI eller en REST-slutpunkt.

Mer information finns i [Övervaka Azure Kubernetes Service hälsotillstånd för containrar][container-health].

## <a name="clusters-and-nodes"></a>Kluster och noder

AKS-noder körs på virtuella Azure-datorer (VM). Med AKS-noder kan du ansluta lagring till noder och poddar, uppgradera kluster komponenter och använda GPU: er. AKS stöder Kubernetes-kluster som kör flera Node-pooler för att stödja blandade operativ system och Windows Server-behållare.  

Mer information om Kubernetes-kluster, Node-och Node-funktioner finns i [Kubernetes Core Concepts for AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Klusternoder och poddskalning

När behovet av resurser ändras kan du ändra skalan för klusternoder och poddar på din tjänst uppåt eller nedåt. Du kan använda den vågräta autoskalningen för poddar eller autoskalningen för kluster. Med den här metoden för att skala kan AKS-klustret automatiskt justera till krav och endast köra de resurser som behövs.

Mer information finns i [Skala ett kluster i Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Uppgraderingar av klusternod

AKS erbjuder flera Kubernetes-versioner. När nya versioner blir tillgängliga i AKS uppgraderas klustret med hjälp av Azure Portal eller Azure CLI. Under uppgraderingen blir noderna noggrant avspärrade och tömda för att minimera störningar i program som körs.  

Mer information om livscykelversioner finns i [Kubernetes-versioner som stöds i AKS][aks-supported versions]. Uppgraderingssteg finns i [Uppgradera ett kluster i Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU-aktiverade noder

AKS stöder skapande av GPU-aktiverade noder i pooler. Azure tillhandahåller för närvarande en eller flera GPU-aktiverade virtuella datorer. GPU-aktiverade virtuella datorer är utformade för beräknings intensiva, grafik intensiva och visualiserings arbets belastningar.

Mer information finns i [Använda GPU-kort på AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Data behandlings noder (offentlig för hands version)

AKS har stöd för att skapa Intel SGX-baserade, konfidentiella data behandlings-Node-pooler (DCSv2 VM). Med konfidentiella data behandlings noder kan behållare köras i en maskin varu baserad, enclaves (Trusted Execution Environment). Isolering mellan behållare, kombinerat med kod integritet genom attestering, kan hjälpa dig med säkerhets strategin för skydds djupet. Konfidentiella beräknings noder stöder både konfidentiella behållare (befintliga Docker-appar) och enklaven behållare.

Mer information finns i avsnittet [konfidentiella data behandlings noder på AKS][conf-com-node].

### <a name="storage-volume-support"></a>Stöd för lagringsvolym

Du kan montera lagringsvolymer för beständiga data för att stödja arbetsbelastningar för program. Du kan använda både statiska och dynamiska volymer. Beroende på hur många anslutna poddar som förväntas dela lagrings volymerna kan du använda lagring som backas upp av Azure-diskar för enkel Pod-åtkomst eller Azure Files för flera samtidiga Pod-åtkomst.

Mer information finns i [lagrings alternativ för program i AKS][concepts-storage].

Kom igång med dynamiska, permanenta volymer med hjälp av [Azure-diskar][azure-disk] eller [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuella nätverk och ingress

Ett AKS-kluster kan distribueras till ett befintligt virtuellt nätverk. I den här konfigurationen tilldelas varje Pod i klustret en IP-adress i det virtuella nätverket och kan kommunicera direkt med andra poddar i klustret och andra noder i det virtuella nätverket. Poddar kan också ansluta till andra tjänster i ett peer-kopplat virtuellt nätverk och till lokala nätverk via ExpressRoute eller VPN-anslutningar för plats-till-plats (S2S).  

Mer information finns i [nätverks koncepten för program i AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Ingress with HTTP-programroutning

HTTP-programroutningen gör det enkelt att komma åt program som distribueras till AKS-klustret. När aktiverad, konfigurerar HTTP-programroutningen en ingångskontroll i AKS-klustret.  

När program distribueras konfigureras offentligt tillgängliga DNS-namn automatiskt. HTTP-programroutningen konfigurerar en DNS-zon och integrerar den med AKS-klustret. Du kan sedan distribuera Kubernetes-ingressresurser som vanligt.  

Kom igång med ingående trafik, se [HTTP-programroutning][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrering av utvecklingsverktyg

Kubernetes har ett omfattande eko system med utvecklings-och hanterings verktyg som fungerar sömlöst med AKS. Dessa verktyg omfattar Helm och Kubernetes-tillägget för Visual Studio Code. Dessa verktyg fungerar sömlöst med AKS.  

Dessutom tillhandahåller Azure flera verktyg som hjälper dig att effektivisera Kubernetes, till exempel Azure dev Spaces och DevOps starter.  

Azure Dev Spaces ger en snabb, iterativ Kubernetes-utvecklingsupplevelse för team. Du kan köra och felsöka containrar direkt i AKS med minimal konfiguration. Komma igång med [Azure Dev Spaces][azure-dev-spaces].

DevOps starter ger en enkel lösning för att överföra befintlig kod och git-databaser till Azure. DevOps starter automatiskt:
* Skapar Azure-resurser (t. ex. AKS). 
* Konfigurerar en versions pipeline i Azure DevOps Services som innehåller en build-pipeline för CI; 
* Konfigurerar en versions pipeline för CD. särskilt 
* Genererar en Azure Application Insights-resurs för övervakning. 

Mer information finns i [DevOps starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Stöd för docker-avbildning och privat containerregister

AKS har stöd för Docker-avbildningsformatet. Integrera AKS med Azure Container Registry (ACR) för privat lagring av Docker-avbildningar.

Information om hur du skapar en privat avbildnings lagrings plats finns [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-certifiering

AKS har CNCF-certifierad som Kubernetes överensstämmer.

## <a name="regulatory-compliance"></a>Regelefterlevnad

AKS är kompatibel med SOC, ISO, PCI DSS och HIPAA. Mer information finns i [Översikt över Microsoft Azure efterlevnad][compliance-doc].

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att distribuera och hantera AKS med snabb starten av Azure CLI.

> [!div class="nextstepaction"]
> [AKS-snabbstart][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
