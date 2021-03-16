---
title: Noder och pooler i Azure Batch
description: Lär dig mer om Compute-noder och pooler och hur de används i ett Azure Batch arbets flöde från en utvecklings synpunkt.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 7d4c2d45849deb011498efe4c8a1ae91724b9acd
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563903"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Noder och pooler i Azure Batch

I ett Azure Batch arbets flöde är en *Compute-nod* (eller *nod*) en virtuell dator som bearbetar en del av programmets arbets belastning. En *pool* är en samling av de noder som programmet ska köras på. Den här artikeln förklarar mer om noder och pooler, tillsammans med överväganden när du skapar och använder dem i ett Azure Batch-arbetsflöde.

## <a name="nodes"></a>Noder

En nod är en virtuell Azure-dator (VM) eller en virtuell moln tjänst som är dedikerad för bearbetning av en del av programmets arbets belastning. Storleken på en nod avgör antalet CPU-kärnor, minneskapaciteten och storleken på det lokala filsystemet som allokeras till noden.

Du kan skapa pooler för Windows- eller Linux-noder med Azure Cloud Services eller Marketplace-avbildningar för [Azure Virtual Machines](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) eller anpassade avbildningar du förbereder.

Noder kan köra alla körbara filer eller skript som stöds av nodens operativsystemmiljö, Körbara filer eller skript inkluderar \* . exe, \* . cmd, \* . bat och PowerShell-skript (för Windows) och binärfiler, Shell och Python-skript (för Linux).

Alla beräkningsnoder i Batch innehåller också:

