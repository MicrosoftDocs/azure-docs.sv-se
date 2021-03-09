---
title: Distribuera Kubernetes-tillstånds lösa program på Azure Stack Edge Pro GPU-enhet med kubectl | Microsoft Docs
description: Beskriver hur du skapar och hanterar en Kubernetes program distribution med hjälp av kubectl på en Microsoft Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 40065189f3c1dd52f462985aef344e9cbeb12f0b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522384"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Distribuera ett program med Kubernetes tillstånd via kubectl på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du distribuerar ett tillstånds lösa program med kubectl-kommandon i ett befintligt Kubernetes-kluster. Den här artikeln vägleder dig genom processen att skapa och konfigurera poddar i ditt tillstånds lösa program.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa ett Kubernetes-kluster och använda `kubectl` kommando rads verktyget, måste du se till att:

- Du har inloggnings uppgifter till en 1-nod Azure Stack Edge Pro-enhet.

- Windows PowerShell 5,0 eller senare är installerat på ett Windows-klientsystem för att få åtkomst till Azure Stack Edge Pro-enheten. Du kan också ha andra klienter med ett operativ system som stöds. Den här artikeln beskriver proceduren när du använder en Windows-klient. Om du vill hämta den senaste versionen av Windows PowerShell går du till [Installera Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).

