---
title: Ta bort Azure Arc-aktiverad SQL-hanterad instans
description: Ta bort Azure Arc-aktiverad SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90942165"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Ta bort Azure Arc-aktiverad SQL-hanterad instans
I den här artikeln beskrivs hur du kan ta bort en Azure Arc-aktiverad SQL-hanterad instans.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Visa befintliga Azure Arc-aktiverade SQL-hanterade instanser
Om du vill visa SQL-hanterade instanser kör du följande kommando:

```console
azdata arc sql mi list
```

Utdata bör se ut ungefär så här:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Ta bort en Azure Arc-aktiverad SQL-hanterad instans
Om du vill ta bort en SQL-hanterad instans kör du följande kommando:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

Utdata bör se ut ungefär så här:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Frigör Kubernetes permanent volym anspråk (PVC)

Att ta bort en SQL-hanterad instans tar inte bort tillhör ande [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Det här är avsiktligt. Avsikten är att hjälpa användaren att komma åt databasfilerna om instansen tas bort av misstag. Det är inte obligatoriskt att ta bort PVC:er. Men det rekommenderas. Om du inte frigör dessa PVC: er kan du ta slut på fel eftersom ditt Kubernetes-kluster kommer slut på disk utrymme. Gör så här för att frigöra PVC:er:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. lista PVC: er för den server grupp som du har tagit bort
Kör följande kommando för att visa en lista över PVC: er:
```console
kubectl get pvc
```

I följande exempel nedan noterar du PVC: er för de SQL-hanterade instanser som du har tagit bort.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. ta bort alla PVC: er
Ta bort data och logg-PVC: er för varje SQL-hanterad instans som du har tagit bort.
Det allmänna formatet för kommandot är: 
```console
kubectl delete pvc <name of pvc>
```

Exempel:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Vart och ett av dessa kubectl-kommandon bekräftar att PVC: n har tagits bort. Exempel:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Som det visas kan inte borttagning av PVC: er komma att få ditt Kubernetes-kluster i en situation där det kommer att utlösa fel. Vissa av de här felen kan vara att det inte går att logga in till ditt Kubernetes-kluster med azdata eftersom poddar kan avlägsnas från den på grund av det här lagrings problemet (normalt Kubernetes beteende).
>
> Du kan till exempel se meddelanden i loggarna som liknar:  
> - Anteckningar: microsoft.com/ignore-pod-health: true  
> - Status: misslyckades  
> - Orsak: avlägsnad  
> - Meddelande: noden har ont om resurs: beständig lagring. Container Controller använde 16372Ki, vilket överskrider sin begäran av 0.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [funktioner och funktioner i Azure Arc-aktiverad SQL-hanterad instans](managed-instance-features.md)

[Börja med att skapa en datakontrollant](create-data-controller.md)

Har du redan skapat en datakontrollant? [Skapa en Azure Arc-aktiverad SQL-hanterad instans](create-sql-managed-instance.md)