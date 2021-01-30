---
title: Förstå Kubernetes Storage Management på Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur Kubernetes Storage Management sker på en Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: alkohli
ms.openlocfilehash: 2d079f2fa3e67f1ec915a02de3e195ccac538209
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063324"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes lagrings hantering på din Azure Stack Edge Pro GPU-enhet

På din Azure Stack Edge Pro-enhet skapas ett Kubernetes-kluster när du konfigurerar Compute-rollen. När Kubernetes-klustret har skapats kan program i behållare distribueras i Kubernetes-klustret i poddar. Det finns olika sätt att tillhandahålla lagring till poddar i ditt Kubernetes-kluster. 

I den här artikeln beskrivs metoder för att etablera lagring på ett Kubernetes-kluster i allmänhet och särskilt inom ramen för din Azure Stack Edge Pro-enhet. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Lagrings krav för Kubernetes-poddar

Kubernetes-poddar är tillstånds lösa men programmen de kör är vanligt vis tillstånds känsliga. Eftersom poddar kan vara kort livs längd och de startas om, redundansväxlas eller flyttas mellan Kubernetes-noder måste följande krav uppfyllas för lagring som är kopplad till pod. 

Lagrings utrymmet måste:

- Live utanför pod.
- Vara oberoende av Pod-livscykeln.
- Vara tillgänglig från alla Kubernetes-noder.

För att förstå hur lagring hanteras för Kubernetes måste en förstå två API-resurser: 

- **PersistentVolume (PV)**: det här är en lagrings enhet i Kubernetes-klustret. Kubernetes-lagringen kan vara statiskt etablerad som `PersistentVolume` . Det kan också vara dynamiskt etablerad som  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: det här är en begäran om lagring av en användare. Virtuella kretsar använder PV-resurser. PVC: er kan begära angivna storleks-och åtkomst lägen. 

    Eftersom användarna behöver `PersistentVolumes` varierande egenskaper för olika problem, måste kluster administratörerna kunna erbjuda en mängd olika `PersistentVolumes` som skiljer sig åt på fler sätt än bara storleks-och åtkomst lägena. För dessa behov behöver du `StorageClass` resursen.

Lagrings etablering kan vara statisk eller dynamisk. Var och en av etablerings typerna beskrivs i följande avsnitt.

## <a name="static-provisioning"></a>Statisk etablering

Administratörer för Kubernetes-kluster kan etablera lagringen statiskt. För att göra det kan de använda lagrings Server del baserat på SMB/NFS-filsystem eller använda iSCSI-diskar som ansluter lokalt över nätverket i en lokal miljö, eller till och med Azure Files eller Azure-diskar i molnet. Den här typen av lagring tillhandahålls inte som standard och kluster administratörer måste planera och hantera denna etablering. 
 
Här är ett diagram som illustrerar hur statiskt allokerat lagrings utrymme används i Kubernetes: 

