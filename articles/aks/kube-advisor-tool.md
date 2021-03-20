---
title: Kontrol lera distributioner för bästa praxis
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du söker efter implementering av bästa praxis i dina distributioner i Azure Kubernetes service med Kube-Advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 7730146f30487eb5d20f0d3138e9e5ba799daa99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94681533"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Söka efter Kubernetes metodtips i ditt kluster

Det finns flera metod tips som du bör följa på dina Kubernetes-distributioner för att säkerställa bästa prestanda och återhämtning för dina program. Du kan använda verktyget Kube för att söka efter distributioner som inte följer dessa förslag.

## <a name="about-kube-advisor"></a>Om Kube-Advisor

[Verktyget Kube-Advisor][kube-advisor-github] är en behållare som är utformad för att köras i klustret. Den frågar Kubernetes-API-servern om information om dina distributioner och returnerar en uppsättning föreslagna förbättringar.

Kube-verktyget kan rapportera om resurs begär Anden och gränser som saknas i PodSpecs för Windows-program och Linux-program, men Kube-Advisor-verktyget måste vara schemalagt för en Linux-pod. Du kan schemalägga en POD så att den körs på en adresspool med ett särskilt operativ system med hjälp av en [Node-selektor][k8s-node-selector] i pod-konfigurationen.

> [!NOTE]
> Verktyget Kube-Advisor stöds av Microsoft på bästa möjliga sätt. Problem och förslag bör arkiveras på GitHub.

## <a name="running-kube-advisor"></a>Köra Kube-Advisor

För att köra verktyget på ett kluster som är konfigurerat för [Kubernetes-rollbaserad åtkomst kontroll (KUBERNETES RBAC)](./azure-ad-integration-cli.md), med hjälp av följande kommandon. Det första kommandot skapar ett Kubernetes-tjänstkonto. Det andra kommandot kör verktyget i en POD med det tjänst kontot och konfigurerar Pod för borttagning när det har avslut ATS. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Om du inte använder Kubernetes RBAC kan du köra kommandot på följande sätt:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Inom några sekunder bör du se en tabell som beskriver potentiella förbättringar av dina distributioner.

![Kube-Advisor-utdata](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Utförda kontroller

Verktyget validerar flera Kubernetes metod tips, var och en med sina egna föreslagna åtgärder.

### <a name="resource-requests-and-limits"></a>Resursbegäranden och -gränser

Kubernetes stöder definiering [av resurs begär Anden och begränsningar för Pod-specifikationer][kube-cpumem]. Begäran definierar den lägsta CPU och det minne som krävs för att köra behållaren. Gränsen definierar den högsta processor och det minne som ska tillåtas.

Som standard anges inga förfrågningar eller gränser för Pod-specifikationer. Detta kan leda till att noder blir överschemade och att behållare är har. Kube-Advisor visar poddar utan begär Anden och begränsningar.

## <a name="cleaning-up"></a>Rensning

Om klustret har Kubernetes RBAC aktiverat kan du rensa `ClusterRoleBinding` efter att du har kört verktyget med följande kommando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Om du kör verktyget mot ett kluster som inte är Kubernetes RBAC-aktiverat krävs ingen rensning.

## <a name="next-steps"></a>Nästa steg

- [Felsöka problem med Azure Kubernetes-tjänsten](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
