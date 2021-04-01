---
title: Säkerställ verksamhets kontinuitet & haveri beredskap med Azure-kopplade regioner
description: Säkerställ program återhämtning med Azure regional hop koppling
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511237"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Affärskontinuitet och haveriberedskap (BCDR): Länkade Azure-regioner

## <a name="what-are-paired-regions"></a>Vad är kopplade regioner?

En Azure-region består av en uppsättning data Center som distribueras inom en latens-definierad perimeter och är ansluten via ett dedikerat nätverk med låg latens.  Detta säkerställer att Azure-tjänster inom en Azure-region erbjuder bästa möjliga prestanda och säkerhet.  

Ett geografiskt område i Azure definierar ett område i världen som innehåller minst en Azure-region. Geografiska områden definierar en diskret marknad, vanligt vis med två eller flera regioner, som bevarar data placering och efterlevnad av gränser.  Hitta mer information om Azures globala infrastruktur [här](https://azure.microsoft.com/global-infrastructure/regions/)

Ett regionalt par består av två regioner i samma geografi. Azure serialiserar plattforms uppdateringar (planerat underhåll) över regionala par, vilket säkerställer att endast en region i varje par uppdateringar i taget. Om ett avbrott påverkar flera regioner prioriteras minst en region i varje par för återställning.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Vissa Azure-tjänster kan dra nytta av kopplade regioner för att säkerställa affärs kontinuiteten och skydda mot data förlust.  Azure tillhandahåller flera [lagrings lösningar](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) som kan dra nytta av kopplade regioner för att säkerställa data tillgänglighet. [Azure Geo-redundant lagring](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replikerar till exempel data till en sekundär region automatiskt, vilket säkerställer att data är varaktiga även i händelse av att det inte går att återskapa den primära regionen. 

Observera att alla Azure-tjänster inte replikerar data automatiskt, eller att alla Azure-tjänster automatiskt kommer tillbaka från en felaktig region till dess par.  I sådana fall måste återställning och replikering konfigureras av kunden.

## <a name="can-i-select-my-regional-pairs"></a>Kan jag välja mina regionala par?

Nej. Vissa Azure-tjänster förlitar sig på regionala par, till exempel Azures [redundanta lagring](./storage/common/storage-redundancy.md). Dessa tjänster tillåter inte att du skapar nya regionala par.  På samma sätt kan du inte definiera egna regionala par för att dra nytta av dessa tjänster eftersom Azure styr planerat underhåll och återställnings prioritering för regionala par. Du kan dock skapa en egen lösning för haveri beredskap genom att skapa tjänster i valfritt antal regioner och dra nytta av Azure-tjänster för att para ihop dem. 

Du kan till exempel använda Azure-tjänster som [AzCopy](./storage/common/storage-use-azcopy-v10.md) för att schemalägga säkerhets kopiering av data till ett lagrings konto i en annan region.  Med hjälp av [Azure DNS och Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)kan kunder utforma en elastisk arkitektur för sina program som kommer att överleva förlusten av den primära regionen.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Är jag begränsad till att använda tjänster i mina regionala par?

Nej. Även om en specifik Azure-tjänst kan vara beroende av ett regionalt par kan du vara värd för dina andra tjänster i valfri region som uppfyller dina affärs behov.  En Azure GRS Storage-lösning kan para ihop data i Kanada Central med en peer i Kanada, öst, samtidigt som du använder beräknings resurser som finns i USA, östra.  

## <a name="must-i-use-azure-regional-pairs"></a>Måste jag använda Azures regionala par?

Nej. Kunder kan använda Azure-tjänster för att skapa en elastisk tjänst utan att behöva lita på Azures regionala par.  Vi rekommenderar dock att du konfigurerar haveri beredskap för affärs kontinuitet (BCDR) i regionala par för att dra nytta av att [isolera](./security/fundamentals/isolation-choices.md) och förbättra [tillgängligheten](./availability-zones/az-overview.md). För program med stöd för flera aktiva regioner rekommenderar vi att du använder båda regionerna i ett regionpar där det är möjligt. Detta säkerställer optimal tillgänglighet för program och minimerad återställnings tid i händelse av en katastrof. När det är möjligt kan du utforma ditt program för [maximal återhämtnings kapacitet](/azure/architecture/framework/resiliency/overview) och enkel [haveri beredskap](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Regionala Azure-par

| Geografi | Regionalt par A | Regionalt par B  |
|:--- |:--- |:--- |
| Asia-Pacific |Asien, östra (Hongkong) | Sydostasien (Singapore) |
| Australien |Australien, östra |Australien, sydöstra |
| Australien |Australien, centrala |Australien, centrala 2 |
| Brasilien |Brasilien, södra |USA, södra centrala |
| Kanada |Kanada, centrala |Kanada, östra |
| Kina |Kina, norra |Kina, östra|
| Kina |Kina, norra 2 |Kina, östra 2|
| Europa |Europa, norra (Irland) |Europa, västra (Nederländerna) |
| Frankrike |Frankrike, centrala|Frankrike, södra|
| Tyskland |Tyskland, centrala |Tyskland, nordöstra |
| Indien |Indien, centrala |Indien, södra |
| Indien |Indien, västra |Indien, södra |
| Japan |Japan, östra |Japan, västra |
| Korea |Sydkorea, centrala |Sydkorea, södra |
| Nordamerika |East US |USA, västra |
| Nordamerika |USA, östra 2 |Central US |
| Nordamerika |USA, norra centrala |USA, södra centrala |
| Nordamerika |USA, västra 2 |USA, västra centrala |
| Norge | Östra Norge | Norge, väst |
| Sydafrika | Sydafrika, norra |Sydafrika, västra |
| Schweiz | Schweiz, norra |Schweiz, västra |
| Storbritannien |Storbritannien, västra |Storbritannien, södra |
| Förenade Arabemiraten | Förenade Arabemiraten, norra | Förenade Arabemiraten Central
| USA:s försvarsdepartement |USA DoD, östra |USA DoD, centrala |
| US Government |US Gov, Arizona |US Gov, Texas |
| US Government |USA Gov, Iowa |US Gov, Virginia |
| US Government |US Gov, Virginia |US Gov, Texas |

> [!Important]
> - Västra Indien är bara kopplad till en riktning. Den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är central Indien.
> - Södra Brasilien är unikt eftersom det är länkat till en region utanför dess geografi. Södra Brasilien: s sekundära region är södra centrala USA. Södra centrala USA: s sekundära region är inte Brasilien, södra.


## <a name="an-example-of-paired-regions"></a>Ett exempel på länkade regioner
Bilden nedan illustrerar ett hypotetiskt program som använder det regionala paret för haveri beredskap. De gröna siffrorna markerar de olika aktiviteterna i de tre Azure-tjänsterna (Azure Compute, Storage och Database) och hur de är konfigurerade att replikeras mellan regioner. De unika fördelarna med att distribuera över kopplade områden är markerade med de orange numren.

![Översikt över fördelar med kopplade regioner](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Bild 2 – hypotetiskt, regionalt par för Azure

## <a name="cross-region-activities"></a>Aktiviteter över flera regioner
Som det hänvisas till i bild 2.

1. **Azure Compute (IaaS)** – du måste etablera ytterligare beräknings resurser i förväg för att säkerställa att resurserna är tillgängliga i en annan region under en katastrof. Mer information finns i [teknisk vägledning för Azure-återhämtning](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** – om du använder hanterade diskar kan du läsa mer om [säkerhets kopiering över flera regioner](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) med Azure Backup och [Replikera virtuella datorer](./site-recovery/azure-to-azure-tutorial-enable-replication.md) från en region till en annan med Azure Site Recovery. Om du använder lagrings konton konfigureras Geo-redundant lagring (GRS) som standard när ett Azure Storage-konto skapas. Med GRS replikeras dina data automatiskt tre gånger inom den primära regionen och tre gånger i den kopplade regionen. Mer information finns i [Azure Storage alternativ för redundans](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – med Azure SQL Database geo-replikering kan du konfigurera asynkron replikering av transaktioner till vilken region som helst i världen. Vi rekommenderar dock att du distribuerar dessa resurser i en kopplad region för de flesta katastrof återställnings scenarier. Mer information finns i [geo-replikering i Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager** – i Resource Manager isoleras komponenter logiskt mellan olika regioner automatiskt. Det innebär att logiska försök i en region är mindre sannolika att påverka ett annat.

## <a name="benefits-of-paired-regions"></a>Fördelar med kopplade regioner

5. **Fysisk isolering** – när det är möjligt föredrar Azure minst 300 miles av separering mellan data Center i ett regionalt par, även om detta inte är praktiskt eller möjligt i alla geografiska områden. Separation av fysiskt Data Center minskar sannolikheten för natur katastrofer, civila rester, strömavbrott eller fysiska nätverks avbrott som påverkar båda regionerna samtidigt. Isoleringen omfattas av begränsningarna inom geografin (geografisk storlek, tillgänglighet för ström-/nätverks infrastruktur, regler osv.).  

6. **Plattforms oberoende replikering** – vissa tjänster som Geo-Redundant Storage tillhandahåller automatisk replikering till den kopplade regionen.

7. **Region återställnings ordning** – i händelse av ett brett avbrott prioriteras återställning av en region av varje par. Program som distribueras i länkade regioner körs då garanterat i minst en prioriterad region. Om ett program distribueras mellan regioner som inte paras ihop, kan återställningen bli fördröjd – i värsta fall kan de valda regionerna vara de två sista som ska återställas.

8. **Sekventiella uppdateringar** – planerade Azure-systemuppdateringar distribueras till kopplade regioner sekventiellt (inte på samma gång) för att minimera stillestånd, påverkan av buggar och logiska fel i händelse av en felaktig uppdatering.

9. **Data placering** – en region finns inom samma geografiska område som dess par (med undantag för Brasilien) för att uppfylla data placering-krav för skatte-och rättsvårds syfte.