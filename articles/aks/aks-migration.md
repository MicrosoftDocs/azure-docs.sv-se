---
title: Migrera till Azure Kubernetes service (AKS)
description: Migrera till Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: d5e3543fd6b7cd1b5534d6e363e51f1778cc7fc9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012135"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrera till Azure Kubernetes service (AKS)

För att hjälpa dig att planera och utföra en lyckad migrering till Azure Kubernetes service (AKS) innehåller den här guiden information om den aktuella rekommenderade AKS-konfigurationen. Även om den här artikeln inte omfattar varje scenario, innehåller den länkar till mer detaljerad information för att planera en lyckad migrering.

Det här dokumentet hjälper till att stödja följande scenarier:

* Containerrar vissa program och migrerar dem till AKS med hjälp av [Azure Migrate](../migrate/migrate-services-overview.md).
* Migrera ett AKS-kluster som backas upp av [tillgänglighets uppsättningar](../virtual-machines/windows/tutorial-availability-sets.md) till [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).
* Migrera ett AKS-kluster för att använda en [standard-SKU-belastningsutjämnare](./load-balancer-standard.md).
* Migrering från [Azure Container Service (ACS) – tas ur bruk 31 januari 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) till AKS.
* Migrerar från [AKS-motorn](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) till AKS.
* Migrera från icke-Azure-baserade Kubernetes-kluster till AKS.
* Flytta befintliga resurser till en annan region.

När du migrerar bör du se till att mål Kubernetes-versionen är i fönstret som stöds för AKS. Äldre versioner kanske inte ligger inom det intervall som stöds och kräver att en versions uppgradering stöds av AKS. Mer information finns i [AKS Kubernetes-versioner som stöds](./supported-kubernetes-versions.md).

