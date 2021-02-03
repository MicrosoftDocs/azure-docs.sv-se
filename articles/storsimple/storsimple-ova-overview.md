---
title: Översikt över Microsoft Azure StorSimple virtuell matris
description: Beskriver den virtuella StorSimple-matrisen, en integrerad lagrings lösning som hanterar lagrings aktiviteter mellan en lokal virtuell matris och Microsoft Azure moln lagring.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: af48e1f415e0ca0b1027d277f70c3f0f1a11e687
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526773"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduktion till den virtuella StorSimple-matrisen

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Översikt

Den Microsoft Azure StorSimple virtuella matrisen är en integrerad lagrings lösning som hanterar lagrings aktiviteter mellan en lokal virtuell matris som körs i en hypervisor och Microsoft Azure moln lagring. Den virtuella matrisen är en effektiv, kostnads effektiv och enkel hanterad fil server eller iSCSI server-lösning som eliminerar många av de problem och kostnader som är kopplade till företags lagring och data skydd. Den virtuella matrisen passar särskilt bra för lagring av data som inte används ofta.

Den här artikeln innehåller en översikt över den virtuella matrisen – här är några andra resurser:

* Bästa praxis finns i [metod tips för virtuella StorSimple-matriser](storsimple-ova-best-practices.md).
* En översikt över enheter med StorSimple 8000-serien finns i [StorSimple 8000-serien: en hybrid moln lösning](storsimple-overview.md).
* Information om enheter med StorSimple 5000/7000-serien finns i [onlinehjälpen för StorSimple](http://onlinehelp.storsimple.com/).

Den virtuella matrisen stöder iSCSI-eller SMB-protokollet (Server Message Block). Den körs på din befintliga hypervisor-infrastruktur och ger lagrings nivåer för molnet, säkerhets kopiering av molnet, snabb återställning, återställning på objekt nivå och funktioner för haveri beredskap.

I följande tabell sammanfattas viktiga funktioner i den virtuella StorSimple-matrisen.

| Funktion | StorSimple virtuell matris |
| --- | --- |
| Installations krav |Använder Virtualization Infrastructure (Hyper-V eller VMware) |
| Tillgänglighet |Enkel nod |
| Total kapacitet (inklusive moln) |Upp till 64 TB användbar kapacitet per virtuell matris |
| Lokal kapacitet |390 GB till 6,4 TB användbar kapacitet per virtuell matris (du måste etablera 500 GB till 8 TB disk utrymme) |
| Ursprungliga protokoll |iSCSI eller SMB |
| Mål för återställningstid (RTO) |iSCSI: mindre än 2 minuter oavsett storlek |
| Mål för återställningspunkt (RPO) |Dagliga säkerhets kopieringar och säkerhets kopieringar på begäran |
| Lagrings nivåer |Använder värme mappning för att avgöra vilka data som ska ske i nivå av eller ut |
| Support |Virtualiseringsbaserad infrastruktur som stöds av leverantören |
| Prestanda |Varierar beroende på underliggande infrastruktur |
| Data mobilitet |Kan återställa till samma enhet eller göra en återställning på objekt nivå (fil Server) |
| Lagringsnivåer |Lokal hypervisor-lagring och moln |
| Resurs storlek |Skiktat: upp till 20 TB; lokalt fäst: upp till 2 TB |
| Volym storlek |Skiktad: 500 GB till 5 TB; lokalt fäst: 50 GB till 200 GB <br> Maximal lokal reservation för skiktade volymer är 200 GB. |
| Ögonblicksbilder |Kraschkonsekvent |
| Återställning på objektnivå |Ja användare kan återställa från resurser |

## <a name="why-use-storsimple"></a>Varför ska jag använda StorSimple?

StorSimple ansluter användare och servrar till Azure Storage på bara några minuter, utan program ändringar.

I följande tabell beskrivs några av de viktiga fördelarna med StorSimple Virtual Array-lösningen.

| Funktion | Fördelar |
| --- | --- |
| Transparent integrering |Den virtuella matrisen stöder iSCSI-eller SMB-protokollet. Data förflyttningen mellan den lokala nivån och moln nivån är sömlös och transparent för användaren. |
| Minskade lagrings kostnader |Med StorSimple kan du etablera tillräckligt med lokal lagring för att uppfylla de aktuella kraven för de mest använda frekventa data. Allteftersom lagrings behoven växer kan StorSimple-nivåer kalla data till kostnads effektiv moln lagring. Data dedupliceras och komprimeras innan de skickas till molnet för att ytterligare minska lagrings kraven och kostnaderna. |
| Förenklad lagrings hantering |StorSimple tillhandahåller centraliserad hantering i molnet med hjälp av StorSimple Enhetshanteraren för att hantera flera enheter. |
| Förbättrad katastrof återställning och efterlevnad |StorSimple underlättar snabbare haveri beredskap genom att återställa metadata omedelbart och återställa data efter behov. Normala åtgärder kan fortsätta med minimalt avbrott. |
| Data mobilitet |Data som är på nivå av molnet kan nås från andra platser för återställnings-och migrerings syfte. Du kan bara återställa data till den ursprungliga virtuella matrisen. Du kan dock använda funktioner för haveri beredskap för att återställa hela den virtuella matrisen till en annan virtuell matris. |



## <a name="storsimple-workload-summary"></a>StorSimple arbets belastnings Sammanfattning

En sammanfattning av StorSimple arbets belastningar som stöds är tabellen nedan.

|Scenario     |Arbetsbelastning     |Stöds      |Begränsningar               | Versioner som gäller|
|-------------|-------------|---------------|---------------------------|--------------------|
|ROBO (Remote Office/avdelnings kontor)  |Fildelning     |Yes      |Se [Max gränsen för fil Server](storsimple-ova-limits.md).<br></br>Se [system krav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |
|Moln arkivering  |Fildelning i Arkiv     |Yes      |Se [Max gränsen för fil Server](storsimple-ova-limits.md).<br></br>Se [system krav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |

Den virtuella StorSimple-matrisen passar bäst för data som används sällan. Även om den virtuella matrisen har en lokal cache för att öka prestandan, bör användarna anta att enhets tjänsterna på den lägsta lagrings nivån (molnet). Varje virtuell matris kan skriva och läsa i Azure Storage med cirka 100 Mbit/s. Länken delas över alla begär Anden som kommer till enheten och kan bli en Flask hals som visas i diagrammet nedan.

![Moln arkivering](./media/storsimple-ova-overview/cloud-archiving.png)

När flera samtidiga användare har åtkomst till den virtuella matrisen, delar alla anslutningen till Azure, vilket leder till lägre prestanda. Det finns inga garanterade prestanda per användare och enheten bearbetar enskilda förfrågningar när de tas emot.

StorSimple virtuella matris passar inte för arbets belastningar som kräver hög tillgänglighet. Den virtuella matrisen är en enhet med en nod som upplever nedtid när program uppdateringar installeras. Administratörer bör planera för en underhålls period på 30 minuter 3-4 gånger per år.

## <a name="workflows"></a>Arbetsflöden

Den virtuella StorSimple-matrisen är särskilt lämplig för följande arbets flöden:

* [Molnbaserad lagrings hantering](#cloud-based-storage-management)
* [Plats oberoende säkerhets kopiering](#location-independent-backup)
* [Data skydd och haveri beredskap](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Molnbaserad lagrings hantering
Du kan använda tjänsten StorSimple Enhetshanteraren som körs i Azure Portal för att hantera data som lagras på flera enheter och på flera platser. Detta är särskilt användbart i scenarier med distribuerade grenar. Du måste skapa separata instanser av tjänsten StorSimple Enhetshanteraren för att hantera virtuella matriser och fysiska StorSimple-enheter. Den virtuella matrisen använder nu den nya Azure Portal i stället för den klassiska Azure-portalen.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![molnbaserad lagrings hantering](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Plats oberoende säkerhets kopiering
Med den virtuella matrisen tillhandahåller moln ögonblicks bilder en plats oberoende, tidpunkts kopia av en volym eller resurs. Moln ögonblicks bilder är aktiverade som standard och kan inte inaktive ras. Alla volymer och resurser säkerhets kopie ras samtidigt via en enda daglig säkerhets kopierings princip, och du kan ta ytterligare ad hoc-säkerhetskopieringar när det behövs.

### <a name="data-protection-and-disaster-recovery"></a>Data skydd och haveri beredskap
Den virtuella matrisen stöder följande scenarier för data skydd och haveri beredskap:

* **Återställning av volym eller resurs** – Använd återställningen som nytt arbets flöde för att återställa en volym eller resurs. Använd den här metoden för att återställa hela volymen eller resursen.
* **Återställning på objekt nivå** – resurser ger förenklad åtkomst till de senaste säkerhets kopieringarna. Du kan enkelt återställa en enskild fil från en särskild mapp *. säkerhets kopia* som är tillgänglig i molnet. Den här återställnings funktionen är användar driven och ingen administrativ åtgärd krävs.
* **Haveri beredskap** – Använd funktionen redundans för att återställa alla volymer eller resurser till en ny virtuell matris. Du skapar den nya virtuella matrisen och registrerar den med StorSimple-Enhetshanteraren tjänsten och växlar sedan över den ursprungliga virtuella matrisen. Den nya virtuella matrisen kommer sedan att anta de etablerade resurserna.

## <a name="storsimple-virtual-array-components"></a>StorSimple virtuella array-komponenter

Den virtuella matrisen innehåller följande komponenter:

* [Virtuell matris](#virtual-array) – en hybrid moln lagrings enhet som baseras på en virtuell dator som tillhandahålls i din virtualiserade miljö eller hypervisor.
* [StorSimple Enhetshanteraren service](#storsimple-device-manager-service) – en utökning av Azure Portal som låter dig hantera en eller flera StorSimple-enheter från ett enda webb gränssnitt som du kan komma åt från olika geografiska platser. Du kan använda tjänsten StorSimple Enhetshanteraren för att skapa och hantera tjänster, Visa och hantera enheter och aviseringar och hantera volymer, resurser och befintliga ögonblicks bilder.
* [Lokalt webb användar gränssnitt](#local-web-user-interface) – ett WEBbaserat användar gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med StorSimple Enhetshanteraren-tjänsten. 
* [Kommando rads gränssnitt](#command-line-interface) – ett Windows PowerShell-gränssnitt som du kan använda för att starta en support-session på den virtuella matrisen.
  I följande avsnitt beskrivs varje komponent i större detalj och förklarar hur lösningen ordnar data, allokerar lagring och underlättar lagrings hantering och data skydd.

### <a name="virtual-array"></a>Virtuell matris

Den virtuella matrisen är en lagrings lösning med en nod som tillhandahåller primär lagring, hanterar kommunikation med moln lagring och hjälper till att säkerställa säkerheten och konfidentialiteten för alla data som lagras på enheten.

Den virtuella matrisen är tillgänglig i en modell som är tillgänglig för nedladdning. Den virtuella matrisen har en maximal kapacitet på 6,4 TB på enheten (med ett underliggande lagrings krav på 8 TB) och 64 TB inklusive moln lagring.

Den virtuella matrisen har följande funktioner:

* Det är kostnads effektivt. Den använder din befintliga Virtualization-infrastruktur och kan distribueras på din befintliga Hyper-V-eller VMware-hypervisor.
* Den finns i data centret och kan konfigureras som en iSCSI-server eller en fil server.
* Den är integrerad med molnet.
* Säkerhets kopior lagras i molnet, vilket kan under lätta haveri beredskap och förenkla återställningen på objekt nivå (ILR).
* Du kan tillämpa uppdateringar på den virtuella matrisen, precis som du skulle tillämpa dem på en fysisk enhet.

> [!NOTE]
> Det går inte att expandera en virtuell matris. Därför är det viktigt att tillhandahålla tillräckligt med lagrings utrymme när du skapar den virtuella matrisen.

### <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänsten

Microsoft Azure StorSimple tillhandahåller ett webbaserat användar gränssnitt, tjänsten StorSimple Enhetshanteraren som gör det möjligt att centralt hantera StorSimple-lagring. Du kan använda tjänsten StorSimple Enhetshanteraren för att utföra följande uppgifter:

* Hantera flera virtuella StorSimple-matriser från en enda tjänst.
* Konfigurera och hantera säkerhets inställningar för virtuella StorSimple-matriser. (Kryptering i molnet är beroende av Microsoft Azure API: er.)
* Konfigurera autentiseringsuppgifter och egenskaper för lagrings konto.
* Konfigurera och hantera volymer eller resurser.
* Säkerhetskopiera och återställa data på volymer eller resurser.
* Övervaka prestanda.
* Granska Systeminställningar och identifiera möjliga problem.

Du kan använda tjänsten StorSimple Enhetshanteraren för att göra daglig administration av den virtuella matrisen.

Mer information finns i [använda tjänsten StorSimple Enhetshanteraren för att administrera StorSimple-enheten](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokalt webb användar gränssnitt

Den virtuella matrisen innehåller ett webbaserat användar gränssnitt som används för konfiguration av en gång och registrering av enheten med StorSimple Enhetshanteraren-tjänsten. Du kan använda den för att stänga av och starta om den virtuella matrisen, köra diagnostiska tester, uppdatera program vara, ändra enhetens administratörs lösen ord, Visa system loggar och kontakta Microsoft Support för att skicka en tjänst förfrågan.

Information om hur du använder det webbaserade användar gränssnittet finns i [använda det webbaserade gränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Kommando rads gränssnitt

Med Windows PowerShell-gränssnittet som ingår kan du starta en support-session med Microsoft Support så att de kan hjälpa dig att felsöka och lösa problem som kan uppstå i den virtuella matrisen.

## <a name="storage-management-technologies"></a>Lagrings hanterings tekniker

Förutom den virtuella matrisen och andra komponenter använder StorSimple-lösningen följande program varu tekniker för att ge snabb åtkomst till viktiga data, minska lagrings förbrukningen och skydda data som lagras i den virtuella matrisen:

* [Automatisk lagrings nivå](#automatic-storage-tiering) 
* [Lokalt fästa resurser och volymer](#locally-pinned-shares-and-volumes)
* Deduplicering och komprimering för data skiktad eller säkerhets kopie ras till molnet 
* [Schemalagda säkerhets kopieringar och säkerhets kopieringar på begäran](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatisk lagrings nivå
Den virtuella matrisen använder en ny mekanism för skiktning för att hantera lagrade data över den virtuella matrisen och molnet. Det finns bara två nivåer: den lokala virtuella matrisen och Azures moln lagring. StorSimple virtuella matris ordnar automatiskt data till nivåerna baserat på en värme karta som spårar aktuell användning, ålder och relationer till andra data. Data som är mest aktiva (hetaste) lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. (Alla säkerhets kopior lagras i molnet.) StorSimple justerar och ordnar om data och lagrings tilldelningar när användnings mönster ändras. Till exempel kan viss information bli mindre aktiv över tid. När den blir progressivt mindre aktiv ligger den i ett steg i molnet. Om samma data aktive ras på nytt, bevaras den i lagringsmatrisen.

Data för en viss resurs eller volym på nivån garanterar sitt eget lokala lagrings utrymme (cirka 10 procent av det totala etablerade utrymmet för resursen eller volymen). Även om det minskar tillgängligt lagrings utrymme på den virtuella matrisen för resursen eller volymen, säkerställer det att nivåer för en resurs eller volym inte påverkas av nivå behoven för andra resurser eller volymer. En mycket upptagen arbets belastning på en resurs eller volym kan därför inte tvinga alla andra arbets belastningar till molnet.

Nivåbaserade volymer som skapas för iSCSI har en maximal lokal reservation på 200 GB oavsett storleken på volymen.

![automatisk lagrings nivå](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Du kan ange en volym som fixerad lokalt, vilket innebär att data förblir kvar på den virtuella matrisen och inte på nivå av molnet. Mer information finns på [lokalt fästa resurser och volymer](#locally-pinned-shares-and-volumes).

> [!IMPORTANT]
> När du använder StorSimple ska du inte konvertera blobbar till arkivering, även om enheten har fasats ut. Om du vill hämta data från enheten måste du extrahera Blobbarna från arkivering till frekvent eller låg frekvent typ, vilket ger betydande kostnader.


### <a name="locally-pinned-shares-and-volumes"></a>Lokalt fästa resurser och volymer

Du kan skapa lämpliga resurser och volymer som fästa lokalt. Den här funktionen säkerställer att data som krävs av kritiska program finns kvar i den virtuella matrisen och att de inte skiktas i molnet. Lokalt fästa resurser och volymer har följande funktioner:

* De omfattas inte av moln fördröjningar eller anslutnings problem.
* De har fortfarande nytta av StorSimple-funktioner för moln säkerhets kopiering och haveri beredskap.

Du kan återställa en lokalt fästad resurs eller volym som en nivå eller en delad resurs eller volym som fästs lokalt. 

Mer information om lokalt fästa volymer finns i [använda tjänsten StorSimple Enhetshanteraren för att hantera volymer](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplicering och komprimering för data skiktad eller säkerhets kopie ras till molnet

StorSimple använder deduplicering och data komprimering för att ytterligare minska lagrings kraven i molnet. Deduplicering minskar den totala mängden data som lagras genom att eliminera redundans i den lagrade data uppsättningen. När information ändras ignorerar StorSimple de data som inte har ändrats och endast ändringarna samlas in. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort Duplicerad information.

> [!NOTE]
> Data som lagras på den virtuella matrisen dedupliceras eller komprimeras inte. All deduplicering och komprimering sker precis innan data skickas till molnet.


### <a name="scheduled-and-on-demand-backups"></a>Schemalagda säkerhets kopieringar och säkerhets kopieringar på begäran

Med StorSimple Data Protection-funktioner kan du skapa säkerhets kopior på begäran. Ett standard schema för säkerhets kopiering säkerställer dessutom att data säkerhets kopie ras dagligen. Säkerhets kopieringar görs i form av stegvisa ögonblicks bilder som lagras i molnet. Ögonblicks bilder, som endast registrerar ändringarna sedan den senaste säkerhets kopieringen, kan skapas och återställas snabbt. Dessa ögonblicks bilder kan vara viktiga för haveri beredskap eftersom de ersätter sekundära lagrings system (till exempel säkerhets kopiering på band) och gör att du kan återställa data till ditt data Center eller till alternativa platser om det behövs.

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple-Enhetshanteraren för virtuell serie samlar in personlig information i två nyckel instanser:
 - Varna användar inställningar där e-postadresser till användare har kon figurer ATS. Den här informationen kan rensas av administratören. 
 - Användare som kan komma åt data på resurserna. En lista över användare som kan komma åt resurs data visas och kan exporteras. Den här listan tas bort när resursen tas bort.

Mer information hittar du i [Sekretess policyn för Microsoft i säkerhets Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbereder den virtuella mat ris portalen](storsimple-virtual-array-deploy1-portal-prep.md).
