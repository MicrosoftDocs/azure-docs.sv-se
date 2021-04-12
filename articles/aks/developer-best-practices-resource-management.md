---
title: Metod tips för resurs hantering
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för programutvecklare för resurs hantering i Azure Kubernetes service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105280"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Metod tips för programutvecklare för att hantera resurser i Azure Kubernetes service (AKS)

När du utvecklar och kör program i Azure Kubernetes service (AKS) finns det några viktiga områden att tänka på. Hur du hanterar dina program distributioner kan påverka slutanvändarens upplevelse av tjänster som du tillhandahåller. För att lyckas bör du tänka på några metod tips som du kan följa när du utvecklar och kör program i AKS.

Den här artikeln fokuserar på att köra ditt kluster och dina arbets belastningar från ett program utvecklings perspektiv. Information om rekommenderade metoder finns i [kluster operatörer metod tips för isolering och resurs hantering i Azure Kubernetes service (AKS)][operator-best-practices-isolation]. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Pod resurs begär Anden och begränsningar.
> * Sätt att utveckla och distribuera program med Bridge till Kubernetes och Visual Studio Code.
> * Så här använder du `kube-advisor` verktyget för att söka efter problem med distributioner.

## <a name="define-pod-resource-requests-and-limits"></a>Definiera resurs begär Anden och begränsningar för Pod

> **Vägledning och metodtips**
> 
> Ange Pod-begäranden och begränsningar för alla poddar i dina YAML-manifest. Om AKS-klustret använder *resurs kvoter* och du inte definierar dessa värden kan distributionen nekas.

Använd Pod-begäranden och-gränser för att hantera beräknings resurserna i ett AKS-kluster. Pod-begäranden och-gränser informerar den Kubernetes Scheduler som beräknar resurser för att tilldela en pod.

### <a name="pod-cpumemory-requests"></a>Pod CPU/minnes begär Anden
*Pod-begäranden* definierar en mängd CPU och minne som Pod behöver regelbundet.

I dina Pod-specifikationer är det **bästa praxis och mycket viktigt** att definiera dessa förfrågningar och gränser baserat på ovanstående information. Om du inte tar med dessa värden kan Kubernetes Scheduler inte ta hänsyn till de resurser som dina program behöver för att få hjälp med att schemalägga beslut.

Övervaka programmets prestanda för att justera Pod-begärandena. 
* Om du funktionen underskattar Pod-begäranden kan ditt program få försämrade prestanda på grund av överschemaläggning av en nod. 
* Om begär Anden är överskattat kan ditt program ha fått bättre svårigheter att komma åt schemat.

### <a name="pod-cpumemory-limits"></a>Pod-CPU/minnes gränser * * 
*Pod-gränser* anger den maximala mängd processor och minne som en POD kan använda. 

* *Minnes gränser* definierar vilka poddar som ska avlivas när noderna är instabila på grund av otillräckliga resurser. Utan rätt gränser kommer poddar att avlivas tills resurs trycket lyfts upp. 
* Även om en POD kan överskrida *processor gränsen* med jämna mellanrum kommer Pod inte att avlivas för att överskrida processor gränsen. 

Pod-gränser definierar när en pod har förlorat kontrollen av resursförbrukning. När den överskrider gränsen markeras Pod för avlivning. Det här beteendet upprätthåller en nods hälsa och minimerar påverkan på poddar som delar noden. Om du inte anger en POD begränsas den till det högsta tillgängliga värdet på en specifik nod.

Undvik att ange en POD-gräns som är högre än dina noder kan stödja. Varje AKS-nod reserverar en angiven mängd processor och minne för kärn komponenterna i Kubernetes. Ditt program kan försöka förbruka för många resurser på noden för att andra poddar ska kunna köras.

Övervaka programmets prestanda vid olika tidpunkter under dagen eller veckan. Fastställ tider för högsta efter frågan och justera Pod-gränserna till de resurser som krävs för att uppfylla de maximala behoven.

> [!IMPORTANT]
>
> I pod-specifikationerna definierar du dessa begär Anden och gränser baserat på ovanstående information. Om du inte tar med dessa värden kan Kubernetes Scheduler inte redovisning för resurser som dina program behöver för att kunna schemalägga beslut.

Om Scheduler placerar en POD på en nod med otillräckliga resurser kommer program prestanda försämras. Kluster administratörer **måste** ange *resurs kvoter* i ett namn område som kräver att du anger resurs begär Anden och gränser. Mer information finns i [resurs kvoter på AKS-kluster][resource-quotas].

