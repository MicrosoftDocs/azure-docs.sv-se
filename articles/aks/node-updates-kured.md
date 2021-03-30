---
title: Hantera omstart av Linux-nod med kured
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du uppdaterar Linux-noder och startar om dem automatiskt med kured i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88164248"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)

För att skydda dina kluster tillämpas säkerhets uppdateringar automatiskt på Linux-noder i AKS. Dessa uppdateringar omfattar säkerhets korrigeringar för operativ system eller kernel-uppdateringar. Vissa av dessa uppdateringar kräver att en nod startas om för att slutföra processen. AKS startar inte om dessa Linux-noder automatiskt för att slutföra uppdaterings processen.

Processen för att hålla Windows Server-noderna aktuella är lite annorlunda. Windows Server-noder får inte dagliga uppdateringar. I stället utför du en AKS-uppgradering som distribuerar nya noder med den senaste Server avbildningen och korrigeringarna för bas fönstret. Information om AKS-kluster som använder Windows Server-noder finns i [uppgradera en Node-pool i AKS][nodepool-upgrade].

Den här artikeln visar hur du använder [kured (KUbernetes REboot daemon)][kured] för att se om det finns Linux-noder som kräver en omstart, och som automatiskt hanterar omschemaläggningen av pågående poddar och omstart av en nod.

> [!NOTE]
> `Kured` är ett projekt med öppen källkod från Weaveworks. Stöd för det här projektet i AKS tillhandahålls på bästa möjliga sätt. Ytterligare support finns i #weave-communityns slack-kanal.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Förstå uppdaterings upplevelsen av AKS-noden

I ett AKS-kluster körs dina Kubernetes-noder som virtuella Azure-datorer (VM). Dessa Linux-baserade virtuella datorer använder en Ubuntu avbildning, där operativ systemet har kon figurer ATS för automatisk sökning efter uppdateringar varje natt. Om säkerhets-eller kernel-uppdateringar är tillgängliga laddas de ned och installeras automatiskt.

![Uppdatering och omstart av AKS-nod med kured](media/node-updates-kured/node-reboot-process.png)

Vissa säkerhets uppdateringar, till exempel kernel-uppdateringar, kräver en omstart av en nod för att slutföra processen. En Linux-nod som kräver en omstart skapar en fil med namnet */var/run/reboot-required*. Den här omstarts processen sker inte automatiskt.

Du kan använda dina egna arbets flöden och processer för att hantera omstarter av noder eller använda `kured` för att dirigera processen. Med `kured` distribueras en [DaemonSet][DaemonSet] som kör en POD på varje Linux-nod i klustret. Dessa poddar i DaemonSet tittar efter förekomsten av */var/run/reboot-required* -filen och startar sedan en process för att starta om noderna.

### <a name="node-upgrades"></a>Nods uppgraderingar

Det finns en ytterligare process i AKS som gör det möjligt att *Uppgradera* ett kluster. En uppgradering är vanligt vis att flytta till en senare version av Kubernetes, inte bara att använda säkerhets uppdateringar för noder. En AKS-uppgradering utför följande åtgärder:

* En ny nod distribueras med de senaste säkerhets uppdateringarna och Kubernetes-versionen som tillämpas.
* En gammal nod är avspärrade och töms.
* Poddar är schemalagda för den nya noden.
* Den gamla noden tas bort.

Du kan inte ha kvar samma Kubernetes-version under en uppgraderings händelse. Du måste ange en nyare version av Kubernetes. Om du vill uppgradera till den senaste versionen av Kubernetes kan du [uppgradera ditt AKS-kluster][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Distribuera kured i ett AKS-kluster

Om du vill distribuera `kured` DaemonSet installerar du följande officiella Kured-Helm-diagram. Detta skapar en roll och kluster roll, bindningar och ett tjänst konto och distribuerar sedan DaemonSet med hjälp av `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Du kan också konfigurera ytterligare parametrar för `kured` , till exempel integration med Prometheus eller slack. Mer information om ytterligare konfigurations parametrar finns i [Kured Helm-diagrammet][kured-install].

## <a name="update-cluster-nodes"></a>Uppdatera klusternoder

Som standard söker Linux-noder i AKS efter uppdateringar varje kväll. Om du inte vill vänta kan du utföra en uppdatering manuellt för att kontrol lera att den `kured` fungerar korrekt. Börja med att följa stegen för [SSH till en av dina AKS-noder][aks-ssh]. När du har en SSH-anslutning till Linux-noden söker du efter uppdateringar och tillämpar dem på följande sätt:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Om uppdateringar tillämpades som kräver en omstart av en nod, skrivs en fil till */var/run/reboot-required*. `Kured` söker efter noder som kräver en omstart var 60 minut som standard.

## <a name="monitor-and-review-reboot-process"></a>Övervaka och granska omstart

När en av replikerna i DaemonSet har identifierat att en omstart av en nod krävs, placeras ett lås på noden via Kubernetes-API: et. Det här låset förhindrar att ytterligare poddar schemaläggs på noden. Låset indikerar också att endast en nod ska startas om i taget. När noden avspärrade av, töms poddar från noden och noden startas om.

Du kan övervaka status för noderna med hjälp av kommandot [kubectl get Nodes][kubectl-get-nodes] . Följande exempel på utdata visar en nod med statusen *SchedulingDisabled* när noden förbereds för omstart:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

När uppdaterings processen är klar kan du Visa status för noderna med hjälp av kommandot [kubectl get Nodes][kubectl-get-nodes] med `--output wide` parametern. Med den här ytterligare utmatningen kan du se en skillnad i *kernel-versionen* av de underliggande noderna, som visas i följande exempel på utdata. *AKS-nodepool1-28993262-0* uppdaterades i ett föregående steg och visar kernel-version *4.15.0-1039-Azure*. Noden *AKS-nodepool1-28993262-1* som inte har uppdaterats visar kernel *-version 4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller information om hur du använder `kured` för att starta om Linux-noder automatiskt som en del av säkerhets uppdaterings processen. Om du vill uppgradera till den senaste versionen av Kubernetes kan du [uppgradera ditt AKS-kluster][aks-upgrade].

Information om AKS-kluster som använder Windows Server-noder finns i [uppgradera en Node-pool i AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
