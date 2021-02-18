---
title: Kluster konfiguration i Azure Kubernetes Services (AKS)
description: Lär dig hur du konfigurerar ett kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572788"
---
# <a name="configure-an-aks-cluster"></a>Konfigurera ett AKS-kluster

Som en del av att skapa ett AKS-kluster kan du behöva anpassa kluster konfigurationen så att den passar dina behov. I den här artikeln beskrivs några alternativ för att anpassa ditt AKS-kluster.

## <a name="os-configuration"></a>OS-konfiguration

AKS stöder nu Ubuntu 18,04 som standard operativ system (OS) i allmän tillgänglighet (GA) för kluster i Kubernetes versioner som är högre än 1,18 för versionerna under 1,18, AKS Ubuntu 16,04 är fortfarande standard bas avbildningen. Från Kubernetes v 1.18 och högre är standard basen AKS Ubuntu 18,04.

> [!IMPORTANT]
> Resurspooler som skapats på Kubernetes v-1.18 eller större standardvärden till `AKS Ubuntu 18.04` Node-avbildningen. Nodkonfigurationer på en Kubernetes-version som stöds är mindre än 1,18 ta emot `AKS Ubuntu 16.04` som Node-avbildningen, men kommer att uppdateras till `AKS Ubuntu 18.04` en gång som Kubernetes-versionen för Node-poolen uppdateras till v 1.18 eller senare.
> 
> Vi rekommenderar starkt att du testar dina arbets belastningar på AKS Ubuntu 18,04-nodkonfigurationer innan du använder kluster på 1,18 eller senare.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Använda AKS Ubuntu 18,04 (GA) på nya kluster

Kluster som skapats på Kubernetes v-1.18 eller större standardvärden till `AKS Ubuntu 18.04` Node-avbildningen. Nodkonfigurationer i en Kubernetes-version som stöds är mindre än 1,18 kommer fortfarande att få `AKS Ubuntu 16.04` som Node-avbildningen, men kommer att uppdateras till `AKS Ubuntu 18.04` när klustret eller nodens Kubernetes version har uppdaterats till v 1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbets belastningar på AKS Ubuntu 18,04-nodkonfigurationer innan du använder kluster på 1,18 eller senare.

Skapa ett kluster med hjälp av `AKS Ubuntu 18.04` noden avbildning genom att skapa ett kluster som kör Kubernetes v 1.18 eller mer som visas nedan

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Använda AKS Ubuntu 18,04 (GA) på befintliga kluster

Kluster som skapats på Kubernetes v-1.18 eller större standardvärden till `AKS Ubuntu 18.04` Node-avbildningen. Nodkonfigurationer i en Kubernetes-version som stöds är mindre än 1,18 kommer fortfarande att få `AKS Ubuntu 16.04` som Node-avbildningen, men kommer att uppdateras till `AKS Ubuntu 18.04` när klustret eller nodens Kubernetes version har uppdaterats till v 1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbets belastningar på AKS Ubuntu 18,04-nodkonfigurationer innan du använder kluster på 1,18 eller senare.

Om dina kluster eller resurspooler är klara för `AKS Ubuntu 18.04` Node-avbildningen, kan du uppgradera dem till en v-1.18 eller högre upp enligt nedan.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Om du bara vill uppgradera en pool med bara en nod:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Testa AKS Ubuntu 18,04 (GA) på befintliga kluster

Resurspooler som skapats på Kubernetes v-1.18 eller större standardvärden till `AKS Ubuntu 18.04` Node-avbildningen. Nodkonfigurationer i en Kubernetes-version som stöds är mindre än 1,18 kommer fortfarande att tas emot `AKS Ubuntu 16.04` som Node-avbildningen, men kommer att uppdateras till `AKS Ubuntu 18.04` en gång som Kubernetes-versionen för Node-poolen uppdateras till v 1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbets belastningar på AKS Ubuntu 18,04 Node-pooler innan du uppgraderar dina produktionspooler.

