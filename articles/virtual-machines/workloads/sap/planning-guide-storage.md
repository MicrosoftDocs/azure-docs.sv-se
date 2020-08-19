---
title: Azure Storage-typer för SAP-arbetsbelastning
description: Planera Azure Storage-typer för SAP-arbetsbelastningar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 668f8ffdc4b797219dc1f3c23fecb858d8f706ad
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510869"
---
# <a name="azure-storage-types-for-sap-workload"></a>Azure Storage-typer för SAP-arbetsbelastning
Azure har flera olika lagrings typer som skiljer sig mycket i funktioner, data flöde, svars tid och priser. Några av lagrings typerna är inte eller av begränsad användning för SAP-scenarier. Flera typer av Azure-lagring är väl lämpade eller optimerade för vissa SAP-arbetsbelastnings scenarier. I synnerhet för SAP HANA fick vissa Azure Storage-typer certifierade för användning med SAP HANA. I det här dokumentet ska vi gå igenom de olika typerna av lagring och beskriva deras kapacitet och användbarhet med SAP-arbetsbelastningar och SAP-komponenter.

Markera om enheterna som används i den här artikeln. Leverantörer av offentliga moln flyttade till att använda GiB ([gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) eller TIB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) som storleks enheter, i stället för gigabyte eller terabyte). Därför använder all Azure-dokumentation och priser dessa enheter.  I hela dokumentet refererar vi bara till dessa storleks enheter för MiB, GiB och TiB-enheter. Du kan behöva planera med MB, GB och TB. Därför bör du vara medveten om några små skillnader i beräkningarna om du behöver ändra storlek på ett data flöde på 400 MiB/s, i stället för ett data flöde på 250 MiB/s.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage återhämtning

Microsoft Azure lagring av Standard HDD, Standard SSD, Azure Premium-lagring och Ultra disk behåller den grundläggande virtuella hård disken (med OS) och anslutna VM-datadiskar eller virtuella hård diskar i tre kopior på tre olika lagringsnoder. Växling till en annan replik och dirigering av en ny replik om det skulle uppstå ett diskfel är transparent. Som ett resultat av denna redundans behöver du **inte** använda någon typ av lagrings redundans på flera Azure-diskar. Detta faktum kallas lokalt redundant lagring (LRS). LRS är standard för dessa typer av lagring i Azure. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ger tillräcklig redundans för att uppnå samma service avtal som annan intern Azure-lagring.

Det finns flera olika typer av redundans, som beskrivs i artikeln [Azure Storage replikering](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) som gäller för vissa av de olika lagrings typerna Azure måste erbjuda. 

### <a name="azure-managed-disks"></a>Azure Managed disks

Managed disks är en resurs typ i Azure Resource Manager som kan användas i stället för virtuella hård diskar som lagras i Azure Storage-konton. Managed Disks automatiskt justera med [tillgänglighets uppsättningen] [virtuella-Machines-Manage-Availability] för den virtuella datorn som de är kopplade till och därför öka tillgängligheten för den virtuella datorn och de tjänster som körs på den virtuella datorn. Mer information finns i [översikts artikeln](../../windows/managed-disks-overview.md).

I det här exemplet visas fördelarna med de hanterade diskarna som är relaterade till återhämtning:

- Du distribuerar dina två DBMS-VM för ditt SAP-system i en Azures tillgänglighets uppsättning 
- När Azure distribuerar de virtuella datorerna kommer disken med operativ system avbildningen att placeras i ett annat lagrings kluster. Detta förhindrar att de virtuella datorerna påverkas av ett problem med ett enda Azure Storage-kluster
- När du skapar nya hanterade diskar som du tilldelar till dessa virtuella datorer för att lagra data och loggfiler i databasen, distribueras även dessa nya diskar för de två virtuella datorerna i separata lagrings kluster, så att ingen av diskarna i den första virtuella datorn delar lagrings kluster med diskarna för den andra virtuella datorn

Om du distribuerar utan hanterade diskar i kunddefinierade lagrings konton är diskallokering godtyckligt och har ingen medvetenhet om det faktum att virtuella datorer distribueras i en AvSet i återhämtnings syfte.

> [!NOTE]
> Av den anledningen och flera andra förbättringar som endast är tillgängliga via hanterade diskar, kräver vi att nya distributioner av virtuella datorer som använder Azure block Storage för sina diskar (all Azure-lagring förutom Azure NetApp Files) behöver använda Azure Managed disks för de virtuella hård diskarna/OS-diskarna, data diskar som innehåller SAP-databasfiler. Oberoende av om du distribuerar de virtuella datorerna via tillgänglighets uppsättning, i Tillgänglighetszoner eller oberoende av uppsättningarna och zonerna. Diskar som används för att lagra säkerhets kopior behöver inte nödvändigt vis vara hanterade diskar.

