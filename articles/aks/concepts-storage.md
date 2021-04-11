---
title: Koncept – lagring i Azure Kubernetes Services (AKS)
description: Lär dig mer om lagring i Azure Kubernetes service (AKS), inklusive volymer, beständiga volymer, lagrings klasser och anspråk
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105790"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Lagrings alternativ för program i Azure Kubernetes service (AKS)

Program som körs i Azure Kubernetes service (AKS) kan behöva lagra och hämta data. Vissa program arbets belastningar kan använda lokal, snabb lagring på onödiga, tömda noder, andra kräver lagring som finns kvar på Fler vanliga data volymer i Azure-plattformen. 

Flera poddar kan behöva:
* Dela samma data volymer. 
* Återanslut data volymer om Pod har schemalagts om på en annan nod. 

Slutligen kan du behöva mata in känslig data-eller program konfigurations information i poddar.

Den här artikeln beskriver de viktigaste begreppen som tillhandahåller lagring för dina program i AKS:

- [Volymer](#volumes)
- [Beständiga volymer](#persistent-volumes)
- [Lagrings klasser](#storage-classes)
- [Beständiga volymanspråk](#persistent-volume-claims)

![Lagrings alternativ för program i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Volymer

Kubernetes behandlar vanligt vis enskilda poddar som tillfälliga och disponibla resurser. Program har olika metoder som är tillgängliga för att använda och spara data. En *volym* är ett sätt att lagra, hämta och bevara data över poddar och genom programmets livs cykel.

Traditionella volymer skapas som Kubernetes-resurser som backas upp av Azure Storage. Du kan manuellt skapa data volymer som ska tilldelas poddar direkt eller låta Kubernetes skapa dem automatiskt. Data volymer kan använda Azure-diskar eller Azure Files.

### <a name="azure-disks"></a>Azure-diskar

Använd *Azure-diskar* för att skapa en Kubernetes *DataDisk* -resurs. Diskar kan använda:
* Azure Premium Storage, som backas upp av SSD med höga prestanda, eller 
* Azure standard Storage, som backas upp av vanliga hård diskar. 

> [!TIP]
>Använd Premium Storage för de flesta arbets belastningar för produktion och utveckling. 

Eftersom Azure-diskar monteras som *ReadWriteOnce* är de bara tillgängliga för en enda pod. För lagrings volymer som kan nås av flera poddar samtidigt använder du Azure Files.

### <a name="azure-files"></a>Azure Files
Använd *Azure Files* för att montera en SMB 3,0-resurs som backas upp av ett Azure Storage konto till poddar. Med filer kan du dela data över flera noder och poddar och använda:
* Azure Premium Storage, som backas upp av SSD med höga prestanda, eller 
* Azure standard Storage som backas upp av vanliga hård diskar.

### <a name="volume-types"></a>Volym typer
Kubernetes-volymer representerar mer än bara en traditionell disk för lagring och hämtning av information. Kubernetes-volymer kan också användas som ett sätt att mata in data i en POD för användning av behållarna. 

Vanliga volym typer i Kubernetes är:

#### <a name="emptydir"></a>emptyDir

Används ofta som tillfälligt utrymme för en pod. Alla behållare i en POD kan komma åt data på volymen. Data som skrivs till den här volym typen finns kvar endast för livs längd för pod. Volymen tas bort när du tar bort pod. Den här volymen använder vanligt vis den underliggande disk lagringen för den lokala noden, men den kan också bara finnas i nodens minne.

#### <a name="secret"></a>hemlighet

Du kan använda *hemliga* volymer för att mata in känsliga data i poddar, till exempel lösen ord. 
1. Skapa en hemlighet med Kubernetes-API: et. 
1. Definiera din POD eller distribution och begär en speciell hemlighet. 
    * Hemligheter ges endast till noder med en schemalagd Pod som kräver dem.
    * Hemligheten lagras i *tmpfs*, som inte skrivs till disk. 
1. När du tar bort den sista Pod på en nod som kräver en hemlighet, tas hemligheten bort från nodens tmpfs. 
   * Hemligheter lagras inom ett angivet namn område och kan endast nås av poddar inom samma namnrymd.

#### <a name="configmap"></a>configMap

Du kan använda *configMap* för att mata in egenskaper för nyckel värdes par i poddar, till exempel program konfigurations information. Definiera program konfigurations information som en Kubernetes-resurs, enkelt uppdaterad och tillämpas på nya instanser av poddar när de distribueras. 

Som att använda en hemlighet:
1. Skapa en ConfigMap med hjälp av Kubernetes-API: et. 
1. Begär ConfigMap när du definierar en POD eller distribution. 
   * ConfigMaps lagras inom ett angivet namn område och kan bara användas av poddar inom samma namnrymd.

## <a name="persistent-volumes"></a>Beständiga volymer

Volymer som definieras och skapats som en del av Pod-livscykeln finns bara tills du tar bort pod. Poddar förväntar sig ofta att lagringen ska vara kvar om en POD omplaneras på en annan värd under en underhålls händelse, särskilt i StatefulSets. En *beständig volym* (PV) är en lagrings resurs som skapas och hanteras av Kubernetes-API: et som kan finnas utanför en enskild Pod livs längd.

Du kan använda Azure-diskar eller-filer för att tillhandahålla PersistentVolume. Som anges i avsnittet [volymer](#volumes) , bestäms ofta valet av diskar eller filer genom behovet av samtidig åtkomst till data eller prestanda nivå.

![Beständiga volymer i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volumes.png)

En PersistentVolume kan skapas *statiskt* av en kluster administratör eller *dynamiskt* skapas av Kubernetes API-servern. Om en pod har schemalagts och begär Anden för närvarande inte är tillgängligt, kan Kubernetes skapa den underliggande lagringen för Azure disk eller filer och koppla den till pod. Dynamisk etablering använder en *StorageClass* för att identifiera vilken typ av Azure-lagring som måste skapas.

## <a name="storage-classes"></a>Lagrings klasser

Om du vill definiera olika lagrings nivåer, till exempel Premium och standard, kan du skapa en *StorageClass*. 

StorageClass definierar också *reclaimPolicy*. När du tar bort Pod och den permanenta volymen inte längre behövs, styr reclaimPolicy beteendet för den underliggande Azure Storage-resursen. Den underliggande lagrings resursen kan antingen tas bort eller sparas för användning med en framtida pod.

I AKS skapas fyra initialer `StorageClasses` för klustret med hjälp av plugin-program för in-Tree-lagring:

| Behörighet | Anledning |
|---|---|
| `default` | Använder Azure StandardSSD Storage för att skapa en hanterad disk. Med anspråks principen ser du till att den underliggande Azure-disken tas bort när den permanenta volym som används tas bort. |
| `managed-premium` | Använder Azure Premium Storage för att skapa en hanterad disk. Reclaim-principen igen säkerställer att den underliggande Azure-disken tas bort när den permanenta volym som användes är borttagen. |
| `azurefile` | Använder Azure standard Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen. |
| `azurefile-premium` | Använder Azure Premium Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.|

För kluster som använder det nya [CSI (container Storage Interface) externt plugin-program (för hands version) skapas följande extra `StorageClasses` :

| Behörighet | Anledning |
|---|---|
| `managed-csi` | Använder Azure StandardSSD lokalt redundant lagring (LRS) för att skapa en hanterad disk. Med anspråks principen ser du till att den underliggande Azure-disken tas bort när den permanenta volym som används tas bort. Lagrings klassen konfigurerar även de permanenta volymerna som ska kunna utökas, du behöver bara redigera det beständiga volym anspråket med den nya storleken. |
| `managed-csi-premium` | Använder Azure Premium lokalt redundant lagring (LRS) för att skapa en hanterad disk. Reclaim-principen igen säkerställer att den underliggande Azure-disken tas bort när den permanenta volym som användes är borttagen. På samma sätt tillåter den här lagrings klassen att permanenta volymer expanderas. |
| `azurefile-csi` | Använder Azure standard Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen. |
| `azurefile-csi-premium` | Använder Azure Premium Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.|

Om du inte anger en StorageClass för en beständig volym används standard-StorageClass. Se till att volymer använder rätt lagring som du behöver när du begär beständiga volymer. 

Du kan skapa en StorageClass för ytterligare behov med hjälp av `kubectl` . I följande exempel används Premium-Managed Disks och anger att den underliggande Azure-disken ska *behållas* när du tar bort pod:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS synkroniserar standard lagrings klasserna och skriver över alla ändringar du gör i dessa lagrings klasser.

## <a name="persistent-volume-claims"></a>Beständiga volymanspråk

En PersistentVolumeClaim begär antingen disk-eller fil lagring för en viss StorageClass, åtkomst läge och storlek. Kubernetes-API-servern kan etablera den underliggande Azure Storage-resursen dynamiskt om ingen befintlig resurs kan uppfylla anspråket baserat på den definierade StorageClass. 

Pod-definitionen innehåller volym monteringen när volymen har anslutits till pod.

![Beständiga volym anspråk i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

När en tillgänglig lagrings resurs har tilldelats Pod begär ande lagring, är PersistentVolume *kopplad* till en PersistentVolumeClaim. Beständiga volymer är 1:1 mappade till anspråk.

Följande exempel på YAML-manifest visar ett beständigt volym anspråk som använder den *hanterade Premium-* StorageClass och begär en disk *5Gi* i storlek:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

När du skapar en POD-definition anger du även:
* Beständiga volym anspråk för att begära önskad lagring. 
* *VolumeMount* för dina program för att läsa och skriva data. 

Följande exempel på YAML-manifest visar hur det tidigare beständiga volym anspråket kan användas för att montera en volym på */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

För att montera en volym i en Windows-behållare anger du enhets beteckning och sökväg. Exempel:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Nästa steg

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

Information om hur du skapar dynamiska och statiska volymer som använder Azure-diskar eller Azure Files finns i följande instruktions artiklar:

- [Skapa en statisk volym med hjälp av Azure-diskar][aks-static-disks]
- [Skapa en statisk volym med hjälp av Azure Files][aks-static-files]
- [Skapa en dynamisk volym med Azure-diskar][aks-dynamic-disks]
- [Skapa en dynamisk volym med hjälp av Azure Files][aks-dynamic-files]

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identitet][aks-concepts-identity]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
