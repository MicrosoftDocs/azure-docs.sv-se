---
title: Vanliga frågor och svar om Azure Red Hat OpenShift
description: Här är svar på vanliga frågor om Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634353"
---
# <a name="azure-red-hat-openshift-faq"></a>Vanliga frågor och svar om Azure Red Hat

I den här artikeln besvaras vanliga frågor och svar om Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Installation och uppgradering

### <a name="which-azure-regions-are-supported"></a>Vilka Azure-regioner stöds?

En lista över regioner som stöds för Azure Red Hat OpenShift 4. x finns i [tillgängliga regioner](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all).

En lista över regioner som stöds för Azure Red Hat OpenShift 3,11 finns i [produkt tillgänglighet per region](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Vilka storlekar för virtuella datorer kan jag använda?

En lista över virtuella dator storlekar som stöds för Azure Red Hat OpenShift 4 finns i [resurser som stöds för Azure Red Hat OpenShift 4](support-policies-v4.md).

En lista över virtuella dator storlekar som stöds för Azure Red Hat OpenShift 3,11 finns i [resurser som stöds för Azure Red Hat OpenShift 3,11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Vad är det maximala antalet poddar i ett kluster med ett i Azure Red Hat?  Vilket är det maximala antalet poddar per nod i Azure Red Hat OpenShift?

Det faktiska antalet poddar som stöds beror på programmets krav på minne, processor och lagring.

Azure Red Hat OpenShift 4. x har en gräns på 250 Pod per nod och en gräns på 100 Compute Node. Detta är det högsta antalet poddar som stöds i ett kluster till 250 &times; 100 = 25 000.

Azure Red Hat OpenShift 3,11 har en gräns på 50 Pod per nod och en gräns på 20 Compute-noder. Detta är det högsta antalet poddar som stöds i ett kluster till 50 &times; 20 = 1 000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan ett kluster ha Compute-noder över flera Azure-regioner?

Nej. Alla noder i ett kluster med ett OpenShift-kluster i Azure Red Hat måste ha sitt ursprung i samma Azure-region.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Kan ett kluster distribueras över flera tillgänglighets zoner?

Ja. Detta sker automatiskt om klustret distribueras till en Azure-region som har stöd för tillgänglighets zoner. Mer information finns i [tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Är kontroll Plans noderna sammankopplade som de är med Azure Kubernetes service (AKS)?

Nej. Alla resurser, inklusive de överordnade kluster noderna, körs i din kund prenumeration. De här typerna av resurser placeras i en skrivskyddad resurs grupp.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Finns klustret i en kund prenumeration? 

Det Azure-hanterade programmet finns i en låst resurs grupp med kund prenumerationen. Kunder kan visa objekt i den resurs gruppen men inte ändra dem.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Finns det något element i Azure Red Hat OpenShift som delas med andra kunder? Eller är allt oberoende?

Varje Azure Red Hat OpenShift-kluster är dedikerad till en bestämd kund och är i kund prenumerationen. 

### <a name="are-infrastructure-nodes-available"></a>Är Infrastructure-noder tillgängliga?

På Azure Red Hat OpenShift 4. x-kluster är inte infrastruktur noder tillgängliga för tillfället.

I Azure Red Hat OpenShift 3,11-kluster ingår Infrastructure-noder som standard.

## <a name="how-do-i-handle-cluster-upgrades"></a>Hur gör jag för att hanterar du kluster uppgraderingar?

Information om uppgraderingar, underhåll och versioner som stöds finns i Guide för [Support Lifecycle](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Hur uppdateras värd operativ systemet och OpenShift-programvaran?

Värd operativ systemen och OpenShift-programvaran uppdateras som Azure Red Hat OpenShift förbrukar mindre versioner och korrigeringar från den överordnade OpenShift container Platform.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Vad är processen för att starta om den uppdaterade noden?

Noderna startas om som en del av en uppgradering.

## <a name="cluster-operations"></a>Klusteråtgärder

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Kan jag använda Prometheus för att övervaka mina program?

Prometheus levereras som förinstallerade och konfigurerade för Azure Red Hat OpenShift 4. x-kluster. Läs mer om [kluster övervakning](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html).

För Azure Red Hat OpenShift 3,11-kluster kan du distribuera Prometheus i ditt namn område och övervaka program i namn området. Mer information finns i [distribuera Prometheus-instans i Azure Red Hat OpenShift-kluster](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kan jag använda Prometheus för att övervaka mått som rör kluster hälsa och-kapacitet?

I Azure Red Hat OpenShift 4. x: Ja.

I Azure Red Hat OpenShift 3,11: Nej.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Kan loggar av underliggande virtuella datorer strömmas till ett system för kund logg analys?

Loggar från de underliggande virtuella datorerna hanteras av den hanterade tjänsten och visas inte för kunderna.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Hur kan en kund få till gång till mått som processor/minne på nodnivå för att vidta åtgärder för skalning, fel söknings problem osv. Jag kan inte att köra kubectl överst i ett kluster med ett starkt Shift-kluster i Azure Red Hat.

För Azure Red Hat OpenShift 4. x-kluster innehåller OpenShift-webbkonsolen alla mått på Node-nivån. Mer information finns i Red Hat-dokumentationen om [visning av kluster information](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html).

För Azure Red Hat OpenShift 3,11-kluster kan kunder komma åt CPU/minnes mått på nodnivå genom att använda kommandot `oc adm top nodes` eller `kubectl top nodes` med kluster rollen kund administratör. Kunder kan också komma åt CPU/minnes mått för `pods` med kommandot `oc adm top pods` eller `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Om vi skalar upp distributionen kan Azure-feldomäner mappas till Pod-placering för att säkerställa att alla poddar för en tjänst inte blir blockerade av ett fel i en enskild feldomän?

Det finns som standard fem fel domäner när du använder skalnings uppsättningar för virtuella datorer i Azure. Varje virtuell dator instans i en skalnings uppsättning placeras i någon av dessa fel domäner. Detta säkerställer att program som distribueras till Compute-noderna i ett kluster placeras i olika fel domäner.

Mer information finns i [välja rätt antal fel domäner för skalnings uppsättningen för den virtuella datorn](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Finns det något sätt att hantera Pod-placering?

Kunder kan hämta noder och Visa etiketter som kund administratör. Detta ger ett sätt att rikta in alla virtuella datorer i skalnings uppsättningen.

Varnings skydd måste användas när du använder vissa etiketter:

- Värdnamn får inte användas. Värdnamn roteras ofta med uppgraderingar och uppdateringar och kan ändras.
- Om kunden har en begäran om specifika etiketter eller en distributions strategi kan detta uppnås men skulle kräva tekniska åtgärder och stöds inte idag.

Mer information finns i [kontrol lera Pod-placering](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Är image-registret tillgängligt externt så att jag kan använda verktyg som Jenkins?

För 4. x-kluster måste du exponera ett säkert register och konfigurera autentisering. Mer information finns i följande Red Hat-dokumentation:

- [Exponerar ett register](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [Åtkomst till registret](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

I 3,11-kluster är Docker-avbildnings registret tillgängligt. Docker-registret är tillgängligt från `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Du kan också använda Azure Container Registry.

## <a name="networking"></a>Nätverk

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan jag distribuera ett kluster till ett befintligt virtuellt nätverk?

I 4. x-kluster kan du distribuera ett kluster till ett befintligt VNet.

I 3,11-kluster kan du inte distribuera ett kluster till ett befintligt VNet. Du kan ansluta ett Azure Red Hat OpenShift 3,11-kluster till ett befintligt VNet via peering.

### <a name="is-cross-namespace-networking-supported"></a>Stöds anslutningar mellan namnrymder?

Kunder och enskilda projekt administratörer kan anpassa kors namns nätverk (inklusive neka det) per projekt med hjälp av `NetworkPolicy` objekt.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Jag försöker använda peer-datorer i ett virtuellt nätverk i en annan prenumeration, men det gick inte att hämta VNet CIDR-fel.

I prenumerationen som har det virtuella nätverket, se till att registrera `Microsoft.ContainerService` providern med följande kommando: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Kan vi ange IP-intervall för distribution på det privata virtuella nätverket, vilket undviker konflikt med andra företags virtuella nätverk när de har peer-kopplats?

I 4. x-kluster kan du ange egna IP-intervall.

I 3,11-kluster har Azure Red Hat OpenShift stöd för VNet-peering och gör det möjligt för kunden att tillhandahålla ett VNet för peer-koppla med och en VNet-CIDR där OpenShift-nätverket fungerar.

Det virtuella nätverket som skapats av Azure Red Hat OpenShift kommer att skyddas och kommer inte att acceptera konfigurations ändringar. Det virtuella nätverket som är peer-kopplat styrs av kunden och finns i prenumerationen.

### <a name="is-the-software-defined-network-module-configurable"></a>Är den programdefinierade nätverks modulen konfigurerbar?

Det programdefinierade nätverket är `openshift-ovs-networkpolicy` och kan inte konfigureras.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Vilken Azure Load Balancer används av Azure Red Hat OpenShift?  Är det standard eller Basic och det kan konfigureras?

Azure Red Hat OpenShift använder standard Azure Load Balancer och går inte att konfigurera.

## <a name="permissions"></a>Behörigheter

### <a name="can-an-admin-manage-users-and-quotas"></a>Kan en administratör hantera användare och kvoter?

Ja. En administratör med en Red Hat OpenShift-administratör kan hantera användare och kvoter, förutom att få åtkomst till alla användare som skapats av projektet.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan jag begränsa ett kluster till endast vissa Azure AD-användare?

Ja. Du kan begränsa vilka Azure AD-användare som kan logga in i ett kluster genom att konfigurera Azure AD-programmet. Mer information finns i så här gör du för att [: begränsa appen till en uppsättning användare](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Kan jag begränsa användare från att skapa projekt?

Ja. Logga in på klustret som administratör och kör det här kommandot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Mer information finns i OpenShift-dokumentationen om inaktive ring av själv etablering för kluster versionen:

- [Inaktiverar själv etablering i 4,6-kluster](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Inaktiverar själv etablering i 3,11-kluster](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Vilka UNIX-rättigheter (i IaaS) är tillgängliga för Masters/infraröda/app-noder?

För 4. x-kluster är Node Access tillgängligt via rollen kluster-admin. Mer information finns i [Översikt över KUBERNETES RBAC](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

Node Access tillåts inte för 3,11-kluster.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Vilka OCP-rättigheter har vi? Kluster-admin? Projekt-admin?

För 4. x-kluster är rollen kluster administratör tillgänglig. Mer information finns i [Översikt över KUBERNETES RBAC](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

Mer information om 3,11-kluster finns i [Översikt över kluster administration](https://docs.openshift.com/aro/admin_guide/index.html) .

### <a name="which-identity-providers-are-available"></a>Vilka identitets leverantörer är tillgängliga?

För 4. x-kluster konfigurerar du din egen identitets leverantör. Mer information finns i Red Hat-dokumentationen om hur du [konfigurerar identitets leverantörer](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html).

För 3,11-kluster kan du använda Azure AD-integrering. 

## <a name="storage"></a>Storage

### <a name="is-data-on-my-cluster-encrypted"></a>Är data i mitt kluster krypterad?

Som standard krypteras data i vila. Azure Storages plattformen krypterar automatiskt dina data innan de behålls och dekrypterar data innan de kan hämtas. Mer information finns i [Azure Storage tjänst kryptering för vilande data](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Är data lagrade i etcd krypterade i Azure Red Hat OpenShift?

För Azure Red Hat OpenShift 4-kluster krypteras inte data som standard, men du kan välja att aktivera kryptering. Mer information finns i guiden om att [kryptera etcd](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html).

För 3,11-kluster krypteras inte data på etcd-nivån. Det finns för närvarande inte stöd för möjligheten att aktivera kryptering för tillfället. OpenShift stöder den här funktionen, men tekniska ansträngningar krävs för att göra det på väg kartan. Data krypteras på disk nivå. Mer information finns i [kryptera data i data lager lager](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Kan vi välja vilken beständig lagrings lösning som helst, t. ex. OCS? 

För 4. x-kluster konfigureras Azure disk (Premium_LRS) som standard lagrings klass. Ytterligare lagrings leverantörer och konfigurations information (inklusive Azure-fil) finns i Red Hat-dokumentationen om [beständig lagring](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html).

För 3,11-kluster tillhandahålls två lagrings klasser som standard: en för Azure-disk (Premium_LRS) och en för Azure-fil.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>Lagrar ARO kund information utanför klustrets region?

Nej. Alla data som skapats i ett ARO-kluster upprätthålls i klustrets region.
