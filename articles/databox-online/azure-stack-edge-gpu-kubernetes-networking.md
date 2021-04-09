---
title: Förstå Kubernetes-nätverk på Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur Kubernetes-nätverk fungerar på en Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560258"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes nätverk i din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

På din Azure Stack Edge Pro-enhet skapas ett Kubernetes-kluster när du konfigurerar Compute-rollen. När Kubernetes-klustret har skapats kan program i behållare distribueras i Kubernetes-klustret i poddar. Det finns olika sätt att använda nätverk för poddar i ditt Kubernetes-kluster. 

Den här artikeln beskriver nätverk i ett Kubernetes-kluster i allmänhet och specifikt inom ramen för din Azure Stack Edge Pro-enhet. 

## <a name="networking-requirements"></a>Nätverkskrav

Här är ett exempel på en typisk 2-nivå-app som distribueras till Kubernetes-klustret.

- Appen har en front webb server och ett databas program i Server delen. 
- Varje Pod tilldelas en IP-adress men dessa IP-adresser kan ändras vid omstart och redundansväxlingen av pod. 
- Varje app består av flera poddar och det ska finnas belastnings utjämning av trafiken över alla Pod-repliker. 

![Kubernetes nätverks krav](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

Scenariot ovan resulterar i följande nätverks krav:

 - Det finns ett behov av att det externa programmet kan nås av en program användare utanför Kubernetes-klustret via ett namn eller en IP-adress. 
 - Programmen i Kubernetes-klustret, till exempel klient delen och Server delens poddar, bör kunna kommunicera med varandra.

För att lösa båda kraven ovan introducerar vi en Kubernetes-tjänst. 


## <a name="networking-services"></a>Nätverkstjänster

Det finns två typer av Kubernetes-tjänster: 

- **Kluster-IP-tjänst** – Tänk på detta som att tillhandahålla en konstant slut punkt för programmet poddar. Alla Pod som är kopplade till dessa tjänster kan inte nås från utsidan av Kubernetes-klustret. Den IP-adress som används med dessa tjänster kommer från adress utrymmet i det privata nätverket. 
- **Belastnings Utjämnings-IP** -som kluster-IP-tjänsten, men den associerade IP-adressen kommer från det externa nätverket och kan nås från utanför Kubernetes-klustret.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Kubernetes nätverk på Azure Stack Edge Pro

Calico, Metallb och Core DNS är alla komponenter som är installerade för nätverk på din Azure Stack Edge Pro. 

- **Calico** tilldelar en IP-adress från ett privat IP-adressintervall till varje Pod och konfigurerar nätverk för dessa poddar så att Pod på en nod kan kommunicera med Pod på en annan nod. 
- **Metallb** körs på en i-kluster-Pod och tilldelar IP-adresser till tjänster av typen Load Balancer. IP-adresser för belastnings utjämning väljs från tjänstens IP-intervall som tillhandahålls via det lokala användar gränssnittet. 
- **Core DNS** – det här tillägget konfigurerar DNS-post mappnings tjänst namn till kluster-IP-adress.

De IP-adresser som används för Kubernetes-noder och de externa tjänsterna tillhandahålls via sidan **Compute Network** i enhetens lokala användar gränssnitt.

![Kubernetes IP-tilldelning i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP-tilldelningen är för:

- **Kubernetes Node IP-adresser**: detta IP-adressintervall används för Kubernetes-huvud-och arbetsnoder. Dessa IP-adresser används när Kubernetes-noder kommunicerar med varandra.

- **Kubernetes external service** IP-adresser: detta IP-adressintervall används för externa tjänster (kallas även Load Balancer tjänster) som exponeras utanför Kubernetes-klustret.  


## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera Kubernetes-nätverk på Azure Stack Edge Pro, se:

- [Exponera ett tillstånds löst program externt i Azure Stack Edge Pro via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Exponera ett tillstånds löst program externt i Azure Stack Edge Pro via kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).