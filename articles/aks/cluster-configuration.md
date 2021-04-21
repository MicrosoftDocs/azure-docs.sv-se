---
title: Klusterkonfiguration i Azure Kubernetes Services (AKS)
description: Lär dig hur du konfigurerar ett kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775878"
---
# <a name="configure-an-aks-cluster"></a>Konfigurera ett AKS-kluster

Som en del av att skapa ett AKS-kluster kan du behöva anpassa klusterkonfigurationen så att den passar dina behov. Den här artikeln innehåller några alternativ för att anpassa ditt AKS-kluster.

## <a name="os-configuration"></a>OS-konfiguration

AKS stöder nu Ubuntu 18.04 som standardnodoperativsystem (OS) i allmän tillgänglighet (GA) för kluster i Kubernetes-versioner som är högre än 1.18 För versioner under 1.18 är AKS Ubuntu 16.04 fortfarande standardbasavbildningen. Från kubernetes v1.18 och senare är standardbasen AKS Ubuntu 18.04.

> [!IMPORTANT]
> Nodpooler som skapats på Kubernetes v1.18 eller högre som standard är `AKS Ubuntu 18.04` nodavbildningar. Nodpooler på en Kubernetes-version som stöds som är mindre än 1.18 får som nodavbildning, men kommer att uppdateras till när kubernetes-versionen för nodpoolen har uppdaterats till `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 eller senare.
> 
> Vi rekommenderar starkt att du testar dina arbetsbelastningar på AKS Ubuntu 18.04-nodpooler innan du använder kluster på 1.18 eller högre.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Använda AKS Ubuntu 18.04 (GA) på nya kluster

Kluster som skapas på Kubernetes v1.18 eller högre som standard är `AKS Ubuntu 18.04` nodavbildningar. Nodpooler i en Kubernetes-version som stöds som är mindre än 1.18 får fortfarande som nodavbildning, men kommer att uppdateras till när kubernetes-versionen för klustret eller nodpoolen har uppdaterats till `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbetsbelastningar på AKS Ubuntu 18.04-nodpooler innan du använder kluster på 1.18 eller högre.

Om du vill skapa ett kluster med en nodavbildning skapar du bara ett kluster som kör `AKS Ubuntu 18.04` kubernetes v1.18 eller högre enligt nedan

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Använda AKS Ubuntu 18.04 (GA) på befintliga kluster

Kluster som skapas på Kubernetes v1.18 eller högre som standard är `AKS Ubuntu 18.04` nodavbildningar. Nodpooler i en Kubernetes-version som stöds som är mindre än 1.18 får fortfarande som nodavbildning, men kommer att uppdateras till när kubernetes-versionen för klustret eller nodpoolen har uppdaterats till `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbetsbelastningar på AKS Ubuntu 18.04-nodpooler innan du använder kluster på 1.18 eller högre.

Om dina kluster eller nodpooler är redo för nodavbildning kan du helt enkelt uppgradera dem till `AKS Ubuntu 18.04` en v1.18 eller senare enligt nedan.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Om du bara vill uppgradera en nodpool:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Testa AKS Ubuntu 18.04 (GA) på befintliga kluster

Nodpooler som skapats på Kubernetes v1.18 eller högre som standard är `AKS Ubuntu 18.04` nodavbildningar. Nodpooler i en Kubernetes-version som stöds som är mindre än 1.18 får fortfarande som nodavbildning, men kommer att uppdateras till när nodpoolens Kubernetes-version har uppdaterats till `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 eller senare.

Vi rekommenderar starkt att du testar dina arbetsbelastningar på AKS Ubuntu 18.04-nodpooler innan du uppgraderar dina produktionsnodpooler.

Om du vill skapa en nodpool med hjälp av en nodavbildning skapar du helt enkelt en nodpool som `AKS Ubuntu 18.04` kör kubernetes v1.18 eller högre. Klusterkontrollplanet måste också finnas minst på v1.18 eller senare, men dina andra nodpooler kan finnas kvar i en äldre kubernetes-version.
Nedan uppgraderar vi först kontrollplanet och skapar sedan en ny nodpool med v1.18 som ska ta emot den nya nodavbildningen OS-versionen.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Konfiguration av containerkörning

