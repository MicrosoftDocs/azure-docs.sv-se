---
title: Migrera SAP HANA på Azure (stora instanser) till Azure Virtual Machines | Microsoft Docs
description: Så här migrerar du SAP HANA på Azure (stora instanser) till Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677036"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA vid migrering av stora Azure-instanser till Azure Virtual Machines
Den här artikeln beskriver möjliga scenarier för distribution av stora Azure-instanser och erbjuder planerings-och migrations metoder med minimerad över gångs drift

## <a name="overview"></a>Översikt
Eftersom meddelandet från de stora Azure-instanserna för SAP HANA (HLI) i september 2016 har många kunder antagit denna maskin vara som ett tjänst erbjudande för sin minnes intern beräknings plattform.  Under de senaste åren har Azures storleks tillägg för virtuella datorer, kopplade till stödet för HANA-utskalning, överskridit de flesta företags kunders kapacitets krav för ERP-databaser.  Vi börjar se kunder som uttrycker intresset att migrera sin SAP HANA arbets belastning från fysiska servrar till virtuella Azure-datorer.
Den här guiden är inte ett steg-för-steg-konfigurations dokument. Den beskriver vanliga distributions modeller och erbjuder rekommendationer för planering och migrering.  Avsikten är att kunna ta hänsyn till nödvändiga överväganden för förberedelser för att minimera över gångs avbrott.