- En fördefinierad [mappstruktur](files-and-directories.md) och associerade [miljövariabler](jobs-and-tasks.md) som är tillgängliga som referens för aktiviteterna.
- **Brandväggsinställningar** som konfigureras för att styra åtkomsten.
- [Fjärråtkomst](error-handling.md#connect-to-compute-nodes) till både Windows Remote Desktop Protocol (RDP)-och Linux-noder (Secure Shell) (om du inte [skapar poolen med fjärråtkomst inaktive rad](pool-endpoint-configuration.md)).

Som standard kan noder kommunicera med varandra, men de kan inte kommunicera med virtuella datorer som inte ingår i samma pool. Om noderna ska kunna kommunicera säkert med andra virtuella datorer, eller med ett lokalt nätverk, kan du etablera poolen [i ett undernät för ett virtuellt Azure-nätverk (VNet)](batch-virtual-network.md). När du gör det kan noderna nås via offentliga IP-adresser. Dessa offentliga IP-adresser skapas av batch och kan ändras under poolens livstid. Du kan också [skapa en pool med statiska offentliga IP-adresser](create-pool-public-ip.md) som du kontrollerar, vilket säkerställer att de inte ändras.

## <a name="pools"></a>Pooler

En pool är en samling noder som ditt program körs på.

Azure Batch-pooler baseras på den grundläggande Azure-beräkningsplattformen. De tillhandahåller storskalig allokering, programinstallation, data distribution, hälso övervakning och flexibel justering ([skalning](#automatic-scaling-policy)) av antalet datornoder i en pool.

Varje nod som läggs till i en pool tilldelas ett unikt namn och en IP-adress. När en nod tas bort från en pool försvinner alla ändringar som gjorts i operativsystemet eller filer, och dess namn och IP-adress frisläpps för framtida användning. När en nod lämnar poolen är dess livslängd över.

En pool kan endast användas av det Batch-konto som den skapades under. Ett batch-konto kan skapa flera pooler som uppfyller resurs kraven för de program som ska köras.

Poolen kan skapas manuellt eller [automatiskt av batch-tjänsten](#autopools) när du anger vilket arbete som ska utföras. När du skapar en pool kan du ange följande attribut:

- [Operativ system och version för noden](#operating-system-and-version)
- [Nodtyp och mål antal noder](#node-type-and-target)
- [Node-storlek](#node-size)
- [Princip för automatisk skalning](#automatic-scaling-policy)
- [Schemaläggningsprincip för aktiviteter](#task-scheduling-policy)
- [Kommunikations status](#communication-status)
- [Starta aktiviteter](#start-tasks)
- [Programpaket](#application-packages)
- [Konfiguration av virtuella nätverk (VNet) och brandväggar](#virtual-network-vnet-and-firewall-configuration)
- [Giltighet](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch-konton har en standardkvot som begränsar antalet kärnor i ett Batch-konto. Antalet kärnor motsvarar antalet beräkningsnoder. Du hittar standardkvoterna och instruktioner om hur du [ökar en kvot](batch-quota-limit.md#increase-a-quota) i [Kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md). Om din pool inte uppnår antalet målnoder kan det bero på kärnkvoten.

## <a name="operating-system-and-version"></a>Operativ system och version

När du skapar en batch-pool anger du konfigurationen för den virtuella Azure-datorn och den typ av operativ system som du vill köra på varje Compute-nod i poolen.

## <a name="configurations"></a>Konfigurationer

Det finns två typer av pool-konfigurationer som är tillgängliga i batch.

> [!IMPORTANT]
> Även om du kan skapa pooler med hjälp av en konfiguration, ska nya pooler konfigureras med hjälp av konfiguration av virtuell dator och inte Cloud Services konfiguration. Alla aktuella och nya batch-funktioner kommer att stödjas av konfigurations pooler för virtuella datorer. Cloud Services konfigurations grupper stöder inte alla funktioner och inga nya funktioner planeras. Du kommer inte att kunna skapa nya CloudServiceConfiguration-pooler eller lägga till nya noder i befintliga pooler [efter den 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

### <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator

Konfigurationen av den **virtuella datorn** anger att poolen består av virtuella Azure-datorer. Dessa virtuella datorer kan skapas från Linux- eller Windows-avbildningar.

[Batch Node agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-Control-gränssnittet mellan noden och batch-tjänsten. Det finns olika implementeringar av Node-agenten, som kallas SKU: er, för olika operativ system. När du skapar en pool med noder i en konfiguration av en virtuell dator måste du inte bara ange storleken på noderna och källan för avbildningarna som användes för att skapa dem, utan även **referensen till avbildningen av den virtuella datorn** och Batch-**nodagentens SKU** som ska installeras på noderna. Mer information om hur du anger dessa poolegenskaper finns i [Etablera Linux-beräkningsnoder i Azure Batch-pooler](batch-linux-nodes.md). Om du vill kan du koppla en eller flera tomma datadiskar till en pool med virtuella datorer som skapats av Microsoft Azure Marketplace-avbildningar. Alternativt kan du inkludera datadiskar i de anpassade avbildningar som används för att skapa de virtuella datorerna. När du inkluderar data diskar måste du montera och Formatera diskarna inifrån en virtuell dator för att använda dem.

### <a name="cloud-services-configuration"></a>Cloud Services konfiguration

> [!WARNING]
> Cloud Services konfigurations pooler är [föråldrade](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Använd konfigurations pooler för virtuella datorer i stället. Mer information finns i [migrera konfiguration av batch-pool från Cloud Services till virtuell dator](batch-pool-cloud-service-to-virtual-machine-configuration.md).

**Cloud Services-konfigurationen** anger att poolen består av Azure Cloud Services-noder. Cloud Services tillhandahåller endast Windows Compute-noder.

Tillgängliga operativ system för Cloud Services konfigurations-pooler visas i [Azure gäst operativ system versioner och SDK-kompatibilitet](../cloud-services/cloud-services-guestos-update-matrix.md), och tillgängliga Compute Node-storlekar anges i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). När du skapar en pool som innehåller Cloud Services noder anger du nodens storlek och dess *OS-familj* (som avgör vilka versioner av .net som installeras med operativ systemet). Cloud Services distribueras snabbare till Azure än virtuella datorer som kör Windows. Om du vill ha pooler med Windows-beräkningsnoder får du bättre distributionstid med Cloud Services.

Precis som med arbets roller i Cloud Services kan du ange en *operativ system version*. Vi rekommenderar att du anger `Latest (*)` för *operativ system versionen* så att noderna uppgraderas automatiskt och det inte finns något arbete som krävs för att hantera nya versioner. Det huvudsakliga skälet till att välja en viss operativsystemversion är att säkerställa programkompatibiliteten, så att du kan testa bakåtkompatibiliteten innan versionen uppdateras. Efter valideringen kan *operativ system versionen* för poolen uppdateras och den nya operativ system avbildningen kan installeras. Alla pågående aktiviteter avbryts och placeras i kö.

### <a name="node-agent-skus"></a>Node agent-SKU: er

När du skapar en pool måste du välja lämplig **nodeAgentSkuId**, beroende på vilket operativsystem din VHD-basavbildning har. Du kan hämta en mappning av tillgängliga ID: n för Node-agenten till sina OS-avbildningar genom att anropa den [lista som stöds av Node agent-SKU: er](/rest/api/batchservice/list-supported-node-agent-skus) .

### <a name="custom-images-for-virtual-machine-pools"></a>Anpassade avbildningar för virtuell datorpooler

Information om hur du skapar en pool med anpassade avbildningar finns i [använda galleriet för delade avbildningar för att skapa en anpassad pool](batch-sig-images.md).

Du kan också skapa en anpassad pool av virtuella datorer med hjälp av en [hanterad avbildnings](batch-custom-images.md) resurs. Information om hur man förbereder anpassade Linux-avbildningar från virtuella Azure-datorer finns i [Så här skapar du en avbildning av en virtuell dator eller VHD](../virtual-machines/linux/capture-image.md). Information om att förbereda anpassade Windows-avbildningar från virtuella Azure-datorer finns i [Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Stöd för containrar i pooler med virtuella datorer

När du skapar en pool med virtuella datorer med hjälp av Batch-API:er kan du ställa in poolen på att köra uppgifter i Docker-containrar. För närvarande måste du skapa poolen med hjälp av en avbildning som stöder Docker-container. Använd Windows Server 2016 Datacenter och en containeravbildning från Microsoft Azure Marketplace. Alternativt kan du ange en anpassad VM-avbildning som innehåller Docker Community Edition eller Enterprise Edition och alla nödvändiga drivrutiner. Poolinställningarna måste innehålla en [containerkonfiguration](/rest/api/batchservice/pool/add) som kopierar containeravbildningar till de virtuella datorerna när poolen skapas. Uppgifter som körs i poolen kan sedan referera till containeravbildningar och körningsalternativ för containrar.

Mer information finns i [Run Docker container applications on Azure Batch](batch-docker-container-workloads.md) (Köra Docker-behållarprogram på Azure Batch).

## <a name="node-type-and-target"></a>Nodtyp och mål

När du skapar en pool kan du ange vilka typer av noder som du vill ha och mål numret för var och en. De två typerna av noder är:

- **Dedikerade noder.** Dedikerade beräkningsnoder är reserverade för dina arbetsbelastningar. De kostar mer än noder med låg prioritet, men de avbryts aldrig.
- **Noder med låg prioritet.** Noder med låg prioritet utnyttjar överkapacitet i Azure för att köra Batch-arbetsbelastningar. Noder med låg prioritet är billigare per timme än dedikerade noder och aktiverar arbets belastningar som kräver betydande beräknings kraft. Mer information finns i [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Använda virtuella datorer med låg prioritet med Batch).

Noder med låg prioritet kan avbrytas om det inte finns tillräckligt med överskott i Azure. Om en nod avbryts när aktiviteter körs placeras aktiviteterna i kö igen och körs när en beräkningsnod blir tillgänglig igen. Noder med låg prioritet är ett bra alternativ för arbetsbelastningar om tiden för slutförande av jobbet är flexibelt och om arbetet är fördelat på flera noder. Innan du bestämmer dig för att använda noder med låg prioritet för ditt scenario måste du se till att arbetet som går förlorat på grund av avbrott är minimalt och enkelt att återskapa.

Du kan ha både noder med låg prioritet och dedikerade beräkningsnoder i samma pool. Varje typ av nod har en egen mål inställning där du kan ange önskat antal noder.

Antalet beräkningsnoder kallas *mål* eftersom din pool i vissa fall kanske inte når det önskade antalet noder. Detta kan hända om poolen först nått [kärnkvoten](batch-quota-limit.md) för ditt Batch-konto. Eller så kanske poolen inte når målet om du har tillämpat en formel för automatisk skalning på poolen som begränsar det högsta antalet noder.

Information om priser för både låg prioritet och dedikerade noder finns i [batch-priser](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Node-storlek

När du skapar en Azure Batch-pool kan du välja bland nästan alla VM-familjer och storlekar i Azure. Azure erbjuder ett utbud av VM-storlekar för olika arbetsbelastningar, som särskilda [HPC](../virtual-machines/sizes-hpc.md)- eller [GPU-aktiverade](../virtual-machines/sizes-gpu.md) VM-storlekar. Observera att Node-storlekar bara kan väljas vid den tidpunkt då en pool skapas. Det innebär att det inte går att ändra nodens storlek när en pool har skapats.

Mer information finns i [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Välja VM-storlek för beräkningsnoder i Azure Batch-poolen).

## <a name="automatic-scaling-policy"></a>Princip för automatisk skalning

För dynamiska arbets belastningar kan du tillämpa en princip för automatisk skalning för en pool. Batch-tjänsten utvärderar regelbundet din formel och justerar antalet noder dynamiskt i poolen enligt den aktuella arbets belastningen och resursanvändningen för ditt beräknings scenario. På så sätt kan du sänka den totala kostnaden för programkörning genom att endast använda de resurser som du behöver och frisläppa de som du inte behöver.

Du aktiverar automatisk skalning genom att skriva en [formel för automatisk skalning](batch-automatic-scaling.md#autoscale-formulas) och associera formeln med en pool. Batch-tjänsten använder formeln för att bestämma antalet noder i poolen för nästa skalningsintervall (ett intervall som du kan konfigurera). Du kan ange inställningarna för automatisk skalning för en pool när du skapar den eller aktivera skalning för en pool senare. Du kan också uppdatera skalningsinställningarna för en skalningsaktiverad pool.

Till exempel kanske ett jobb kräver att du skickar ett stort antal uppgifter som ska utföras. Du kan tilldela en skalningsformel till poolen som justerar antalet noder i poolen baserat på antalet köade aktiviteter och aktiviteternas slutförandefrekvens. Batch-tjänsten utvärderar med jämna mellanrum formeln och ändrar storlek på poolen baserat på arbetsbelastningen och dina övriga formelinställningar. Tjänsten lägger till noder efter behov när det finns ett stort antal köade aktiviteter och tar bort noder när det inte finns några uppgifter i kö eller inte körs några uppgifter.

En skalningsformel kan baseras på följande mått:

- **Tidsmått** baseras på statistik som samlas in var femte minut under angivet antalet timmar.
- **Resursmått** baseras på processoranvändning, bandbreddsanvändning, minnesanvändning och antalet noder.
- **Aktivitetsmått** baseras på aktivitetens tillstånd, t.ex. *Aktiv* (köad), *Körs* eller *Slutförd*.

Om den automatiska skalningen minskar antalet beräkningsnoder i en pool måste du bestämma hur pågående aktiviteter ska hanteras vid nedskalningen. Batch tillhandahåller ett [*alternativ för noden*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) som du kan inkludera i dina formler. Du kan till exempel ange att pågående aktiviteter ska stoppas direkt och sedan placeras i kö för att köras på en annan nod eller att de ska slutföras innan noden tas bort från poolen. Observera att om du anger alternativet för nodens tilldelning som `taskcompletion` eller `retaineddata` kommer att förhindra åtgärder för att ändra storlek på poolen förrän alla aktiviteter har slutförts, eller om alla aktiviteter för kvarhållning av aktiviteter har gått ut.

Mer information om automatisk skalning av program finns i [Skala beräkningsnoder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md).

> [!TIP]
> Du kan maximera användningen av beräkningsresurserna genom att ange målantalet noder till noll i slutet av ett jobb, men tillåta att aktiviteter som körs slutförs.

## <a name="task-scheduling-policy"></a>Schemaläggningsprincip för aktiviteter

Konfigurationsalternativet för [högsta antal aktiviteter per nod](batch-parallel-node-tasks.md) anger det högsta antal aktiviteter som kan köras parallellt på varje beräkningsnod i poolen.

Standardkonfigurationen specificerar att en aktivitet i taget ska köras på en nod, men det finns scenarier där det kan vara bra om två eller fler aktiviteter kan köras samtidigt på en nod. Information om hur du kan dra fördel av flera aktiviteter per nod finns i [exempelscenariot](batch-parallel-node-tasks.md#example-scenario) i artikeln om [samtidiga nodaktiviteter](batch-parallel-node-tasks.md).

Du kan också ange en *Fyllnings typ*, som bestämmer om batch sprider aktiviteterna jämnt över alla noder i en pool, eller paketerar varje nod med maximalt antal uppgifter innan aktiviteter tilldelas till en annan nod.

## <a name="communication-status"></a>Kommunikations status

I de flesta fall körs aktiviteterna oberoende av varandra och behöver inte kommunicera med varandra. Det kan dock finnas program där aktiviteterna måste kommunicera (till exempel i [MPI-scenarier](batch-mpi.md)).

Du kan konfigurera en pool att tillåta **kommunikation** mellan noder så att noder i en pool kan kommunicera vid körning. När kommunikation mellan noder är aktiverat kan noderna i pooler med Cloud Services-konfiguration kommunicera med varandra på portar som är större än 1100, och pooler med VM-konfiguration begränsar inte trafiken på någon port.

Att aktivera kommunikation mellan noder påverkar också placeringen av noderna i kluster och kan begränsa det maximala antalet noder i en pool på grund av distributions begränsningar. Om programmet inte kräver kommunikation mellan noder kan Batch-tjänsten allokera ett potentiellt stort antal noder till poolen från många olika kluster och datacenter för ökad parallell bearbetningskapacitet.

## <a name="start-tasks"></a>Starta aktiviteter

Om du vill kan du lägga till en [Start aktivitet](jobs-and-tasks.md#start-task) som körs på varje nod när noden ansluts till poolen, och varje gången en nod startas om eller avbildningen återställs. Startaktiviteten är särskilt användbar för att förbereda beräkningsnoder för körningen av aktiviteter, till exempel installationen av de program som aktiviteterna kör på beräkningsnoderna.

## <a name="application-packages"></a>Programpaket

Du kan ange programpaket som ska distribueras till beräkningsnoderna i poolen. Programpaket underlättar distributionen och versionshanteringen för de program som dina aktiviteter kör. Programpaket som du anger för en pool installeras på varje nod som ansluter till den poolen, samt varje gång en nod startas om och varje gång nodens avbildning återställs.

Mer information om hur du använder programpaket för att distribuera program till dina Batch-noder finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Konfiguration av virtuella nätverk (VNet) och brandväggar

När du etablerar en pool av beräkningsnoder i Batch kan du associera poolen med ett undernät för ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).

### <a name="vnet-requirements"></a>VNet-krav

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Läs mer om hur du konfigurerar en Batch-pool i ett VNet i [Create a pool of virtual machines with your virtual network](batch-virtual-network.md) (Skapa en pool av virtuella datorer med det virtuella nätverket).

> [!TIP]
> För att säkerställa att de offentliga IP-adresserna som används för att komma åt noderna inte ändras, kan du [skapa en pool med angivna offentliga IP-adresser som du styr](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Livslängd för pooler och beräkningsnoder

När du utformar din Azure Batch-lösning måste du ange hur och när pooler skapas och hur länge Compute-noderna i dessa pooler ska vara tillgängliga.

Ett alternativ är att skapa en pool för varje jobb som du skickar och sedan ta bort poolen så fort aktiviteterna har slutförts. Detta maximerar användningen eftersom noderna bara allokeras vid behov och de stängs ned när de är inaktiva. Det innebär att jobbet måste vänta tills noderna har allokerats, men det är viktigt att notera att aktiviteterna är schemalagda för körning så snart noderna tilldelas individuellt och start aktiviteten har slutförts. Batch väntar *inte* tills alla noderna i en pool är tillgängliga innan aktiviteterna tilldelas till noderna. vilket säkerställer maximal användning av alla tillgängliga noder.

Å andra sidan, om det är högsta prioritet att jobben startar direkt, kan en pool skapas i förväg och dess noder göras tillgängliga innan jobben skickas. I det här scenariot kan aktiviteterna starta direkt, men noderna kan vara inaktiva medan de väntar på att aktiviteter ska tilldelas.

En kombinerad metod används vanligt vis för att hantera en variabel men kontinuerlig belastning. Du kan ha en pool där flera jobb skickas och skala antalet noder upp eller ned enligt jobb belastningen. Detta kan göras reaktivt baserat på den aktuella belastningen eller proaktivt om det går att förutse belastningen. Mer information finns i [princip för automatisk skalning](#automatic-scaling-policy).

## <a name="autopools"></a>Autopooler

En [autopool](/rest/api/batchservice/job/add#autopoolspecification) är en pool som skapas av batch-tjänsten när ett jobb skickas, i stället för att skapas före de jobb som ska köras i poolen. Batch-tjänsten hanterar livs längden för en autopool enligt de egenskaper som du anger. Oftast är dessa pooler också inställda att ta bort automatiskt när deras jobb har slutförts.

## <a name="security-with-certificates"></a>Säkerhet med certifikat

Vanligtvis måste du använda certifikat när du krypterar eller avkrypterar känslig aktivitetsinformation, till exempel nyckeln för ett [Azure Storage-konto](accounts.md#azure-storage-accounts). För detta ändamål kan du installera certifikat på noderna. Krypterade hemligheter skickas till aktiviteter via kommandoradsparametrar eller bäddas in i någon av aktivitetsresurserna, och de installerade certifikaten kan användas för att dekryptera dem.

Du använder åtgärden [Lägg till certifikat](/rest/api/batchservice/certificate/add) (Batch REST) eller metoden [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (Batch .NET) för att lägga till ett certifikat till ett Batch-konto. Därefter kan du associera certifikatet med en ny eller befintlig pool.

När ett certifikat associeras med en pool installeras certifikatet av Batch-tjänsten på varje nod i poolen. Batch-tjänsten installerar relevanta certifikat när noden startar, innan aktiviteter startas (inklusive aktiviteten [Starta aktivitet](jobs-and-tasks.md#start-task) och [jobb hanterare](jobs-and-tasks.md#job-manager-task)).

Om du lägger till ett certifikat i en befintlig pool måste du starta om dess datornoder för att certifikatet ska kunna tillämpas på noderna.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [jobb och uppgifter](jobs-and-tasks.md).
- Lär dig hur du kan [identifiera och undvika fel i en pool-och nods bakgrunds åtgärder ](batch-pool-node-error-checking.md).
