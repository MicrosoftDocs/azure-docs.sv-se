---
title: Distribuera en Azure SQL Edge-behållare i Kubernetes – Azure SQL Edge
description: Lär dig mer om att distribuera en Azure SQL Edge-behållare i Kubernetes
keywords: SQL Edge, container, Kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395299"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Distribuera en Azure SQL Edge-behållare i Kubernetes

Azure SQL Edge kan distribueras i ett Kubernetes-kluster både som en IoT Edge modul via Azure IoT Edge som körs på Kubernetes eller som en fristående container pod. I resten av den här artikeln kommer vi att fokusera på den fristående behållar distributionen i ett Kubernetes-kluster. Information om hur du distribuerar Azure IoT Edge på Kubernetes finns [Azure IoT Edge på Kubernetes (för hands version)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

Den här självstudien visar hur du konfigurerar en Azure SQL Edge-instans med hög tillgänglighet i en behållare i ett Kubernetes-kluster. 

> [!div class="checklist"]
> * Skapa ett SA-lösenord
> * Skapa lagring
> * Skapa distributionen
> * Anslut med SQL Server Management Studio (SSMS)
> * Verifiera haveri och återställning

Kubernetes 1,6 och senare har stöd för [lagrings klasser](https://kubernetes.io/docs/concepts/storage/storage-classes/), [beständiga volym anspråk](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)och [volym typen Azure-disk](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Du kan skapa och hantera dina Azure SQL Edge-instanser internt i Kubernetes. Exemplet i den här artikeln visar hur du skapar en [distribution](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) för att uppnå en konfiguration med hög tillgänglighet som liknar en instans av en delad disk-redundanskluster. I den här konfigurationen spelar Kubernetes rollen som kluster-Orchestrator. När en Azure SQL Edge-instans i en behållare kraschar, startar Orchestrator en annan instans av behållaren som bifogas till samma permanenta lagrings utrymme.

![Azure SQL Edge i ett Kubernetes-kluster](media/deploy-kubernetes/kubernetes-sql-edge.png)

I föregående diagram `azure-sql-edge` är en behållare i en [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes dirigerar resurserna i klustret. En [replik uppsättning](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garanterar att Pod återställs automatiskt efter ett nodfel. Program ansluter till tjänsten. I det här fallet representerar tjänsten en belastningsutjämnare som är värd för en IP-adress som är oförändrad efter felet `azure-sql-edge` .

I följande diagram `azure-sql-edge` har behållaren misslyckats. Som Orchestrator garanterar Kubernetes rätt antal felfria instanser i replik uppsättningen och startar en ny behållare enligt konfigurationen. Orchestrator startar en ny Pod på samma nod och `azure-sql-edge` återansluter till samma permanenta lagrings utrymme. Tjänsten ansluter till den nya återskapade `azure-sql-edge` .

![Azure SQL Edge i ett Kubernetes-kluster när Pod har misslyckats](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

I följande diagram misslyckades noden som är värd för `azure-sql-edge` behållaren. Orchestrator startar den nya Pod på en annan nod och `azure-sql-edge` återansluter till samma permanenta lagrings utrymme. Tjänsten ansluter till den nya återskapade `azure-sql-edge` .

![Azure SQL Edge i ett Kubernetes-kluster efter att noden har misslyckats](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Förutsättningar

* **Kubernetes-kluster**
   - I självstudien krävs ett Kubernetes-kluster. I stegen används [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) för att hantera klustret. 

   - I den här självstudien kommer vi att använda Azure Kubernetes-tjänsten för att distribuera Azure SQL Edge. Se [distribuera ett Azure Kubernetes service-kluster (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) för att skapa och ansluta till ett Kubernetes-kluster med en nod i AKS med `kubectl` . 

   >[!NOTE]
   >För att skydda mot nodfel kräver ett Kubernetes-kluster mer än en nod.

* **Azure CLI**
   - Anvisningarna i den här självstudien har verifierats mot Azure CLI-2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Skapa ett Kubernetes-namnområde för SQL Edge-distribution

Skapa ett nytt namn område i Kubernetes-klustret. Det här namn området kommer att användas för att distribuera SQL Edge och alla nödvändiga artefakter. Mer information om Kubernetes-namnområden finns i [namn områden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Skapa ett SA-lösenord

Skapa ett SA-lösenord i Kubernetes-klustret. Kubernetes kan hantera känslig konfigurations information, t. ex. lösen ord som [hemligheter](https://kubernetes.io/docs/concepts/configuration/secret/).

Följande kommando skapar ett lösen ord för SA-kontot:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Ersätt `MyC0m9l&xP@ssw0rd` med ett komplext lösen ord.

## <a name="create-storage"></a>Skapa lagring

Konfigurera en [beständig volym](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) och [beständigt volym anspråk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) i Kubernetes-klustret. Slutför följande steg: 

1. Skapa ett manifest för att definiera lagrings klassen och det beständiga volym anspråket.  Manifestet anger lagrings provisioor, parametrar och [anspråks princip](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). Kubernetes-klustret använder det här manifestet för att skapa den beständiga lagringen. 

   Följande yaml-exempel definierar en lagrings klass och ett beständigt volym anspråk. Lagrings klassens etablering är `azure-disk` , eftersom det här Kubernetes-klustret finns i Azure. Lagrings konto typen är `Standard_LRS` . Beständiga volym anspråk heter `mssql-data` . Beständiga volym anspråks metadata innehåller en anteckning som ansluter tillbaka till lagrings klassen. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Spara filen (till exempel **PVC. yaml**).

2. Skapa ett beständigt volym anspråk i Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` är den plats där du sparade filen.

   Den permanenta volymen skapas automatiskt som ett Azure Storage-konto och binds till det beständiga volym anspråket. 

    ![Skärm bild av kommandot persistent volym anspråk](media/deploy-kubernetes/pvc-cmd.png)

3. Verifiera det beständiga volym anspråket.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` är namnet på det beständiga volym anspråket.

   I föregående steg heter beständigt volym anspråk `mssql-data` . Om du vill se metadata om det beständiga volym anspråket kör du följande kommando:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Returnerade metadata innehåller ett värde som kallas `Volume` . Värdet mappas till namnet på blobben.

   ![Skärm bild av returnerade metadata, inklusive volym](media/deploy-kubernetes/describe-volume.png)

4. Verifiera den permanenta volymen.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` Returnerar metadata om den permanenta volym som har skapats automatiskt och bundits till det beständiga volym anspråket. 

## <a name="create-the-deployment"></a>Skapa distributionen

I det här exemplet beskrivs den behållare som är värd för Azure SQL Edge-instansen som ett Kubernetes-distributions objekt. Distributionen skapar en replik uppsättning. Replik uppsättningen skapar pod. 

I det här steget skapar du ett manifest som beskriver behållaren baserat på Azure SQL Edge Docker-avbildningen. Manifestet refererar till `mssql-data` beständigt volym anspråk och `mssql` hemligheten som du redan har använt i Kubernetes-klustret. Manifestet beskriver också en [tjänst](https://kubernetes.io/docs/concepts/services-networking/service/). Den här tjänsten är en belastningsutjämnare. Belastningsutjämnaren garanterar att IP-adressen kvarstår efter att Azure SQL Edge-instansen har återställts. 

1. Skapa ett manifest (en YAML-fil) för att beskriva distributionen. I följande exempel beskrivs en distribution, inklusive en behållare som baseras på avbildningen av Azure SQL Edge-behållare.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Kopiera föregående kod till en ny fil med namnet `sqldeployment.yaml` . Uppdatera följande värden: 

   * MSSQL_PID `value: "Developer"` : anger behållaren för att köra Azure SQL Edge Developer Edition. Developer Edition är inte licensierad för produktions data. Om distributionen är för produktions användning ställer du in versionen på `Premium` . 

      >[!NOTE]
      >Mer information finns i [How to License Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Det här värdet kräver en post för `claimName:` som mappar till det namn som används för det beständiga volym anspråket. I den här självstudien används `mssql-data`. 

   * `name: SA_PASSWORD`: Konfigurerar behållar avbildningen att ange SA-lösenordet, enligt definitionen i det här avsnittet.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     När Kubernetes distribuerar behållaren refererar den till den hemlighet som namnges `mssql` för att hämta värdet för lösen ordet. 

   >[!NOTE]
   >Genom att använda `LoadBalancer` tjänst typen kan Azure SQL Edge-instansen nås via fjärr anslutning (via Internet) på port 1433.

   Spara filen (till exempel **sqledgedeploy. yaml**).

2. Skapa distributionen.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` är den plats där du sparade filen.

   ![Skärm bild av kommandot distribution](media/deploy-kubernetes/deploy-sql-cmd.png)

   Distributionen och tjänsten skapas. Azure SQL Edge-instansen finns i en behållare som är ansluten till beständig lagring.

   Om du vill visa status för Pod skriver du `kubectl get pod -n <namespace name>` .

   ![Skärm bild av kommandot Get Pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   I föregående bild har Pod statusen `Running` . Den här statusen indikerar att behållaren är klar. Det här kan ta flera minuter.

   >[!NOTE]
   >När distributionen har skapats kan det ta några minuter innan Pod visas. Fördröjningen beror på att klustret hämtar Azure SQL Edge-behållar avbildningen från Docker Hub. När avbildningen hämtas första gången kan efterföljande distributioner gå snabbare om distributionen till en nod som redan har avbildningen är cachelagrad. 

3. Kontrol lera att tjänsterna körs. Kör följande kommando:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Det här kommandot returnerar tjänster som kör, samt de interna och externa IP-adresserna för tjänsterna. Anteckna den externa IP-adressen för `mssql-deployment` tjänsten. Använd den här IP-adressen för att ansluta till Azure SQL Edge. 

   ![Skärm bild av kommandot Get service](media/deploy-kubernetes/get-service-cmd.png)

   Om du vill ha mer information om status för objekten i Kubernetes-klustret kör du:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Ansluta till Azure SQL Edge-instansen

Om du har konfigurerat behållaren enligt beskrivningen kan du ansluta till ett program utanför det virtuella Azure-nätverket. Använd `sa` kontot och den externa IP-adressen för tjänsten. Använd det lösen ord som du konfigurerade som Kubernetes-hemlighet. Mer information om hur du ansluter till en Azure SQL Edge-instans finns [i ansluta till Azure SQL Edge](connect.md).

## <a name="verify-failure-and-recovery"></a>Verifiera haveri och återställning

Du kan ta bort Pod för att kontrol lera att det inte går att verifiera. Gör så här:

1. Visar en lista över Pod som kör Azure SQL Edge.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Notera namnet på pod som kör Azure SQL Edge.

2. Ta bort pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` är värdet som returneras från föregående steg för Pod namn. 

Kubernetes återskapar automatiskt Pod för att återställa en Azure SQL Edge-instans och ansluta till den beständiga lagringen. Används `kubectl get pods` för att kontrol lera att en ny Pod har distribuerats. Använd `kubectl get services` för att kontrol lera att IP-adressen för den nya behållaren är densamma. 

## <a name="summary"></a>Sammanfattning

I den här självstudien har du lärt dig hur du distribuerar Azure SQL Edge-behållare till ett Kubernetes-kluster för hög tillgänglighet. 

> [!div class="checklist"]
> * Skapa ett SA-lösenord
> * Skapa lagring
> * Skapa distributionen
> * Ansluta till Azure SQL Edge Management Studios (SSMS)
> * Verifiera haveri och återställning

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Kubernetes](../aks/intro-kubernetes.md)
- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge med hjälp av IoT Edge](tutorial-deploy-azure-resources.md).
- [Data strömning i Azure SQL Edge](stream-data.md)