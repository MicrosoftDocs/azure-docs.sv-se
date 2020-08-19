---
title: Migrera från en Azure Red Hat OpenShift 3,11 till Azure Red Hat OpenShift 4
description: Migrera från en Azure Red Hat OpenShift 3,11 till Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migrering, Aro, OpenShift, Red Hat
ms.openlocfilehash: 68e8be24273dfab490d2423b76c372f2ff9f2b38
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513265"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrera från Azure Red Hat OpenShift 3,11 till Azure Red Hat OpenShift 4

Azure Red Hat OpenShift på OpenShift 4 ger Kubernetes 1,16 på Red Hat Core OS, privata kluster, använder ditt eget stöd för virtuella nätverk och fullständig kluster administratörs roll. Dessutom är många nya funktioner nu tillgängliga, till exempel support för operatorn Framework, operator Hub och OpenShift service-nät.

För att kunna övergå från Azure Red Hat OpenShift 3,11 till Azure Red Hat OpenShift 4, se till att granska [skillnaderna i lagring, nätverk, loggning, säkerhet och övervakning](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

I den här artikeln visar vi hur du migrerar från ett Azure Red Hat OpenShift 3,11-kluster till ett Azure Red Hat 4-kluster.

> [!NOTE]
> Verktyg för att migrera med Red Hat OpenShift, till exempel verktyget kontroll plan migration Assistance och verktyget för kluster program migrering (CAM) kan inte användas med Azure Red Hat OpenShift 3,11-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt Azure Red Hat OpenShift 3,11-kluster.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Skapa ett nytt Azure Red Hat OpenShift 4-kluster

Först [skapar du det Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md) som du vill använda som mål kluster. Här använder vi den grundläggande konfigurationen. Om du är intresse rad av olika inställningar läser du [själv studie kursen skapa en Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).

Skapa ett virtuellt nätverk med två tomma undernät för Master-och Worker-noderna.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Använd sedan följande kommando för att skapa klustret.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Konfigurera målets OpenShift 4-kluster

### <a name="authentication"></a>Autentisering

För att användare ska kunna interagera med Azure Red Hat OpenShift måste de först autentisera till klustret. Authentication Layer identifierar den användare som är associerad med begär anden till API: t för Azure Red Hat OpenShift. Auktoriserings skiktet använder sedan information om den begär ande användaren för att avgöra om begäran är tillåten.

När ett Azure Red Hat OpenShift 4-kluster skapas skapas en tillfällig administrativ användare. [Anslut till klustret](tutorial-connect-cluster.md), Lägg till användare och grupper och [Konfigurera de behörigheter som krävs](https://docs.openshift.com/aro/4/authentication/understanding-authentication.html) för båda.

### <a name="networking"></a>Nätverk

Azure Red Hat OpenShift 4 använder några olika operatorer för att konfigurera nätverket i klustret: [kluster nätverks operatör](https://docs.openshift.com/aro/4/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS-operatör](https://docs.openshift.com/aro/4/networking/dns-operator.html)och [inkommande operator](https://docs.openshift.com/aro/4/networking/ingress-operator.html). Mer information om hur du konfigurerar nätverk i ett Azure Red Hat OpenShift 4-kluster finns i [nätverks diagrammet](concepts-networking.md) och [förstå nätverk](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

### <a name="storage"></a>Storage
Azure Red Hat OpenShift 4 har stöd för följande PersistentVolume-plugin-program:

- AWS Elastic Block Store (EBS)
- Azure-disk
- Azure-fil
- GCE-beständig disk
- HostPath
- iSCSI
- Lokal volym
- NFS
- Red Hat OpenShift-container lagring

Information om hur du konfigurerar dessa lagrings typer finns i [Konfigurera beständig lagring](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Register

Azure Red Hat OpenShift 4 kan bygga avbildningar från din käll kod, distribuera dem och hantera deras livs cykel. För att möjliggöra detta ger Azure Red Hat OpenShift 4 ett [internt, integrerat behållar avbildnings register](https://docs.openshift.com/aro/4/registry/registry-options.html) som kan distribueras i din Azure Red Hat OpenShift-miljö för att hantera avbildningar lokalt.

Om du använder externa register som [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/), [Red Hat Quay](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-quay-overview_registry-options)-register eller ett [inaktiverat Red Hat-register](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), följer du stegen för att ange autentiseringsuppgifter för klustret så att klustret får åtkomst till databaserna.

### <a name="monitoring"></a>Övervakning

Azure Red Hat OpenShift innehåller en förkonfigurerad, förinstallerad och självuppdaterande övervaknings stack som baseras på Prometheus-projektet med öppen källkod och det större eko systemet. Den ger övervakning av kluster komponenter och innehåller en uppsättning aviseringar som omedelbart meddelar kluster administratören om eventuella problem och en uppsättning Grafana-instrumentpaneler. Kluster övervaknings stacken stöds bara för övervakning av Azure Red Hat OpenShift-kluster. Mer information finns i [kluster övervakning för Azure Red Hat OpenShift](https://docs.openshift.com/aro/4/monitoring/cluster_monitoring/about-cluster-monitoring.html).

Om du har använt [Azure Monitor för behållare för Azure Red Hat OpenShift 3,11](../azure-monitor/insights/container-insights-azure-redhat-setup.md)kan du också aktivera Azure Monitor för behållare för [Azure Red Hat OpenShift 4-kluster](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) och fortsätta använda samma Log Analytics-arbetsyta.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Flytta din DNS-eller belastnings Utjämnings konfiguration till det nya klustret

Om du använder Azure Traffic Manager lägger du till slut punkter som refererar till mål klustret och prioriterar dessa slut punkter.

## <a name="deploy-application-to-your-target-cluster"></a>Distribuera program till ditt mål kluster

När du har konfigurerat mål klustret korrekt för din arbets belastning [ansluter du till klustret](tutorial-connect-cluster.md) och skapar nödvändiga program, komponenter eller tjänster för dina projekt. Med hjälp av Azure Red Hat OpenShift kan du skapa dessa från git, behållar avbildningar, Red Hat Developer-katalogen, en Dockerfile, en YAML/JSON-definition eller genom att välja en databas tjänst från katalogen.

## <a name="delete-your-source-cluster"></a>Ta bort ditt käll kluster
När du har bekräftat att ditt Azure Red Hat OpenShift 4-kluster har kon figurer ATS korrekt, tar du bort ditt Azure Red Hat OpenShift 3,11-kluster.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Nästa steg
Kolla in Azure Red Hat OpenShift-dokumentationen som tillhandahålls av Red Hat [här](https://docs.openshift.com/aro/4/welcome/index.html).