![Statisk etablering via PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Följande steg inträffar: 

1. **Etablera lagring**: kluster administratören etablerar lagringen. I det här exemplet skapar kluster administratören en eller flera SMB-resurser som automatiskt skapar permanenta volym objekt i Kubernetes-klustret som motsvarar dessa resurser. 

1. **Anspråks lagring**: du skickar en PVC-distribution som begär lagringen. Detta anspråk för lagring är PersistentVolumeClaim (PVC). Om storleken och åtkomst läget för PV matchar värdet för PVC: n, är PVC: n till NUVÄRDEt. PVC och PV mappar en-till-en.

1. **Montera PVC till behållaren**: när PVC: n är kopplad till nuvärdet kan du montera denna PVC på en sökväg i din behållare. När program logiken i behållaren läser/skriver från/till den här sökvägen skrivs data till SMB-lagringen.
 

## <a name="dynamic-provisioning"></a>Dynamisk etablering

Här är ett diagram som illustrerar hur statiskt allokerat lagrings utrymme används i Kubernetes: 

![Dynamisk etablering via StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Följande steg inträffar: 

1. **Definiera lagrings klass**: kluster administratören definierar en lagrings klass beroende på operativ miljön för ditt Kubernetes-kluster. Kluster administratören distribuerar också en-etablering, som ännu inte har en annan POD eller ett program distribuerat i Kubernetes-klustret. Etableraren har all information för att etablera resurserna dynamiskt.  

1. **Anspråks lagring**: du skickar ett program som skulle kräva lagringen. När en PVC har skapats med den här lagrings klass referensen anropas etableringen. 

1. **Etablera lagring dynamiskt**: etableraren skapar dynamiskt den resurs som är associerad med den lokala disk lagringen. När resursen har skapats skapas även ett PV-objekt som automatiskt motsvarar den här resursen.

1. **Montera PVC till container**: när PVC: n är kopplad till nuvärdet kan du montera PVC: n på behållaren på en sökväg på samma sätt som statisk etablering och läsa från eller skriva till resursen.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Lagrings etablering på Azure Stack Edge Pro

På den Azure Stack Edge Pro-enheten skapas statiskt allokerat `PersistentVolumes` med enhetens lagrings funktioner. När du etablerar en resurs och **använder alternativet dela med Edge Compute** är aktiverat, skapar den här åtgärden en PV-resurs automatiskt i Kubernetes-klustret.

![Skapa lokal delning i Azure Portal för statisk etablering](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Om du vill använda moln nivåer kan du skapa en gräns moln resurs med hjälp av alternativet dela med Edge-beräkning aktiverat. Ett PV skapas igen automatiskt för den här resursen. Alla program data som du skriver till Edge-resursen på nivå av molnet. 

![Skapa moln delning i Azure Portal för statisk etablering](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Du kan skapa både SMB-och NFS-resurser för att etablera PVs statiskt på Azure Stack Edge Pro-enhet. När PV har allokerats skickar du en PVC som gör det möjligt att kräva lagringen. Här är ett exempel på en PVC-distribution `yaml` som hävdar lagringen och använder resurserna som du etablerade.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Mer information finns i [distribuera ett tillstånds känsligt program via statisk etablering på Azure Stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

För att få åtkomst till samma statiskt allokerade lagring, är motsvarande volym monterings alternativ för lagrings bindningar för IoT följande: `/home/input`Är sökvägen till den plats där volymen är tillgänglig i behållaren.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro har också en inbyggd `StorageClass` anropad `ase-node-local` som använder ett datadisk utrymme som är kopplat till Kubernetes-noden. Detta `StorageClass` stöder dynamisk etablering. Du kan skapa en `StorageClass` referens i pod-program och ett PV skapas automatiskt åt dig. Mer information finns i Kubernetes- [instrumentpanelen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) för att fråga efter `ase-node-local StorageClass` .

![Inbyggd lagrings klass i Kubernetes-instrumentpanelen](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Mer information finns i [distribuera ett tillstånds känsligt program via dynamisk etablering på Azure Stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Välj lagrings typ

Du kan behöva välja lagrings typ beroende på vilken arbets belastning du distribuerar. 

- Om du vill ha `ReadWriteMany` åtkomst läge för din `PersistentVolumes` plats där volymerna monteras som skrivskyddade av många noder, använder du statisk etablering för SMB/NFS-resurserna.

- Om de program som du distribuerar har ett krav på POSIX-kompatibilitet, till exempel program som MongoDB, PostgreSQL, MySQL eller Prometheus, använder du den inbyggda StorageClass. Åtkomst lägena är `ReadWriteOnce` eller volymen monteras med en enda nod som Read-Write. 


Mer information om åtkomst lägen finns i [Kubernetes Volumes Access mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Nästa steg

Information om hur du kan etablera en statiskt `PersistentVolume` , finns i:

- [Distribuera ett tillstånds känsligt program via statisk etablering på Azure Stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Information om hur du kan etablera en `StorageClass` , finns i:

- [Distribuera ett tillstånds känsligt program via dynamisk etablering på Azure Stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