Om du migrerar till en nyare version av Kubernetes granskar du [support policyn för Kubernetes version och versions förvrängning](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Flera verktyg med öppen källkod kan hjälpa dig med din migrering, beroende på ditt scenario:

* [Velero](https://velero.io/) (kräver Kubernetes 1.7 +)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [Flytta om](https://github.com/mhausenblas/reshifter)

I den här artikeln sammanfattas information om migreringen för:

> [!div class="checklist"]
> * Containerrar program via Azure Migrate 
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Se till att giltiga kvoter
> * Hög tillgänglighet och affärs kontinuitet
> * Överväganden för tillstånds lösa program
> * Överväganden för tillstånds känsliga program
> * Distribution av kluster konfigurationen

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Använd Azure Migrate för att migrera dina program till AKS

Azure Migrate erbjuder en enhetlig plattform för att utvärdera och migrera till lokala Azure-servrar, infrastruktur, program och data. För AKS kan du använda Azure Migrate för följande uppgifter:

* [Använd ASP.NET-program och migrera till AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Använd Java-webbprogram och migrera till AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS med Standard Load Balancer och Virtual Machine Scale Sets

AKS är en hanterad tjänst som erbjuder unika funktioner med lägre hanterings kostnader. Eftersom AKS är en hanterad tjänst måste du välja från en uppsättning [regioner](./quotas-skus-regions.md) som AKS stöder. Du kan behöva ändra dina befintliga program för att hålla dem friska på AKS kontroll plan under över gången från det befintliga klustret till AKS.

Vi rekommenderar att du använder AKS-kluster som backas upp av [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) och [Azure standard Load Balancer](./load-balancer-standard.md) för att se till att du får till gång till funktioner som:
* [Flera noder i pooler](./use-multiple-node-pools.md),
* [Tillgänglighetszoner](../availability-zones/az-overview.md),
* [Auktoriserade IP-intervall](./api-server-authorized-ip-ranges.md),
* [Kluster autoskalning](./cluster-autoscaler.md),
* [Azure policy för AKS](../governance/policy/concepts/policy-for-kubernetes.md)och
* Andra nya funktioner när de släpps.

AKS-kluster som backas upp av [tillgänglighets uppsättningar för virtuella datorer](../virtual-machines/availability.md#availability-sets) saknar stöd för många av dessa funktioner.

I följande exempel skapas ett AKS-kluster med en pool med flera noder som backas upp av en skalnings uppsättning för virtuell dator (VM). Klustret:
* Använder en standard belastnings utjämning. 
* Aktiverar den automatiska skalnings funktionen för klustret i klustrets Node-pool.
* Ställer in minst *1* och högst *3* noder.

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

När du migrerar kluster kan du ha anslutna externa Azure-tjänster. Även om följande tjänster inte kräver resurs fritid, kräver de att uppdatera anslutningar från tidigare till nya kluster för att underhålla funktioner.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Lagringskonto
* Externa databaser

## <a name="ensure-valid-quotas"></a>Se till att giltiga kvoter

Eftersom andra virtuella datorer ska distribueras till din prenumeration under migreringen bör du kontrol lera att kvoterna och gränserna är tillräckliga för dessa resurser. Om det behövs kan du begära en ökning av [vCPU-kvoten](../azure-portal/supportability/per-vm-quota-requests.md).

Du kan behöva begära en ökning av [nätverks kvoterna](../azure-portal/supportability/networking-quota-requests.md) för att se till att du inte har några IP-adresser. Mer information finns i [nätverk och IP-intervall för AKS](./configure-kubenet.md).

Mer information finns i [prenumerations-och tjänst begränsningar i Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Du kan kontrol lera dina aktuella kvoter genom att gå till [bladet prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)i Azure Portal, välja din prenumeration och sedan välja **användning + kvoter**.

## <a name="high-availability-and-business-continuity"></a>Hög tillgänglighet och affärs kontinuitet

Om programmet inte kan hantera nedtid måste du följa bästa praxis för scenarier med migrering av hög tillgänglighet. Läs mer om [metod tips för komplex planering av affärs kontinuitet, haveri beredskap och maximal drift tid i Azure Kubernetes service (AKS)](./operator-best-practices-multi-region.md).

För komplexa program migrerar du vanligt vis över tid snarare än samtidigt, vilket innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare använde `ClusterIP` tjänster för att kommunicera kan behöva exponeras som typ `LoadBalancer` och skyddas på lämpligt sätt.

För att slutföra migreringen vill du peka klienter till de nya tjänster som körs på AKS. Vi rekommenderar att du omdirigerar trafik genom att uppdatera DNS för att peka på Load Balancer som sitter framför ditt AKS-kluster.

[Azure Traffic Manager](../traffic-manager/index.yml) kan dirigera kunder till det önskade Kubernetes-klustret och program instansen. Traffic Manager är en DNS-baserad trafikbelastnings utjämning som kan distribuera nätverks trafik över flera regioner. För bästa prestanda och redundans ska du dirigera all program trafik genom Traffic Manager innan den går till ditt AKS-kluster. 

I en distribution med flera kluster bör kunderna ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna på varje AKS-kluster. Definiera de här tjänsterna genom att använda Traffic Manager-slutpunkter. Varje slut punkt är *IP för tjänst belastnings utjämning*. Använd den här konfigurationen för att dirigera nätverks trafik från Traffic Manager slut punkt i en region till slut punkten i en annan region.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure-tjänsten för front dörr](../frontdoor/front-door-overview.md) är ett annat alternativ för att dirigera trafik för AKS-kluster. Med Azures tjänst för front dörr kan du definiera, hantera och övervaka den globala routningen för din webb trafik genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. 

### <a name="considerations-for-stateless-applications"></a>Överväganden för tillstånds lösa program

Tillstånds lös programmigrering är det enklaste fallet:
1. Tillämpa dina resurs definitioner (YAML eller Helm) på det nya klustret.
1. Se till att allt fungerar som förväntat.
1. Omdirigera trafik för att aktivera det nya klustret.

### <a name="considerations-for-stateful-applications"></a>Överväganden för tillstånds känsliga program

Planera noggrant migreringen av tillstånds känsliga program för att undvika data förlust eller oväntad stillestånds tid.

* Om du använder Azure Files kan du montera fil resursen som en volym i det nya klustret. Se [montera statiska Azure Files som en volym](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Om du använder Azure Managed Disks kan du bara montera disken om den inte är ansluten till någon virtuell dator. Se [montera statisk Azure-disk som en volym](./azure-disk-volume.md#mount-disk-as-volume).
* Om ingen av dessa metoder fungerar kan du använda en alternativ för säkerhets kopiering och återställning. Se [Velero på Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar kan Azure Files monteras till flera värdar samtidigt. I ditt AKS-kluster förhindrar Azure och Kubernetes dig inte att skapa en pod som ditt AKS-kluster fortfarande använder. Förhindra data förlust och oväntade beteenden genom att se till att klustren inte skriver till samma filer samtidigt.

Om ditt program kan vara värd för flera repliker som pekar på samma fil resurs, följer du stegen för stegvis migrering och distribuerar YAML-definitionerna till det nya klustret. 

Om inte, så innebär en möjlig migrering följande steg:

1. Verifiera att programmet fungerar korrekt.
1. Peka din aktiva trafik till ditt nya AKS-kluster.
1. Koppla bort det gamla klustret.

Om du vill börja med en tom resurs och göra en kopia av data källan kan du använda [`az storage file copy`](/cli/azure/storage/file/copy) kommandona för att migrera dina data.


#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS följer du normalt de här stegen:

1. Skrivningar från sidan till programmet. 
    * Det här steget är valfritt och kräver stillestånds tid.
1. Ta ögonblicks bilder av diskarna.
1. Skapa nya hanterade diskar från ögonblicks bilderna.
1. Skapa beständiga volymer i AKS.
1. Uppdatera Pod-specifikationerna för att [använda befintliga volymer](./azure-disk-volume.md) i stället för PersistentVolumeClaims (statisk etablering).
1. Distribuera ditt program till AKS.
1. Verifiera att programmet fungerar korrekt.
1. Peka din aktiva trafik till ditt nya AKS-kluster.

> [!IMPORTANT]
> Om du väljer att inte använda offline-skrivning måste du replikera data till den nya distributionen. Annars saknar du de data som skrevs efter att du tog disk ögonblicks bilderna.

Vissa verktyg med öppen källkod kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Tillägget Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar över resurs grupper och Azure-regioner.
* [Azure Kube CLI-tillägget](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.


### <a name="deployment-of-your-cluster-configuration"></a>Distribution av kluster konfigurationen

Vi rekommenderar att du använder din befintliga-pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en fungerande konfiguration till AKS. Du kan använda Azure-pipelines för att [bygga och distribuera dina program till AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template). Klona dina befintliga distributions uppgifter och se till att `kubeconfig` de pekar på det nya AKS-klustret.

Om detta inte är möjligt exporterar du resurs definitioner från ditt befintliga Kubernetes-kluster och tillämpar dem sedan på AKS. Du kan använda `kubectl` för att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Flytta befintliga resurser till en annan region

Du kanske vill flytta ditt AKS-kluster till en [annan region som stöds av AKS][region-availability]. Vi rekommenderar att du skapar ett nytt kluster i den andra regionen och sedan distribuerar dina resurser och program till det nya klustret. 

Om du har några tjänster som [Azure dev Spaces][azure-dev-spaces] som körs på ditt AKS-kluster måste du dessutom installera och konfigurera tjänsterna i klustret i den nya regionen.


I den här artikeln sammanfattade vi migrerings information för:

> [!div class="checklist"]
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Se till att giltiga kvoter
> * Hög tillgänglighet och affärs kontinuitet
> * Överväganden för tillstånds lösa program
> * Överväganden för tillstånds känsliga program
> * Distribution av kluster konfigurationen


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
