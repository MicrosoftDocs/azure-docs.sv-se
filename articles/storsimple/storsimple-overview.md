---
title: Översikt över StorSimple 8000-serien | Microsoft Docs
description: Beskriver StorSimple-nivåer, enhet, virtuell enhet, tjänster och lagrings hantering och introducerar viktiga termer som används i StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/02/2021
ms.author: timlt
ms.openlocfilehash: 3f0ec053d806208b4a3c15604714f6bdb87399e7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613229"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serien: en hybrid moln lagrings lösning

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Välkommen till Microsoft Azure StorSimple, en integrerad lagrings lösning som hanterar lagrings aktiviteter mellan lokala enheter och Microsoft Azure moln lagring. StorSimple är en effektiv, kostnads effektiv och lätt att hantera storage area network (SAN)-lösning som eliminerar många av de problem och utgifter som är associerade med företags lagring och data skydd. Den tillverkarspecifika enheten StorSimple 8000 används, och lösningen kan integreras med molntjänster. Du får en uppsättning hanteringsverktyg och en enhetlig vy av hela företagslagringen inklusive molnlagring. (Den StorSimple distributions information som publicerats på Microsoft Azure webbplats gäller endast StorSimple 8000-seriens enheter. Om du använder en enhet med StorSimple 5000/7000-serien går du till [StorSimple-hjälpen](http://onlinehelp.storsimple.com/).)

StorSimple använder [lagrings nivåer](#automatic-storage-tiering) för att hantera lagrade data över olika lagrings medier. Den aktuella arbets minnet lagras lokalt på solid state-hårddiskar (SSD). Data som används mindre ofta lagras på hård diskar (HDD) och Arkiv data skickas till molnet. Dessutom använder StorSimple deduplicering och komprimering för att minska mängden lagrings utrymme som data förbrukar. Mer information finns i [deduplicering och komprimering](#deduplication-and-compression). För definitioner av andra viktiga termer och begrepp som används i dokumentationen för StorSimple 8000-serien, gå till [StorSimple-terminologi](#storsimple-terminology) i slutet av den här artikeln.

Förutom lagrings hantering gör StorSimple data skydds funktioner det möjligt för dig att skapa säkerhets kopior på begäran och schemalagda säkerhets kopieringar och sedan lagra dem lokalt eller i molnet. Säkerhets kopieringar görs i form av stegvisa ögonblicks bilder, vilket innebär att de kan skapas och återställas snabbt. Moln ögonblicks bilder kan vara viktiga för haveri beredskap eftersom de ersätter sekundära lagrings system (t. ex. band säkerhets kopiering) och gör att du kan återställa data till ditt data Center eller till alternativa platser om det behövs.

![video ikon](./media/storsimple-overview/video_icon.png) Titta på videon om du vill ha en snabb introduktion till Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Varför ska jag använda StorSimple?
I följande tabell beskrivs några av de viktiga fördelarna som Microsoft Azure StorSimple tillhandahåller.

| Funktion | Fördelar |
| --- | --- |
| Transparent integrering |Använder iSCSI-protokollet för att länka data lagrings utrymmen i bakgrunden. Data som lagras i molnet, i data centret eller på fjärrservrar, kan lagras på en enda plats. |
| Minskade lagrings kostnader |Allokerar tillräckligt lokalt eller moln lagrings utrymme för att uppfylla aktuella krav och utökar endast moln lagring vid behov. Det minskar lagrings kraven och kostnaderna ytterligare genom att eliminera redundanta versioner av samma data (deduplicering) och med komprimering. |
| Förenklad lagrings hantering |Innehåller system administrations verktyg för att konfigurera och hantera data som lagras lokalt, på en fjärrserver och i molnet. Dessutom kan du hantera säkerhets kopierings-och återställnings funktioner från en snapin-modul i Microsoft Management Console (MMC).|
| Förbättrad katastrof återställning och efterlevnad |Kräver ingen utökad återställnings tid. I stället återställs data när det behövs, så att normala åtgärder kan fortsätta med minimalt avbrott. Dessutom kan du konfigurera principer för att ange säkerhets kopierings scheman och datakvarhållning. |
| Data mobilitet |Data som överförs till Microsoft Azure moln tjänster kan nås från andra platser för återställnings-och migrerings syfte. Dessutom kan du använda StorSimple för att konfigurera StorSimple-moln utrustning på virtuella datorer som körs i Microsoft Azure. De virtuella datorerna kan sedan använda virtuella enheter för att komma åt lagrade data för test-eller återställnings syfte. |
| Verksamhetskontinuitet |Tillåter att användare i StorSimple 5000-7000-serien migrerar sina data till en StorSimple 8000-serie enhet. |
| Tillgänglighet i Azure Governments portalen |StorSimple finns i Azure Government-portalen. Mer information finns i [distribuera din lokala StorSimple-enhet i myndighets portalen](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Data skydd och tillgänglighet |StorSimple 8000-serien har stöd för Zone-redundant lagring (ZRS), förutom lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Läs [den här artikeln om Azure Storage redundans alternativ](../storage/common/storage-redundancy.md) för ZRS-information. |
| Stöd för kritiska program |Med StorSimple kan du identifiera lämpliga volymer som fästa lokalt för att säkerställa att data som krävs av kritiska program inte skiktas i molnet. Lokalt fästa volymer omfattas inte av moln fördröjningar eller anslutnings problem. Mer information om lokalt fästa volymer finns i [använda tjänsten StorSimple Enhetshanteraren för att hantera volymer](storsimple-8000-manage-volumes-u2.md). |
| Låg latens och höga prestanda |Du kan skapa moln utrustning som drar nytta av funktionerna för hög prestanda och låg latens i Azure Premium Storage. Mer information om StorSimple Premium Cloud-apparater finns i [distribuera och hantera en StorSimple Cloud Appliance i Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-komponenter
Den Microsoft Azure StorSimple lösningen innehåller följande komponenter:

* **Microsoft Azure StorSimple enhet** – en lokal hybrid lagrings mat ris som innehåller SSD och hård diskar, tillsammans med redundanta styrenheter och funktioner för automatisk redundans. Styrenheterna hanterar lagrings nivåer, placerar data som används för närvarande (eller frekvent) på lokal lagring (på enheten eller lokala servrar) och flyttar mindre data som används ofta till molnet.
* **StorSimple Cloud Appliance** – även kallat den virtuella StorSimple-enheten. En program varu version av StorSimple-enheten som replikerar arkitekturen och de flesta funktioner i den fysiska hybrid lagrings enheten. StorSimple Cloud Appliance körs på en enskild nod på en virtuell Azure-dator. Premium-virtuella enheter, som utnyttjar Azure Premium Storage, är tillgängliga i uppdatering 2 och senare.
* **StorSimple Enhetshanteraren service** – en utökning av Azure Portal som gör att du kan hantera en StorSimple-enhet eller StorSimple Cloud Appliance från ett enda webb gränssnitt. Du kan använda tjänsten StorSimple Enhetshanteraren för att skapa och hantera tjänster, Visa och hantera enheter, Visa aviseringar, hantera volymer och Visa och hantera säkerhets kopierings principer och säkerhets kopierings katalogen.
* **Windows PowerShell för StorSimple** – ett kommando rads gränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din StorSimple-enhet, konfigurera nätverks gränssnittet på enheten, installera vissa typer av uppdateringar, Felsöka enheten genom att komma åt support sessionen och ändra enhetens tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till serie konsolen eller använda Windows PowerShell-fjärrkommunikation.
* **Azure PowerShell StorSimple-cmdletar** – en samling Windows PowerShell-cmdletar som gör att du kan automatisera uppgifter på tjänst nivå och migrering från kommando raden. Mer information om Azure PowerShell-cmdletar för StorSimple finns i [cmdlet-referensen](/powershell/module/servicemanagement/azure.service/#azure).
* **StorSimple Snapshot Manager** – en MMC-snapin-modul som använder volym grupper och Windows-tjänsten Volume Shadow Copy för att generera programkonsekventa säkerhets kopieringar. Dessutom kan du använda StorSimple Snapshot Manager för att skapa säkerhets kopierings scheman och klona eller återställa volymer.
* **StorSimple Adapter för SharePoint** – ett verktyg som transparent utökar Microsoft Azure StorSimple lagring och data skydd till SharePoint-servergrupper, samtidigt som du gör StorSimple-lagring synlig och hanterbar från SharePoint Central administrations Portal.

Diagrammet nedan ger en övergripande bild av Microsoft Azure StorSimple arkitektur och komponenter.

![StorSimple-arkitektur](./media/storsimple-overview/overview-big-picture.png)

I följande avsnitt beskrivs var och en av dessa komponenter i större detalj och förklarar hur lösningen ordnar data, allokerar lagring och underlättar lagrings hantering och data skydd. Det sista avsnittet innehåller definitioner för några av de viktiga termer och begrepp som är relaterade till StorSimple-komponenter och deras hantering.

## <a name="storsimple-device"></a>StorSimple-enhet
Den Microsoft Azure StorSimple enheten är en lokal hybrid lagrings mat ris som tillhandahåller primär lagring och iSCSI-åtkomst till data som lagras på den. Den hanterar kommunikation med moln lagring och hjälper till att säkerställa säkerheten och konfidentialiteten för alla data som lagras i Microsoft Azure StorSimples lösningen.

StorSimple-enheten innehåller SSD och hård diskar (HDD), samt stöd för klustring och automatisk redundans. Den innehåller en delad processor, delad lagring och två speglade styrenheter. Varje kontrollant tillhandahåller följande:

* Anslutning till en värddator
* Upp till sex nätverks portar för att ansluta till det lokala nätverket (LAN)
* Maskin varu övervakning
* Icke-flyktigt RAM-minne (Random Access Memory) som bevarar information även om strömförsörjningen avbryts
* Kluster medveten uppdatering för att hantera program uppdateringar på servrar i ett redundanskluster så att uppdateringarna har minimal eller ingen påverkan på tjänstens tillgänglighet
* Klustertjänsten, som fungerar som ett backend-kluster, vilket ger hög tillgänglighet och minimerar eventuella negativa effekter som kan uppstå om en hård disk eller SSD Miss lyckas eller tas offline

Endast en kontrollant är aktiv vid en viss tidpunkt. Om den aktiva styrenheten Miss lyckas aktive ras den andra styrenheten automatiskt.

Mer information finns på [StorSimple maskin varu komponenter och status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Du kan använda StorSimple för att skapa en moln installation som replikerar arkitekturen och funktionerna i den fysiska hybrid lagrings enheten. StorSimple Cloud Appliance (kallas även den virtuella StorSimple) körs på en enskild nod på en virtuell Azure-dator. (En moln installation kan bara skapas på en virtuell Azure-dator. Du kan inte skapa en på en StorSimple-enhet eller en lokal server.)

Moln installationen har följande funktioner:

* Den fungerar som en fysisk installation och kan erbjuda ett iSCSI-gränssnitt till virtuella datorer i molnet.
* Du kan skapa ett obegränsat antal moln enheter i molnet och aktivera och inaktivera dem vid behov.
* Det kan hjälpa till att simulera lokala miljöer i haveri beredskap, utvecklings-och test scenarier och kan hjälpa dig att hämta objekt på objekt nivå från säkerhets kopior.

StorSimple Cloud Appliance finns i två modeller: 8010-enheten (tidigare kallat 1100-modell) och 8020-enheten. 8010-enheten har en maximal kapacitet på 30 TB. 8020-enheten, som utnyttjar Azure Premium Storage, har en maximal kapacitet på 64 TB. (På lokala nivåer lagrar Azure Premium Storage data på SSD medan standard lagring lagrar data på hård diskar.) Du måste ha ett Azure Premium Storage-konto för att kunna använda Premium Storage.

Mer information om StorSimple Cloud Appliance finns i [distribuera och hantera en StorSimple Cloud Appliance i Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänsten
Microsoft Azure StorSimple tillhandahåller ett webbaserat användar gränssnitt (StorSimple Enhetshanteraren-tjänsten) som gör det möjligt att centralt hantera data Center och moln lagring. Du kan använda tjänsten StorSimple Enhetshanteraren för att utföra följande uppgifter:

* Konfigurera systeminställningar för StorSimple-enheter.
* Konfigurera och hantera säkerhets inställningar för StorSimple-enheter.
* Konfigurera autentiseringsuppgifter och egenskaper för molnet.
* Konfigurera och hantera volymer på en server.
* Konfigurera volym grupper.
* Säkerhetskopiera och återställa data.
* Övervaka prestanda.
* Granska Systeminställningar och identifiera möjliga problem.

Du kan använda tjänsten StorSimple Enhetshanteraren för att utföra alla administrativa uppgifter förutom uppgifter som kräver systemets tids period, till exempel inledande installation och installation av uppdateringar.

Mer information finns i [använda tjänsten StorSimple Enhetshanteraren för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell för StorSimple
Windows PowerShell för StorSimple tillhandahåller ett kommando rads gränssnitt som du kan använda för att skapa och hantera tjänsten Microsoft Azure StorSimple och konfigurera och övervaka StorSimple-enheter. Det är ett Windows PowerShell-baserat kommando rads gränssnitt som innehåller dedikerade cmdlets för att hantera din StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan:

* Registrera en enhet.
* Konfigurera nätverks gränssnittet på en enhet.
* Installera vissa typer av uppdateringar.
* Felsök enheten genom att komma åt support sessionen.
* Ändra enhetens tillstånd.

Du kan komma åt Windows PowerShell för StorSimple från en serie konsol (på en värddator som är ansluten direkt till enheten) eller via fjärr anslutning med hjälp av Windows PowerShell-fjärrkommunikation. Vissa Windows PowerShell för StorSimple uppgifter, till exempel den första enhets registreringen, kan bara göras i serie konsolen.

Mer information finns i [använda Windows PowerShell för StorSimple för att administrera enheten](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdletar
De Azure PowerShell StorSimple-cmdletarna är en samling Windows PowerShell-cmdletar som gör att du kan automatisera uppgifter på tjänst nivå och migrering från kommando raden. Mer information om Azure PowerShell-cmdletar för StorSimple finns i [cmdlet-referensen](/powershell/module/servicemanagement/azure.service/).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager är en snapin-modul för Microsoft Management Console (MMC) som du kan använda för att skapa konsekventa säkerhets kopior av lokala data och moln data vid en viss tidpunkt. Snapin-modulen körs på en Windows Server-baserad värd. Du kan använda StorSimple-Snapshot Manager för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper för att säkerställa att säkerhetskopierade data är programkonsekventa.
* Hantera säkerhets kopierings principer så att data säkerhets kopie ras enligt ett förutbestämt schema och lagras på en angiven plats (lokalt eller i molnet).
* Återställa volymer och enskilda filer.

Säkerhets kopior samlas in som ögonblicks bilder, som endast registrerar ändringarna sedan den senaste ögonblicks bilden togs och kräver mindre lagrings utrymme än fullständiga säkerhets kopieringar. Du kan skapa säkerhets kopierings scheman eller utföra omedelbara säkerhets kopieringar om det behövs. Dessutom kan du använda StorSimple Snapshot Manager för att upprätta lagrings principer som styr hur många ögonblicks bilder som ska sparas. Om du senare behöver återställa data från en säkerhets kopia kan StorSimple Snapshot Manager välja från katalogen med lokala eller molnbaserade ögonblicks bilder. 

Om en katastrof inträffar eller om du behöver återställa data av en annan anledning, kan StorSimple Snapshot Manager återställas stegvis eftersom det behövs. Data återställning kräver inte att du stänger av hela systemet när du återställer en fil, ersätter utrustning eller flyttar åtgärder till en annan plats.

Mer information finns i [StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple-adapter för SharePoint
Microsoft Azure StorSimple inkluderar StorSimple Adapter för SharePoint, en valfri komponent som transparent utökar StorSimple-lagring och data skydds funktioner till SharePoint-servergrupper. Kortet fungerar med en provider för fjärran sluten Blob Storage (RBS) och SQL Server RBS-funktionen, så att du kan flytta BLOBBAR till en server som säkerhets kopie ras av Microsoft Azure StorSimple systemet. Microsoft Azure StorSimple lagrar sedan BLOB-data lokalt eller i molnet, baserat på användning.

StorSimple-adaptern för SharePoint hanteras inifrån Central administrations portalen för SharePoint. Därför förblir SharePoint-hanteringen centraliserad och all lagring visas i SharePoint-servergruppen.

Mer information finns på [StorSimple Adapter för SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Lagrings hanterings tekniker
Förutom den dedikerade StorSimple-enheten, den virtuella enheten och andra komponenter använder Microsoft Azure StorSimple följande program varu tekniker för att ge snabb åtkomst till data och för att minska lagrings förbrukningen:

* [Automatisk lagrings nivå](#automatic-storage-tiering) 
* [Tunn allokering](#thin-provisioning) 
* [Deduplicering och komprimering](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatisk lagrings nivå
Microsoft Azure StorSimple ordnar automatiskt data i logiska nivåer baserat på aktuell användning, ålder och relation till andra data. Data som är mest aktiva lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. Följande diagram illustrerar den här lagrings metoden.

![StorSimple lagrings nivåer](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

För att aktivera snabb åtkomst lagrar StorSimple mycket aktiva data (frekventa data) på SSD i StorSimple-enheten. Den lagrar data som används ibland (varm data) på hård diskar i enheten eller på servrar i data centret. Den flyttar inaktiva data, säkerhets kopierings data och data som bevaras för arkivering eller efterlevnad i molnet. 

> [!NOTE]
> I uppdatering 2 eller senare kan du ange en volym som fäst lokalt, vilket innebär att data förblir kvar på den lokala enheten och inte är i nivå av molnet. 


StorSimple justerar och ordnar om data och lagrings tilldelningar när användnings mönster ändras. Till exempel kan viss information bli mindre aktiv över tid. Eftersom det blir progressivt mindre aktivt migreras det från SSD till hård diskar och sedan till molnet. Om samma data blir aktiva igen migreras den tillbaka till lagrings enheten.

Processen för lagrings nivåer sker på följande sätt:

1. En system administratör konfigurerar ett Microsoft Azure moln lagrings konto.
2. Administratören använder serie konsolen och tjänsten StorSimple Enhetshanteraren (körs i Azure Portal) för att konfigurera enhets-och fil servern, skapa volymer och data skydds principer. Lokala datorer (till exempel fil servrar) använder iSCSI (Internet Small Computer System Interface) för att få åtkomst till StorSimple-enheten.
3. Från början lagrar StorSimple data på enhetens fast SSD-nivå.
4. När SSD-nivån närmar sig kapacitet, StorSimple deduplicerar och komprimerar de äldsta data blocken och flyttar dem till hård disk nivån.
5. När hård disk nivån närmar sig kapaciteten, krypterar StorSimple de äldsta data blocken och skickar dem säkert till Microsoft Azure lagrings konto via HTTPS.
6. Microsoft Azure skapar flera repliker av data i sitt Data Center och i ett fjärranslutna Data Center, vilket säkerställer att data kan återställas om en katastrof inträffar.
7. När fil servern begär data som lagras i molnet returnerar StorSimple det sömlöst och lagrar en kopia på SSD-nivån på StorSimple-enheten.

> [!IMPORTANT]
> När du använder StorSimple ska du inte konvertera blobbar till arkivering, även om enheten har fasats ut. Om du vill hämta data från enheten måste du extrahera Blobbarna från arkivering till frekvent eller låg frekvent typ, vilket ger betydande kostnader.

#### <a name="how-storsimple-manages-cloud-data"></a>Hur StorSimple hanterar moln data

StorSimple deduplicerar kunddata över alla ögonblicks bilder och primära data (data som skrivits av värdar). Även om dedupliceringen är bra för lagrings effektiviteten, blir frågan om "Vad är i molnet" komplicerat. De nivåbaserade primära data och ögonblicks bild data överlappar varandra. Ett enda data segment i molnet kan användas som data skiktade primära data och kan också refereras till av flera ögonblicks bilder. Varje moln ögonblicks bild säkerställer att en kopia av alla tidpunkts data är låst i molnet tills ögonblicks bilden tas bort.

Data tas bara bort från molnet när det inte finns några referenser till dessa data. Om vi till exempel har tagit en moln ögonblicks bild av alla data som finns i StorSimple-enheten och sedan tagit bort vissa primära data, skulle vi se att de _primära data_ släpps direkt. _Moln data_, som innehåller data på nivå och säkerhets kopior, förblir desamma eftersom en ögonblicks bild fortfarande refererar till moln data. När moln ögonblicks bilden har tagits bort (och en annan ögonblicks bild som refererade till samma data), släpps moln förbrukningen. Innan vi tar bort moln data kontrollerar vi att inga ögonblicks bilder fortfarande refererar till dessa data. Den här processen kallas _skräp insamling_ och är en bakgrunds tjänst som körs på enheten. Borttagning av moln data sker inte omedelbart när skräp insamlings tjänsten söker efter andra referenser till dessa data före borttagningen. Hastigheten för skräp insamlingen beror på det totala antalet ögonblicks bilder och den totala datan. Normalt rensas moln data på mindre än en vecka.


### <a name="thin-provisioning"></a>Tunn allokering
Tunn allokering är en virtualiseringsteknik där tillgängligt lagrings utrymme verkar överskrida fysiska resurser. I stället för att reservera tillräckligt med lagrings utrymme i förväg, använder StorSimple tunn etablering för att allokera tillräckligt med utrymme för att uppfylla de aktuella kraven. Den elastiska typen av moln lagring underlättar den här metoden eftersom StorSimple kan öka eller minska moln lagringen för att möta föränderliga krav.

> [!NOTE]
> Lokalt fästa volymer är inte tunt allokerade. Lagring som tilldelas till en lokal volym tillhandahålls i sin helhet när volymen skapas.


### <a name="deduplication-and-compression"></a>Deduplicering och komprimering
Microsoft Azure StorSimple använder deduplicering och data komprimering för att ytterligare minska lagrings kraven.

Deduplicering minskar den totala mängden data som lagras genom att eliminera redundans i den lagrade data uppsättningen. När information ändras ignorerar StorSimple de data som inte har ändrats och endast ändringarna samlas in. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort onödig information. 

> [!NOTE]
> Data på lokalt fästa volymer är inte deduplicerade eller komprimerade. Säkerhets kopiering av lokalt fästa volymer dedupliceras dock och komprimeras.


## <a name="storsimple-workload-summary"></a>StorSimple arbets belastnings Sammanfattning
En sammanfattning av de StorSimple-arbetsbelastningar som stöds är tabellen nedan.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Samarbete |Fildelning |Ja | |Alla versioner |
| Samarbete |Delning av distribuerad fil |Ja | |Alla versioner |
| Samarbete |SharePoint |Ja* |Stöds bara med lokalt fästa volymer |Uppdatering 2 och senare |
| Arkivering |Enkel filarkivering |Ja | |Alla versioner |
| Virtualisering |Virtuella datorer |Ja* |Stöds bara med lokalt fästa volymer |Uppdatering 2 och senare |
| Databas |SQL |Ja* |Stöds bara med lokalt fästa volymer |Uppdatering 2 och senare |
| Videoövervakning |Videoövervakning |Ja* |Stöds endast om StorSimple-enheten endast är dedikerad för den här arbets belastningen |Uppdatering 2 och senare |
| Backup |Säkerhets kopia av primärt mål |Ja* |Stöds endast om StorSimple-enheten endast är dedikerad för den här arbets belastningen |Uppdatering 3 och senare |
| Backup |Säkerhets kopia av sekundärt mål |Ja* |Stöds endast om StorSimple-enheten endast är dedikerad för den här arbets belastningen |Uppdatering 3 och senare |

*Ja&#42; lösnings rikt linjer och begränsningar bör tillämpas.*

Följande arbets belastningar stöds inte av enheter med StorSimple 8000-serien. Om den distribueras på StorSimple, kommer dessa arbets belastningar att resultera i en konfiguration som inte stöds.

* Medicinsk bild
* Exchange
* VDI
* Oracle
* SAP
* Stordata
* Innehålls distribution
* Starta från SCSI

Nedan följer en lista över de infrastruktur komponenter som stöds av StorSimple.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Allmänt |Express Route |Ja | |Alla versioner |
| Allmänt |DataCore FC |Ja* |Stöds med DataCore SANsymphony |Alla versioner |
| Allmänt |DFSR |Ja* |Stöds bara med lokalt fästa volymer |Alla versioner |
| Allmänt |Indexering |Ja* |För volymer med nivåer stöds endast metadata-indexering (inga data).<br>Fullständig indexering stöds för lokalt fästa volymer. |Alla versioner |
| Allmänt |Virusskydd |Ja* |För nivåbaserade volymer stöds endast genomsökning vid öppen och Close.<br> För lokalt fästa volymer stöds fullständig genomsökning. |Alla versioner |

*Ja&#42; lösnings rikt linjer och begränsningar bör tillämpas.*

Nedan följer en lista över annan program vara som används med StorSimple för att bygga lösningar.

| Arbets belastnings typ | Program vara som används med StorSimple | Versioner som stöds|Länk till lösnings guide| 
| --- | --- | --- | --- |
| Säkerhetskopiera mål |Veeam |Veeam v 9 och senare |[StorSimple som ett säkerhets kopierings mål med Veaam](storsimple-configure-backup-target-veeam.md)|
| Säkerhetskopiera mål |Veritas Backup Exec |Säkerhetskopiera exec 16 och senare |[StorSimple som ett säkerhets kopierings mål med Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Säkerhetskopiera mål |Veritas NetBackup |NetBackup 7.7. x och senare  |[StorSimple som ett säkerhets kopierings mål med NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Global fildelning <br></br> Samarbete |Talon  |[StorSimple med Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple-terminologi
Innan du distribuerar din Microsoft Azure StorSimple-lösning rekommenderar vi att du läser igenom följande villkor och definitioner.

### <a name="key-terms-and-definitions"></a>Viktiga termer och definitioner
| Term (akronym eller förkortning) | Beskrivning |
| --- | --- |
| åtkomst kontroll post (ACR) |En post som är kopplad till en volym på din Microsoft Azure StorSimple enhet som avgör vilka värdar som kan ansluta till den. Bestämningen baseras på iSCSI-kvalificerat namn (IQN) för värdarna (som finns i ACR) som ansluter till din StorSimple-enhet. |
| AES-256 |En algoritm på 256-bitars Advanced Encryption Standard (AES) för kryptering av data när den flyttas till och från molnet. |
| storlek på allokeringsenhet (Australien) |Den minsta mängd disk utrymme som kan allokeras för att lagra en fil i dina Windows-filsystem. Om en fil storlek inte är jämnt delbar med kluster storleken måste det extra utrymmet användas för att lagra filen (upp till nästa multipel av kluster storleken), vilket resulterar i förlorat utrymme och fragmentering av hård disken. <br>Den rekommenderade mappen för Azure StorSimple-volymer är 64 KB eftersom den fungerar bra med algoritmerna för deduplicering. |
| automatiserad lagrings nivå |Automatiskt flytta mindre aktiva data från SSD till hård diskar och sedan till en nivå i molnet och sedan aktivera hantering av all lagring från ett centralt användar gränssnitt. |
| säkerhets kopierings katalog |En samling säkerhets kopior, vanligt vis relaterat av den program typ som användes. Den här samlingen visas på bladet säkerhets kopierings katalog i användar gränssnittet för StorSimple-Enhetshanteraren. |
| Säkerhetskopiera katalog fil |En fil som innehåller en lista med tillgängliga ögonblicks bilder som för närvarande lagras i säkerhets kopierings databasen för StorSimple Snapshot Manager. |
| säkerhets kopierings princip |Ett urval av volymer, typ av säkerhets kopiering och en tidsplan som gör det möjligt att skapa säkerhets kopior enligt ett fördefinierat schema. |
| Binary Large Objects (BLOBBAR) |En samling binära data som lagras som en enskild enhet i ett databas hanterings system. BLOBBAR är vanligt vis bilder, ljud eller andra multimedie objekt, även om binär körbar kod lagras som en BLOB. |
| CHAP (Challenge Handshake Authentication Protocol) |Ett protokoll som används för att autentisera peer-anslutningen, baserat på peer-delning av ett lösen ord eller en hemlighet. CHAP kan vara enkelriktat eller ömsesidigt. Med enkelriktad CHAP autentiserar målet en initierare. Ömsesidig CHAP kräver att målet autentiserar initieraren och att initieraren autentiserar målet. |
| clone |En duplicerad kopia av en volym. |
| Molnet som en nivå (CaaT) |Moln lagring integrerad som en nivå inom lagrings arkitekturen så att all lagring visas som en del av ett företags lagrings nätverk. |
| Cloud Service Provider (CSP) |En leverantör av tjänster för molnbaserad data behandling. |
| moln ögonblicks bild |En tidpunkts kopia av volym data som lagras i molnet. En moln ögonblicks bild motsvarar en ögonblicks bild som replikeras på ett annat lagrings system utanför platsen. Moln ögonblicks bilder är särskilt användbara i scenarier med haveri beredskap. |
| krypterings nyckel för moln lagring |Ett lösen ord eller en nyckel som används av din StorSimple-enhet för att komma åt krypterade data som skickas av enheten till molnet. |
| kluster medveten uppdatering |Hantera program uppdateringar på servrar i ett kluster för växling vid fel, så att uppdateringarna har minimal eller ingen påverkan på tjänstens tillgänglighet. |
| datapath |En samling funktions enheter som utför samkopplade data bearbetnings åtgärder. |
| inaktivera |En permanent åtgärd som bryter anslutningen mellan StorSimple-enheten och den tillhör ande moln tjänsten. Moln ögonblicks bilder av enheten finns kvar efter den här processen och kan klonas eller användas för haveri beredskap. |
| disk spegling |Replikering av logiska disk volymer på separata hård diskar i real tid för att säkerställa kontinuerlig tillgänglighet. |
| dynamisk disk spegling |Replikering av logiska disk volymer på dynamiska diskar. |
| dynamiska diskar |Ett disk volym format som använder LDM (Logical Disk Manager) för att lagra och hantera data på flera fysiska diskar. Dynamiska diskar kan för storas för att ge mer ledigt utrymme. |
| EBOD-hölje (Extended bunt of Disks) |En sekundär inne slutning av din Microsoft Azure StorSimples enhet som innehåller extra hård disk diskar för ytterligare lagrings utrymme. |
| fett etablering |En konventionell lagrings etablering där lagrings utrymmet allokeras baserat på förväntade behov (och är vanligt vis utöver det aktuella behovet). Se även *tunn allokering*. |
| hård disk (HDD) |En enhet som använder roterande Platters för att lagra data. |
| Hybrid moln lagring |En lagrings arkitektur som använder lokala resurser och resurser på plats, inklusive moln lagring. |
| ISCSI (Internet Small Computer System Interface) |En Internet Protocol (IP)-baserad lagrings nätverks standard för länkning av data lagrings utrustning eller anläggningar. |
| iSCSI-initierare |En program varu komponent som gör det möjligt för en värd dator som kör Windows att ansluta till ett externt iSCSI-baserat lagrings nätverk. |
| Kvalificerat iSCSI-namn (IQN) |Ett unikt namn som identifierar ett iSCSI-mål eller en initierare. |
| iSCSI-mål |En program varu komponent som tillhandahåller centraliserade iSCSI-diskenheter i San-nätverk. |
| Live-arkivering |En lagrings metod där arkiverings data är åtkomliga hela tiden (till exempel inte lagras på en annan plats på bandet). Microsoft Azure StorSimple använder Live-arkivering. |
| lokalt fäst volym |en volym som finns på enheten och skiktas aldrig till molnet. |
| lokal ögonblicks bild |En tidpunkts kopia av volym data som lagras på den Microsoft Azure StorSimple enheten. |
| Microsoft Azure StorSimple |En kraftfull lösning som består av en lagrings enhet och program vara för data Center som gör det möjligt för IT-organisationer att utnyttja moln lagring som om det är data Center lagring. StorSimple fören klar data skydd och data hantering samtidigt som kostnaderna minskar. Lösningen konsoliderar primär lagring, arkivering, säkerhets kopiering och haveri beredskap (DR) genom sömlös integrering med molnet. Genom att kombinera SAN-lagring och moln data hantering i en plattform i företags klass, kan StorSimple-enheter möjliggöra hastighet, enkelhet och pålitlighet för alla lagrings behov. |
| Energi-och kylnings modul (PCM) |Maskin varu komponenter för din StorSimple-enhet som består av strömförsörjningen och kyl fläkten. Därför är modulen för namn kraft och kylning. Enhetens primära enhet har två 764W PCMs medan EBOD-kabinettet har två 580W PCMs. |
| primärt kabinett |Huvud kammaren för din StorSimple-enhet som innehåller program plattforms styrenheterna. |
| mål för återställnings tid (RTO) |Den längsta tid som ska användas innan en affärs process eller ett system återställs helt efter en katastrof. |
| SAS (Serial Attached SCSI) |En typ av hård disk (HDD). |
| krypterings nyckel för tjänst data |En nyckel som har gjorts tillgänglig för alla nya StorSimple-enheter som registreras med StorSimple Enhetshanteraren-tjänsten. De konfigurations data som överförs mellan StorSimple Enhetshanteraren-tjänsten och enheten krypteras med en offentlig nyckel och kan sedan bara dekrypteras på enheten med hjälp av en privat nyckel. Krypterings nyckeln för tjänst data gör att tjänsten kan hämta den privata nyckeln för dekryptering. |
| tjänst registrerings nyckel |En nyckel som gör det möjligt att registrera StorSimple-enheten med StorSimple Enhetshanteraren-tjänsten så att den visas i Azure Portal för ytterligare hanterings åtgärder. |
| SCSI (Small Computer System Interface) |En uppsättning standarder för att fysiskt ansluta datorer och skicka data mellan dem. |
| SSD (Solid State Drive) |En disk som inte innehåller några rörliga delar. till exempel en flash-enhet. |
| storage account |En uppsättning autentiseringsuppgifter som är länkade till ditt lagrings konto för en specifik moln tjänst leverantör. |
| StorSimple-adapter för SharePoint |En Microsoft Azure StorSimple-komponent som transparent utökar StorSimple lagring och data skydd till SharePoint-servergrupper. |
| StorSimple Device Manager-tjänsten |Ett tillägg till Azure Portal som gör att du kan hantera dina lokala Azure-StorSimple och virtuella enheter. |
| StorSimple Snapshot Manager |En snapin-modul i Microsoft Management Console (MMC) för att hantera säkerhets kopierings-och återställnings åtgärder i Microsoft Azure StorSimple. |
| ta säkerhets kopia |En funktion som gör att användaren kan ta en interaktiv säkerhets kopia av en volym. Det är ett alternativt sätt att göra en manuell säkerhets kopiering av en volym i stället för att göra en automatisk säkerhets kopiering via en definierad princip. |
| tunn allokering |En metod för att optimera effektiviteten med vilken det tillgängliga lagrings utrymmet används i lagrings system. I tunn allokering allokeras lagringen mellan flera användare baserat på det minsta utrymme som krävs av varje användare vid en specifik tidpunkt. Se även *fat-etablering*. |
| nivåer |Ordna data i logiska grupper baserat på aktuell användning, ålder och relation till andra data. StorSimple ordnar automatiskt data i nivåer. |
| volym |Logiska lagrings områden som presenteras i form av enheter. StorSimple-volymer motsvarar de volymer som monteras av värden, inklusive de volymer som upptäcks genom användningen av iSCSI och en StorSimple-enhet. |
| Volym behållare |En gruppering av volymer och de inställningar som gäller för dem. Alla volymer i din StorSimple-enhet är grupperade i volym behållare. Volym behållar inställningarna omfattar lagrings konton, krypterings inställningar för data som skickas till molnet med tillhör ande krypterings nycklar och bandbredd som används för åtgärder som involverar molnet. |
| volym grupp |I StorSimple Snapshot Manager är en volym grupp en samling volymer som har kon figurer ATS för att under lätta säkerhets kopieringen. |
| Tjänsten Volume Shadow Copy (VSS) |En Windows Server-tjänst för operativ system som underlättar program konsekvens genom att kommunicera med VSS-medvetna program för att koordinera skapandet av stegvisa ögonblicks bilder. VSS säkerställer att programmen är tillfälligt inaktiva när ögonblicks bilder tas. |
| Windows PowerShell för StorSimple |Ett Windows PowerShell-baserat kommando rads gränssnitt som används för att hantera din StorSimple-enhet. Samtidigt som några av de grundläggande funktionerna i Windows PowerShell upprätthålls, har det här gränssnittet ytterligare dedikerade cmdlets som är kopplade till att hantera en StorSimple-enhet. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).