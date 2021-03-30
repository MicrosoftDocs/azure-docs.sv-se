---
title: Visa konfigurationen av en ARC-aktiverad PostgreSQL-Server grupp
titleSuffix: Azure Arc enabled data services
description: Visa konfigurationen av en ARC-aktiverad PostgreSQL-Server grupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93279409"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Visa konfigurationen av en ARC-aktiverad PostgreSQL-Server grupp

Den här artikeln förklarar hur du visar konfigurationen för dina Server grupper. Det gör det genom att förutse några frågor som du kan uppmanas till dig själv och det svarar. Ibland kan det finnas flera giltiga svar. Den här artikeln passar de vanligaste eller användbara. Den grupperar dessa frågor efter tema:

- från en Kubernetes-punkt i vyn
- från en Azure Arc-aktiverad data tjänst punkt

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Från en Kubernetes-punkt i vyn

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Hur många poddar används av Azure Arc Enabled PostgreSQL-skalning?

Visa en lista över Kubernetes-resurser av typen postgres. Kör kommandot:

```console
kubectl get postgresqls [-n <namespace name>]
```

Utdata från det här kommandot visar en lista över Server grupper som skapats. För varje anger det antalet poddar. Exempel:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Det här exemplet visar att 2 Server grupper skapas och varje körning på 3 poddar (1 koordinator + 2 arbetare). Det innebär att Server grupperna som skapas i den här Azure Arc-datakontrollanten använder 6 poddar.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Vilka poddar används av Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper?

Kör följande:

```console
kubectl get pods [-n <namespace name>]
```

Detta returnerar listan över poddar. Du ser de poddar som används av dina Server grupper baserat på de namn som du gav dessa Server grupper. Exempel:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

I det här exemplet är de sex poddar som är värdar för de två Server grupper som skapas:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Vilken server grupps Pod används för vilken roll Server gruppen?

Alla Pod namn som suffixet med `-0` representerar en koordinator-nod. Alla nodnamn suffix med `-x` var 1 eller större är arbetsnoden. I exemplet ovan gäller följande:
- Koordinatorerna `postgres01-0` är: `postgres02-0`
- Arbets tagarna är: `postgres01-2` , `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Vad är statusen för poddar?

Kör `kubectl get pods` och titta på kolumnen `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Vilka permanenta volym anspråk (PVC) används? 

För att förstå vilka PVC: er som används, samt vilka som används för data, loggar och säkerhets kopieringar, kör: 

```console
kubectl get pvc [-n <namespace name>]
```

Som standard anger prefixet för namnet på en PVC användningen:

- `backups-`...: är en PVC som används för säkerhets kopieringar
- `data-`...: är PVC som används för datafiler
- `logs-`...: är en PVC som används för transaktions loggar/WAL-filer

Exempel:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Från en Azure Arc-aktiverad Data Services-plats:

* Hur många Server grupper skapas i en data styrenhet för båge?
* Vad är deras namn?
* Hur många arbets noder använder de?
* Vilken version av postgres körs de?

Använd något av följande kommandon.
- **Med kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Till exempel ger den nedanstående utdata där varje rad är en `servergroup` . Strukturen för namnet i nedanstående visning skapas som:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   Utdata ovan visar 2 Server grupper som är av postgres version 12. Om versionen var postgres 11 skulle namnet på CRD vara postgresql-11.arcdata.microsoft.com i stället.

   Var och en av dem körs på 3 noder/poddar: 1 koordinator och 2 arbetare.

- **Med azdata:**

Kör följande kommando. Utdata visar liknande information som kubectl visar:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Hur mycket minne och virtuella kärnor används och vilka tillägg skapades för en grupp?

Kör något av följande kommandon

**Med Kubectl:**

Använd kubectl för att beskriva en postgres-resurs. För att göra det behöver du dess typ (namnet på Kubernetes-resursen (CRD) för motsvarande postgres-version som visas ovan) och namnet på Server gruppen.
Det allmänna formatet för kommandot är:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Exempel:

```console
kubectl describe postgresql-12/postgres02
```

Detta kommando visar konfigurationen för Server gruppen:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>Tidigare än oktober 2020 släpptes `Workers` `Shards` tidigare i föregående exempel. Mer information finns i [versions anteckningar – Azure Arc-aktiverade data tjänster (för hands version)](release-notes.md) .

Nu ska vi ringa ut vissa specifika punkter i beskrivningen av det som `servergroup` visas ovan. Vad säger vi om den här server gruppen?

- Det är av version 12 av postgres: 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- Den skapades under månaden augusti 2020:
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- Två postgres-tillägg har skapats i den här server gruppen: `citus` och `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- Den använder två arbetsnoder
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- Det garanterar att man använder 1 CPU/vCore och 512 MB RAM-minne per nod. Det kommer att använda mer än 4 CPU/virtuella kärnor och 1 024 MB minne:
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - Den finns tillgänglig för frågor och har inga problem. Alla noder är igång:
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Med azdata:**

Det allmänna formatet för kommandot är:

```console
azdata arc postgres server show -n <server group name>
```

Exempel:

```console
azdata arc postgres server show -n postgres02
```

Returnerar nedanstående utdata i ett format och innehåll som liknar det som returneras av kubectl.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Nästa steg
- [Läs om begreppen Azure Arc Enabled PostgreSQL storskalig](concepts-distributed-postgres-hyperscale.md)
- [Läs om hur du skalar ut (lägga till arbetsnoder) en Server grupp](scale-out-postgresql-hyperscale-server-group.md)
- [Läs om hur du skalar upp/ned (öka eller minska minnes-och/eller virtuella kärnor) en Server grupp](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Läs mer om lagrings konfiguration](storage-configuration.md)
- [Läs hur du övervakar en databas instans](monitor-grafana-kibana.md)
- [Använd PostgreSQL-tillägg i Azure Arc-aktiverade PostgreSQL för storskalig Server grupp](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Konfigurera säkerhet för din Azure-Arc-aktiverade PostgreSQL-hyperskala-servergrupp](configure-security-postgres-hyperscale.md)
