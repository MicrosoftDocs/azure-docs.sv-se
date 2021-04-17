---
title: Migrera till Azure Kubernetes Service (AKS)
description: Migrera till Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7856b2b0668eca7a172b738a7eeea640e466f003
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483120"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrera till Azure Kubernetes Service (AKS)

Den här guiden hjälper dig att planera och utföra en lyckad migrering till Azure Kubernetes Service (AKS) med information om den aktuella rekommenderade AKS-konfigurationen. Den här artikeln beskriver inte alla scenarier, men innehåller länkar till mer detaljerad information för att planera en lyckad migrering.

Det här dokumentet hjälper dig att stödja följande scenarier:

* Containerisera vissa program och migrera dem till AKS med hjälp [av Azure Migrate](../migrate/migrate-services-overview.md).
* Migrera ett AKS-kluster som backas upp [av tillgänglighetsuppsättningar](../virtual-machines/windows/tutorial-availability-sets.md) till [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).
* Migrera ett AKS-kluster för att använda en [standard-SKU-lastbalanserare.](./load-balancer-standard.md)
* Migrera från Azure Container Service [(ACS) – tillbaka 31 januari 2020 till](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) AKS.
* Migrera från [AKS-motorn](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) till AKS.
* Migrera från icke-Azure-baserade Kubernetes-kluster till AKS.
* Flytta befintliga resurser till en annan region.

När du migrerar ser du till att kubernetes-målversionen är inom det fönster som stöds för AKS. Äldre versioner kanske inte ligger inom det intervall som stöds och kräver en versionsuppgradering som stöds av AKS. Mer information finns i [AKS-versioner som stöds av Kubernetes.](./supported-kubernetes-versions.md)

