---
title: Azure Arc-aktiverad hanterad instans med hög tillgänglighet
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Lär dig hur du distribuerar Azure Arc-aktiverade hanterade instanser med hög tillgänglighet.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032202"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure Arc-aktiverad hanterad instans med hög tillgänglighet

Den Azure Arc-aktiverade hanterade instansen distribueras på Kubernetes som ett behållar program och använder Kubernetes-konstruktioner som tillstånds känsliga uppsättningar och beständiga lagrings enheter för att tillhandahålla inbyggd hälso övervakning, fel avkänning och funktioner för redundans för att upprätthålla tjänstens hälsa. För ökad tillförlitlighet kan du också konfigurera Azure Arc-aktiverad hanterad instans att distribuera med extra repliker i en konfiguration med hög tillgänglighet. Övervakning, fel identifiering och automatisk redundans hanteras av data styrenheten Arc Data Services. Den här tjänsten tillhandahålls utan åtgärder från användaren – allt från tillgänglighets grupps konfiguration, konfiguration av databas speglings slut punkter, för att lägga till databaser i tillgänglighets gruppen eller för att skapa redundansväxling och uppgradering. Det här dokumentet utforskar båda typerna av hög tillgänglighet.

## <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet 

Inbyggd hög tillgänglighet tillhandahålls av Kubernetes när Fjärrlagring av permanent lagring har kon figurer ATS och delats med noder som används av migreringen av Arc-datatjänsten. I den här konfigurationen spelar Kubernetes rollen som kluster-Orchestrator. När den hanterade instansen i en behållare eller den underliggande noden Miss lyckas, startar Orchestrator en annan instans av behållaren och kopplar till samma permanenta lagring. Den här typen är aktive rad som standard när du distribuerar den hanterade instansen Azure Arc-aktiverad.

### <a name="verify-built-in-high-availability"></a>Verifiera inbyggd hög tillgänglighet

Det här avsnittet kontrollerar du den inbyggda hög tillgänglighet som tillhandahålls av Kubernetes. När du följer stegen för att testa den här funktionen tar du bort Pod för en befintlig hanterad instans och kontrollerar att Kubernetes återställs från den här åtgärden. 

### <a name="prerequisites"></a>Förutsättningar

- Kubernetes-kluster måste ha [delad, Fjärrlagring](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) 
- En Azure-Arc-aktiverad hanterad instans distribuerad med en replik (standard)

1. Visa poddar. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Ta bort den hanterade instansen pod.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Till exempel

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Visa poddar för att kontrol lera att den hanterade instansen återställs.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Till exempel

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

När alla behållare i pod har återställts kan du ansluta till den hanterade instansen.

## <a name="deploy-with-always-on-availability-groups"></a>Distribuera med Always on-tillgänglighetsgrupper

För ökad tillförlitlighet kan du konfigurera Azure Arc-aktiverad hanterad instans att distribuera med extra repliker i en konfiguration med hög tillgänglighet. 

Funktioner som tillgänglighets grupper aktiverar:

- När de distribueras med flera repliker skapas en enda tillgänglighets grupp med namnet `containedag` . Som standard `containedag` har tre repliker, inklusive primär. Alla CRUD-åtgärder för tillgänglighets gruppen hanteras internt, inklusive att skapa tillgänglighets gruppen eller ansluta till repliker till tillgänglighets gruppen som skapats. Det går inte att skapa fler tillgänglighets grupper i den hanterade Azure Arc-instansen.

- Alla databaser läggs automatiskt till i tillgänglighets gruppen, inklusive alla användar-och system databaser som `master` och `msdb` . Den här funktionen ger en vy över flera system över tillgänglighets grupp replikerna. Observera både `containedag_master` och `containedag_msdb` databaser om du ansluter direkt till instansen. `containedag_*`Databaserna representerar `master` och `msdb` i tillgänglighets gruppen.

- En extern slut punkt tillhandahålls automatiskt för anslutning till databaser i tillgänglighets gruppen. Den här slut punkten `<managed_instance_name>-svc-external` spelar roll för tillgänglighets gruppens lyssnare.

### <a name="deploy"></a>Distribuera

Kör följande kommando för att distribuera en hanterad instans med tillgänglighets grupper.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Kontrollera status
När instansen har distribuerats kör du följande kommandon för att kontrol lera status för instansen:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Exempel på utdata:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Observera det ytterligare antalet `Replicas` och `AGstatus` fältet som visar tillgänglighets gruppens hälso tillstånd. Om alla repliker är upp och synkroniserade är det här värdet `healthy` . 

### <a name="restore-a-database"></a>Återställ en databas 
Ytterligare steg krävs för att återställa en databas till en tillgänglighets grupp. Följande steg visar hur du återställer en databas till en hanterad instans och lägger till den i en tillgänglighets grupp. 

1. Exponera den externa slut punkten för den primära instansen genom att skapa en ny Kubernetes-tjänst.

    Bestäm vilka POD som är värdar för den primära repliken genom att ansluta till den hanterade instansen och köra:

    ```sql
    SELECT @@SERVERNAME
    ```
    Skapa Kubernetes-tjänsten på den primära instansen genom att köra kommandot nedan om ditt Kubernetes-kluster använder nodePort-tjänster. Ersätt `podName` med namnet på servern som returnerades i föregående steg, `serviceName` med det önskade namnet för Kubernetes-tjänsten skapad.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    För en LoadBalancer-tjänst kör du samma kommando, förutom att den typ av tjänst som skapats är `LoadBalancer` . Exempel: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Här är ett exempel på det här kommandot körs mot Azure Kubernetes-tjänsten, där Pod som är värd för den primära är `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Hämta IP-adressen för Kubernetes-tjänsten som skapats:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Återställ databasen till den primära instans slut punkten.

    Lägg till säkerhets kopian av databasen i behållaren för den primära instansen.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Exempel

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Återställ säkerhets kopierings filen för databasen genom att köra kommandot nedan.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Exempel

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Lägg till databasen i tillgänglighets gruppen.

    För att databasen ska läggas till i AG måste den köras i fullständigt återställnings läge och en logg säkerhets kopia måste göras. Kör TSQL-instruktionerna nedan om du vill lägga till den återställda databasen i tillgänglighets gruppen.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    Följande exempel lägger till en databas med namnet `WideWorldImporters` som återställdes på instansen:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Vi rekommenderar att du rensar genom att ta bort Kubernetes-tjänsten som skapats ovan genom att köra det här kommandot:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Begränsningar

Azure Arc-aktiverade hanterade instans tillgänglighets grupper har samma [begränsningar som för stora data kluster tillgänglighets grupper. Klicka här om du vill veta mer.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [funktioner och funktioner i Azure Arc-aktiverad SQL-hanterad instans](managed-instance-features.md)