> [!NOTE]
> Azure Managed disks tillhandahåller endast lokal redundans (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Lagrings scenarier med SAP-arbetsbelastningar
Beständig lagring krävs i SAP-arbetsbelastningar i olika komponenter i stacken som du distribuerar i Azure. De här scenarierna visas som minst:

- Beständig den virtuella hård disken för den virtuella datorn som innehåller operativ systemet och annan program vara som du installerar på den disken. Den här disken/virtuella hård disken är roten på den virtuella datorn. Alla ändringar som görs i den måste vara bestående. I nästa steg ska du stoppa och starta om den virtuella datorn, men alla ändringar som gjorts innan fortfarande finns kvar. Särskilt i fall där den virtuella datorn distribueras av Azure till en annan värd än den kördes ursprungligen
- Sparade data diskar. De här diskarna är virtuella hård diskar som du lägger till för att lagra program data i. Dessa program data kan vara data och logga/göra om filer i en databas, säkerhetskopiera filer eller program varu installationer. Innebär att en disk utanför din bas-VHD som innehåller operativ systemet
- Fil resurser eller delade diskar som innehåller din globala transport katalog för NetWeaver eller S/4HANA. Innehållet i dessa resurser används antingen av program vara som körs i flera virtuella datorer eller som används för att bygga scenarier med kluster med hög tillgänglighet
- /Sapmnt-katalogen eller vanliga fil resurser för EDI-processer eller liknande. Innehållet i dessa resurser används antingen av program vara som körs i flera virtuella datorer eller som används för att bygga scenarier med kluster med hög tillgänglighet

I följande avsnitt beskrivs de olika typerna av Azure Storage och deras användbarhet för SAP-arbetsbelastningar som gäller för de fyra scenarierna ovan. En allmän kategorisering av hur olika typer av Azure-lagring ska användas finns dokumenterade i artikeln [vilka disk typer som är tillgängliga i Azure?](../../linux/disks-types.md). Rekommendationerna för att använda olika typer av Azure-lagring för SAP-arbetsbelastningen kommer inte att vara större än.

Support begränsningar för Azure Storage-typer för SAP NetWeaver/Application Layer of S/4HANA finns i [SAP support note 2015553](https://launchpad.support.sap.com/#/notes/2015553) for SAP HANA Certified and Supported Azure Storage types läs artikeln [SAP HANA konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md).

I avsnitten som beskriver de olika typerna av Azure Storage får du mer information om begränsningarna och möjligheterna med hjälp av lagrings utrymmet i SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Lagrings rekommendationer för SAP Storage-scenarier
Innan du går in på detaljerna presenterar vi sammanfattningen och rekommendationerna som redan finns i början av dokumentet. Informationen för specifika typer av Azure Storage följer det här avsnittet av dokumentet. Sammanfatta lagrings rekommendationerna för SAP-lagrings scenarier i en tabell, det ser ut så här:

| Användnings scenario | Standard HDD | Standard SSD | Premium Storage | Ultradisk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| OS-disk | inte lämplig |  begränsad lämplig (icke-Prod) | rekommenderas | inte möjlig | inte möjlig |
| Global transport katalog | stöds inte | stöds inte | rekommenderas | rekommenderas | rekommenderas |
| /sapmnt | inte lämplig | begränsad lämplig (icke-Prod) | rekommenderas | rekommenderas | rekommenderas |
| DBMS data volym SAP HANA M/Mv2 VM-familjer | stöds inte | stöds inte | rekommenderas | rekommenderas | rekommenderas<sup>2</sup> |
| DBMS logg volym SAP HANA M/Mv2 VM-familjer | stöds inte | stöds inte | rekommenderas<sup>1</sup> | rekommenderas | rekommenderas<sup>2</sup> | 
| DBMS data Volume SAP HANA Esv3/Edsv4 VM-familjer | stöds inte | stöds inte | rekommenderas | rekommenderas | rekommenderas<sup>2</sup> |
| DBMS logg volym SAP HANA Esv3/Edsv4 VM-familjer | stöds inte | stöds inte | stöds inte | rekommenderas | rekommenderas<sup>2</sup> | 
| DBMS-datavolymen är inte-HANA | stöds inte | begränsad lämplig (icke-Prod) | rekommenderas | rekommenderas | stöds inte |
| DBMS logg volym icke-HANA M/Mv2 VM-familjer | stöds inte | begränsad lämplig (icke-Prod) | rekommenderas<sup>1</sup> | rekommenderas | stöds inte |
| DBMS-logg volym icke-HANA icke-Mv2 VM-familjer | stöds inte | begränsad lämplig (icke-Prod) | lämpligt för upp till medel stora arbets belastningar | rekommenderas | stöds inte |


<sup>1</sup> med användning av [Azure-Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) för virtuella datorer i M/Mv2 för logg/gör om logg volymer <sup>2</sup> med ANF krävs/Hana/data såväl som/Hana/log för ANF 

De egenskaper du kan förväntar dig från listan med olika lagrings typer som:

| Användnings scenario | Standard HDD | Standard SSD | Premium Storage | Ultradisk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Service avtal för data flöde/IOPS | nej | nej | ja | ja | ja |
| Svars tids läsningar | hög | medel till hög | börjar | under-millisekund | under-millisekund |
| Svars tider skrivningar | hög | medel till hög  | låg (under millisekunder<sup>1</sup>) | under-millisekund | under-millisekund |
| HANA stöds | nej | nej | Ja<sup>1</sup> | ja | ja |
| Möjliga disk ögonblicks bilder | ja | ja | ja | nej | ja |
| Allokering av diskar i olika lagrings kluster när du använder tillgänglighets uppsättningar | via Managed disks | via Managed disks | via Managed disks | disk typen stöds inte för virtuella datorer som distribuerats via tillgänglighets uppsättningar | nr<sup>3</sup> |
| Justerat med Tillgänglighetszoner | ja | ja | ja | ja | kräver engagemang för Microsoft |
| Zonindelade-redundans | inte för hanterade diskar | inte för hanterade diskar | inte för hanterade diskar | nej | nej |
| GEO-redundans | inte för hanterade diskar | inte för hanterade diskar | nej | nej | nej |


<sup>1</sup> med användning av [Azure Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) för virtuella datorer i M/Mv2 för logg/gör om-logg volymer

<sup>2</sup> kostnader är beroende av etablerad IOPS och data flöde

<sup>3</sup> skapandet av olika ANF kapacitets grupper garanterar inte distribution av kapacitets grupper till olika lagrings enheter


> [!IMPORTANT]
> För att uppnå mindre än 1 millisekund I/O-latens med hjälp av Azure NetApp Files (ANF), måste du arbeta med Microsoft för att ordna rätt placering mellan dina virtuella datorer och NFS-resurserna baserat på ANF. Så långt finns det ingen mekanism på plats som ger ett automatiskt närhet mellan en virtuell dator som distribuerats och de NFS-volymer som finns på ANF. Med hänsyn till den olika konfigurationen av olika Azure-regioner kan den tillagda nätverks fördröjningen skicka i/O-svars tiden utöver 1 millisekund om den virtuella datorn och NFS-resursen inte allokeras i närheten.


> [!IMPORTANT]
> Ingen av de för närvarande tillgängliga Azure block Storage-baserade hanterade diskarna, eller Azure NetApp Files erbjuder zonindelade eller geografisk redundans. Därför måste du kontrol lera att dina hög tillgänglighets-och haveri beredskaps arkitekturer inte förlitar sig på någon typ av Azures interna lagrings replikering för dessa hanterade diskar, NFS-eller SMB-resurser.


## <a name="azure-premium-storage"></a>Azure Premium-lagring
Azure Premium SSD-lagring introducerades med målet att tillhandahålla:

* Låg I/O-latens
* Service avtal för IOPS och data flöde
* Mindre variation i i/O-latens

Den här typen av lagring är riktad mot DBMS-arbetsbelastningar, lagrings trafik som kräver låg engångs fördröjning i millisekunder och service avtal för IOPS och data flödes kostnad för Azure Premium Storage är inte den faktiska data volym som lagras på sådana diskar, men storleken på en sådan disk, oberoende av mängden data som lagras på disken. Du kan också skapa diskar i Premium Storage som inte är direkt mappar till de storleks kategorier som visas i artikel [Premium SSD](../../linux/disks-types.md#premium-ssd). Slut satser från den här artikeln är:

- Lagringen är ordnad i intervall. Till exempel, en disk i intervallet 513 GiB till 1024 GiB-kapacitet delar samma funktioner och samma månads kostnader
- IOPS per GiB spårar inte linjärt över storleks kategorierna. Mindre diskar under 32 GiB har högre IOPS-priser per GiB. För diskar utöver 32 GiB till 1024 GiB är IOPS-priset per GiB mellan 4-5 IOPS per GiB. För större diskar upp till 32 767 GiB pågår IOPS-priset per GiB under 1
- I/O-dataflödet för den här lagringen är inte linjärt med storleken på disk kategorin. För mindre diskar, som kategorin mellan 65 GiB och 128 GiB, är data flödet runt 780KB/GiB. För de extremt stora diskarna som en 32 767 GiB-disk är data flödet runt 28KB/GiB
- IOPS-och data flödes service avtal kan inte ändras utan att diskens kapacitet ändras

Azure har ett enda service avtal för virtuell dator med 99,9% som är knutet till användningen av Azure Premium Storage eller Azure Ultra disk Storage. SLA är dokumenterat i [SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För att kunna följa detta service avtal för virtuell dator måste bas-VHD-disken samt **all** ansluten disk vara antingen Azure Premium Storage eller Azure Ultra disk Storage.

Funktions mat ris för SAP-arbetsbelastningen ser ut så här:

| Kapacitet| Kommentar| Anteckningar/länkar | 
| --- | --- | --- | 
| OS-bas-VHD | korrekt | alla system |
| Datadisk | korrekt | alla system – [särskilt för SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP global transport katalog | JA | [Tillåtna](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | korrekt | alla system |
| Lagring av säkerhets kopior | korrekt | för kortsiktig lagring av säkerhets kopior |
| Resurser/delad disk | inte tillgänglig | Behöver Azure Premium-filer eller tredje part |
| Återhämtning | LRS | Ingen GRS eller ZRS är tillgänglig för diskar |
| Svarstid | låg till medel | - |
| IOPS-SLA | JA | - |
| IOPS linjär till kapacitet | halvt linjärt i hakparenteser  | [Priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximalt antal IOPS per disk | 20 000 [beroende av disk storlek](https://azure.microsoft.com/pricing/details/managed-disks/) | Överväg även [VM-gränser](../../sizes.md) |
| SLA för data flöde | JA | - |
| Data flöde linjärt till kapacitet | halvt linjärt i hakparenteser | [Priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-certifierad | JA | [särskilt för SAP HANA](../../how-to-enable-write-accelerator.md) |
| Möjliga disk ögonblicks bilder | JA | - |
| Möjliga Azure Backup VM-ögonblicksbilder | JA | Förutom för [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) cachelagrade diskar  |
| Kostnader | SÄKER | - |

Azure Premium Storage uppfyller inte SAP HANA KPI: er för lagrings fördröjning med de gemensamma caching-typer som erbjuds med Azure Premium Storage. För att kunna uppfylla KPI: er för lagrings fördröjning för SAP HANA logg skrivningar måste du använda Azure Skrivningsaccelerator-cachelagring enligt beskrivningen i artikeln [aktivera Skrivningsaccelerator](../../how-to-enable-write-accelerator.md). Azure Skrivningsaccelerator fördelar alla andra DBMS-system för transaktions logg skrivningar och gör om logg skrivningar. Därför rekommenderar vi att du använder dem i alla SAP-DBMS-distributioner. För SAP HANA är användningen av Azure Skrivningsaccelerator tillsammans med Azure Premium Storage obligatorisk.



**Sammanfattning:** Azure Premium Storage är en av de Azure Storage-typer som rekommenderas för SAP-arbetsbelastningar. Den här rekommendationen gäller inte för produktion och produktions system. Azure Premium Storage passar för att hantera databas arbets belastningar. Användningen av Azure Skrivningsaccelerator kommer att förbättra Skriv fördröjningen mot Azure Premium-diskar i huvudsak. Men för DBMS-system med höga IOPS-och data flödes hastigheter behöver du antingen överetablera lagrings kapacitet eller använda funktioner som Windows lagrings utrymmen eller logiska volym hanterare i Linux för att bygga stripe-uppsättningar som ger dig den önskade kapaciteten på den ena sidan, men även den nödvändiga IOPS eller data flödet med bästa möjliga kostnads effektivitet.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-funktioner för Premium Storage
För Azure Premium Storage-diskar som är mindre än eller lika med 512 GiB erbjuds burst-funktionerna. Exakt hur disk-burst fungerar beskrivs i artikeln [disk bursting](../../linux/disk-bursting.md). När du läser artikeln förstår du konceptet med att Periodisera IOPS och data flöde när din I/O-arbetsbelastning är lägre än den nominella IOPS och data flödet för diskarna (mer information om det nominella data flödet finns i [priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/)). Du kommer att Periodisera delta i IOPS och data flöde mellan den aktuella användningen och de nominella värdena för disken. Burst-överföringarna är begränsade till högst 30 minuter.

De idealiska fall där denna Burst-funktion kan planeras i kommer förmodligen att vara de volymer eller diskar som innehåller datafiler för olika DBMS. I/O-arbetsbelastningen förväntas mot dessa volymer, särskilt med små till mellan intervall system förväntas se ut så här:

- Låg till måttlig Läs belastnings belastning eftersom data som helst cachelagras i minnet eller som i händelse av HANA bör vara helt i minnet
- Burst-överföring som utlösts av databas kontroll punkter eller lagrings punkter som utfärdas med jämna mellanrum
- Säkerhets kopierings arbets belastning som läser i en kontinuerlig ström i fall där säkerhets kopior inte körs via lagrings ögonblicks bilder
- För SAP HANA, Läs in data i minnet efter en instans omstart

Särskilt i mindre DBMS-system där din arbets belastning hanterar några hundra transaktioner per sekund, kan en sådan Burst-funktion även vara användbar för de diskar eller volymer som lagrar transaktionen eller gör om loggen. Förväntad arbets belastning mot sådan disk eller volymerna ser ut så här:

- Vanliga skrivningar till den disk som är beroende av arbets belastningen och typen av arbets belastning eftersom varje incheckning som utfärdats av programmet sannolikt utlöser en I/O-åtgärd
- Högre arbets belastning i data flödet för fall av operativa uppgifter som att skapa eller återskapa index
- Läs burst-överföring när du utför transaktions logg eller gör om logg säkerhets kopior


## <a name="azure-ultra-disk"></a>Azure Ultra disk
Azures ultradiskar tillhandahåller disklagring med ett snabbt dataflöde, hög IOPS och konsekvent snabba svarstider för virtuella Azure IaaS-datorer. Vissa ytterligare fördelar med Ultra disks är möjligheten att dynamiskt ändra diskens IOPS och data flöde, tillsammans med dina arbets belastningar, utan att behöva starta om dina virtuella datorer. Ultra disks lämpar sig för data intensiva arbets belastningar som SAP DBMS-arbetsbelastningar. Ultra disks kan bara användas som data diskar och kan inte användas som bas-VHD-disk som lagrar operativ systemet. Vi rekommenderar användningen av Azure Premium Storage som en virtuell hård disk.

När du skapar en Ultra disk har du tre dimensioner som du kan definiera:

- Diskens kapacitet. Intervallen är från 4 GiB till 65 536 GiB
- Allokerad IOPS för disken. Olika max värden gäller för diskens kapacitet. Mer information finns i artikeln [Ultra disk](../../linux/disks-types.md#ultra-disk) .
- Allokerad lagrings bandbredd. En annan Maximal bandbredd gäller beroende på diskens kapacitet. Mer information finns i artikeln [Ultra disk](../../linux/disks-types.md#ultra-disk) .

Kostnaden för en enskild disk bestäms av de tre dimensionerna som du kan definiera för specifika diskar separat. 


Funktions mat ris för SAP-arbetsbelastningen ser ut så här:

| Kapacitet| Kommentar| Anteckningar/länkar | 
| --- | --- | --- | 
| OS-bas-VHD | fungerar inte | - |
| Datadisk | korrekt | alla system  |
| SAP global transport katalog | JA | [Tillåtna](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | korrekt | alla system |
| Lagring av säkerhets kopior | korrekt | för kortsiktig lagring av säkerhets kopior |
| Resurser/delad disk | inte tillgänglig | Behöver tredje part |
| Återhämtning | LRS | Ingen GRS eller ZRS är tillgänglig för diskar |
| Svarstid | Mycket låg | - |
| IOPS-SLA | JA | - |
| IOPS linjär till kapacitet | halvt linjärt i hakparenteser  | [Priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximalt antal IOPS per disk | 1 200 till 160 000 | beroende av disk kapacitet |
| SLA för data flöde | JA | - |
| Data flöde linjärt till kapacitet | halvt linjärt i hakparenteser | [Priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-certifierad | JA | - |
| Möjliga disk ögonblicks bilder | NO | - |
| Möjliga Azure Backup VM-ögonblicksbilder | NO | - |
| Kostnader | Högre än Premium Storage | - |



**Sammanfattning:** Azure Ultra disks är en lämplig lagrings plats med låg latens för alla typer av SAP-arbetsbelastningar. Hittills kan Ultra disk bara användas i kombinationer med virtuella datorer som har distribuerats via Tillgänglighetszoner (zonindelade-distribution). Ultra disk stöder inte lagrings ögonblicks bilder vid den här tidpunkten. I motsats till all annan lagring kan Ultra disk inte användas för den virtuella hård disken. Ultra disk är perfekt för situationer där I/O-arbetsbelastningen varierar ett parti och du vill anpassa distribuerade lagrings data flöde eller IOPS till lagrings arbets belastnings mönster i stället för att ändra maximal användning av bandbredd och IOPS.


## <a name="azure-netapp-files-anf"></a>Azure NetApp-filer (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) är resultatet av ett samarbete mellan Microsoft och NetApp med målet att tillhandahålla högpresterande Azure Native NFS-och SMB-resurser. Betoningen är att tillhandahålla hög bandbredd och lagring med låg latens som möjliggör distribution av DBMS, och över tid möjliggör vanliga drift funktioner i NetApp-lagringen via Azure. NFS/SMB-resurser erbjuds i tre olika service nivåer som skiljer sig i lagrings data flöde och i pris. Service nivåerna finns dokumenterade i artikel [service nivåer för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Följande service nivåer rekommenderas för de olika typerna av SAP-arbets belastning:

- SAP DBMS-arbetsbelastning: prestanda, helst Ultra
- SAPMNT-resurs: prestanda, helst Ultra
- Global transport katalog: prestanda, helst mycket

> [!NOTE]
> Den minsta etablerings storleken är en 4 TiB-enhet som kallas för kapacitets grupp. Sedan skapar du volymer från den här kapacitets gruppen. Den minsta volymen som du kan bygga är 100 GiB. Du kan expandera en pool med kapacitet i TiB steg. För priser, se artikeln [Azure NetApp Files priser](https://azure.microsoft.com/pricing/details/netapp/)

ANF Storage stöds för närvarande för flera SAP-arbets belastnings scenarier:

- Tillhandahålla SMB-eller NFS-resurser för SAPs globala transport katalog
- Avsnittet Dela sapmnt i scenarier med hög tillgänglighet beskrivs i:
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program](./high-availability-guide-windows-netapp-files-smb.md)
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md)
    - [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA distributioner med hjälp av NFS v 4.1-resurser för/Hana/data-och/Hana/log-volymer och/eller NFS v 4.1-eller NFS v3-volymer för/Hana/Shared-volymer enligt beskrivningen i artikeln [SAP HANA Azure Virtual Machine Storage Configurations](./hana-vm-operations-storage.md)

> [!NOTE]
> Inga andra DBMS-arbetsbelastningar stöds för Azure NetApp Files baserade NFS-eller SMB-resurser. Uppdateringar och ändringar kommer att tillhandahållas om det ska ändras.

Precis som med Azure Premium Storage kan en fast eller linjär data flödes storlek per GB vara ett problem när du måste följa vissa lägsta siffror i data flödet. Så här är fallet för SAP HANA. Med ANF kan det här problemet bli mer uttalat än med Azure Premium-disken. Om du använder Azure Premium-diskar kan du ta flera mindre diskar med ett relativt högt data flöde per GiB och stripe över dem så att de blir kostnads effektiva och har högre genomflöde med lägre kapacitet. Den här typen av striping fungerar inte för NFS-eller SMB-resurser som finns på ANF. Den här begränsningen ledde till distribution av överkapacitet som:

- För att till exempel få ett data flöde på 250 MiB/s på en NFS-volym som finns på ANF, måste du distribuera 1,95 TiB-kapacitet för den ultra Service-nivån. 
- För att uppnå 400 MiB/s måste du distribuera 3,125 TiB-kapacitet. Men du kan behöva överbelastning av kapaciteten för att uppnå det data flöde du behöver för volymen. Den här överetableringen av kapacitet påverkar prissättningen av mindre HANA-instanser. 
- I utrymmet för att använda NFS ovanpå ANF för SAP/sapmnt-katalogen går det vanligt vis långt med den lägsta kapaciteten på 100 GiB till 150 GiB som tillämpas av Azure NetApp Files. Kund upplevelsen visade dock att det relaterade data flödet på 12,8 MiB/s (med Ultra Service Level) kanske inte är tillräckligt och kan ha negativ inverkan på stabiliteten i SAP-systemet. I sådana fall kan kunder undvika problem genom att öka volymen på/sapmnt-volymen så att mer data flöde tillhandahålls till den volymen.

Funktions mat ris för SAP-arbetsbelastningen ser ut så här:

| Kapacitet| Kommentar| Anteckningar/länkar | 
| --- | --- | --- | 
| OS-bas-VHD | fungerar inte | - |
| Datadisk | korrekt | Endast SAP HANA  |
| SAP global transport katalog | JA | SMB och NFS |
| SAP-sapmnt | korrekt | alla system SMB (endast Windows) eller NFS (endast Linux) |
| Lagring av säkerhets kopior | korrekt | - |
| Resurser/delad disk | JA | SMB 3,0, NFS v3 och NFS v 4.1 |
| Återhämtning | LRS | Ingen GRS eller ZRS är tillgänglig för diskar |
| Svarstid | Mycket låg | - |
| IOPS-SLA | JA | - |
| IOPS linjär till kapacitet | strikt linjär  | Beroende av [service nivå](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| SLA för data flöde | JA | - |
| Data flöde linjärt till kapacitet | halvt linjärt i hakparenteser | Beroende av [service nivå](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA-certifierad | JA | - |
| Möjliga disk ögonblicks bilder | JA | - |
| Möjliga Azure Backup VM-ögonblicksbilder | NO | - |
| Kostnader | Högre än Premium Storage | - |


Ytterligare inbyggda funktioner för ANF-lagring:

- Möjlighet att utföra ögonblicks bilder av volymen
- Kloning av ANF-volymer från ögonblicks bilder
- Återställa volymer från ögonblicks bilder (snapin-återställning)

**Sammanfattning**: Azure NetApp Files är en Hana-certifierad lagring med låg latens som gör det möjligt att distribuera NFS-och SMB-volymer eller-resurser. Lagringen levereras med tre olika service nivåer som ger olika data flöde och IOPS på ett linjärt sätt per GiB kapacitet för volymen. ANF-lagringen aktiverar för att distribuera SAP HANA skalnings scenarier med en nod i vänte läge. Lagrings utrymmet är lämpligt för att tillhandahålla fil resurser efter behov för/sapmnt eller SAP global transport katalog. ANF-lagringen levereras med funktions tillgänglighet som är tillgänglig som inbyggda NetApp-funktioner.  



## <a name="azure-standard-ssd-storage"></a>Azure standard SSD-lagring
Jämfört med Azure standard Storage-lagring ger Azure standard SSD-lagring bättre tillgänglighet, konsekvens, tillförlitlighet och latens. Den är optimerad för arbets belastningar som behöver konsekvent prestanda på lägre IOPS-nivåer. Det här lagrings utrymmet är det minsta lagrings utrymme som används för SAP-system med låg IOPS och data flödes krav. Funktions mat ris för SAP-arbetsbelastningen ser ut så här:

| Kapacitet| Kommentar| Anteckningar/länkar | 
| --- | --- | --- | 
| OS-bas-VHD | begränsad lämplig | icke-produktionssystem |
| Datadisk | begränsad lämplig | vissa icke-produktionssystem med låga IOPS-och latens krav |
| SAP global transport katalog | NO | [Stöds ej](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | begränsad lämplig | icke-produktionssystem |
| Lagring av säkerhets kopior | korrekt | - |
| Resurser/delad disk | inte tillgänglig | Behöver tredje part |
| Återhämtning | LRS, GRS | Ingen ZRS är tillgänglig för diskar |
| Svarstid | hög | för hög för global överförings katalog för SAP eller produktions system |
| IOPS-SLA | NO | - |
| Maximalt antal IOPS per disk | 500 | Oberoende av disk storleken |
| SLA för data flöde | NO | - |
| HANA-certifierad | NO | - |
| Möjliga disk ögonblicks bilder | JA | - |
| Möjliga Azure Backup VM-ögonblicksbilder | JA | - |
| Kostnader | LÅG | - |



**Sammanfattning:** Azure standard SSD-lagring är den minsta rekommendationen för virtuella datorer som inte är virtuella datorer för bas-VHD, vilket gör att DBMS-distributioner med relativa svars tider och/eller låga IOPS och data flödes hastigheter. Den här Azure Storage-typen stöds inte längre för att vara värd för den globala SAP-transport katalogen. 



## <a name="azure-standard-hdd-storage"></a>Azure standard HDD-lagring
Azure Standard HDD Storage var den enda lagrings typen när Azure-infrastrukturen fick certifiering för SAP NetWeaver-arbetsbelastning under året 2014. I år 2014 var Azure Virtual Machines liten och låg i lagrings data flödet. Den här lagrings typen kunde därför bara hålla sig uppdaterad med kraven. Lagringen är idealisk för försvars känsliga arbets belastningar som du inte upplever i SAP-utrymmet. Med det ökande genomflödet av virtuella Azure-datorer och den ökade arbets belastning som de virtuella datorerna producerar, betraktas inte den här lagrings typen för användning med SAP-scenarier längre. Funktions mat ris för SAP-arbetsbelastningen ser ut så här:

| Kapacitet| Kommentar| Anteckningar/länkar | 
| --- | --- | --- | 
| OS-bas-VHD | inte lämplig | - |
| Datadisk | inte lämplig | - |
| SAP global transport katalog | NO | [Stöds ej](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | NO | Stöds inte |
| Lagring av säkerhets kopior | korrekt | - |
| Resurser/delad disk | inte tillgänglig | Behöver Azure Files eller tredje part |
| Återhämtning | LRS, GRS | Ingen ZRS är tillgänglig för diskar |
| Svarstid | hög | för hög för användning i DBMS, SAP global transport katalog eller sapmnt/saploc |
| IOPS-SLA | NO | - |
| Maximalt antal IOPS per disk | 500 | Oberoende av disk storleken |
| SLA för data flöde | NO | - |
| HANA-certifierad | NO | - |
| Möjliga disk ögonblicks bilder | JA | - |
| Möjliga Azure Backup VM-ögonblicksbilder | JA | - |
| Kostnader | LÅG | - |



**Sammanfattning:** Standard HDD är en Azure Storage-typ som endast ska användas för att lagra SAP-säkerhetskopieringar. Den bör endast användas som bas-VHD för i stället inaktiva system, t. ex. tillbakadragna system som används för att söka efter data här och där. Men inga aktiva utvecklings-, frågor och produktions-VM: ar bör baseras på lagringen. Eller om databasfilerna finns på lagrings platsen


## <a name="azure-vm-limits-in-storage-traffic"></a>Azure VM-gränser i lagrings trafik
I motsats till lokala scenarier, spelar den enskilda VM-typen som du väljer, en viktig roll i den lagrings bandbredd som du kan uppnå. För olika lagrings typer måste du tänka på följande:

| Lagringstyp| Linux | Windows | Kommentarer |
| --- | --- | --- | --- |
| Standard HDD | [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md) | [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md) | Det är sannolikt svårt att röra lagrings gränserna för medel stora eller stora virtuella datorer |
| Standard SSD | [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md) | [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md) | Det är sannolikt svårt att röra lagrings gränserna för medel stora eller stora virtuella datorer |
| Premium Storage | [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md) | [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md) | Lätt att trycka på virtuella datorer med IOPS eller lagrings data flöde med lagrings konfiguration |
| Ultra disk Storage | [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md) | [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md) | Lätt att trycka på virtuella datorer med IOPS eller lagrings data flöde med lagrings konfiguration |
| Azure NetApp Files | [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md) | [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md) | Lagrings trafik använder bandbredd för nätverks data flöde och inte lagrings bandbredd! |

Som begränsningar kan du tänka på följande:

- Ju mindre virtuell dator är, ju färre diskar du kan koppla. Detta gäller inte för ANF. Eftersom du monterar NFS-eller SMB-resurser får du ingen gräns för hur många delade volymer som ska kopplas
- De virtuella datorerna har I/O-genomflöde och IOPS-gränser som enkelt kan överskridas med Premium Storage-diskar och Ultra disks
- Med ANF förbrukar trafiken till de delade volymerna den virtuella datorns nätverks bandbredd och inte lagrings bandbredd
- Med stora NFS-volymer i TiB kapacitets utrymme kommer data flödet som använder en sådan volym ut från en enda virtuell dator att platå baserat på begränsningar i Linux för en enda session som interagerar med den delade volymen. 

När du konfigurerar virtuella Azure-datorer i livs cykeln för ett SAP-system bör du utvärdera IOPS-och lagrings data flödes gränserna för den nya och större VM-typen. I vissa fall kan det också vara bra att ändra lagrings konfigurationen till de nya funktionerna i den virtuella Azure-datorn. 


## <a name="striping-or-not-striping"></a>Randning eller not randning
Genom att skapa en stripe-uppsättning av flera Azure-diskar i en större volym kan du samla IOPS och data flöde för de enskilda diskarna till en volym. Den används endast för Azure standard Storage och Azure Premium Storage. Azure Ultra disk där du kan konfigurera genomflödet och IOPS oberoende av kapaciteten för en disk, kräver inte att stripe-uppsättningar används. Delade volymer baserade på NFS eller SMB kan inte stripas. På grund av den icke-linjära typen av data flöde i Azure Premium Storage och IOPS kan du etablera mindre kapacitet med samma IOPS och data flöde än stora enskilda Azure Premium Storage-diskar. Det är metoden för att uppnå högre genomflöde eller IOPS med lägre kostnad med hjälp av Azure Premium Storage. Ett exempel:

- Randning över två p15 Premium Storage-diskar ger dig till gång till ett data flöde av 
- 250 MiB/s. En sådan volym kommer att ha 512 GiB-kapacitet. Om du vill ha en enda disk som ger dig 250 MiB-genomflöde per sekund måste du välja en P40-disk med 2 TiB-kapacitet. 
- Eller så kan du få ett data flöde på 400 MiB/s genom att Stripa fyra P10 Premium Storage-diskar med en total kapacitet på 512 GiB genom randning. Om du vill ha en enskild disk med minst 500 MiB-genomflöde per sekund måste du välja en P60 Premium Storage-disk med 8 TiB. Eftersom kostnads besparingar eller Premium-lagring är nära linjära med kapaciteten kan du använda randiga kostnader.

Vissa regler måste följas vid randning:

- Ingen virtuell dator som har kon figurer ATS i VM-lagringen ska användas eftersom Azure Storage behåller data redundanta redan
- Diskarna som stripe-uppsättningen tillämpas på måste ha samma storlek

Randning över flera mindre diskar är det bästa sättet att uppnå en bra pris-/prestanda kvot med Azure Premium Storage. Det är förstås att striping har ytterligare distributions-och hanterings kostnader.

För vissa rekommendationer för rand storlek läser du dokumentationen för olika DBMS, t. ex. [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Nästa steg
Läs artiklarna:

- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md)
- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](./hana-vm-operations-storage.md)
 