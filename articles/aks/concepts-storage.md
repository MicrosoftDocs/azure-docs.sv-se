---
title: Koncept – lagring i Azure Kubernetes Services (AKS)
description: Lär dig mer om lagring i Azure Kubernetes service (AKS), inklusive volymer, beständiga volymer, lagrings klasser och anspråk
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127965"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Lagrings alternativ för program i Azure Kubernetes service (AKS)

Program som körs i Azure Kubernetes service (AKS) kan behöva lagra och hämta data. För vissa program arbets belastningar kan data lagringen använda lokal, snabb lagring på den nod som inte längre behövs när poddar tas bort. Andra program arbets belastningar kan kräva lagring som finns kvar på Fler vanliga data volymer i Azure-plattformen. Flera poddar kan behöva dela samma data volymer eller återkoppla data volymer om Pod har schemalagts på nytt på en annan nod. Slutligen kan du behöva mata in känslig data-eller program konfigurations information i poddar.

![Lagrings alternativ för program i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/aks-storage-options.png)

Den här artikeln beskriver de viktigaste begreppen som tillhandahåller lagring för dina program i AKS:

- [Volymer](#volumes)
- [Beständiga volymer](#persistent-volumes)
- [Lagrings klasser](#storage-classes)
- [Beständiga volymanspråk](#persistent-volume-claims)

## <a name="volumes"></a>Volymer

Program behöver ofta kunna lagra och hämta data. Eftersom Kubernetes vanligt vis behandlar enskilda poddar som tillfälliga, disponibla resurser, är olika metoder tillgängliga för program att använda och bevara data vid behov. En *volym* är ett sätt att lagra, hämta och bevara data över poddar och genom programmets livs cykel.

Traditionella volymer för lagring och hämtning av data skapas som Kubernetes-resurser som backas upp av Azure Storage. Du kan manuellt skapa dessa data volymer som ska tilldelas poddar direkt eller låta Kubernetes skapa dem automatiskt. Dessa data volymer kan använda Azure-diskar eller Azure Files:

- *Azure-diskar* kan användas för att skapa en Kubernetes *DataDisk* -resurs. Diskar kan använda Azure Premium Storage, som backas upp av högpresterande SSD eller Azure standard Storage, som backas upp av vanliga hård diskar. Använd Premium Storage för de flesta arbets belastningar för produktion och utveckling. Azure-diskar monteras som *ReadWriteOnce*, så de är bara tillgängliga för en enda pod. För lagrings volymer som kan nås av flera poddar samtidigt använder du Azure Files.
- *Azure Files* kan användas för att montera en SMB 3,0-resurs som backas upp av ett Azure Storage konto till poddar. Med filer kan du dela data över flera noder och poddar. Filer kan använda Azure standard Storage som backas upp av vanliga hård diskar, eller Azure Premium-lagring, som backas upp av SSD med höga prestanda.

I Kubernetes kan volymer representera mer än bara en traditionell disk där information kan lagras och hämtas. Kubernetes-volymer kan också användas som ett sätt att mata in data i en POD för användning av behållarna. Vanliga ytterligare volym typer i Kubernetes är:

- *emptyDir* – den här volymen används ofta som tillfälligt utrymme för en pod. Alla behållare i en POD kan komma åt data på volymen. Data som skrivs till den här volym typen behålls bara för livs längd för Pod – när Pod tas bort tas volymen bort. Den här volymen använder vanligt vis den underliggande disk lagringen för den lokala noden, men den kan också bara finnas i nodens minne.
- *Secret* – den här volymen används för att mata in känsliga data i poddar, till exempel lösen ord. Du skapar först en hemlighet med Kubernetes-API: et. När du definierar din POD eller distribution kan du begära en speciell hemlighet. Hemligheter anges bara för noder som har en schemalagd Pod som kräver det, och hemligheten lagras i *tmpfs*, som inte skrivs till disk. När den sista Pod på en nod som kräver en hemlighet tas bort, tas hemligheten bort från nodens tmpfs. Hemligheter lagras inom ett angivet namn område och kan endast nås av poddar inom samma namnrymd.
- *configMap* – den här volym typen används för att mata in egenskaper för nyckel värdes par i poddar, till exempel program konfigurations information. I stället för att definiera program konfigurations information i en behållar avbildning kan du definiera den som en Kubernetes-resurs som enkelt kan uppdateras och tillämpas på nya instanser av poddar när de distribueras. Precis som med en hemlighet skapar du först en ConfigMap med hjälp av Kubernetes-API: et. Den här ConfigMap kan sedan begäras när du definierar en POD eller distribution. ConfigMaps lagras inom ett angivet namn område och kan bara användas av poddar inom samma namnrymd.

## <a name="persistent-volumes"></a>Beständiga volymer

Volymer som definieras och skapas som en del av Pod-livscykeln finns bara tills Pod har tagits bort. Poddar förväntar sig ofta att lagringen ska vara kvar om en POD omplaneras på en annan värd under en underhålls händelse, särskilt i StatefulSets. En *beständig volym* (PV) är en lagrings resurs som skapas och hanteras av Kubernetes-API: et som kan finnas utanför en enskild Pod livs längd.

Azure-diskar eller-filer används för att tillhandahålla PersistentVolume. Som vi noterade i föregående avsnitt om volymer, bestäms ofta valet av diskar eller filer genom behovet av samtidig åtkomst till data eller prestanda nivå.

![Beständiga volymer i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volumes.png)

En PersistentVolume kan skapas *statiskt* av en kluster administratör eller *dynamiskt* skapas av Kubernetes API-servern. Om en pod är schemalagd och begär lagring som inte är tillgänglig för tillfället, kan Kubernetes skapa den underliggande lagringen av Azure-disk eller filer och koppla den till pod. Dynamisk etablering använder en *StorageClass* för att identifiera vilken typ av Azure-lagring som måste skapas.

## <a name="storage-classes"></a>Lagrings klasser

Om du vill definiera olika lagrings nivåer, till exempel Premium och standard, kan du skapa en *StorageClass*. StorageClass definierar också *reclaimPolicy*. Den här reclaimPolicy styr beteendet för den underliggande Azure Storage-resursen när Pod tas bort och den permanenta volymen kanske inte längre krävs. Den underliggande lagrings resursen kan tas bort eller sparas för användning med en framtida pod.

I AKS skapas fyra initialer `StorageClasses` för klustret med hjälp av plugin-program för in-Tree-lagring:

- `default` – Använder Azure StandardSSD Storage för att skapa en hanterad disk. Med anspråks principen ser du till att den underliggande Azure-disken tas bort när den permanenta volym som används tas bort.
- `managed-premium` – Använder Azure Premium Storage för att skapa en hanterad disk. Reclaim-principen igen säkerställer att den underliggande Azure-disken tas bort när den permanenta volym som användes är borttagen.
- `azurefile` – Använder Azure standard Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.
- `azurefile-premium` – Använder Azure Premium Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.

För kluster som använder det nya [CSI (container Storage Interface) externt plugin-program (för hands version) skapas följande ytterligare `StorageClasses` :
- `managed-csi` – Använder Azure StandardSSD lokalt redundant lagring (LRS) för att skapa en hanterad disk. Med anspråks principen ser du till att den underliggande Azure-disken tas bort när den permanenta volym som används tas bort. Lagrings klassen konfigurerar även de permanenta volymerna som ska kunna utökas, du behöver bara redigera det beständiga volym anspråket med den nya storleken.
- `managed-csi-premium` – Använder Azure Premium lokalt redundant lagring (LRS) för att skapa en hanterad disk. Reclaim-principen igen säkerställer att den underliggande Azure-disken tas bort när den permanenta volym som användes är borttagen. På samma sätt tillåter den här lagrings klassen att permanenta volymer expanderas.
- `azurefile-csi` – Använder Azure standard Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.
- `azurefile-csi-premium` – Använder Azure Premium Storage för att skapa en Azure-filresurs. Reclaim-principen garanterar att den underliggande Azure-filresursen tas bort när den permanenta volym som användes är borttagen.

Om ingen StorageClass har angetts för en beständig volym används standard-StorageClass. Var försiktig när du begär beständiga volymer så att de använder rätt lagrings utrymme som du behöver. Du kan skapa en StorageClass för ytterligare behov med hjälp av `kubectl` . I följande exempel används Premium-Managed Disks och anger att den underliggande Azure-disken ska *behållas* när Pod tas bort:

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

En PersistentVolumeClaim begär antingen disk-eller fil lagring för en viss StorageClass, åtkomst läge och storlek. Kubernetes-API-servern kan dynamiskt etablera den underliggande lagrings resursen i Azure om det inte finns någon befintlig resurs som uppfyller anspråket som baseras på den definierade StorageClass. Pod-definitionen innehåller volym monteringen när volymen har anslutits till pod.

![Beständiga volym anspråk i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

En PersistentVolume är *kopplad* till en PersistentVolumeClaim när en tillgänglig lagrings resurs har tilldelats till den Pod som begär den. Det finns en 1:1-mappning av beständiga volymer till anspråk.

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

När du skapar en POD-definition anges beständigt volym anspråk för att begära önskad lagring. Du anger också *volumeMount* för dina program för att läsa och skriva data. Följande exempel på YAML-manifest visar hur det tidigare beständiga volym anspråket kan användas för att montera en volym på */mnt/Azure*:

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
