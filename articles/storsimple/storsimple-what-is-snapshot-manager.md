---
title: Vad är StorSimple Snapshot Manager? | Microsoft Docs
description: Beskriver StorSimple-Snapshot Manager, dess arkitektur och dess funktioner.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017992"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>En introduktion till StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul för Microsoft Management Console (MMC) som fören klar data skydd och säkerhets kopierings hantering i en Microsoft Azure StorSimple miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple data i Data Center och i molnet som en enda integrerad lagrings lösning, vilket fören klar säkerhets kopierings processerna och minskar kostnaderna.

Den här översikten beskriver StorSimple-Snapshot Manager, beskriver dess funktioner och förklarar dess roll i Microsoft Azure StorSimple. 

En översikt över hela Microsoft Azure StorSimple system, inklusive StorSimple-enheten, StorSimple Manager service, StorSimple Snapshot Manager och StorSimple Adapter för SharePoint finns i [StorSimple 8000-serien: en hybrid moln lagrings lösning](storsimple-overview.md). 

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuella matriser (även kallade StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple uppdatering 2 på din StorSimple-enhet måste du ladda ned den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple uppdatering 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibel och fungerar med alla utgivna versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple-Snapshot Manager måste du uppdatera den (du behöver inte avinstallera den tidigare versionen innan du installerar den nya versionen).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager syfte och arkitektur
StorSimple Snapshot Manager tillhandahåller en central hanterings konsol som du kan använda för att skapa konsekventa säkerhets kopior av lokala data och moln data vid en viss tidpunkt. Du kan till exempel använda-konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper för att säkerställa att säkerhetskopierade data är programkonsekventa.
* Hantera säkerhets kopierings principer så att data säkerhets kopie ras enligt ett förutbestämt schema.
* Skapa lokala och molnbaserade ögonblicks bilder, som kan lagras i molnet och används för haveri beredskap.

StorSimple-Snapshot Manager hämtar listan över program som har registrerats med VSS-providern på värden. För att skapa programkonsekventa säkerhets kopieringar kontrollerar den sedan vilka volymer som används av ett program och föreslår volym grupper som ska konfigureras. StorSimple Snapshot Manager använder dessa volym grupper för att skapa säkerhets kopior som är programkonsekventa. (Program konsekvens finns när alla relaterade filer och databaser synkroniseras och representerar programmets sanna tillstånd vid en viss tidpunkt.) 

StorSimple Snapshot Manager säkerhets kopieringar tar form av stegvisa ögonblicks bilder, som bara fångar in ändringarna sedan den senaste säkerhets kopieringen. Därför kräver säkerhets kopieringar mindre lagrings utrymme och kan skapas och återställas snabbt. StorSimple Snapshot Manager använder Windows tjänsten Volume Shadow Copy (VSS) för att säkerställa att ögonblicks bilder fångar programkonsekventa data. (Mer information finns i avsnittet integration med Windows tjänsten Volume Shadow Copy.) Med StorSimple Snapshot Manager kan du skapa säkerhets kopierings scheman eller vidta omedelbara säkerhets kopieringar vid behov. Om du behöver återställa data från en säkerhets kopia kan StorSimple Snapshot Manager välja från en katalog med lokala eller molnbaserade ögonblicks bilder. Azure StorSimple återställer bara de data som behövs vid behov, vilket förhindrar fördröjningar i data tillgänglighet under återställnings åtgärder.)