- Compute är aktiverat på den Azure Stack Edge Pro-enheten. Om du vill aktivera beräkning går du till **beräknings** sidan i enhetens lokala användar gränssnitt. Välj sedan ett nätverks gränssnitt som du vill aktivera för beräkning. Välj **Aktivera**. Genom att aktivera beräknings resultatet skapas en virtuell växel på enheten i nätverks gränssnittet. Mer information finns i [Aktivera Compute Network på Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Din Azure Stack Edge Pro-enhet har en Kubernetes-kluster server som kör som är version v 1.9 eller senare. Mer information finns i [skapa och hantera ett Kubernetes-kluster på Microsoft Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Du har installerat `kubectl` .

## <a name="deploy-a-stateless-application"></a>Distribuera ett tillstånds lösa program

Innan vi börjar bör du ha:

1. Skapade ett Kubernetes-kluster.
2. Konfigurera ett namn område.
3. Associera en användare med namn området.
4. Användar konfigurationen sparades till `C:\Users\<username>\.kube` .
5. Installerat `kubectl` .

Nu kan du börja köra och hantera tillstånds lösa program distributioner på en Azure Stack Edge Pro-enhet. Innan du börjar använda `kubectl` måste du kontrol lera att du har rätt version av `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Kontrol lera att du har rätt version av kubectl och konfigurerat konfigurationen

För att kontrol lera versionen av `kubectl` :

1. Kontrol lera att versionen av `kubectl` är större än eller lika med 1,9:

   ```powershell
   kubectl version
   ```
    
   Ett exempel på utdata visas nedan:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   I det här fallet är klient versionen av kubectl v 1.15.2 och är kompatibel för att fortsätta.

2. Hämta en lista över de poddar som körs på ditt Kubernetes-kluster. En pod är en program behållare eller process som körs på ditt Kubernetes-kluster.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Ett exempel på kommando användning visas nedan:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   Utdata bör ange att inga resurser (poddar) hittas eftersom det inte finns några program som körs i klustret.

   Kommandot fyller i katalog strukturen för "C:\Users \\ &lt; username &gt; \\ . Kube" \" med konfigurationsfiler. Kommando rads verktyget kubectl använder dessa filer för att skapa och hantera tillstånds lösa program i ditt Kubernetes-kluster.

3. Kontrol lera manuellt katalog strukturen för "C:\Users \\ &lt; username &gt; \\ . Kube" \" för att verifiera att *kubectl* har fyllt i den med följande undermappar:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Om du vill visa en lista över alla kubectl-kommandon skriver du `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Skapa ett tillstånds lösa program med hjälp av en distribution

Nu när du har kontrollerat att kubectl kommando rads version är korrekt och du har de nödvändiga konfigurationsfilerna kan du skapa en tillstånds lös program distribution.

En pod är den grundläggande körnings enheten för ett Kubernetes-program, den minsta och enklaste enheten i Kubernetes objekt modell som du skapar eller distribuerar. En POD kapslar också in lagrings resurser, en unik nätverks-IP och alternativ som styr hur behållarna ska köras.

Typen av tillstånds lösa program som du skapar är en nginx webb Server distribution.

Alla kubectl-kommandon som du använder för att skapa och hantera tillstånds lösa program distributioner måste ange det namn område som är associerat med konfigurationen. Du skapade namn området när du anslöt till klustret på den Azure Stack Edge Pro-enheten i självstudien [skapa och hantera ett Kubernetes-kluster på Microsoft Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md) med `New-HcsKubernetesNamespace` .

Använd för att ange namn området i ett kubectl-kommando `kubectl <command> -n <namespace-string>` .

Följ de här stegen för att skapa en nginx-distribution:

1. Tillämpa ett tillstånds löst program genom att skapa ett Kubernetes-distributions objekt:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   I det här exemplet är sökvägen till program YAML-filen en extern källa.

   Här är ett exempel på hur du kan använda kommandot och dess utdata:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Alternativt kan du spara följande markdown på din lokala dator och ersätta sökvägen och fil namnet i parametern *-f* . Till exempel "C:\Kubernetes\deployment.yaml". Konfigurationen för program distributionen skulle vara:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Det här kommandot skapar en standard nginx-distribution som har två poddar för att köra programmet.

2. Hämta beskrivningen av den Kubernetes-nginx som du skapade:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Exempel på användning av kommandot, med utdata, visas nedan:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   För *repliker* -inställningen visas:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   Inställningen *repliker* visar att distributions specifikationen kräver två poddar och att dessa poddar har skapats och uppdaterats och är redo att användas.

   > [!NOTE]
   > En replik uppsättning ersätter poddar som tas bort eller avslutas av någon anledning, t. ex. om det skulle uppstå ett diskfel eller en avbrotts enhets uppgradering. Därför rekommenderar vi att du använder en replik uppsättning även om programmet bara kräver en enda pod.

3. Visa en lista över poddar i distributionen:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Exempel på användning av kommandot, med utdata, visas nedan:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   Utdata kontrollerar att vi har två poddar med unika namn som vi kan referera till med hjälp av kubectl.

4. Så här visar du information om en enskild Pod i distributionen:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Exempel på användning av kommandot, med utdata, visas nedan:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Skala om program distributionen genom att öka antalet repliker

Varje pod är avsedd att köra en enda instans av ett visst program. Om du vill skala programmet vågrätt för att köra flera instanser kan du öka antalet poddar till ett för varje instans. I Kubernetes kallas detta för replikering.
Du kan öka antalet poddar i program distributionen genom att använda en ny YAML-fil. YAML-filen ändrar repliker-inställningen till 4, vilket ökar antalet poddar i distributionen till fyra poddar. Så här ökar du antalet poddar från 2 till 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Alternativt kan du spara följande markdown på din lokala dator och ersätta sökvägen och fil namnet för parametern *-f* för `kubectl apply` . Till exempel "C:\Kubernetes\deployment-scale.yaml". Konfigurationen för programmets distributions skala skulle vara:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Så här kontrollerar du att distributionen har fyra poddar:

```powershell
kubectl get pods -l app=nginx
```

Exempel på utdata för en omskalning av distribution från två till fyra poddar visas nedan:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Som du kan se från utdata har du nu fyra poddar i distributionen som kan köra ditt program.

### <a name="delete-a-deployment"></a>Ta bort en distribution

Om du vill ta bort distributionen, inklusive alla poddar, måste du köra `kubectl delete deployment` Ange namnet på distributionen *nginx-Deployment* och namn områdets namn. Ta bort distributionen:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Ett exempel på kommando användning, med utdata, visas nedan:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Nästa steg

[Översikt över Kubernetes](azure-stack-edge-gpu-kubernetes-overview.md)