---
title: Distribuera PHP- `Guestbook` app på Arc-aktiverade Kubernetes på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du distribuerar ett program för PHP- `Guestbook` tillstånd med Redis med GitOps på ett Arc-aktiverat Kubernetes-kluster för din Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 920f7912a1289fe92618d893b94943784e4a9a3a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520735"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Distribuera ett `Guestbook` program med php-tillstånd utan Redis på Arc-aktiverade Kubernetes-kluster på Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln visar hur du skapar och distribuerar ett enkelt webb program med flera nivåer med hjälp av Kubernetes och Azure Arc. Det här exemplet består av följande komponenter:

- En Redis-Master med en instans som lagrar `guestbook` poster
- Flera replikerade Redis-instanser för att betjäna läsningar
- Flera webb klient dels instanser

Distributionen görs med hjälp av GitOps på Kubernetes-klustret på din Azure Stack Edge Pro-enhet. 

Den här proceduren är avsedd för personer som har granskat [Kubernetes-arbetsbelastningar på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-workload-management.md) och som är bekanta med begreppen [Vad är Azure Arc Enabled Kubernetes (för hands version)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan distribuera det tillstånds lösa programmet måste du kontrol lera att du har slutfört följande krav på enheten och klienten som du ska använda för att få åtkomst till enheten:

> [!NOTE]
> Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

### <a name="for-device"></a>För enheten

1. Du har inloggnings uppgifter till en 1-nod Azure Stack Edge Pro-enhet.
    1. Enheten är aktive rad. Se [Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
    1. Enheten har den beräknings roll som kon figurer ATS via Azure Portal och har ett Kubernetes-kluster. Se [Konfigurera Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Du har aktiverat Azure-bågen på det befintliga Kubernetes-klustret på enheten och du har en motsvarande Azure Arc-resurs i Azure Portal. Detaljerade anvisningar finns i [Aktivera Azure Arc på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>För klient åtkomst till enheten

1. Du har ett Windows-klientsystem som ska användas för att få åtkomst till Azure Stack Edge Pro-enheten.
  
    - Klienten kör Windows PowerShell 5,0 eller senare. Om du vill hämta den senaste versionen av Windows PowerShell går du till [Installera Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Du kan också ha andra klienter med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Den här artikeln beskriver proceduren när du använder en Windows-klient. 
    
1. Du har slutfört proceduren som beskrivs i [komma åt Kubernetes-klustret på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md). Du har:
    
    - Installerat `kubectl` på klienten. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Kontrol lera att `kubectl` klient versionen inte är mer än en version från den Kubernetes huvud version som körs på din Azure Stack Edge Pro-enhet. 
      - Används `kubectl version` för att kontrol lera vilken version av kubectl som körs på klienten. Anteckna den fullständiga versionen.
      - I det lokala användar gränssnittet för din Azure Stack Edge Pro-enhet går du till **Översikt** och noterar Kubernetes-program varu numret. 
      - Kontrol lera att dessa två versioner är kompatibla med den mappning som finns i den Kubernetes-version som stöds. <!--insert link-->

1. Du har en [GitOps-konfiguration som du kan använda för att köra en Azure Arc-distribution](https://github.com/kagoyal/dbehaikudemo). I det här exemplet ska du använda följande `yaml` filer för att distribuera på din Azure Stack Edge Pro-enhet.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Distribuera konfigurationen

Följ dessa steg om du vill konfigurera Azure Arc-resursen för att distribuera en GitOps-konfiguration via Azure Portal: 

1. I Azure Portal går du till den Azure Arc-resurs som du har skapat när du aktiverade Azure-bågen i Kubernetes-klustret på enheten. 

    ![Gå till Azure Arc-resurs](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Gå till **konfigurationer** och välj **+ Lägg till konfiguration**.

    ![Skärm bild som visar Azure-bågen aktiverat Kubernetes-kluster med Lägg till konfiguration valt.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. I **Lägg till konfiguration** anger du lämpliga värden för fälten och väljer sedan **Använd**.

    |Parameter  |Beskrivning |
    |---------|---------|
    |Konfigurations namn     | Namn på konfigurations resursen.        |
    |Namn på operatörs instans     |Instans namn för operatorn för att identifiera en speciell konfiguration. Name är en sträng med högst 253 tecken som måste vara gemener, alfanumeriskt, bindestreck och punkt.         |
    |Namn område för operatör     | Ange till **demotestguestbook** för att matcha det namn område som anges i distributionen `yaml` . <br> Fältet definierar namn området där operatorn är installerad. Name är en sträng med högst 253 tecken som måste vara gemener, alfanumeriskt, bindestreck och punkt.         |
    |URL för databas     |<br>Sökväg till git-lagringsplatsen i `http://github.com/username/repo` eller `git://github.com/username/repo` format där din GitOps-konfiguration finns.         |
    |Operator omfång     | Välj **namn område**. <br>Den här parametern definierar omfattningen där operatorn är installerad. Välj namn område för att installera operatören i namn området som anges i yaml-filerna för distribution.       |
    |Operatortyp     | Lämna kvar som standard. <br>Den här parametern anger typen av operator – som standard, anges som flöde.        |
    |Operator parametrar     | Lämna tomt. <br>Den här parametern innehåller parametrar som ska skickas till flödes operatorn.        |
    |Helm     | Ange att den här parametern är **inaktive rad**. <br>Aktivera det här alternativet om du ska göra diagrambaserade distributioner.        |


    ![Lägga till konfiguration](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Konfigurations distributionen startar och **operatörs statusen** visas som **väntande**. 

    ![Skärm bild som visar Azure-bågen aktiverat Kubernetes-kluster i ett väntande tillstånd när det uppdateras.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Distributionen tar några minuter. När distributionen är klar visas **operatörs status** som **installerad**.

    ![Skärm bild som visar Azure-bågen aktiverat Kubernetes-kluster i ett installations läge.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Verifiera distributionen

Distributionen via GitOps-konfigurationen skapar ett `demotestguestbook` namn område som anges i de distributions `yaml` filer som finns i git-lagrings platsen.

1. När du har tillämpat GitOps-konfigurationen [ansluter du till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Kör följande kommando för att visa en lista över de poddar som körs i `demotestguestbook` namn området som motsvarar distributionen.

    `kubectl get pods -n <your-namespace>`
  
    Här är ett exempel på utdata.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. I det här exemplet distribuerades klient dels tjänsten som typ: LoadBalancer. Du måste hitta IP-adressen för den här tjänsten för att kunna visa `guestbook` . Kör följande kommando.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Klient dels tjänsten med `type:LoadBalancer` har en extern IP-adress. Den här IP-adressen är från det IP-adressintervall som du angav för externa tjänster när du konfigurerar inställningarna för beräknings nätverket på enheten. Använd den här IP-adressen för att visa `guestbook` på URL: en: `https://<external-IP-address>` .

    ![Visa gäst bok](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Ta bort distribution

Om du vill ta bort distributionen kan du ta bort konfigurationen från Azure Portal. Om du tar bort konfigurationen raderas de objekt som har skapats, inklusive distributioner och tjänster.

1. I Azure Portal går du till Azure Arc-resursens > konfigurationer. 
1. Leta upp den konfiguration som du vill ta bort. Välj... anropa snabb menyn och välj **ta bort**.
    ![Ta bort konfiguration](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Det kan ta flera minuter innan konfigurationen tas bort.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder Kubernetes-instrumentpanelen för att övervaka distributioner på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)