## <a name="assumptions"></a>Antaganden
Den här artikeln gör följande antaganden:
- Den enda beräknade räntan är en homogen HANA-databas för beräknings tjänst från Hana stor instans (HLI) till virtuell Azure-dator utan betydande uppgradering eller uppdatering av program vara. Dessa mindre uppdateringar inkluderar användningen av en nyare OS-version eller HANA-version som uttryckligen anges som stöds av relevanta SAP-anteckningar. 
- Alla uppdateringar/uppgraderingar-aktiviteter måste utföras före eller efter migreringen.  Till exempel SAP HANA MCOS konvertering till MDC-distribution. 
- Den metod för migrering som skulle erbjuda den minsta stillestånds tiden är SAP HANA system replikering. Andra migrations metoder ingår inte i omfånget för det här dokumentet.
- Den här vägledningen gäller för både rev3-och Rev4-SKU: er för HLI.
- HANA-distributions arkitektur förblir huvudsakligen oförändrad under migreringen.  Det vill säga att ett system med en enda instans av DR förblir på samma sätt vid målet.
- Kunderna har granskat och förstått Serviceavtal (SLA) för mål-arkitekturen (to-the). 
- Kommersiella villkor mellan HLIs och virtuella datorer skiljer sig åt. Kunderna bör övervaka användningen av sina virtuella datorer för kostnads hantering.
- Kunderna förstår att HLI är en dedikerad beräknings plattform medan de virtuella datorerna körs på delad men isolerad infrastruktur.
- Kunderna har verifierat att mål datorerna har stöd för din avsedda arkitektur. Om du vill se alla VM-SKU: er som stöds för SAP HANA distribution, se [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Kunderna har verifierat design-och migrations planen.
- Planera för den virtuella datorn med katastrof återställning tillsammans med den primära platsen.  Kunder kan inte använda HLI som DR-nod för den primära platsen som körs på virtuella datorer efter migreringen.
- Kunderna kopierade de nödvändiga säkerhetskopieringsfilerna till virtuella datorer, baserat på affärs återställnings-och efterlevnads krav. Med tillgängliga säkerhets kopieringar för virtuella datorer kan du använda den för återställning till tidpunkt under över gångs perioden.
- För HSR HA måste kunderna konfigurera och konfigurera STONITH-enheten per SAP HANA HA guider för [SLES](./high-availability-guide-suse-pacemaker.md) och [RHEL](./high-availability-guide-rhel-pacemaker.md).  Den är inte förkonfigurerad som HLI-fodralet.
- Den här metoden för migrering behandlar inte HLI-SKU: er med Optane-konfigurationen.

## <a name="deployment-scenarios"></a>Distributionsscenarier
Vanliga distributions modeller med HLI-kunder sammanfattas i följande tabell.  Det går att migrera till virtuella Azure-datorer för alla HLI-scenarier.  För att kunna dra nytta av kompletterande tillgängliga Azure-tjänster kan mindre arkitektur ändringar krävas.

| Scenario-ID | HLI-scenario | Migrera till VM-orda Grant? | Markera om |
| --- | --- | --- | --- |
| 1 | [En nod med en SID](./hana-supported-scenario.md#single-node-with-one-sid) | Ja | - |
| 2 | [En nod med MCOS](./hana-supported-scenario.md#single-node-mcos) | Ja | - |
| 3 | [Enkel nod med DR med Storage Replication](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Inga | Storage Replication är inte tillgängligt med Azure Virtual Platform, ändra den aktuella DR-lösningen till antingen HSR eller säkerhets kopiering/återställning |
| 4 | [Enkel nod med DR (flera syften) med Storage Replication](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Inga | Storage Replication är inte tillgängligt med Azure Virtual Platform, ändra den aktuella DR-lösningen till antingen HSR eller säkerhets kopiering/återställning |
| 5 | [HSR med STONITH för hög tillgänglighet](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Ja | Inga förkonfigurerade SBD för virtuella mål datorer.  Välj och distribuera en STONITH-lösning.  Möjliga alternativ: Azure staket-agenten (stöds för både [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA med HSR, DR med Storage Replication](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Inga | Ersätt Storage Replication for DR-behoven med antingen HSR eller säkerhets kopiering/återställning |
| 7 | [Automatisk redundans för värd (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Ja | Använda ANF för delad lagring med virtuella Azure-datorer |
| 8 | [Skala ut med vänte läge](./hana-supported-scenario.md#scale-out-with-standby) | Ja | BW/4HANA med M128s, M416s, M416ms virtuella datorer med ANF enbart för lagring |
| 9 | [Skala ut utan vänte läge](./hana-supported-scenario.md#scale-out-without-standby) | Ja | BW/4HANA med M128s, M416s, M416ms VM (med eller utan användning av ANF för lagring) |
| 10 | [Skala ut med DR med hjälp av Storage Replication](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Inga | Ersätt Storage Replication for DR-behoven med antingen HSR eller säkerhets kopiering/återställning |
| 11 | [Enkel nod med DR med HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [Enkel nod HSR till DR (kostnads optimerad)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [HA och DR med HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [HA och DR med HSR (kostnads optimerad)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Skala ut med DR med hjälp av HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Ja | BW/4HANA med M128s. M416s, M416ms VM (med eller utan att använda ANF för lagring) |


## <a name="source-hli-planning"></a>Käll planering (HLI)
När du registrerar en HLI-server gick både Microsoft Service Management och kunder med planering av inställningarna för beräkning, nätverk, lagring och operativ system för att köra SAP HANA databasen.  Liknande planering måste äga rum för migreringen till den virtuella Azure-datorn.

### <a name="sap-hana-housekeeping"></a>SAP HANA underhåll 
Det är en bra idé att städa upp databas innehållet så att oönskade, inaktuella data eller inaktuella loggar inte migreras till den nya databasen.  Underhåll innebär vanligt vis att ta bort eller arkivera gamla, förfallna eller inaktiva data.  Dessa åtgärder för data hygien bör testas i icke-produktionssystem för att verifiera data trimningens giltighet innan produktions användningen.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Tillåt nätverks anslutning för nya virtuella datorer och virtuella nätverk 
I en kunds HLI-distribution har nätverket ställts in baserat på den information som beskrivs i artikeln [SAP HANA (stora instanser) nätverks arkitektur](./hana-network-architecture.md). Routning av nätverks trafik görs också på det sätt som beskrivs i avsnittet routning i Azure.
- När du konfigurerar en ny virtuell dator som migreringsmålet, om den placeras i det befintliga virtuella nätverket med IP-adressintervall som redan har behörighet att ansluta till HLI, krävs ingen ytterligare anslutnings uppdatering.
- Om den nya virtuella Azure-datorn placeras i en ny Microsoft Azure Virtual Network, kan finnas i en annan region och peer-kopplas med det befintliga virtuella nätverket, kan ExpressRoute-tjänstenyckeln och resurs-ID från den ursprungliga HLI-etableringen vara användbara för att tillåta åtkomst för det här nya IP-intervallet för virtuellt nätverk.  Koordinera med Microsoft Service Management för att aktivera det virtuella nätverket för HLI-anslutning.  Obs! för att minimera nätverks fördröjningen mellan program-och databas skikten måste både program-och databas skikten finnas i samma virtuella nätverk.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Befintlig tillgänglighets uppsättning för app Layer, Tillgänglighetszoner och närhets placerings grupp (PPG)
Den aktuella distributions modellen är färdig för att uppfylla vissa service nivå mål.  I den här flytten ser du till att mål infrastrukturen uppfyller eller överskrider de angivna målen.  
Mer troligt än inte, kunder SAP-program servrar placeras i en tillgänglighets uppsättning.  Om den aktuella distributions tjänst nivån är tillfredsställande och 
- Om den virtuella mål datorn antar värd namnet för det logiska HLI-namnet kan du uppdatera DNS-adressen (Domain Name Service) som pekar på den virtuella datorns IP-adress utan att uppdatera några SAP-profiler
- Om du inte använder PPG ska du placera alla program-och DB-servrar i samma zon för att minimera nätverks fördröjningen.
- Om du använder PPG kan du läsa avsnittet i det här dokumentet: mål planering, tillgänglighets uppsättning, Tillgänglighetszoner och närhets placerings grupp (PPG).

### <a name="storage-replication-discontinuance-process-if-used"></a>Avställnings process för lagrings replikering (om den används)
Om Storage Replication används som DR-lösning ska den avslutas (oplaneras) när SAP-programmet har stängts av.  Dessutom har den senaste SAP HANA katalogen, logg filen och data säkerhets kopieringarna repliker ATS till lagrings volymerna för fjärranslutna DR-HLI.  Detta är en försiktighets åtgärd om en katastrof uppstår under den fysiska servern till Azure VM-övergång.

### <a name="data-backups-preservation-consideration"></a>Överväganden vid bevarande av data säkerhets kopior
När du har övertagit till SAP HANA på den virtuella Azure-datorn är alla ögonblicks bildbaserade data eller logg säkerhets kopior på HLI inte lätt att komma åt eller återställas till en virtuell dator om det behövs. Innan den Azure-baserade säkerhets kopian i den tidigaste över gångs perioden skapar tillräckligt med historik för att uppfylla krav för återställning efter tidpunkt, rekommenderar vi att du använder säkerhets kopieringar på filnivå utöver ögonblicks bilder på HLI, dagar eller veckor innan du klipps över.  De här säkerhets kopiorna kopieras till ett Azure Storage-konto som är tillgängligt för den nya SAP HANA virtuella datorn.
Förutom att säkerhetskopiera HLI-innehållet är det klokt att ha fullständiga säkerhets kopior av SAP-landskapen lättillgängliga om en återställning behövs.

### <a name="adjusting-system-monitoring"></a>Justera system övervakning 
Kunderna använder många olika verktyg för att övervaka och skicka varnings meddelanden för system i sin SAP-liggande form.  Det här objektet är bara ett anrop för lämplig åtgärd för att införa ändringar för övervakning och uppdatera aviserings meddelandets mottagare om det behövs.

### <a name="microsoft-operations-team-involvement"></a>Microsoft Operations team-inblandning 
Öppna en biljett från Azure Portal baserat på den befintliga HLI-instansen.  När support ärendet har skapats kontaktar en support tekniker dig via e-post.  

### <a name="engage-microsoft-account-team"></a>Engagera Microsoft-konto-teamet
Planera migreringen nära förnyelse tiden för HLI-kontraktet för att minimera onödig kostnad för beräknings resurser. Om du vill inaktivera bladet HLI måste du samordna avslutningen av kontraktet och den faktiska avstängningen av enheten.

## <a name="destination-planning"></a>Destinations planering
Håll koll på en ny infrastruktur så att den kan ta en befintlig förtjänar, men se till att det nya tillägget passar i det stora av sakerna.  Nedan visas några viktiga punkter för Contemplation.

### <a name="resource-availability-in-the-target-region"></a>Resurs tillgänglighet i mål regionen 
Det aktuella distributions området för SAP-programservrar är vanligt vis nära nära varandra med tillhör ande HLIs.  HLIs erbjuds dock på färre platser än tillgängliga Azure-regioner.  När du migrerar den fysiska HLI till Azure VM, är det också en bra tid att finjustera avståndet mellan alla relaterade tjänster för prestanda optimering.  När du gör det är en viktig faktor att se till att den valda regionen har alla resurser som krävs.  Till exempel, tillgänglighet för en viss VM-familj eller Azure-zoner för hög tillgänglighets installation.

### <a name="virtual-network"></a>Virtuellt nätverk 
Kunder måste välja om de vill köra den nya HANA-databasen i ett befintligt virtuellt nätverk eller skapa en ny.  Den primära besluts faktorn är den aktuella nätverks utformningen för SAP-liggande.  Även när infrastrukturen går från en zon till en distribution med två zoner och använder PPG, tillämpar den arkitektur ändringar. Mer information finns i artikeln Azure- [PPG för optimal nätverks fördröjning med SAP-program](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Säkerhet
Oavsett om den nya SAP HANA VM-vilplan i ett nytt eller befintligt VNet/undernät, representerar den en ny affärs kritisk tjänst som kräver skydd.  Åtkomst kontroll som är kompatibel med företags information säkerhets princip bör utvärderas och distribueras för denna nya tjänst klass.

### <a name="vm-sizing-recommendation"></a>Rekommendation för VM-storlek
Den här migreringen är också en möjlighet att rätt storlek på din HANA Compute-motor.  En kan använda HANA- [systemvyer](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) tillsammans med Hana Studio för att förstå system resurs konsumtionen, vilket ger till gång till rätt storlek för att öka utgifts effektiviteten.

### <a name="storage"></a>Storage 
Lagrings prestanda är en av de faktorer som påverkar SAP-programmets användar upplevelse.  Bas på en given VM SKU finns minst en lagrings plats som har publicerats [SAP HANA konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md). Vi rekommenderar att du kontrollerar dessa minimi krav och jämför med den befintliga HLI system statistiken för att säkerställa tillräcklig IO-kapacitet och prestanda för den nya HANA-VM: en.

Om du konfigurerar PPG för den nya HANA-virtuella datorn och dess associerade allvarlighets grader skickar du ett support ärende för att kontrol lera och se till att lagrings platsen och den virtuella datorn befinner sig. Eftersom din säkerhets kopierings lösning kan behöva ändras, bör lagrings kostnaden också återanvändas för att undvika oväntade drifts utgifter.

### <a name="storage-replication-for-disaster-recovery"></a>Lagrings replikering för haveri beredskap
Med HLI erbjöds Storage Replication som standard alternativ för haveri beredskap. Den här funktionen är inte standard alternativet för SAP HANA på Azure VM. Överväg HSR, säkerhets kopiering/återställning eller andra lösningar som stöds och som uppfyller dina affärs behov.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Placerings grupper för tillgänglighets uppsättningar, Tillgänglighetszoner och närhet 
Om du vill förkorta avståndet mellan program lagret och SAP HANA att hålla nätverks fördröjningen minst måste den nya databasen och de aktuella SAP-programservrarna placeras i en PPG. Se [placerings gruppen närhet](./sap-proximity-placement-scenarios.md) för att lära dig hur Azures tillgänglighets uppsättning och Tillgänglighetszoner fungerar med PPG för SAP-distributioner.
Om medlemmar i mål HANA-systemet distribueras i fler än en Azure-zon bör kunderna ha en tydlig vy över svars profilen för de valda zonerna. Placeringen av SAP-system komponenter är optimalt för proximal avstånd mellan SAP-program och-databasen.  [Testverktyget för tillgänglighets området](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) för den offentliga domänen gör det enklare att mäta måttet.  


### <a name="backup-strategy"></a>Säkerhets kopierings strategi
Många kunder använder redan säkerhets kopierings lösningar från tredje part för SAP HANA på HLI.  I så fall måste du konfigurera ytterligare en skyddad virtuell dator och HANA-databaser.  Pågående HLI säkerhets kopierings jobb kan nu schemaläggas om datorn tas ur bruk efter migreringen.
Azure Backup för SAP HANA på den virtuella datorn är nu allmänt tillgänglig.  Se dessa länkar om du vill ha detaljerad information om: [säkerhets kopiering](../../../backup/backup-azure-sap-hana-database.md), [återställning](../../../backup/sap-hana-db-restore.md), [hantering](../../../backup/sap-hana-db-manage.md) SAP HANA säkerhets kopiering på virtuella Azure-datorer.

### <a name="dr-strategy"></a>DR-strategi
Om dina service nivå mål har en längre återställnings tid är en enkel säkerhets kopiering till Blob Storage och återställning på plats eller återställning till en ny virtuell dator den enklaste och billigaste DR-strategin.  
Precis som den stora instans plattformen där HANA DR vanligt vis görs med HSR; På den virtuella Azure-datorn är HSR också den mest naturliga och interna SAP HANA DR-lösningen.  Oavsett om käll distributionen är enkel instans eller klustrad, krävs en replik av käll infrastrukturen i DR-regionen.
Den här DR-repliken kommer att konfigureras när den primära HLI har slutförts.  DR HANA DB registreras på den primära SAP HANA på VM-instansen som en sekundär replikerings plats.  

### <a name="sap-application-server-connectivity-destination-change"></a>Ändring av anslutnings mål för SAP-Programserver
HSR-migreringen resulterar i en ny HANA DB-värd och därmed ett nytt DB-värdnamn för program lagret, och SAP-profiler måste ändras för att återspegla det nya värd namnet.  Om växlingen görs genom namn matchning som bevarar värd namnet krävs ingen profil ändring.

### <a name="operating-system"></a>Operativsystem
Operativ system avbildningarna för HLI och virtuella datorer, trots att de är på samma versions nivå, är SLES 12 SP4, till exempel inte identiska. Kunderna måste validera nödvändiga paket, snabb korrigeringar, korrigeringar, kernel-och säkerhets korrigeringar på HLI för att installera samma paket på målet.  Det finns stöd för att använda HSR för att replikera från ett äldre operativ system till en virtuell dator med en nyare OS-version.  Kontrol lera de speciella versioner som stöds genom att granska [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Ny SAP-licens förfrågan
Ett enkelt anrop för att begära en ny SAP-licens för det nya HANA-systemet nu när den har migrerats till virtuella datorer.

### <a name="service-level-agreement-sla-differences"></a>Skillnader i service nivå avtal (SLA)
Författarna vill ta ut skillnaden mellan tillgänglighets-SLA mellan HLI och Azure VM.  Till exempel erbjuder klustrade HLIs-par 99,99% tillgänglighet. För att uppnå samma service avtal måste en distribuera virtuella datorer i tillgänglighets zoner. I den här [artikeln](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) beskrivs tillgänglighet med tillhör ande distributions arkitekturer så att kunderna kan planera sin mål infrastruktur på lämpligt sätt.


## <a name="migration-strategy"></a>Migreringsstrategi
I det här dokumentet tar vi bara upp metoden HANA-systemreplikering för migreringen från HLI till den virtuella Azure-datorn.  Beroende på vilken mål lagrings lösning som distribueras, är processen annorlunda. De övergripande stegen beskrivs nedan.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuell dator med Premium/Ultra-disks för data
För virtuella datorer som har distribuerats med Premium eller Ultra disks, är standard konfigurationen för SAP HANA system replikering tillgänglig för att konfigurera HSR.  [SAP-hjälp artikeln](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) innehåller en översikt över de steg som ingår när du konfigurerar systemreplikering, tar över ett sekundärt system, växlar tillbaka till den primära och inaktiverar systemreplikering.  För migreringen behöver vi bara konfigurera, ta över och inaktivera replikeringsinställningar.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Virtuell dator med ANF för data-och logg volymer
På hög nivå måste de senaste HLI lagrings ögonblicks bilderna av de fullständiga data-och logg volymerna kopieras till Azure Storage var de är tillgängliga och kan återskapas av målet HANA VM.  Kopierings processen kan göras med valfritt internt verktyg för Linux-kopiering.  

> [!IMPORTANT]
> Kopiering och data överföring kan ta flera timmar beroende på databas storleken HANA och nätverks bandbredden.  Mass kopierings processen bör göras i förväg av de primära HANA-DATABASens stillestånds tid.

### <a name="mcos-to-mdc-conversion"></a>MCOS till MDC-konvertering
En distributions modell för flera komponenter i en system (MCOS) användes av några av våra HLI-kunder.  Motivationen var att kringgå begränsningen av lagrings ögonblicks bilder för flera databaser (MDC) för tidigare SAP HANA versioner.  I MCOS-modellen staplas flera oberoende SAP HANAs instanser i ett HLI-blad.  Att använda HSR för migreringen fungerar bra men resulterar i flera HANA-VM: ar med en klient databas var och en.  Detta slut tillstånd gör ett busier landskap än vad en kund kan ha varit van vid.  Med SAP HANA 2,0 standard-distribution som MDC är ett användbart alternativ att utföra [Hana-klient flyttning](https://launchpad.support.sap.com/#/notes/2472478) efter migreringen av HSR.  Den här processen konsoliderar dessa oberoende HANA-databaser till klienter i en enda HANA-behållare.  

### <a name="application-layer-consideration"></a>Övervägande för program lager
DB-servern visas som centrum för ett SAP-system.  Alla program servrar bör finnas nära SAP HANA DB.  I vissa fall när ny användning av PPG önskas kan du flytta befintliga program servrar till PPG där den virtuella HANA-datorn kan krävas.  Det kan vara lättare att skapa nya program servrar om du redan har mallar för distribution.  
Om befintliga program servrar och den nya HANA-VM: en är optimalt placerade, behöver inga nya program servrar skapas om inte ytterligare kapacitet krävs.  
Om en ny infrastruktur har skapats för att förbättra tjänstens tillgänglighet kan befintliga program servrar bli onödigt och bör stängas av och tas bort.
Om den virtuella mål datorns värdnamn har ändrats och skiljer sig från HLI-värdnamnet måste SAP Application Server-profilerna justeras så att de pekar på den nya värden.  Om endast HANA DB IP-adressen har ändrats krävs en uppdatering av DNS-posten för att kunna leda inkommande anslutningar till den nya HANA-datorn.

### <a name="acceptance-test"></a>Godkännande test
Även om migreringen från HLI till VM inte gör något material ändringar i databas innehållet jämfört med en heterogen migrering rekommenderar vi fortfarande att verifiera viktiga funktioner och prestanda aspekt av den nya installationen.  

### <a name="cutover-plan"></a>Start punkt-plan
Även om migreringen är rakt framåt, innebär det dock att en befintlig databas tas ur bruk.  Noggrann planering för att bevara käll systemet med tillhör ande innehåll och säkerhets kopierings avbildningar är viktiga vid återställning av fallet.  En lämplig planering erbjuder en speedier återföring.   


## <a name="post-migration"></a>Efter migreringen
Migreringsjobbet görs inte förrän vi har säkert frikopplat alla HLI-beroende tjänster eller anslutningar för att säkerställa att data integriteten bevaras.  Stäng också av onödiga tjänster.  Det här avsnittet anropar några av de viktigaste objekten.

### <a name="decommissioning-the-hli"></a>Avställning av HLI
Efter en lyckad migrering av HANA DB till Azure VM ser du till att inga produktiva affärs transaktioner körs på HLI-databasen.  Att hålla HLI som körs under en viss tids period är dock en säker metod som garanterar speedier-återställning om det behövs.  Sedan bör bladet HLI tas ur bruk.  Kunderna bör uppfylla sina HLI-åtaganden med Microsoft genom att kontakta sina Microsoft-representanter.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Ta bort alla proxyservrar (t. ex. program varan iptables, BIGIP) som kon figurer ATS för HLI 
Om en proxy-tjänst som program varan iptables används för att dirigera lokal trafik till och från HLI, behövs den inte längre efter den lyckade migreringen till den virtuella datorn.  Den här anslutnings tjänsten bör dock behållas så länge HLI-bladet fortfarande står kvar.  Stäng bara tjänsten när bladet HLI är helt inaktive rad.

### <a name="remove-global-reach-for-hli"></a>Ta bort Global Reach för HLI 
Global Reach används för att ansluta kundernas ExpressRoute-Gateway med HLI ExpressRoute-gatewayen.  Det gör det möjligt för kunders lokala trafik att komma åt HLI-klienten direkt utan att använda en proxy-tjänst.  Den här anslutningen behövs inte längre i frånvaro av HLI-enheten efter migreringen.  Precis som i program varan iptables proxy-tjänsten bör GlobalReach också vara kvar tills HLI-bladet är helt inaktiverat.

### <a name="operating-system-subscription--movereuse"></a>Operativ systemets prenumeration – flytta/Återanvänd
Eftersom VM-servrarna är Stood och HLI-bladen har inaktiverats, kan OS-prenumerationer ersättas eller återanvändas för att undvika dubbel betalning av OS-licenser.



## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:
- [SAP HANA infrastruktur konfiguration och åtgärder på Azure](./hana-vm-operations.md).
- [SAP-arbetsbelastningar på Azure: planering och distribution check lista](./sap-deployment-checklist.md).
