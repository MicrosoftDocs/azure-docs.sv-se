---
title: Använd kubectl för att distribuera Kubernetes-tillstånds känslig app via statiskt etablerad resurs på Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur du skapar och hanterar en Kubernetes tillstånds känslig program distribution via en statiskt etablerad resurs med hjälp av kubectl på en Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 51c4a873ca0f4d8c3013e77399f0f9b948875fb6
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520718"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Använda kubectl för att köra ett Kubernetes tillstånds känsligt program med en PersistentVolume på din Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln visar hur du distribuerar ett tillstånds känsligt program med en instans i Kubernetes med en PersistentVolume (PV) och en distribution. Distributionen använder `kubectl` kommandon i ett befintligt Kubernetes-kluster och distribuerar MySQL-programmet. 

Den här proceduren är avsedd för de som har granskat [Kubernetes-lagringen på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-storage.md) och som är bekanta med begreppen [Kubernetes-lagring](https://kubernetes.io/docs/concepts/storage/).

Azure Stack Edge Pro stöder också körning av Azure SQL Edge-behållare och dessa kan distribueras på ett liknande sätt som beskrivs här för MySQL. Mer information finns i [Azure SQL Edge](../azure-sql-edge/overview.md).


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
        - Kontrol lera att dessa två versioner är kompatibla med den mappning som finns i den Kubernetes-version som stöds.<!-- insert link--> 


Du är redo att distribuera ett tillstånds känsligt program på din Azure Stack Edge Pro-enhet. 

## <a name="provision-a-static-pv"></a>Etablera ett statiskt PV

Om du vill konfigurera ett PV statiskt måste du skapa en resurs på enheten. Följ de här stegen för att etablera ett PV mot din SMB-resurs. 

> [!NOTE]
> Det speciella exemplet som används i den här instruktions artikeln fungerar inte med NFS-resurser. I allmänhet kan NFS-resurser tillhandahållas på din Azure Stack Edge-enhet med program som inte är databaser.

1. Välj om du vill skapa en Edge-resurs eller en lokal lokal resurs. Följ instruktionerna i [Lägg till en resurs](azure-stack-edge-manage-shares.md#add-a-share) för att skapa en resurs. Se till att markera kryss rutan för att **använda resurs med Edge Compute**.

    ![Edge-lokal resurs för PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. I stället för att skapa en ny resurs måste du montera resursen om du väljer att använda en befintlig resurs.
    
        Gå till **resurser** i Azure Portal för din Azure Stack Edge-resurs. Från den befintliga listan över resurser väljer du och klickar på en resurs som du vill använda.

        ![Välj befintlig lokal resurs för PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Välj **montera** och bekräfta montering när du uppmanas till det.  

        ![Montera befintlig lokal resurs för PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Anteckna resurs namnet. När den här resursen skapas skapas ett permanent volym objekt automatiskt i Kubernetes-klustret som motsvarar den SMB-resurs som du skapade. 

## <a name="deploy-mysql"></a>Distribuera MySQL

Du kommer nu att köra ett tillstånds känsligt program genom att skapa en Kubernetes-distribution och ansluta den till det PV som du skapade i föregående steg med en PersistentVolumeClaim (PVC). 

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

1. Du kommer att använda följande YAML-filer. `mysql-deployment.yml`Filen beskriver en distribution som kör MySQL och refererar till PVC: n. Filen definierar en volym montering för `/var/lib/mysql` och skapar sedan en PVC som söker efter en 20 GB-volym. 

    Detta anspråk uppfylls av alla befintliga PV som har allokerats statiskt när du skapade resursen i det tidigare steget. På din enhet skapas ett stort PV på 32 TB för varje resurs. NUVÄRDEt uppfyller de krav som anges av PVC och PVC: n måste vara kopplad till detta nuvärde.

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
              claimName: mysql-pv-claim
    ```
    
2. Kopiera och Spara som en `mysql-pv.yml` fil i samma mapp som du sparade `mysql-deployment.yml` . Om du vill använda den SMB-resurs som du tidigare skapade med `kubectl` , anger du `volumeName` fältet i PVC-objektet till namnet på resursen. 

    > [!NOTE] 
    > Kontrol lera att YAML-filerna har rätt indrag. Du kan kontrol lera med [yaml luddfri](http://www.yamllint.com/) för att validera och sedan Spara.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Distribuera `mysql-pv.yaml` filen.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Här är ett exempel på utdata från distributionen.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Notera namnet på den PVC som skapats. Du kommer att använda det i ett senare steg. 

4. Distribuera `mysql-deployment.yml` filens innehåll.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Här är ett exempel på utdata från distributionen.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Visa information om distributionen.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
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
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Lista de poddar som skapats av distributionen.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Här är ett exempel på utdata.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Granska PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Här är ett exempel på utdata.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verifiera att MySQL körs


Om du vill köra ett kommando mot en behållare i en pod som kör MySQL, skriver du:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Här är ett exempel på utdata.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
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
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

NUVÄRDEt är inte längre kopplat till PVC: n eftersom den har tagits bort. När PV etablerades när resursen skapades måste du ta bort resursen. Gör så här:

1. Demontera resursen. I Azure Portal går du till **Azure Stack Edge-resurs > resurser** och väljer och klickar på den resurs som du vill demontera. Välj **demontera** och bekräfta åtgärden. Vänta tills resursen har demonterats. Demonteringen frigör resursen (och därmed tillhör ande PersistentVolume) från Kubernetes-klustret. 

    ![Demontera lokal resurs för PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Nu kan du välja **ta bort** och bekräfta borttagning för att ta bort resursen. Detta bör även ta bort resursen och motsvarande PV.

    ![Ta bort lokal resurs för PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar lagring dynamiskt finns i [distribuera ett tillstånds känsligt program via dynamisk etablering på en Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)