När du definierar en processor förfrågan eller en gräns mäts värdet i CPU-enheter. 
* *1,0* CPU motsvarar en underliggande virtuell CPU-kärna på noden. 
    * Samma mått används för GPU: er.
* Du kan definiera bråktal som mäts i millicores. Till exempel är *100 miljoner* *0,1* av en underliggande vCPU-kärna.

I följande grundläggande exempel för en enda NGINX-Pod begär Pod *100 miljoner* CPU-tid och *128Mi* av minne. Resurs gränserna för pod är inställda på *250m* CPU-och *256Mi* -minne:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Mer information om resurs mått och tilldelningar finns i [hantera beräknings resurser för behållare][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Utveckla och felsöka program mot ett AKS-kluster

> **Vägledning och metodtips** 
>
> Utvecklings team bör distribuera och felsöka mot ett AKS-kluster med hjälp av Bridge till Kubernetes.

Med Bridge till Kubernetes kan du utveckla, felsöka och testa program direkt mot ett AKS-kluster. Utvecklare i en grupp samarbetar för att bygga och testa under hela programmets livs cykel. Du kan fortsätta att använda befintliga verktyg som Visual Studio eller Visual Studio Code med bryggan till Kubernetes-tillägg. 

Genom att använda en integrerad utvecklings-och test process med Bridge för att Kubernetes minskas behovet av lokala test miljöer som [minikube][minikube]. I stället kan du utveckla och testa mot ett AKS-kluster, även skyddade och isolerade kluster. 

> [!NOTE]
> Bridge till Kubernetes är avsett att användas med program som körs på Linux-poddar och-noder.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Använd tillägget Visual Studio Code (VS Code) för Kubernetes

> **Vägledning och metodtips** 
>
> Installera och Använd VS Code-tillägget för Kubernetes när du skriver YAML-manifest. Du kan också använda tillägget för integrerad distributions lösning, vilket kan hjälpa program ägare som sällan interagerar med AKS-klustret.

[Visual Studio Code-tillägget för Kubernetes][vscode-kubernetes] hjälper dig att utveckla och distribuera program till AKS. Tillägget tillhandahåller:
* IntelliSense för Kubernetes-resurser, Helm-diagram och mallar. 
* Bläddra, distribuera och redigera funktioner för Kubernetes-resurser i VS Code. 
* En IntelliSense-kontroll för resurs begär Anden eller gränser som anges i pod-specifikationerna:

    ![VS Code-tillägg för Kubernetes varning om saknade minnes gränser](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Sök regelbundet efter program problem med Kube-Advisor

> **Vägledning och metodtips** 
> 
> Kör regelbundet den senaste versionen av `kube-advisor` verktyget med öppen källkod för att identifiera problem i klustret. Kör `kube-advisor` innan du tillämpar resurs kvoter på ett befintligt AKS-kluster för att hitta poddar som inte har några resurs begär Anden och gränser definierade.

Verktyget [Kube-Advisor][kube-advisor] är ett associerat AKS projekt med öppen källkod som söker igenom ett Kubernetes-kluster och rapporterar om identifierade problem. En bra kontroll är att identifiera poddar utan resurs begär Anden och begränsningar på plats.

`kube-advisor`Verktyget kan rapportera om resurs begär Anden och gränser som saknas i PodSpecs för Windows-och Linux-program, men i `kube-advisor` sig måste de schemaläggas på en Linux-pod. Använd en [Node-selektor][k8s-node-selector] i pod-konfigurationen för att schemalägga en pod som ska köras på en adresspool med ett särskilt operativ system.

I ett AKS-kluster som är värd för många utvecklings team och program, hittar du det enklare att spåra poddar med hjälp av resurs begär Anden och begränsningar. Vi rekommenderar `kube-advisor` att du regelbundet kör dina AKS-kluster.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du kör ditt kluster och dina arbets belastningar från ett kluster operatörs perspektiv. Information om rekommenderade metoder finns i [kluster operatörer metod tips för isolering och resurs hantering i Azure Kubernetes service (AKS)][operator-best-practices-isolation].

Information om hur du implementerar några av dessa metod tips finns i följande artiklar:

* [Utveckla med Bridge till Kubernetes][btk]
* [Sök efter problem med Kube-Advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
