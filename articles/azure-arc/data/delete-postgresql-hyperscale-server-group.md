---
title: Ta bort en Azure-båge som är aktive rad PostgreSQL skalnings Server grupp
description: Ta bort en Azure-båge som är aktive rad postgres skalnings Server grupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7932ad3b30910e539acfbff2329a03f80a4d1a0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670366"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Ta bort en Azure-båge som är aktive rad PostgreSQL skalnings Server grupp

Det här dokumentet beskriver stegen för att ta bort en Server grupp från din Azure Arc-installation.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Ta bort Server gruppen

Vi ska till exempel ta bort _postgres01_ -instansen från installations programmet nedan:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Det allmänna formatet för kommandot Ta bort är:
```console
azdata arc postgres server delete -n <server group name>
```
När du kör det här kommandot uppmanas du att bekräfta borttagningen av Server gruppen. Om du använder skript för att automatisera borttagningar måste du använda parametern--Force för att kringgå bekräftelse förfrågan. Du kan till exempel köra ett kommando som: 
```console
azdata arc postgres server delete -n <server group name> --force
```

Om du vill ha mer information om kommandot Ta bort kör du:
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>Ta bort Server gruppen som används i det här exemplet

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Frigör Kubernetes permanent volym anspråk (PVC)

Att ta bort en Server grupp tar inte bort tillhör ande [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Det här är avsiktligt. Avsikten är att hjälpa användaren att komma åt databasfilerna om instansen tas bort av misstag. Det är inte obligatoriskt att ta bort PVC:er. Men det rekommenderas. Om du inte frigör dessa PVC:er uppstår till slut fel eftersom Kubernetes-klustret tror att diskutrymmet håller på att ta slut. Gör så här för att frigöra PVC:er:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. lista PVC: er för den server grupp som du har tagit bort

Kör följande kommando för att visa en lista över PVC: er:

```console
kubectl get pvc [-n <namespace name>]
```

Den returnerar listan över PVC: er, särskilt PVC: er för den server grupp som du har tagit bort. Exempel:

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Det finns 8 PVC: er för den här server gruppen.

### <a name="2-delete-each-of-the-pvcs"></a>2. ta bort alla PVC: er

Ta bort data och logg-PVC: er för var och en av PostgreSQL (koordinator och arbetare) för den server grupp som du har tagit bort.

Det allmänna formatet för kommandot är: 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Exempel:

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

Vart och ett av dessa kubectl-kommandon bekräftar att PVC: n har tagits bort. Exempel:

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> Som det visas kan inte borttagning av PVC: er komma att få ditt Kubernetes-kluster i en situation där det kommer att utlösa fel. Vissa av de här felen kan vara att det inte går att logga in till ditt Kubernetes-kluster med azdata eftersom poddar kan avlägsnas från den på grund av det här lagrings problemet (normalt Kubernetes beteende).
>
> Du kan till exempel se meddelanden i loggarna som liknar:  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>Nästa steg
Skapa [Azure Arc Enabled postgresql-skalning](create-postgresql-hyperscale-server-group.md)
