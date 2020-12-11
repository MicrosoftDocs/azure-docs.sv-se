---
title: Storage-konfiguration
description: Förklaras konfigurations alternativ för Azure Arc Enabled Data Services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107268"
---
# <a name="storage-configuration"></a>Storage-konfiguration

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes lagrings koncept

Kubernetes tillhandahåller ett abstraktions lager för infrastruktur över den underliggande tekniska stacken för virtualisering (valfritt) och maskin vara. Hur Kubernetes sammanfattar lagringen genom **[lagrings klasser](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. Vid tidpunkten för etablering av en POD kan du ange en lagrings klass som ska användas för varje volym. Vid den tidpunkt då Pod etableras, anropas lagrings klassens **[etablerings](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** plats för att etablera lagringen och en **[permanent volym](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** skapas på den etablerade lagringen och Pod monteras sedan på den permanenta volymen av ett **[beständigt volym anspråk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes är ett sätt för providers för lagrings infrastruktur att anslutas till driv rutiner (kallas även "addons") som utökar Kubernetes. Lagrings tillägg måste vara kompatibla med **[behållar lagrings gränssnittet standard](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Det finns dussin tals tillägg som du hittar i den här icke-slutgiltiga **[listan över CSI-drivrutiner](https://kubernetes-csi.github.io/docs/drivers.html)**. Vilken CSI-drivrutin du använder beror på faktorer, till exempel om du kör i en molnbaserad, hanterad Kubernetes-tjänst eller vilken OEM-leverantör du använder för maskin varan.

Du kan se vilka lagrings klasser som har kon figurer ATS i ditt Kubernetes-kluster genom att köra det här kommandot:

```console
kubectl get storageclass
```

Exempel på utdata från ett Azure Kubernetes service-kluster (AKS):

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Du kan få information om en lagrings klass genom att köra det här kommandot:

```console
kubectl describe storageclass/<storage class name>
```

Exempel:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Du kan se de allokerade permanenta volymerna och beständiga volym anspråk genom att köra följande kommandon:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Exempel på hur du visar beständiga volymer:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Exempel på att Visa beständiga volym anspråk:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Faktorer att tänka på när du väljer lagrings konfiguration

Att välja rätt lagrings klass är viktigt för data återhämtning och prestanda. Om du väljer fel lagrings klass kan du ge dina data risk för total data förlust vid ett maskin varu fel eller orsaka mindre optimala prestanda.

Det finns vanligt vis två typer av lagring:

- **Lokal lagring** – lagring som har allokerats på lokala hård diskar på en specifik nod. Den här typen av lagring kan vara idealisk vad gäller prestanda, men kräver särskilt design av dataredundans genom att replikera data mellan flera noder.
- **Fjärran sluten, delad lagring** – lagrings utrymme som har allokerats på en fjärran sluten lagrings enhet, till exempel en San-, NAS-eller moln lagrings tjänst som EBS eller Azure Files. Den här typen av lagring tillhandahåller vanligt vis dataredundans automatiskt, men är inte lika snabb som lokal lagring kan vara.

> [!NOTE]
> Om du använder NFS måste du nu ange allowRunAsRoot till true i din distributions profil fil innan du distribuerar Azure Arc-datakontrollanten.

### <a name="data-controller-storage-configuration"></a>Konfiguration av data styrenhets lagring

Vissa tjänster i Azure-bågen för data tjänster är beroende av att konfigureras för att använda fjärran sluten delad lagring eftersom tjänsterna inte har möjlighet att replikera data. Dessa tjänster finns i samlingen med datacontroller-poddar:

|**Tjänst**|**Beständiga volym anspråk**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**SQL-instans för kontrollant**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Styrenhets-API-tjänst**|`<namespace>/data-controller`|

När data styrenheten är etablerad anges den lagrings klass som ska användas för var och en av dessa permanenta volymer genom att antingen skicka--Storage-klassen | -SC-parameter till `azdata arc dc create` kommandot eller genom att ange lagrings klasserna i control.jspå den distributions mal len som används.

De mallar för distribution som anges i rutan har en standard lagrings klass som är lämplig för mål miljön, men den kan åsidosättas under distributionen. Se de detaljerade stegen för att ändra [distributions profilen](create-data-controller.md) för att ändra lagrings klass konfigurationen för datakontrollantens poddar vid distributions tillfället.

Om du ställer in lagrings klassen med hjälp av--Storage-Class | -SC-parameter som lagrings klassen ska användas för både logg-och data lagrings klasser. Om du ställer in lagrings klasser i mallen för distributions mal len kan du ange olika lagrings klasser för loggar och data.

Viktiga faktorer att tänka på när du väljer en lagrings klass för datakontrollanten poddar:

- Du **måste** använda en fjärran sluten, delad lagrings klass för att säkerställa data hållbarheten och så att om en POD eller nod översätts som när Pod har säkerhetskopierats kan den ansluta igen till den permanenta volymen.
- Data som skrivs till Controller SQL-instansen, Metrics DB och logs DB är vanligt vis ganska låga och inte känsliga för svars tider, vilket innebär att Ultra-snabba prestanda lagring inte är kritiskt. Om du har användare som ofta använder Grafana-och Kibana-gränssnitten och du har ett stort antal databas instanser kan användarna få möjlighet att snabbare utföra lagringen.
- Den lagrings kapacitet som krävs är en variabel med antalet databas instanser som du har distribuerat eftersom loggar och mått samlas in för varje databas instans. Data bevaras i loggarna och måtten DB i två (2) veckor innan de rensas. 
- Att ändra lagrings klassens post distribution är svårt, inte dokumenterat och stöds inte. Se till att välja lagrings klassen korrekt vid distributions tiden.

> [!NOTE]
> Om ingen lagrings klass anges används standard lagrings klassen. Det kan bara finnas en standard lagrings klass per Kubernetes-kluster. Du kan [ändra standard lagrings klass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Konfiguration av databas instans lagring

Varje databas instans har data, loggar och säkerhets kopierings beständiga volymer. Du kan ange lagrings klasserna för dessa permanenta volymer vid distributions tiden. Om ingen lagrings klass anges används standard lagrings klassen.

När du skapar en instans med antingen `azdata arc sql mi create` eller `azdata arc postgres server create` , finns det två parametrar som kan användas för att ange lagrings klasser:

> [!NOTE]
> Några av dessa parametrar är under utveckling och kommer att bli tillgängliga på `azdata arc sql mi create` och `azdata arc postgres server create` i kommande versioner.

|Parameter namn, kort namn|Används för|
|---|---|
|`--storage-class-data`, `-scd`|Används för att ange lagrings klassen för alla datafiler inklusive transaktionsloggfiler|
|`--storage-class-logs`, `-scl`|Används för att ange lagrings klassen för alla loggfiler|
|`--storage-class-data-logs`, `-scdl`|Används för att ange lagrings klassen för databas transaktions logg filen. **Obs!: inte tillgängligt än.**|
|`--storage-class-backups`, `-scb`|Används för att ange lagrings klassen för alla säkerhetskopierade filer. **Obs!: inte tillgängligt än.**|

I tabellen nedan visas en lista över Sök vägarna i den Azure SQL Managed instance-behållare som är mappad till den permanenta volymen för data och loggar:

|Parameter namn, kort namn|Sökväg inuti MSSQL – Miaa container|Beskrivning|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Innehåller kataloger för MSSQL-installationen och andra system processer. MSSQL-katalogen innehåller standard data (inklusive transaktions loggar), fel logg & säkerhets kopierings kataloger|
|`--storage-class-logs`, `-scl`|/var/log|Innehåller kataloger som lagrar konsol utdata (STDERR, STDOUT), annan loggnings information för processer i behållaren|

I tabellen nedan visas en lista över Sök vägarna inuti PostgreSQL-instansen som är mappad till den permanenta volymen för data och loggar:

|Parameter namn, kort namn|Sökväg inuti postgres-behållare|Beskrivning|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Innehåller data-och logg kataloger för postgres-installationen|
|`--storage-class-logs`, `-scl`|/var/log|Innehåller kataloger som lagrar konsol utdata (STDERR, STDOUT), annan loggnings information för processer i behållaren|

Varje databas instans kommer att ha en separat permanent volym för datafiler, loggar och säkerhets kopieringar. Det innebär att det kommer att finnas en separation av I/O för var och en av dessa typer av filer, beroende på hur volym etableringen etablerar lagring. Varje databas instans har sina egna beständiga volym anspråk och beständiga volymer.

Om det finns flera databaser på en specifik databas instans, kommer alla databaser använda samma permanenta volym anspråk, beständig volym och lagrings klass. Alla säkerhets kopior – både säkerhets kopior av differentiella loggar och fullständiga säkerhets kopieringar använder samma beständiga volym anspråk och beständiga volymer. Beständiga volym anspråk för databas instansens poddar visas nedan:

|**Instans**|**Beständiga volym anspråk**|
|---|---|
|**Hanterad Azure SQL-instans**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure Database för PostgreSQL-instans**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL-storskalig**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Ordnings tal sträcker sig från 0 till W där W är antalet arbetare)*|

Viktiga faktorer att tänka på när du väljer en lagrings klass för databas instansen poddar:

- Databas instanser kan distribueras i antingen ett enda Pod-mönster eller ett flera Pod-mönster. Ett exempel på ett enda Pod-mönster är en Developer-instans av en hanterad Azure SQL-instans eller en allmän pris nivå för Azure SQL. Ett exempel på ett flera Pod-mönster är en affärs kritisk pris nivå med hög tillgänglighet för Azure SQL-hanterad instans. (Obs! pris nivåer ingår i utvecklingen och är inte tillgängliga för kunder ännu.)  Databas instanser som distribueras med det enskilda Pod-mönstret **måste** använda en fjärran sluten, delad lagrings klass för att säkerställa data hållbarheten och så att om en POD eller nod översätts som när Pod har säkerhetskopierats kan den ansluta igen till den permanenta volymen. En Azure SQL-hanterad instans med hög tillgänglighet använder däremot alltid i tillgänglighets grupper för att replikera data från en instans till en annan antingen synkront eller asynkront. Särskilt i de fall då data replikeras synkront finns det alltid flera kopior av data, vanligt vis tre (3) kopior. Därför är det möjligt att använda lokala lagrings klasser eller fjärranslutna lagrings klasser för data-och loggfiler. Om du använder lokal lagring, bevaras fortfarande data även om det finns en misslyckad pod, nod eller lagrings maskin vara. Med tanke på den här flexibiliteten kan du välja att använda lokal lagring för bättre prestanda.
- Databas prestanda är i stort sett en funktion i I/O-dataflödet för en specifik lagrings enhet. Om databasen är tungt läst eller tungt beskrivet, bör du välja en lagrings klass med maskin vara som är avsedd för den typen av arbets belastning. Om din databas exempelvis används för skrivningar kan du välja lokal lagring med RAID 0. Om databasen huvudsakligen används för läsning av en liten mängd "frekventa data", men det finns en stor övergripande lagrings volym av kall data, kan du välja en SAN-enhet som kan hantera lagrings utrymme. Att välja rätt lagrings klass skiljer sig från att välja den typ av lagring som du vill använda för alla databaser.
- Om du använder en lokal lagrings volym Provisioning kontrollerar du att de lokala volymer som är etablerade för data, loggar och säkerhets kopieringar varje landning på olika underliggande lagrings enheter för att undvika konkurrens på disk-I/O. Operativ systemet bör också finnas på en volym som monteras på en separat disk (er). Detta är i princip samma vägledning som skulle följas för en databas instans på fysisk maskin vara.
- Eftersom alla databaser på en specifik instans delar ett beständigt volym anspråk och permanent volym bör du se till att inte samplacera upptagna databas instanser på samma databas instans. Om möjligt kan du dela upp upptagna databaser på sina egna databas instanser för att undvika I/O-konkurrens. Du kan också använda Node-etiketten för att rikta in databas instanser på olika noder för att distribuera den övergripande I/O-trafiken över flera noder. Om du använder virtualisering bör du se till att distribuera I/O-trafik inte bara på nodnivå, utan även den kombinerade I/O-aktiviteten som sker av alla noder på en specifik fysisk värd.

## <a name="estimating-storage-requirements"></a>Beräkna lagrings kraven
Varje Pod som innehåller tillstånds känsliga data använder två permanenta volymer i den här versionen – en permanent volym för data och en annan permanent volym för loggar. Tabellen nedan visar antalet beständiga volymer som krävs för en enskild datakontrollant, Azure SQL-hanterad instans, Azure Database for PostgreSQL instans och storskalig instans av Azure PostgreSQL:

|Resurstyp|Antal tillstånds känsliga poddar|Antal beständiga volymer som krävs|
|---|---|---|
|Data Controller|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Hanterad Azure SQL-instans|1|2|
|Azure Database for PostgreSQL instans|1| 2|
|Azure PostgreSQL-storskalig|1 + w (W = antal arbetare)|2 * (1 + W)|

Tabellen nedan visar det totala antalet permanenta volymer som krävs för en exempel distribution:

|Resurstyp|Antal instanser|Antal beständiga volymer som krävs|
|---|---|---|
|Data Controller|1|4 * 2 = 8|
|Hanterad Azure SQL-instans|5|5 * 2 = 10|
|Azure Database for PostgreSQL instans|5| 5 * 2 = 10|
|Azure PostgreSQL-storskalig|2 (antal arbetare = 4 per instans)|2 * 2 * (1 + 4) = 20|
|***Totalt antal beständiga volymer** _||8 + 10 + 10 + 20 = 48|

Den här beräkningen kan användas för att planera lagringen för ditt Kubernetes-kluster baserat på lagrings etableringen eller miljön. Om den lokala lagrings platsen till exempel används för ett Kubernetes-kluster med fem (5) noder för exempel distributionen ovan varje nod kräver minst lagrings utrymme för 10 permanenta volymer. När du konfigurerar ett Azure Kubernetes service-kluster (AKS) med fem (5) noder, plockar du en lämplig VM-storlek för Node-poolen så att 10 data diskar kan anslutas är kritiskt. Mer information om hur du kan ändra storlek på noderna för lagrings behov för AKS-noder finns [här](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Välja rätt lagrings klass

### <a name="on-premises-and-edge-sites"></a>Lokala och gräns platser

Microsoft och dess OEM-, OS-och Kubernetes-partner arbetar på ett certifierings program för Azure Arc Data Services. Det här programmet ger kunderna jämförbara test resultat från ett verktyg för certifierings testning. Testerna utvärderar funktions kompatibilitet, stress testnings resultat och prestanda och skalbarhet. Vart och ett av dessa test resultat indikerar det operativ system som används, Kubernetes-distribution som används, HW-tillägget används och de lagrings klasser som används. På så sätt kan kunderna välja den bästa lagrings klassen, operativ systemet, Kubernetes-distributionen och maskin varan för deras krav. Mer information om det här programmet och de första test resultaten visas närmare den allmänna tillgängligheten för Azure båg Data Services.

#### <a name="public-cloud-managed-kubernetes-services"></a>Offentligt moln, hanterade Kubernetes-tjänster

Vi kan göra följande rekommendationer för offentliga molnbaserade, hanterade Kubernetes-tjänster:

|Offentlig moln tjänst|Rekommendation|
|---|---|
|_ *Azure Kubernetes service (AKS)**|Azure Kubernetes service (AKS) har två typer av lagrings Azure Files och Azure Managed Disks. Varje typ av lagring har två pris-/prestanda nivåer – standard (HDD) och Premium (SSD). Därför är de fyra lagrings klasserna i AKS `azurefile` (Azure Files standard nivå), `azurefile-premium` (Azure Files Premium nivå), `default` (Azure disks standard nivå) och `managed-premium` (Azure disks Premium-nivå). Standard lagrings klassen är `default` (standard nivån för Azure-diskar). Det finns betydande **[pris skillnader](https://azure.microsoft.com/en-us/pricing/details/storage/)** mellan de typer och nivåer som bör delas in i ditt beslut. För produktions arbets belastningar med höga prestanda krav rekommenderar vi att du använder `managed-premium` för alla lagrings klasser. För arbets belastningar för utveckling/testning, bevis på koncept osv. där kostnad är ett övervägande `azurefile` är det minst dyra alternativet. Alla fyra av alternativen kan användas för situationer som kräver fjärran sluten, delad lagring som alla nätverksanslutna lagrings enheter i Azure. Läs mer om [AKS-lagring](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Amazon: s elastiska Kubernetes-tjänst har en primär lagrings klass baserad på [EBS CSI-lagringsenheten](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Detta rekommenderas för produktions arbets belastningar. Det finns en ny lagrings driv rutin – [EFS CSI-lagringsenhet](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) – som kan läggas till i ett EKS-kluster, men den är för närvarande i ett beta steg och kan komma att ändras. Även om AWS säger att den här lagrings driv rutinen stöds för produktion rekommenderar vi inte att du använder den eftersom den fortfarande är i beta syfte och kan komma att ändras. Lagrings klassen EBS är standard och kallas `gp2` . Läs mer om [EKS-lagring](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) har bara en lagrings klass `standard` som kallas, som används för [GCE-beständiga diskar](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Det är bara det enda, det är också standardvärdet. Även om det finns en [lokal, statisk volym](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) för GKE som du kan använda med direktansluten SSD rekommenderar vi inte att du använder den eftersom den inte hanteras eller stöds av Google. Läs mer om [GKE-lagring](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