En containerkörning är programvara som kör containrar och hanterar containeravbildningar på en nod. Körningen hjälper till att abstrahera bort sys-anrop eller operativsystemspecifika funktioner för att köra containrar i Linux eller Windows. AKS-kluster med Kubernetes version 1.19-nodpooler och större `containerd` användning som containerkörning. AKS-kluster som använder Kubernetes före v1.19 för nodpooler använder [Moby](https://mobyproject.org/) (överordnad Docker) som containerkörning.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) är en [OCI-kompatibel](https://opencontainers.org/) (Open Container Initiative) kärncontainerkörning som ger minsta möjliga uppsättning nödvändiga funktioner för att köra containrar och hantera avbildningar på en nod. Den [donerades](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) till Cloud Native Compute Foundation (RIVF) i mars 2017. Den aktuella Moby-versionen som AKS använder utnyttjar redan och är byggd ovanpå `containerd` , som visas ovan.

Med en -baserad nod och nodpooler, i stället för att prata med , pratar kubelet direkt med via plugin-programmet CRI (container runtime interface), vilket tar bort extra hopp i flödet jämfört med `containerd` `dockershim` `containerd` Docker CRI-implementeringen. Därför ser du bättre startfördröjning för poddar och mindre resursanvändning (CPU och minne).

Med hjälp `containerd` av för AKS-noder förbättras startfördröjningen för poddar och användningen av nodresurser med containerkörningen minskar. Dessa förbättringar aktiveras av den här nya arkitekturen där Kubelet kommunicerar direkt med via `containerd` CRI-plugin-programmet medan Kubelet i Moby/Docker-arkitekturen pratar med Docker-motorn och innan den når , vilket innebär att det finns extra hopp i `dockershim` `containerd` flödet.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` fungerar på varje GA-version av Kubernetes i AKS och i varje överordnad Kubernetes-version över v1.19, och stöder alla Kubernetes- och AKS-funktioner.

> [!IMPORTANT]
> Kluster med nodpooler som skapats på Kubernetes v1.19 eller högre som standard `containerd` för containerkörningen. Kluster med nodpooler på en Kubernetes-version som stöds som är mindre än 1.19 får för sin containerkörning, men kommer att uppdateras till när nodpoolens Kubernetes-version har uppdaterats till `Moby` `ContainerD` v1.19 eller senare. Du kan fortfarande använda `Moby` nodpooler och kluster på äldre versioner som stöds tills de inte stöds.
> 
> Vi rekommenderar starkt att du testar dina arbetsbelastningar på AKS-nodpooler med innan du använder kluster `containerD` på 1,19 eller högre.

### <a name="containerd-limitationsdifferences"></a>`Containerd` begränsningar/skillnader

* Om du `containerd` vill använda som containerkörning måste du använda AKS Ubuntu 18.04 som basoperativsystemavbildning.
* Även om docker-verktygsuppsättningen fortfarande finns på noderna använder Kubernetes `containerd` som containerkörning. Eftersom Moby/Docker inte hanterar de Kubernetes-skapade containrarna på noderna kan du därför inte visa eller interagera med dina containrar med hjälp av Docker-kommandon (till exempel ) eller `docker ps` Docker-API:et.
* För rekommenderar vi att du använder som en ersättnings-CLI i stället för Docker CLI för att felsöka poddar, containrar och containeravbildningar på `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) Kubernetes-noder (till exempel  `crictl ps` ). 
   * Den innehåller inte alla funktioner i Docker CLI. Den är endast avsedd för felsökning.
   * `crictl` erbjuder en mer kubernetes-vänlig vy över containrar, där begrepp som poddar osv. finns.