Om du migrerar till en nyare version av Kubernetes granskar du Stödprincip för [Kubernetes-version och versionsskevhet.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

Flera verktyg med öppen källkod kan hjälpa dig med migreringen, beroende på ditt scenario:

* [Velero](https://velero.io/) (Kräver Kubernetes 1.7+)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

I den här artikeln sammanfattar vi migreringsinformation för:

> [!div class="checklist"]
> * Containerisera program via Azure Migrate 
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Kontrollera giltiga kvoter
> * Hög tillgänglighet och affärskontinunivå
> * Överväganden för tillståndslösa program
> * Överväganden för tillståndsful-program
> * Distribution av klusterkonfigurationen

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Använda Azure Migrate för att migrera dina program till AKS

Azure Migrate en enhetlig plattform för att utvärdera och migrera till lokala Azure-servrar, infrastruktur, program och data. För AKS kan du använda Azure Migrate för följande uppgifter:

* [Containerisera ASP.NET program och migrera till AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Containerisera Java-webbprogram och migrera till AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS med Standard Load Balancer och Virtual Machine Scale Sets

AKS är en hanterad tjänst som erbjuder unika funktioner med lägre hanteringskostnader. Eftersom AKS är en hanterad tjänst måste du välja från en uppsättning [regioner som](./quotas-skus-regions.md) AKS stöder. Du kan behöva ändra dina befintliga program för att hålla dem felfria på det AKS-hanterade kontrollplanet under övergången från ditt befintliga kluster till AKS.

Vi rekommenderar att du använder [](../virtual-machine-scale-sets/index.yml) AKS-Virtual Machine Scale Sets och [Azure-Standard Load Balancer](./load-balancer-standard.md) för att säkerställa att du får funktioner som:
* [Flera nodpooler](./use-multiple-node-pools.md),
* [Tillgänglighetszoner](../availability-zones/az-overview.md),
* [Auktoriserade IP-intervall](./api-server-authorized-ip-ranges.md),
* [Cluster Autoscaler](./cluster-autoscaler.md),
* [Azure Policy för AKS](../governance/policy/concepts/policy-for-kubernetes.md)och
* Andra nya funktioner när de lanseras.

AKS-kluster som backas [upp av tillgänglighetsuppsättningar för virtuella](../virtual-machines/availability.md#availability-sets) datorer saknar stöd för många av dessa funktioner.

I följande exempel skapas ett AKS-kluster med en enda nodpool som backas upp av en VM-skalningsuppsättning. Klustret:
* Använder en standardlastbalanserare. 
* Aktiverar autoskalning av kluster i nodpoolen för klustret.
* Anger minst *1 och* högst *3* noder.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Befintliga anslutna Azure-tjänster

När du migrerar kluster kan du ha kopplat externa Azure-tjänster. Följande tjänster kräver inte resurskrävande åtgärder, men de kräver uppdatering av anslutningar från tidigare till nya kluster för att bibehålla funktionaliteten.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Lagringskonto
* Externa databaser

## <a name="ensure-valid-quotas"></a>Se till att kvoter är giltiga

Eftersom andra virtuella datorer kommer att distribueras till din prenumeration under migreringen bör du kontrollera att dina kvoter och gränser är tillräckliga för dessa resurser. Om det behövs begär du en ökning av [vCPU-kvoten](../azure-portal/supportability/per-vm-quota-requests.md).

Du kan behöva begära en ökning för [nätverkskvoter för att säkerställa](../azure-portal/supportability/networking-quota-requests.md) att du inte tar slut på IP-adresser. Mer information finns i nätverk [och IP-intervall för AKS.](./configure-kubenet.md)

Mer information finns i [Azure-prenumeration och tjänstbegränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md) Om du vill kontrollera dina aktuella kvoter går [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)Azure Portal prenumerationsbladet, väljer din prenumeration och väljer **sedan Användning + kvoter.**

## <a name="high-availability-and-business-continuity"></a>Hög tillgänglighet och affärskontinuhet

Om ditt program inte kan hantera driftstopp måste du följa bästa praxis för migreringsscenarier med hög tillgänglighet. Läs mer om metodtips för komplex planering för affärskontinuisering, haveriberedskap och [maximera drifttid i Azure Kubernetes Service (AKS).](./operator-best-practices-multi-region.md)

För komplexa program migrerar du vanligtvis över tid i stället för allt på en gång, vilket innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare `ClusterIP` använder tjänster för kommunikation kan behöva exponeras som typ och skyddas på lämpligt `LoadBalancer` sätt.

För att slutföra migreringen behöver du peka klienter på de nya tjänster som körs på AKS. Vi rekommenderar att du omdirigerar trafik genom att uppdatera DNS så att det pekar Load Balancer som finns framför AKS-klustret.

[Azure Traffic Manager](../traffic-manager/index.yml) kan dirigera kunder till önskat Kubernetes-kluster och programinstans. Traffic Manager är en lastbalanserare för DNS-baserad trafik som kan distribuera nätverkstrafik mellan regioner. För bästa prestanda och redundans bör du dirigera all programtrafik Traffic Manager innan den går till ditt AKS-kluster. 

I en distribution med flera kluster bör kunderna ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna i varje AKS-kluster. Definiera dessa tjänster med hjälp Traffic Manager slutpunkter. Varje slutpunkt är *tjänstens lastbalanserings-IP.* Använd den här konfigurationen för att dirigera nätverkstrafik Traffic Manager slutpunkten i en region till slutpunkten i en annan region.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) är ett annat alternativ för att dirigera trafik för AKS-kluster. Med Azure Front Door Service kan du definiera, hantera och övervaka global routning för webbtrafik genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. 

### <a name="considerations-for-stateless-applications"></a>Överväganden för tillståndslösa program

Tillståndslös programmigrering är det enklaste fallet:
1. Tillämpa dina resursdefinitioner (YAML eller Helm) på det nya klustret.
1. Se till att allt fungerar som förväntat.
1. Omdirigera trafik för att aktivera det nya klustret.

### <a name="considerations-for-stateful-applications"></a>Överväganden för tillståndsful-program

Planera noggrant migreringen av tillståndsfulla program för att undvika dataförlust eller oväntade driftstopp.

* Om du använder Azure Files kan du montera filresursen som en volym i det nya klustret. Se [Montera statiska Azure Files som en volym](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Om du använder Azure Managed Disks kan du bara montera disken om den inte är fäst på en virtuell dator. Se [Montera statisk Azure-disk som en volym.](./azure-disk-volume.md#mount-disk-as-volume)
* Om ingen av dessa metoder fungerar kan du använda säkerhetskopierings- och återställningsalternativ. Se [Velero på Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar Azure Files kan monteras på flera värdar samtidigt. I ditt AKS-kluster hindrar inte Azure och Kubernetes dig från att skapa en podd som AKS-klustret fortfarande använder. Se till att klustren inte skriver till samma filer samtidigt för att förhindra dataförlust och oväntat beteende.

Om programmet kan vara värd för flera repliker som pekar på samma filresurs följer du stegen för tillståndslös migrering och distribuerar DINA YAML-definitioner till det nya klustret. 

Om inte, omfattar en möjlig migreringsmetod följande steg:

1. Kontrollera att programmet fungerar som det ska.
1. Peka din livetrafik till ditt nya AKS-kluster.
1. Koppla från det gamla klustret.

Om du vill börja med en tom resurs och göra en kopia av källdata kan du använda [`az storage file copy`](/cli/azure/storage/file/copy) kommandona för att migrera dina data.


#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS följer du vanligtvis dessa steg:

1. Quiesce skriver till programmet. 
    * Det här steget är valfritt och kräver stilleståndstid.
1. Ta ögonblicksbilder av diskarna.
1. Skapa nya hanterade diskar från ögonblicksbilderna.
1. Skapa beständiga volymer i AKS.
1. Uppdatera poddspecifikationer för [att använda befintliga volymer](./azure-disk-volume.md) i stället för PersistentVolumeClaims (statisk etablering).
1. Distribuera ditt program till AKS.
1. Kontrollera att programmet fungerar som det ska.
1. Peka din livetrafik till ditt nya AKS-kluster.

> [!IMPORTANT]
> Om du väljer att inte ta bort skrivningar måste du replikera data till den nya distributionen. Annars kommer du att missa de data som skrevs efter att du tog ögonblicksbilderna av disken.

Vissa verktyg med öppen källkod kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Azure CLI-tillägget för diskkopiering](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar mellan resursgrupper och Azure-regioner.
* [Azure Kube CLI-tillägget](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.


### <a name="deployment-of-your-cluster-configuration"></a>Distribution av klusterkonfigurationen

Vi rekommenderar att du använder din befintliga pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en känd bra konfiguration till AKS. Du kan använda Azure Pipelines för [att skapa och distribuera dina program till AKS.](/azure/devops/pipelines/ecosystems/kubernetes/aks-template) Klona dina befintliga distributionsuppgifter och se till att `kubeconfig` pekar på det nya AKS-klustret.

Om det inte är möjligt exporterar du resursdefinitioner från ditt befintliga Kubernetes-kluster och tillämpar dem sedan på AKS. Du kan använda `kubectl` för att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Flytta befintliga resurser till en annan region

Du kanske vill flytta ditt AKS-kluster till en [annan region som stöds av AKS][region-availability]. Vi rekommenderar att du skapar ett nytt kluster i den andra regionen och sedan distribuerar dina resurser och program till det nya klustret. 

Om du dessutom har några tjänster som [Azure Dev Spaces][azure-dev-spaces] som körs i ditt AKS-kluster måste du installera och konfigurera dessa tjänster i klustret i den nya regionen.


I den här artikeln sammanfattade vi migreringsinformation för:

> [!div class="checklist"]
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Kontrollera giltiga kvoter
> * Hög tillgänglighet och affärskontinunivå
> * Överväganden för tillståndslösa program
> * Överväganden för tillståndsful-program
> * Distribution av klusterkonfigurationen


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