Om du vill skapa en Node-pool med hjälp av `AKS Ubuntu 18.04` Node-avbildningen skapar du bara en resurspool som kör Kubernetes v 1.18 eller senare. Ditt kluster kontroll plan måste vara minst på v-1.18 eller större, men dina andra noder kan finnas kvar på en äldre Kubernetes-version.
Nedan ska vi först uppgradera kontroll planet och sedan skapa en ny resurspool med v-1.18 som tar emot den nya nodens avbildnings-OS-version.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Konfiguration av container runtime

En container runtime är program vara som kör behållare och hanterar behållar avbildningar på en nod. Körningen gör att du kan köra behållare på Linux eller Windows med hjälp av en viss funktion i abstrakta sys-anrop eller operativ system (OS). AKS-kluster med Kubernetes version 1,19 och större användning `containerd` som behållar körning. AKS-kluster som använder Kubernetes före v 1.19 för Node-pooler använder [Moby](https://mobyproject.org/) (överordnad Docker) som dess behållar körning.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) är ett [OCI](https://opencontainers.org/) -kompatibelt (Open container Initiative)-kompatibel Core container runtime som innehåller den minsta uppsättningen nödvändiga funktioner för att köra behållare och hantera avbildningar på en nod. Den har [donerat](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) till Cloud Native Compute Foundation (CNCF) i mars 2017. Den aktuella Moby-versionen som AKS använder använder redan och är byggd ovanpå `containerd` , som visas ovan.

Med en `containerd` -baserad nod och nodkonfigurationer, i stället för att prata med `dockershim` , kommer kubelet att kommunicera direkt med `containerd` via CRI-plugin-programmet (container runtime Interface) och ta bort extra hopp i flödet jämfört med Docker-CRI-implementeringen. Därför ser du bättre Pod start latens och mindre resurs (CPU-och minnes användning).

Genom `containerd` att använda för AKS-noder förbättrar Pod-startsvars tiden och resursanvändningen för resurs användningen av container runtime minskar. Dessa förbättringar aktive ras i den här nya arkitekturen där kubelet `containerd` kommunicerar direkt med CRI-plugin-programmet medan Moby/Docker Architecture kubelet pratar med `dockershim` och Docker-motorn innan den når `containerd` , och därför har extra hopp i flödet.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` fungerar på alla GA-versioner av Kubernetes i AKS och i varje överordnad Kubernetes-version över v-1.19 och stöder alla Kubernetes-och AKS-funktioner.

> [!IMPORTANT]
> Kluster med resurspooler som skapats på Kubernetes v-1.19 eller som är standardvärdet `containerd` för dess behållar körning. Kluster med noder i en Kubernetes-version som stöds och som är mindre än 1,19 tar emot `Moby` för dess behållar körning, men kommer att uppdateras till `ContainerD` en gång som Kubernetes-versionen för Node-poolen uppdateras till v 1.19 eller senare. Du kan fortfarande använda `Moby` resurspooler och kluster på äldre versioner som stöds tills de faller utanför supporten.
> 
> Vi rekommenderar starkt att du testar dina arbets belastningar på AKS-noder med `containerD` innan du använder kluster på 1,19 eller mer.

### <a name="containerd-limitationsdifferences"></a>`Containerd` begränsningar/skillnader

* Om du vill använda `containerd` som container runtime måste du använda AKS Ubuntu 18,04 som bas operativ system avbildning.
* Även om Docker-verktygen fortfarande finns på noderna, används Kubernetes `containerd` som behållar körning. Eftersom Moby/Docker inte hanterar Kubernetes-skapade behållare på noderna kan du därför inte Visa eller interagera med dina behållare med hjälp av Docker-kommandon (t `docker ps` . ex.) eller Docker-API: et.
* För `containerd` rekommenderar vi att du använder [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) som en ERSÄTTNINGS-cli i stället för Docker CLI för **fel sökning** av poddar, behållare och behållar avbildningar på Kubernetes-noder (till exempel `crictl ps` ). 
   * Den ger inte den fullständiga funktionen i Docker CLI. Den är endast avsedd för fel sökning.
   * `crictl` ger en mer Kubernetes vy över behållare, med begrepp som poddar osv.
* `Containerd` ställer in loggning med hjälp av standardiserat `cri` loggnings format (vilket skiljer sig från vad du för närvarande får från Docker-JSON-drivrutinen). Din loggnings lösning måste ha stöd för `cri` loggnings formatet (t. ex. [Azure Monitor för behållare](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* Du kan inte längre komma åt Docker-motorn `/var/run/docker.sock` eller använda Docker-in-Docker (DinD).
  * Om du för närvarande extraherar program loggar eller övervakar data från Docker-motorn, bör du använda något som [Azure Monitor för behållare](../azure-monitor/containers/container-insights-enable-new-cluster.md) i stället. Dessutom har AKS inte stöd för att köra out-of-band-kommandon på de agent-noder som kan orsaka instabilitet.
  * Även om du använder Moby/Docker kan det vara starkt att skapa avbildningar och direkt dra nytta av Docker-motorn via metoderna ovan. Kubernetes är inte helt medvetna om dessa förbrukade resurser och dessa metoder visar flera problem som beskrivs [här](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) och [här](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), till exempel.
* Skapa avbildningar – du kan fortsätta att använda ditt nuvarande Docker build-arbetsflöde som vanligt, om du inte skapar avbildningar i ditt AKS-kluster. I detta fall bör du överväga att växla till den rekommenderade metoden för att skapa bilder med hjälp av [ACR-uppgifter](../container-registry/container-registry-quickstart-task-cli.md)eller ett säkrare kluster alternativ som [Docker-buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Andra generationens virtuella datorer

Azure stöder [virtuella datorer i generation 2 (Gen2)](../virtual-machines/generation-2.md). Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1 (gen1). Dessa funktioner omfattar ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM).

Virtuella datorer i generation 2 använder den nya UEFI-baserade start arkitekturen i stället för den BIOS-baserade arkitekturen som används av virtuella datorer i generation 1.
Endast vissa SKU: er och storlekar har stöd för virtuella Gen2-datorer. Kontrol lera [listan över storlekar som stöds](../virtual-machines/generation-2.md#generation-2-vm-sizes)för att se om din SKU stöder eller kräver Gen2.

Alla VM-avbildningar stöder inte heller Gen2, på AKS Gen2-VM: ar kommer att använda den nya [AKS Ubuntu 18,04-avbildningen](#os-configuration). Den här avbildningen stöder alla Gen2 SKU: er och storlekar.

## <a name="ephemeral-os"></a>Tillfälligt operativ system

Som standard replikerar Azure automatiskt operativ system disken för en virtuell dator till Azure Storage för att undvika data förlust om den virtuella datorn måste flyttas till en annan värd. Eftersom behållarna inte har utformats för att ha ett lokalt tillstånd, erbjuder det här beteendet ett begränsat värde och ger vissa nack delar, inklusive långsammare etablering av noder och högre Läs-och skriv fördröjning.

Tillfälliga OS-diskar lagras däremot bara på värddatorn, precis som en tillfällig disk. Detta ger lägre Läs-och skriv fördröjning, tillsammans med snabbare nod skalning och kluster uppgraderingar.

Precis som den temporära disken ingår en tillfällig OS-disk i priset på den virtuella datorn, så du debiteras inga ytterligare lagrings kostnader.

> [!IMPORTANT]
>När en användare inte uttryckligen begär Managed disks för operativ systemet, kommer AKS att standardvärdet för tillfälliga operativ system om möjligt för en specifik nodepool-konfiguration.

När du använder ett tillfälligt operativ system måste OS-disken rymmas i VM-cachen. Storlekarna för VM-cachen är tillgängliga i [Azure-dokumentationen](../virtual-machines/dv3-dsv3-series.md) inom parentes bredvid IO-dataflöde ("cachestorlek i GIB").

Om du använder AKS standard storlek för virtuell dator Standard_DS2_v2 med standard storleken på OS-disken på 100 GB som ett exempel, har den här virtuella dator storleken stöd för tillfälliga operativ system, men har bara 86GB cache-storlek. Den här konfigurationen använder som standard Managed disks om användaren inte uttryckligen anges. Om en användare uttryckligen begärt det tillfälliga operativ systemet får de ett verifierings fel.

Om en användare begär samma Standard_DS2_v2 med en 60 GB OS-disk används den här konfigurationen som standard för tillfälliga operativ system: den begärda storleken på 60 GB är mindre än den maximala cachestorleken för 86GB.

Med Standard_D8s_v3 med 100 GB OS-disk stöder den här virtuella dator storleken tillfälliga OS och har 200 GB cache-utrymme. Om en användare inte anger typen av operativ system skulle nodepool ta emot tillfälliga operativ system som standard. 

Det tillfälliga operativ systemet kräver minst version 2.15.0 av Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Använd tillfälliga operativ system på nya kluster

Konfigurera klustret så att det använder tillfälliga OS-diskar när klustret skapas. Använd `--node-osdisk-type` flaggan för att ange ett tillfälligt operativ system som OS-disktyp för det nya klustret.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Om du vill skapa ett vanligt kluster med nätverks anslutna OS-diskar kan du göra det genom att ange `--node-osdisk-type=Managed` . Du kan också välja att lägga till fler tillfälliga OS-nodkonfigurationer enligt nedan.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Använd tillfälliga operativ system på befintliga kluster
Konfigurera en ny Node-pool så att den använder tillfälliga OS-diskar. Använd `--node-osdisk-type` flaggan för att ange som OS-disktyp som typ av operativ system disk för den noden.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Med ett tillfälligt operativ system kan du distribuera VM-och instans avbildningar upp till storleken på VM-cachen. I AKS-fallet använder standardnodens operativ system disk konfiguration 128 GB, vilket innebär att du behöver en VM-storlek som har ett cacheminne som är större än 128 GB. Standard Standard_DS2_v2 har en cachestorlek på 86GB, vilket inte är tillräckligt stort. Standard_DS3_v2 har en cachestorlek på 172GB, vilket är tillräckligt stort. Du kan också minska standard storleken på OS-disken med hjälp av `--node-osdisk-size` . Den minsta storleken för AKS-avbildningar är 30 GB. 

Om du vill skapa Node-pooler med nätverksanslutna OS-diskar kan du göra det genom att ange `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Namn på anpassad resurs grupp

När du distribuerar ett Azure Kubernetes service-kluster i Azure skapas en andra resurs grupp för arbetsnoderna. Som standard namnger AKS resurs gruppen för noden `MC_resourcegroupname_clustername_location` , men du kan också ange ett eget namn.

Om du vill ange ett eget namn på en resurs grupp installerar du AKS-Preview Azure CLI-tillägget version 0.3.2 eller senare. Använd- `--node-resource-group` parametern för `az aks create` kommandot för att ange ett eget namn för resurs gruppen med hjälp av Azure CLI. Om du använder en Azure Resource Manager-mall för att distribuera ett AKS-kluster kan du definiera resurs gruppens namn genom att använda `nodeResourceGroup` egenskapen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Den sekundära resurs gruppen skapas automatiskt av Azure Resource Provider i din egen prenumeration. Du kan bara ange namnet på den anpassade resurs gruppen när klustret skapas. 

När du arbetar med resurs gruppen för noden bör du tänka på att du inte kan:

- Ange en befintlig resurs grupp för resurs gruppen för noden.
- Ange en annan prenumeration för resurs gruppen för noden.
- Ändra resurs grupp namnet för noden när klustret har skapats.
- Ange namn för de hanterade resurserna i resurs gruppen för noden.
- Ändra eller ta bort Azure-skapade Taggar av hanterade resurser i resurs gruppen för noden.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du uppgraderar nodens avbildningar](node-image-upgrade.md) i klustret.
- Se [uppgradera ett Azure Kubernetes service (AKS)-kluster](upgrade-cluster.md) för att lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- Läs mer om [ `containerd` och Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- I listan med [vanliga frågor och svar om AKS](faq.md) hittar du svar på några vanliga frågor om AKS.
- Läs mer om [tillfälliga OS-diskar](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