* `Containerd` uppsättningar loggning med hjälp av det standardiserade loggningsformatet (som skiljer sig från det du för närvarande `cri` får från Dockers json-drivrutin). Din loggningslösning måste ha stöd för `cri` loggningsformatet [(t.ex. Azure Monitor för containrar)](../azure-monitor/containers/container-insights-enable-new-cluster.md)
* Du kan inte längre komma åt Docker-motorn, `/var/run/docker.sock` eller använda Docker-in-Docker (DinD).
  * Om du för närvarande extraherar programloggar eller övervakningsdata från Docker Engine använder du något [som liknar Azure Monitor för containers i](../azure-monitor/containers/container-insights-enable-new-cluster.md) stället. Dessutom stöder inte AKS körning av out-of-band-kommandon på agentnoderna som kan orsaka instabilitet.
  * Även om du använder Moby/Docker rekommenderas inte att skapa avbildningar och direkt utnyttja Docker-motorn via metoderna ovan. Kubernetes är inte helt medveten om de förbrukade resurserna, [](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) och dessa metoder utgör till exempel ett stort antal problem som [beskrivs](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)här och här.
* Skapa avbildningar – Du kan fortsätta att använda ditt nuvarande Docker-byggarbetsflöde som vanligt, såvida du inte skapar avbildningar i ditt AKS-kluster. I det här fallet bör du överväga att byta till den rekommenderade metoden för att skapa avbildningar med [hjälp ACR-uppgifter](../container-registry/container-registry-quickstart-task-cli.md), eller ett säkrare klusteralternativ som [docker buildx.](https://github.com/docker/buildx)

## <a name="generation-2-virtual-machines"></a>Andra generationens virtuella datorer

Azure har stöd för virtuella datorer av andra generationen [(Gen2).](../virtual-machines/generation-2.md) Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1 (Gen1). Dessa funktioner omfattar ökat minne, Intel Software Guard Extensions (Intel SGX) och virtualiserat beständigt minne (vPMEM).

Virtuella datorer i generation 2 använder den nya UEFI-baserade startarkitekturen i stället för den BIOS-baserade arkitektur som används av virtuella datorer i generation 1.
Endast specifika SKU:er och storlekar stöder virtuella Gen2-datorer. Kontrollera listan [över storlekar som stöds för](../virtual-machines/generation-2.md#generation-2-vm-sizes)att se om din SKU stöder eller kräver Gen2.

Dessutom stöder inte alla VM-avbildningar Gen2, på virtuella AKS Gen2-datorer använder den nya [AKS Ubuntu 18.04-avbildningen](#os-configuration). Den här avbildningen stöder alla Gen2-SKU:er och storlekar.

## <a name="ephemeral-os"></a>Tillfälliga operativsystem

Som standard replikerar Azure automatiskt operativsystemdisken för en virtuell dator till Azure Storage för att undvika dataförlust om den virtuella datorn behöver flyttas till en annan värd. Men eftersom containrar inte är utformade för att ha kvar lokalt tillstånd erbjuder det här beteendet ett begränsat värde samtidigt som det ger vissa nackdelar, inklusive långsammare nodetablering och högre läs-/skrivfördröjning.

Tillfälliga OS-diskar lagras däremot bara på värddatorn, precis som en tillfällig disk. Detta ger kortare svarstider för läsning/skrivning, tillsammans med snabbare nodskalning och klusteruppgraderingar.

Precis som den temporära disken ingår en tillfällig OS-disk i priset för den virtuella datorn, så du behöver inte betala några extra lagringskostnader.

> [!IMPORTANT]
>När en användare inte uttryckligen begär hanterade diskar för operativsystemet, kommer AKS som standard att använda tillfälliga operativsystem om det är möjligt för en viss nodepool-konfiguration.

När du använder tillfälliga operativsystem måste OS-disken få plats i DEN virtuella datorns cacheminne. Storlekarna för VM-cache är tillgängliga i [Azure-dokumentationen](../virtual-machines/dv3-dsv3-series.md) inom parentes bredvid IO-dataflöde ("cachestorlek i GiB").

Med AKS standardstorlek för virtuella datorer Standard_DS2_v2 med standardstorleken på 100 GB som exempel stöder den här VM-storleken tillfälliga operativsystem, men har bara 86 GB cachestorlek. Den här konfigurationen använder som standard hanterade diskar om användaren inte uttryckligen anger det. Om en användare uttryckligen har begärt tillfälliga operativsystem får de ett valideringsfel.

Om en användare begär samma Standard_DS2_v2 med en OS-disk på 60 GB blir den här konfigurationen som standard ett ohemligt operativsystem: den begärda storleken på 60 GB är mindre än den maximala cachestorleken på 86 GB.

Med Standard_D8s_v3 med en OS-disk på 100 GB stöder den här VM-storleken tillfälliga operativsystem och har 200 GB cacheutrymme. Om en användare inte anger OS-disktypen får nodpoolen ett ohemligt operativsystem som standard. 

Tillfälliga operativsystem kräver minst version 2.15.0 av Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Använda tillfälliga operativsystem på nya kluster

Konfigurera klustret att använda tillfälliga OS-diskar när klustret skapas. Använd flaggan `--node-osdisk-type` för att ange Tillfälliga operativsystem som OS-disktyp för det nya klustret.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Om du vill skapa ett vanligt kluster med nätverkskopplade OS-diskar kan du göra det genom att ange `--node-osdisk-type=Managed` . Du kan också välja att lägga till fler tillfälliga OS-nodpooler enligt nedan.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Använda tillfälliga operativsystem på befintliga kluster
Konfigurera en ny nodpool att använda tillfälliga OS-diskar. Använd flaggan `--node-osdisk-type` för att ange som OS-disktyp som OS-disktyp för nodpoolen.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Med tillfälliga operativsystem kan du distribuera VIRTUELLA datorer och instansavbildningar upp till storleken på VM-cachen. I AKS-fallet använder standardnodens OS-diskkonfiguration 128 GB, vilket innebär att du behöver en VM-storlek som har en cache som är större än 128 GB. Standardvärdet Standard_DS2_v2 har en cachestorlek på 86 GB, vilket inte är tillräckligt stort. Den Standard_DS3_v2 har en cachestorlek på 172 GB, vilket är tillräckligt stort. Du kan också minska standardstorleken för OS-disken med hjälp av `--node-osdisk-size` . Den minsta storleken för AKS-avbildningar är 30 GB. 

Om du vill skapa nodpooler med nätverkskopplade OS-diskar kan du göra det genom att ange `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Namn på anpassad resursgrupp

När du distribuerar Azure Kubernetes Service kluster i Azure skapas en andra resursgrupp för arbetsnoderna. Som standard ger AKS nodresursgruppen namnet `MC_resourcegroupname_clustername_location` , men du kan också ange ditt eget namn.

Om du vill ange ett eget resursgruppsnamn installerar du Azure CLI-tillägget aks-preview version 0.3.2 eller senare. Använd parametern för kommandot med hjälp av Azure CLI `--node-resource-group` för att ange ett anpassat namn för `az aks create` resursgruppen. Om du använder en Azure Resource Manager för att distribuera ett AKS-kluster kan du definiera resursgruppens namn med hjälp av `nodeResourceGroup` egenskapen .

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Den sekundära resursgruppen skapas automatiskt av Azure-resursprovidern i din egen prenumeration. Du kan bara ange namnet på den anpassade resursgruppen när klustret skapas. 

När du arbetar med nodresursgruppen bör du komma ihåg att du inte kan:

- Ange en befintlig resursgrupp för nodresursgruppen.
- Ange en annan prenumeration för nodresursgruppen.
- Ändra namnet på nodresursgruppen när klustret har skapats.
- Ange namn för de hanterade resurserna i nodresursgruppen.
- Ändra eller ta bort Azure-skapade taggar för hanterade resurser i nodresursgruppen.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du uppgraderar nodavbildningarna](node-image-upgrade.md) i klustret.
- Se [Uppgradera ett Azure Kubernetes Service(AKS)-kluster](upgrade-cluster.md) för att lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- Läs mer om [ `containerd` och Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Se listan med vanliga [frågor om AKS för att](faq.md) få svar på några vanliga AKS-frågor.
- Läs mer om [tillfälliga OS-diskar.](../virtual-machines/ephemeral-os-disks.md)


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