![StorSimple Snapshot Manager-arkitektur](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager-arkitektur** 

## <a name="support-for-multiple-volume-types"></a>Stöd för flera volym typer
Du kan använda StorSimple-Snapshot Manager för att konfigurera och säkerhetskopiera följande typer av volymer: 

* **Standard volymer** – en enkel volym är en enda partition på en standard disk. 
* **Enkla volymer** – en enkel volym är en dynamisk volym som innehåller disk utrymme från en enda dynamisk disk. En enkel volym består av en enda region på en disk eller flera regioner som är länkade till varandra på samma disk. (Du kan bara skapa enkla volymer på dynamiska diskar.) Enkla volymer är inte feltoleranta.
* **Dynamiska volymer** – en dynamisk volym är en volym som skapas på en dynamisk disk. Dynamiska diskar använder en databas för att spåra information om volymer som finns på dynamiska diskar på en dator. 
* **Dynamiska volymer med spegling** – dynamiska volymer med spegling bygger på RAID 1-arkitekturen. Med RAID 1 skrivs identiska data på två eller flera diskar, vilket genererar en speglad uppsättning. En Read-begäran kan sedan hanteras av vilken disk som helst som innehåller begärda data.
* **Klusterdelade volymer** – med klusterdelade volymer (CSV: er) kan flera noder i ett redundanskluster samtidigt läsa eller skriva till samma disk. Redundans från en nod till en annan nod kan ske snabbt, utan att det krävs någon förändring av ägarskapet eller monteringen av enheten, demontering och borttagning av en volym. 

> [!IMPORTANT]
> Blanda inte CSV: er och icke-CSV: er i samma ögonblicks bild. Det finns inte stöd för att blanda CSV: er och icke-CSV: er i en ögonblicks bild. 
> 
> 

Du kan använda StorSimple Snapshot Manager för att återställa hela volym grupper eller klona enskilda volymer och återställa enskilda filer.

* [Volymer och volym grupper](#volumes-and-volume-groups) 
* [Säkerhets kopierings typer och säkerhets kopierings principer](#backup-types-and-backup-policies) 

Mer information om StorSimple Snapshot Manager funktioner och hur du använder dem finns i [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volymer och volym grupper
Med StorSimple Snapshot Manager skapar du volymer och konfigurerar dem sedan till volym grupper. 

StorSimple Snapshot Manager använder volym grupper för att skapa säkerhets kopior som är programkonsekventa. Program konsekvens finns när alla relaterade filer och databaser synkroniseras och representerar det sanna läget för ett program vid en specifik tidpunkt. Volym grupper (som även kallas *konsekvens grupper*) utgör grunden för en säkerhets kopierings-eller återställnings jobb.

Volym grupper är inte desamma som volym behållare. En volym container innehåller en eller flera volymer som delar ett moln lagrings konto och andra attribut, t. ex. kryptering och bandbredds användning. En behållare med en enda volym kan innehålla upp till 256 tunt allokerade StorSimple-volymer. Mer information om volym behållare finns i [Hantera dina volym behållare](./storsimple-8000-manage-volume-containers.md). Volym grupper är samlingar av volymer som du konfigurerar för att under lätta säkerhets kopierings åtgärder. Om du väljer två volymer som tillhör olika volym behållare placerar du dem i en enda volym grupp och skapar sedan en säkerhets kopierings princip för volym gruppen. varje volym säkerhets kopie ras i lämplig volym behållare med hjälp av lämpligt lagrings konto.

> [!NOTE]
> Alla volymer i en volym grupp måste komma från en enda moln tjänst leverantör.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrering med Windows tjänsten Volume Shadow Copy
StorSimple Snapshot Manager använder Windows tjänsten Volume Shadow Copy (VSS) för att avbilda programkonsekventa data. VSS underlättar program konsekvens genom att kommunicera med VSS-medvetna program för att koordinera skapandet av stegvisa ögonblicks bilder. VSS säkerställer att programmen är tillfälligt inaktiva eller quiescent när ögonblicks bilder tas. 

StorSimple Snapshot Manager implementering av VSS fungerar med SQL Server och allmänna NTFS-volymer. Processen ser ut så här: 

1. En beställare, som vanligt vis är en data hanterings-och skydds lösning (t. ex. StorSimple Snapshot Manager) eller ett program för säkerhets kopiering, anropar VSS och ber det att samla in information från skrivar program varan i mål programmet.
2. VSS kontaktar skrivar komponenten för att hämta en beskrivning av data. Skrivaren returnerar beskrivningen av de data som ska säkerhets kopie ras. 
3. VSS signalerar skrivaren för att förbereda programmet för säkerhets kopiering. Skrivaren förbereder data för säkerhets kopiering genom att fylla i öppna transaktioner, uppdatera transaktions loggar och så vidare och sedan meddela VSS.
4. VSS instruerar skrivaren att tillfälligt stoppa programmets data lager och se till att inga data skrivs till volymen medan skugg kopian skapas. Det här steget säkerställer data konsekvens och tar högst 60 sekunder.
5. VSS instruerar providern att skapa skugg kopian. Leverantörer, som kan vara program varu-eller maskin varubaserade, hanterar de volymer som för närvarande körs och skapar skugg kopior av dem på begäran. Providern skapar skugg kopian och meddelar VSS när den har slutförts.
6. VSS kontaktar skrivaren för att meddela programmet att I/O kan återupptas och bekräfta att I/O har pausats under skapandet av skugg kopian. 
7. Om kopieringen lyckades, returnerar VSS kopians plats till beställaren. 
8. Om data skrevs medan skugg kopian skapades, kommer säkerhets kopieringen att vara inkonsekvent. VSS tar bort skugg kopian och meddelar begär Anden. Beställaren kan antingen upprepa säkerhets kopierings processen automatiskt eller meddela administratören om detta vid ett senare tillfälle.

Se följande bild.

![VSS-process](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Process för Windows-tjänsten Volume Shadow Copy** 

## <a name="backup-types-and-backup-policies"></a>Säkerhets kopierings typer och säkerhets kopierings principer
Med StorSimple Snapshot Manager kan du säkerhetskopiera data och lagra dem lokalt och i molnet. Du kan använda StorSimple Snapshot Manager för att säkerhetskopiera data direkt, eller så kan du använda en säkerhets kopierings princip för att skapa ett schema för att säkerhetskopiera automatiskt. Med säkerhets kopierings principer kan du också ange hur många ögonblicks bilder som ska behållas. 

### <a name="backup-types"></a>Säkerhets kopierings typer
Du kan använda StorSimple-Snapshot Manager för att skapa följande typer av säkerhets kopieringar:

* **Lokala ögonblicks bilder** – lokala ögonblicks bilder är tidpunkts kopior av volym data som lagras på StorSimple-enheten. Den här typen av säkerhets kopiering kan vanligt vis skapas och återställas snabbt. Du kan använda en lokal ögonblicks bild som en lokal säkerhets kopia.
* **Moln ögonblicks bilder** – moln ögonblicks bilder är tidpunkts kopior av volym data som lagras i molnet. En moln ögonblicks bild motsvarar en ögonblicks bild som replikeras på ett annat lagrings system utanför platsen. Moln ögonblicks bilder är särskilt användbara i scenarier med haveri beredskap.

### <a name="on-demand-and-scheduled-backups"></a>Säkerhets kopieringar på begäran och schemalagda
Med StorSimple Snapshot Manager kan du initiera en enstaka säkerhets kopia som skapas direkt, eller så kan du använda en säkerhets kopierings princip för att schemalägga återkommande säkerhets kopierings åtgärder.

En säkerhets kopierings policy är en uppsättning automatiserade regler som du kan använda för att schemalägga regelbundna säkerhets kopieringar. Med en princip för säkerhets kopiering kan du definiera frekvensen och parametrarna för att ta ögonblicks bilder av en speciell volym grupp. Du kan använda principer för att ange start-och utgångs datum, tider, frekvenser och bevarande krav för både lokala och molnbaserade ögonblicks bilder. En princip tillämpas direkt efter att du har definierat den. 

Du kan använda StorSimple Snapshot Manager för att konfigurera eller konfigurera om säkerhets kopierings principer när det behövs. 

Du konfigurerar följande information för varje säkerhets kopierings princip som du skapar:

* **Namn** – det unika namnet för den valda säkerhets kopierings principen.
* **Typ** – typ av säkerhets kopierings princip. antingen lokal ögonblicks bild eller moln ögonblicks bild.
* **Volym grupp** – den volym grupp som den valda säkerhets kopierings principen tilldelas till.
* **Kvarhållning** – antalet säkerhets kopior som ska behållas. Om du markerar kryss rutan **alla** behålls alla säkerhets kopior tills det högsta antalet säkerhets kopior per volym nås. då Miss söker principen och genererar ett fel meddelande. Alternativt kan du ange ett antal säkerhets kopior som ska behållas (mellan 1 och 64).
* **Datum** – datumet då säkerhets kopierings policyn skapades.

Information om hur du konfigurerar säkerhets kopierings principer finns i [använda StorSimple Snapshot Manager för att skapa och hantera säkerhets kopierings principer](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Övervakning och hantering av säkerhets kopierings jobb
Du kan använda StorSimple-Snapshot Manager för att övervaka och hantera kommande, schemalagda och slutförda säkerhets kopierings jobb. Dessutom tillhandahåller StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhets kopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

Information om övervakning av säkerhets kopierings jobb finns i [använda StorSimple Snapshot Manager för att visa och hantera säkerhets kopierings jobb](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Hämta [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).