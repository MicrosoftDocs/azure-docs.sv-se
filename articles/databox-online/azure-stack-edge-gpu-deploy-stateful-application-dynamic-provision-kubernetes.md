---
title: Använd kubectl för att distribuera Kubernetes-tillstånds känslig app via en dynamiskt etablerad resurs på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du skapar och hanterar en Kubernetes tillstånds känslig program distribution via en dynamiskt etablerad resurs med hjälp av kubectl på en Microsoft Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520701"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Använda kubectl för att köra ett Kubernetes tillstånds känsligt program med StorageClass på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln visar hur du distribuerar ett tillstånds känsligt program med en instans i Kubernetes med hjälp av en StorageClass för att dynamiskt tillhandahålla lagring och en distribution. Distributionen använder `kubectl` kommandon i ett befintligt Kubernetes-kluster och distribuerar MySQL-programmet. 

Den här proceduren är avsedd för de som har granskat [Kubernetes-lagringen på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-storage.md) och som är bekanta med begreppen [Kubernetes-lagring](https://kubernetes.io/docs/concepts/storage/).


## <a name="prerequisites"></a>Förutsättningar

Innan du kan distribuera det tillstånds känsliga programmet måste du uppfylla följande krav på enheten och klienten som du kommer att använda för att få åtkomst till enheten:

### <a name="for-device"></a>För enheten

- Du har inloggnings uppgifter till en 1-nod Azure Stack Edge Pro-enhet.
    - Enheten är aktive rad. Se [Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
    - Enheten har den beräknings roll som kon figurer ATS via Azure Portal och har ett Kubernetes-kluster. Se [Konfigurera Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>För klient åtkomst till enheten

- Du har ett Windows-klientsystem som ska användas för att få åtkomst till Azure Stack Edge Pro-enheten.
    - Klienten kör Windows PowerShell 5,0 eller senare. Om du vill hämta den senaste versionen av Windows PowerShell går du till [Installera Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Du kan också ha andra klienter med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Den här artikeln beskriver proceduren när du använder en Windows-klient. 
    
    - Du har slutfört proceduren som beskrivs i [komma åt Kubernetes-klustret på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md). Du har:
      - Ett `userns1` namn område har skapats via `New-HcsKubernetesNamespace` kommandot. 
      - En användare har skapats `user1` via `New-HcsKubernetesUser` kommandot. 
      - Beviljas `user1` åtkomst till `userns1` via `Grant-HcsKubernetesNamespaceAccess` kommandot.       
      - Installerat `kubectl` på klienten och sparade `kubeconfig` filen med användar konfigurationen till C: \\ Users \\ &lt; username &gt; \\ . Kube. 
    
    - Kontrol lera att `kubectl` klient versionen inte är mer än en version från den Kubernetes huvud version som körs på din Azure Stack Edge Pro-enhet. 
        - Används `kubectl version` för att kontrol lera vilken version av kubectl som körs på klienten. Anteckna den fullständiga versionen.
        - I det lokala användar gränssnittet för din Azure Stack Edge Pro-enhet går du till **Översikt** och noterar Kubernetes-program varu numret. 
        - Kontrol lera att dessa två versioner är kompatibla med den mappning som finns i den Kubernetes-version som stöds<!-- insert link-->. 


Du är redo att distribuera ett tillstånds känsligt program på din Azure Stack Edge Pro-enhet. 


## <a name="deploy-mysql"></a>Distribuera MySQL

Du kommer nu att köra ett tillstånds känsligt program genom att skapa en Kubernetes-distribution och ansluta den till den inbyggda StorageClass med hjälp av en PersistentVolumeClaim (PVC). 

Alla `kubectl` kommandon som du använder för att skapa och hantera tillstånds känsliga program distributioner måste ange det namn område som är associerat med konfigurationen. Använd för att ange namn området i ett kubectl-kommando `kubectl <command> -n <your-namespace>` .

1. Hämta en lista över poddar som körs på ditt Kubernetes-kluster i ditt namn område. En pod är en program behållare eller process som körs på ditt Kubernetes-kluster.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Här är ett exempel på kommando användning:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   Utdata bör ange att inga resurser (poddar) hittas eftersom det inte finns några program som körs i klustret.

1. Du kommer att använda följande YAML-filer. `mysql-deployment.yml`Filen beskriver en distribution som kör MySQL och refererar till PVC: n. Filen definierar en volym montering för `/var/lib/mysql` och skapar sedan en PVC som söker efter en 20 GB-volym. Ett dynamiskt PV har tillhandahållits och PVC: n är kopplad till detta PV.

    Kopiera och spara följande `mysql-deployment.yml` fil i en mapp på Windows-klienten som du använder för att få åtkomst till Azure Stack Edge Pro-enheten.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim-sc
    ```
    
2. Kopiera och Spara som en `mysql-pvc.yml` fil i samma mapp som du sparade `mysql-deployment.yml` . Om du vill använda den inbyggda StorageClass som Azure Stack Edge Pro-enhet på en ansluten datadisk, anger du `storageClassName` fältet i PVC-objektet till `ase-node-local` och accessModes ska vara `ReadWriteOnce` . 

    > [!NOTE] 
    > Kontrol lera att YAML-filerna har rätt indrag. Du kan kontrol lera med [yaml luddfri](http://www.yamllint.com/) för att validera och sedan Spara.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Distribuera `mysql-pvc.yaml` filen.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Här är ett exempel på utdata från distributionen.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Notera namnet på den PVC som skapas – i det här exemplet `mysql-pv-claim-sc` . Du kommer att använda det i ett senare steg.

4. Distribuera `mysql-deployment.yml` filens innehåll.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Här är ett exempel på utdata från distributionen.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Visa information om distributionen.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Lista de poddar som skapats av distributionen.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Här är ett exempel på utdata.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Granska PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Här är ett exempel på utdata.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verifiera att MySQL körs

Verifiera att programmet körs genom att skriva:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Ange lösen ordet när du uppmanas till det. Lösen ordet finns i `mysql-deployment` filen.

Här är ett exempel på utdata.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## <a name="delete-a-deployment"></a>Ta bort en distribution

Ta bort de distribuerade objekten efter namn om du vill ta bort distributionen. Dessa objekt omfattar distribution, tjänst och PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Här är exempel på utdata från när du tar bort distributionen och tjänsten.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Här är exempel på utdata från när du tar bort PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar nätverk via kubectl finns i [distribuera ett tillstånds lösa program på en Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)