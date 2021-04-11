---
title: Migrering av StorSimple 8000-serien till Azure File Sync
description: Lär dig hur du migrerar en StorSimple 8100-eller 8600-apparat till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1d2de439e661ef5b1d1669187355621f25400bc4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075632"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 och 8600-migrering till Azure File Sync

StorSimple 8000-serien representeras av antingen 8100 eller 8600 fysiska, lokala apparater och deras moln tjänst komponenter. Det är möjligt att migrera data från någon av dessa apparater till en Azure File Syncs miljö. Azure File Sync är standard och strategisk Azure-tjänst som StorSimple-apparater kan migreras till.

StorSimple 8000-serien kommer att uppnå sitt [livs längd](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) i december 2022. Det är viktigt att börja planera migreringen så snart som möjligt. Den här artikeln innehåller information om de åtgärder som krävs för att migrera till Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Fas 1: Förbered för migrering

Det här avsnittet innehåller de steg som du bör utföra i början av migreringen från StorSimple-volymer till Azure-filresurser.

### <a name="inventory"></a>Inventering

När du börjar planera migreringen ska du först identifiera alla StorSimple-enheter och-volymer som du behöver migrera. När du har gjort det kan du välja den bästa sökvägen för migrering.

* StorSimple fysiska apparater (8000-serien) Använd den här migreringsguiden.
* Virtuella apparater, [StorSimple 1200-serien, använder en annan migrations guide](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Översikt över migrerings kostnad

Migreringar till Azure-filresurser från StorSimple-volymer via migreringsjobb i en StorSimple Data Manager resurs är kostnads fria. Andra kostnader kan uppstå under och efter migreringen:

* **Utgående nätverk:** Dina StorSimple-filer är Live i ett lagrings konto inom en angiven Azure-region. Om du etablerar Azure-filresurser som du migrerar till ett lagrings konto som finns i samma Azure-region inträffar ingen utgående kostnad. Du kan flytta dina filer till ett lagrings konto i en annan region som en del av migreringen. I så fall kommer utgående kostnader att gälla för dig.
* **Azure-fil resurs transaktioner:** När filer kopieras till en Azure-filresurs (som en del av en migrering eller utanför en), gäller transaktions kostnaderna som filer och metadata skrivs. Vi rekommenderar att du startar Azure-filresursen på transaktionens optimerade nivå under migreringen. Växla till önskad nivå när migreringen är färdig. Följande faser kommer att anropa detta vid lämplig tidpunkt.
* **Ändra en Azure-fildelnings nivå:** Ändra nivån för en Azure-filresurs kostnader transaktioner. I de flesta fall är det mer kostnads effektivt att följa råd från föregående punkt.
* **Lagrings kostnad:** När den här migreringen börjar kopiera filer till en Azure-filresurs, förbrukas Azure Files lagring och faktureras. Migrerade säkerhets kopior blir [ögonblicks bilder av Azure-filresurser](storage-snapshots-files.md). Ögonblicks bilder av fil resurser förbrukar endast lagrings kapacitet för de skillnader som de innehåller.
* **StorSimple:** Tills du har möjlighet att avetablera StorSimple-enheter och lagrings konton, kommer StorSimple-kostnader för lagring, säkerhets kopiering och installationer att fortsätta att gälla.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direkt delning – åtkomst vs. Azure File Sync

Azure-filresurser öppnar en helt ny värld av möjligheter att strukturera distributionen av fil tjänster. En Azure-filresurs är bara en SMB-resurs i molnet som du kan konfigurera så att användarna får åtkomst direkt över SMB-protokollet med den välkända Kerberos-autentiseringen och befintliga NTFS-behörigheter (ACL: er för filer och mappar) som fungerar internt. Läs mer om [Identity-baserad åtkomst till Azure-filresurser](storage-files-active-directory-overview.md).

Ett alternativ till direkt åtkomst är [Azure File Sync](./storage-sync-files-planning.md). Azure File Sync är en direkt analog för StorSimple-möjligheten att cachelagra filer som används ofta lokalt.

Azure File Sync är en moln tjänst från Microsoft, baserat på två huvud komponenter:

* Filsynkronisering och moln nivåer för att skapa en cache för prestanda åtkomst på valfri Windows Server.
* Fil resurser som intern lagring i Azure som kan nås via flera protokoll som SMB och fil REST.

Azure-filresurser behåller viktiga fil åter givningen på lagrade filer som attribut, behörigheter och tidsstämplar. Med Azure-filresurser behöver du inte längre ha ett program eller en tjänst för att tolka filerna och mapparna som lagras i molnet. Du kan komma åt dem internt över välkända protokoll och klienter som Windows Utforskaren. Med Azure-filresurser kan du lagra generella fil Server data och program data i molnet. Säkerhets kopiering av en Azure-filresurs är en inbyggd funktion som kan förbättras ytterligare av Azure Backup.

Den här artikeln fokuserar på stegen för migreringen. Om du vill veta mer om Azure File Sync innan du migrerar kan du läsa följande artiklar:

* [Översikt över Azure File Sync](./storage-sync-files-planning.md "Översikt")
* [Azure File Sync distributions guide](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Krypterings nyckel för tjänst data för StorSimple

När du först konfigurerar StorSimple-installationen skapade den en "tjänst data krypterings nyckel" och instruerar dig att lagra nyckeln på ett säkert sätt. Den här nyckeln används för att kryptera alla data i det associerade Azure Storage-kontot där StorSimple-enheten lagrar dina filer.

Krypterings nyckeln för tjänst data krävs för en lyckad migrering. Nu är det dags att hämta den här nyckeln från dina poster, en för var och en av de olika enheterna i inventeringen.

Om du inte hittar nycklarna i dina poster kan du generera en ny nyckel från enheten. Varje apparat har en unik krypterings nyckel.

#### <a name="change-the-service-data-encryption-key"></a>Ändra krypterings nyckeln för tjänst data

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Tänk på följande när du bestämmer hur du ska ansluta till StorSimple-enheten:
>
> * Att ansluta via en HTTPS-session är det säkraste och rekommenderade alternativet.
> * Anslutning direkt till enhetens serie konsol är säker, men det går inte att ansluta till serie konsolen över nätverks växlar.
> * HTTP-sessions anslutningar är ett alternativ som *inte är krypterade*. De rekommenderas inte om de inte används i ett stängt, betrott nätverk.

### <a name="storsimple-volume-backups"></a>StorSimple Volume-säkerhetskopieringar

StorSimple erbjuder differentiella säkerhets kopior på volym nivå. Azure-filresurser har också denna möjlighet, som kallas resurs ögonblicks bilder.
Dina migreringsjobb kan bara flytta säkerhets kopior, inte data från den aktiva volymen. Så den senaste säkerhets kopian bör alltid finnas i listan över säkerhets kopior som flyttats i en migrering.

Bestäm om du behöver flytta några äldre säkerhets kopior under migreringen.
Vi rekommenderar att du behåller listan så liten som möjligt, så att dina migreringsjobb slutförs snabbare.

För att identifiera kritiska säkerhets kopior som måste migreras, gör du en check lista över dina säkerhets kopierings principer. Till exempel:
* Den senaste säkerhets kopian. (Obs: den senaste säkerhets kopian bör alltid vara en del av den här listan.)
* En säkerhets kopia per månad i 12 månader.
* En säkerhets kopiering per år i tre år. 

Senare, när du skapar dina migreringsjobb, kan du använda den här listan för att identifiera de exakta säkerhets kopiorna av StorSimple som måste migreras för att uppfylla kraven i listan.

> [!CAUTION]
> Det går inte att välja fler än **50** StorSimple volym säkerhets kopieringar.
> Dina migreringsjobb kan bara flytta säkerhets kopior, aldrig data från den aktiva volymen. Därför är den senaste säkerhets kopian närmast Live-data och bör därför alltid vara en del av listan över säkerhets kopior som ska flyttas i en migrering.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mappa befintliga StorSimple-volymer till Azure-filresurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Antal lagrings konton

Migreringen kommer förmodligen att ha nytta av en distribution av flera lagrings konton som var och en innehåller ett mindre antal Azure-filresurser.

Om dina fil resurser är mycket aktiva (används av många användare eller program) kan två Azure-filresurser uppnå lagrings kontots prestanda gräns. Därför är det bästa sättet att migrera till flera lagrings konton, var och en med sina egna enskilda fil resurser och inte fler än två eller tre resurser per lagrings konto.

Ett bra tips är att distribuera lagrings konton med en enda fil resurs. Du kan poola flera Azure-filresurser till samma lagrings konto, om du har lagrings resurser i dem.

Dessa överväganden gäller mer för [direkt åtkomst till molnet](#direct-share-access-vs-azure-file-sync) (via en virtuell Azure-dator eller-tjänst) än för Azure File Sync. Om du planerar att uteslutande använda Azure File Sync på dessa resurser är det bra att gruppera flera i ett enda Azure Storage-konto. I framtiden kanske du vill lyfta upp och flytta en app till molnet som sedan kan komma åt en fil resurs direkt, men det här scenariot skulle dra nytta av högre IOPS och data flöde. Eller så kan du börja använda en tjänst i Azure som också kan dra nytta av högre IOPS och data flöde.

Om du har skapat en lista över dina resurser mappar du varje resurs till lagrings kontot där det kommer att finnas.

> [!IMPORTANT]
> Välj en Azure-region och kontrol lera att varje lagrings konto och Azure File Sync resurs matchar den region som du har valt.
> Konfigurera inte nätverks-och brand Väggs inställningar för lagrings kontona nu. Att göra dessa konfigurationer i detta läge gör det omöjligt att migrera. Konfigurera de här inställningarna för Azure Storage när migreringen är klar.

### <a name="phase-1-summary"></a>Översikt över fas 1

I slutet av fas 1:

* Du har en översikt över dina StorSimple-enheter och-volymer.
* Data Managers tjänsten är redo att komma åt dina StorSimple-volymer i molnet eftersom du har hämtat din "krypterings nyckel för tjänst data" för varje StorSimple-enhet.
* Du har en plan för vilken volymer och säkerhets kopior (om de inte längre är de senaste) som måste migreras.
* Du vet hur du mappar dina volymer till rätt antal Azure-filresurser och lagrings konton.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fas 2: distribuera resurser för Azure-lagring och migrering

I det här avsnittet beskrivs överväganden kring hur du distribuerar de olika resurs typer som behövs i Azure. Vissa kommer att inneha migreringen av data post och vissa behövs bara för migreringen. Börja inte distribuera resurserna förrän du har slutfört din distributions plan. Det är svårt, ibland omöjligt, att ändra vissa aspekter av dina Azure-resurser när de har distribuerats.

### <a name="deploy-storage-accounts"></a>Distribuera lagrings konton

Du behöver förmodligen distribuera flera Azure Storage-konton. Var och en har ett mindre antal Azure-filresurser, enligt din distributions plan, som slutfördes i föregående avsnitt i den här artikeln. Gå till Azure Portal för att [distribuera dina planerade lagrings konton](../common/storage-account-create.md#create-a-storage-account). Överväg att följa följande grundläggande inställningar för alla nya lagrings konton.

> [!IMPORTANT]
> Konfigurera inte nätverks-och brand Väggs inställningar för dina lagrings konton nu. Att göra dessa konfigurationer i det här läget gör det omöjligt att migrera. Konfigurera de här inställningarna för Azure Storage när migreringen är klar.

#### <a name="subscription"></a>Prenumeration

Du kan använda samma prenumeration som du använde för din StorSimple-distribution eller en annan. Den enda begränsningen är att prenumerationen måste vara i samma Azure Active Directory-klient som StorSimple-prenumerationen. Överväg att flytta StorSimple-prenumerationen till lämplig klient innan du påbörjar en migrering. Du kan bara flytta hela prenumerationen, enskilda StorSimple-resurser kan inte flyttas till en annan klient organisation eller prenumeration.

#### <a name="resource-group"></a>Resursgrupp

Resurs grupper hjälper till med organisation av resurser och administrations hanterings behörigheter. Lär dig mer om [resurs grupper i Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Lagringskontonamn

Namnet på ditt lagrings konto kommer att bli en del av en URL och har vissa begränsningar. I namngivnings konventionen bör du tänka på att lagrings konto namn måste vara unika i världen, tillåta bara gemena bokstäver och siffror, kräva mellan 3 och 24 tecken och Tillåt inte specialtecken som bindestreck eller under streck. Mer information finns i [namngivnings regler för Azure Storage-resurser](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Location

Platsen eller Azure-regionen för ett lagrings konto är mycket viktigt. Om du använder Azure File Sync måste alla dina lagrings konton finnas i samma region som din resurs för synkroniseringstjänsten för lagring. Den Azure-region som du väljer bör vara nära eller central för dina lokala servrar och användare. När din resurs har distribuerats kan du inte ändra dess region.

Du kan välja en annan region än där dina StorSimple-data (lagrings kontot) för närvarande finns.

> [!IMPORTANT]
> Om du väljer en annan region än din aktuella StorSimple lagrings konto plats [kommer utgående kostnader att gälla](https://azure.microsoft.com/pricing/details/bandwidth) under migreringen. Data kommer att lämna StorSimple-regionen och ange din nya lagrings konto region. Inga avgifter för bandbredd gäller om du bor inom samma Azure-region.

#### <a name="performance"></a>Prestanda

Du har möjlighet att välja Premium Storage (SSD) för Azure-filresurser eller standard lagring. Standard lagring innehåller [flera nivåer för en fil resurs](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standard Storage är det bästa alternativet för de flesta kunder som migrerar från StorSimple.

Är du fortfarande osäker?

* Välj Premium Storage om du behöver [prestanda för en Premium Azure-filresurs](understanding-billing.md#provisioned-model).
* Välj standard lagring för generella fil Server arbets belastningar som innehåller data och Arkiv data. Välj också standard lagring om den enda arbets belastningen på resursen i molnet ska Azure File Sync.

#### <a name="account-kind"></a>Typ av konto

* För standard lagring väljer du *StorageV2 (generell användning v2)*.
* Välj *FileStorage* för Premium File-resurser.

#### <a name="replication"></a>Replikering

Det finns flera tillgängliga replikeringsinställningar. Läs mer om de olika typerna av replikering.

Välj endast från något av följande två alternativ:

* *Lokalt Redundant lagring (LRS)*.
* *Zon redundant lagring (ZRS)*, som inte är tillgänglig i alla Azure-regioner.

> [!NOTE]
> Endast LRS-och ZRS-redundans typer är kompatibla med de stora Azure-filresurserna i 100-TiB-kapacitet.

Geo redundant Storage (GRS) i alla variationer stöds inte för närvarande. Du kan byta typ av redundans senare och växla till GRS när supporten för den kommer i Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Aktivera 100-TiB – kapacitets fil resurser

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="En bild som visar fliken Avancerat i Azure Portal för att skapa ett lagrings konto.":::
    :::column-end:::
    :::column:::
        Under avsnittet **Avancerat** i guiden Nytt lagrings konto i Azure Portal kan du aktivera stöd för **stora fil resurser** i det här lagrings kontot. Om det här alternativet inte är tillgängligt för dig väljer du förmodligen fel typ av redundans. Se till att du bara väljer LRS eller ZRS för att det här alternativet ska bli tillgängligt.
    :::column-end:::
:::row-end:::

Väljer för de stora, 100-TiB-kapacitets fil resurserna har flera fördelar:

* Prestandan ökar avsevärt jämfört med de mindre 5 TiB kapacitets fil resurserna (till exempel 10 gånger IOPS).
* Migreringen blir betydligt snabbare.
* Du ser till att en fil resurs har tillräckligt med kapacitet för att rymma alla data som du migrerar till den, inklusive säkerhets kopiorna för lagrings kapacitets skillnaderna kräver.
* Framtida tillväxt omfattas.

### <a name="azure-file-shares"></a>Azure-filresurser

När dina lagrings konton har skapats går du till avsnittet **fil resurs** på lagrings kontot och distribuerar lämpligt antal Azure-filresurser enligt ditt schema för migrering från fas 1. Överväg att följa följande grundläggande inställningar för dina nya fil resurser i Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="En Azure Portal skärm bild som visar det nya fil resurs gränssnittet.":::
    :::column-end:::
    :::column:::
        </br>**Namn**</br>Små bokstäver, siffror och bindestreck stöds.</br></br>**Kvot**</br>Kvoten är jämförbar med en SMB-hårddisk på en Windows Server-instans. Det bästa sättet är att inte ange en kvot här eftersom migreringen och andra tjänster inte fungerar när kvoten nås.</br></br>**Nivåer**</br>Välj **transaktion optimerad** för den nya fil resursen. Under migreringen utförs många transaktioner. Det är mer kostnads effektivt att ändra nivån senare till den nivå som passar din arbets belastning bäst.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Den Azure-resurs som ska innehålla dina migreringsjobb kallas för ett **StorSimple Data Manager**. Välj **Ny resurs** och Sök efter den. Välj sedan **Skapa**.

Den här tillfälliga resursen används för dirigering. Du avetablerar den när migreringen är klar. Den bör distribueras i samma prenumeration, resurs grupp och region som ditt StorSimple-lagrings konto.

### <a name="azure-file-sync"></a>Azure File Sync

Med Azure File Sync kan du lägga till lokal cachelagring av de filer som ofta används. På samma sätt som med funktionerna för cachelagring av StorSimple erbjuder den Azure File Sync moln nivå funktionen en svars tid för lokal åtkomst i kombination med förbättrad kontroll över tillgänglig cache-kapacitet på Windows Server-instansen och synkronisering av flera platser. Om du har en lokal cache som mål, så kan du, i ditt lokala nätverk, förbereda en virtuell Windows Server-dator (fysiska servrar och kluster för växling vid fel) med tillräcklig direktansluten lagrings kapacitet.

> [!IMPORTANT]
> Konfigurera inte Azure File Sync ännu. Det är bäst att ställa in Azure File Sync när migreringen av resursen har slutförts. Distributions Azure File Sync bör inte starta innan fas 4 i en migrering.

### <a name="phase-2-summary"></a>Fas 2-Sammanfattning

I slutet av fas 2 har du distribuerat dina lagrings konton och alla Azure-filresurser över dem. Du har också en StorSimple Data Manager-resurs. Du kommer att använda den senare i fas 3 när du konfigurerar dina migreringsjobb.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fas 3: skapa och kör ett migreringsjobb

I det här avsnittet beskrivs hur du konfigurerar ett migreringsjobb och noga mappar katalogerna på en StorSimple-volym som ska kopieras till den Azure-filresurs du väljer. Kom igång genom att gå till StorSimple Data Manager, hitta **jobb definitioner** på menyn och välja **+ jobb definition**. Rätt mål lagrings typ är standard: **Azure-filresursen**.

![StorSimple 8000-seriens migreringsjobb.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "En skärm bild av dialog rutan jobb definitioner Azure Portal med en ny dialog ruta för jobb definitioner öppnas som frågar efter typen av jobb: kopiera till en fil resurs eller en BLOB-behållare.")

:::row:::
    :::column:::
        ![Migrering av StorSimple 8000-serien.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "En skärm bild av formuläret för att skapa ett nytt jobb för ett migreringsjobb.")
    :::column-end:::
    :::column:::
        **Jobb definitions namn**</br>Det här namnet ska innehålla den uppsättning filer som du flyttar. Det är en bra idé att ge det samma namn som Azure-filresursen. </br></br>**Plats där jobbet körs**</br>När du väljer en region måste du välja samma region som ditt StorSimple-lagrings konto eller, om det inte är tillgängligt, en region nära den. </br></br><h3>Källa</h3>**Käll prenumeration**</br>Välj den prenumeration där du vill lagra StorSimple Enhetshanteraren-resursen. </br></br>**StorSimple-resurs**</br>Välj din StorSimple Enhetshanteraren din installation är registrerad hos. </br></br>**Krypterings nyckel för tjänst data**</br>Se det här [föregående avsnittet i den här artikeln](#storsimple-service-data-encryption-key) om du inte kan hitta nyckeln i dina poster. </br></br>**Enhet**</br>Välj din StorSimple-enhet som innehåller den volym dit du vill migrera. </br></br>**Volym**</br>Välj käll volymen. Senare bestämmer du om du vill migrera hela volymen eller under kataloger till Azure-webbplatsens mål resurs.</br></br> **Säkerhets kopior av volym**</br>Du kan välja *Välj säkerhets kopior* för att välja vilka säkerhets kopior som ska flyttas som en del av det här jobbet. Ett kommande, [dedikerat avsnitt i den här artikeln](#selecting-volume-backups-to-migrate) beskriver processen i detalj.</br></br><h3>Mål</h3>Välj prenumerationen, lagrings kontot och Azure-filresursen som målet för det här migreringsjobbet.</br></br><h3>Katalog mappning</h3>[Ett dedikerat avsnitt i den här artikeln](#directory-mapping)beskriver all relevant information.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Välja volym säkerhets kopior som ska migreras

Det finns viktiga aspekter kring att välja säkerhets kopior som behöver migreras:

- Dina migreringsjobb kan bara flytta säkerhets kopior, inte data från en Live-volym. Den senaste säkerhets kopian är närmast Live-data och bör alltid finnas i listan över säkerhets kopior som har flyttats i en migrering.
- Se till att din senaste säkerhets kopia är nyligen för att hålla delta i Live-resursen så litet som möjligt. Det kan vara värt att aktivera och slutföra en annan säkerhets kopiering av volymen manuellt innan du skapar ett migreringsjobb. Ett litet delta i Live-resursen kommer att förbättra migreringen. Om den här förändringen kan vara noll = inga fler ändringar av StorSimple-volymen har skett efter den senaste säkerhets kopieringen i din lista – sedan fas 5: användarens klipp ut blir drastiskt förenklad och sped uppåt.
- Säkerhets kopiorna måste spelas upp i Azure-filresursen **från äldsta till nyaste**. En äldre säkerhets kopiering kan inte sorteras i listan över säkerhets kopior på Azure-filresursen efter att ett migreringsjobb har körts. Därför måste du se till att listan över säkerhets kopieringar är klar *innan* du skapar ett jobb. 
- Det går inte att ändra listan med säkerhets kopior i ett jobb när jobbet har skapats, även om jobbet aldrig körts. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="En skärm bild av formuläret för att skapa ett nytt jobb som beskriver den del där StorSimple-säkerhetskopieringar har valts för migrering." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Välj säkerhets kopior av din StorSimple-volym för ditt migreringsjobb genom att välja *Välj säkerhets kopior av volymen* i formuläret Skapa jobb.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="En bild som visar att den övre halvan av bladet för att välja säkerhets kopior visar alla tillgängliga säkerhets kopior. En vald säkerhets kopia är nedtonad i listan och läggs till i en andra lista på den nedre halvan av bladet. Du kan också ta bort den igen." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        När bladet val av säkerhets kopiering öppnas delas det upp i två listor. I den första listan visas alla tillgängliga säkerhets kopior. Du kan expandera och begränsa resultat uppsättningen genom filtrering för ett specifikt tidsintervall. (se nästa avsnitt) </br></br>En vald säkerhets kopia visas som nedtonad och läggs till i en andra lista på den nedre halvan av bladet. I den andra listan visas alla säkerhets kopior som valts för migrering. En säkerhets kopia som valts i fel kan också tas bort igen.
        > [!CAUTION]
        > Du måste markera **alla** säkerhets kopior som du vill migrera. Du kan inte lägga till äldre säkerhets kopieringar senare. Du kan inte ändra jobbet för att ändra ditt val när jobbet har skapats.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="En skärm bild som visar valet av ett tidsintervall för bladet för säkerhets kopierings val." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Som standard filtreras listan för att Visa säkerhets kopiorna av StorSimple-volymer inom de senaste sju dagarna så att det blir enkelt att välja den senaste säkerhets kopian. För säkerhets kopieringar tidigare tidigare använder du tids intervalls filtret överst på bladet. Du kan antingen välja från ett befintligt filter eller ange ett anpassat tidsintervall att filtrera efter enbart de säkerhets kopior som tagits under den här perioden.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Det går inte att välja fler än 50 StorSimple volym säkerhets kopieringar. Jobb med ett stort antal säkerhets kopieringar kan Miss lyckas.

### <a name="directory-mapping"></a>Katalog mappning

Katalog mappning är valfritt för ditt migreringsjobb. Om du lämnar avsnittet tomt, kommer *alla* filer och mappar i roten på din StorSimple-volym att flyttas till roten för Azure-filresursen. I de flesta fall är det inte bästa sättet att lagra en hel volyms innehåll i en Azure-filresurs. Det är ofta bättre att dela upp volymens innehåll över flera fil resurser i Azure. Om du inte redan har skapat en plan, se [mappa din StorSimple-volym till Azure-filresurser](#map-your-existing-storsimple-volumes-to-azure-file-shares) först.

Som en del av din migrerings plan kanske du har bestämt att mapparna på en StorSimple volym måste delas upp i flera Azure-filresurser. I så fall kan du göra detta delat genom att:

1. Definiera flera jobb för att migrera mapparna på en volym. Var och en får samma StorSimple volym källa men en annan Azure-filresurs som mål.
1. Ange exakt vilka mappar från StorSimple-volymen som måste migreras till den angivna fil resursen med hjälp av **katalog mappnings** avsnittet i formuläret för att skapa jobb och följa specifika [mappnings-semantik](#semantic-elements).

> [!IMPORTANT]
> Det går inte att verifiera Sök vägarna och mappnings uttrycken i det här formuläret när formuläret skickas. Om mappningar anges felaktigt kan ett jobb antingen Miss lyckas fullständigt eller producera ett oönskat resultat. I så fall är det oftast bäst att ta bort Azure-filresursen, återskapa den och sedan korrigera mappnings instruktionerna i ett nytt migreringsjobb för resursen. Att köra ett nytt jobb med fasta mappnings instruktioner kan åtgärda utelämnade mappar och ta med dem i den befintliga resursen. Men endast mappar som utelämnats på grund av Sök vägs fel kan åtgärdas på det här sättet.

#### <a name="semantic-elements"></a>Semantiska element

En mappning uttrycks från vänster till höger: [\Source sökväg] \> [\target sökväg].

|Semantiskt Character          | Innebörd  |
|:---------------------------|:---------|
| **\\**                     | Indikator för rotnivå.       |
| **\>**                     | [Källa] och [Target-mappning]-operator.     |
|**\|** eller RETUR (ny rad) | Avgränsare för två instruktioner för mappning av mappar. </br>Alternativt kan du utelämna det här alternativet och välja **RETUR** för att hämta nästa mappnings uttryck på en egen rad.        |

### <a name="examples"></a>Exempel
Flyttar innehållet i mappens *användar data* till roten för mål fil resursen:
``` console
\User data > \
```
Flyttar hela volym innehållet till en ny sökväg på mål fil resursen:
``` console
\ > \Apps\HR tracker
```
Flyttar innehållet i källmappen till en ny sökväg på mål fil resursen:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sorterar flera käll platser i en ny katalog struktur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantiska regler

* Ange alltid mappsökvägar i förhållande till rot nivån.
* Börja varje mappsökväg med en indikator för rotnivå " \\ ".
* Ta inte med enhets beteckningar.
* När du anger flera sökvägar kan inte käll-eller mål Sök vägar överlappa varandra:</br>
   Ogiltigt överlappande käll Sök vägs exempel:</br>
    *\\folder\1 > \\ mapp*</br>
    *\\mapp \\ 1 \\ 2 > \\*</br>
   Ogiltig mål Sök väg överlappande exempel:</br>
   *\\mapp > \\*</br>
   *\\> \\*</br>
* Källmappen som inte finns kommer att ignoreras.
* Mappstrukturer som inte finns på målet kommer att skapas.
* Precis som Windows är mappnamnen inte Skift läges känsliga, men är Skift läges känsliga.

> [!NOTE]
> Innehållet i mappen *\System Volume Information* och *$Recycle. bin* på din StorSimple-volym kopieras inte av migreringsjobbet.

### <a name="run-a-migration-job"></a>Köra ett migreringsjobb

Dina migreringsjobb visas under *jobb definitioner* i den data Manager resurs som du har distribuerat till en resurs grupp.
I listan över jobb definitioner väljer du det jobb som du vill köra.

På bladet jobb som öppnas kan du se att jobbet körs i den nedre listan. Den här listan är inlednings vis tom. Längst upp på bladet finns ett kommando som kallas *körnings jobb*. Det här kommandot kommer inte att köra jobbet omedelbart, det öppnar bladet **jobb körning** :

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="En bild som visar bladet jobb körning med en listruta öppen och visar de valda säkerhets kopiorna som ska migreras. Den äldsta säkerhets kopian är markerad och måste väljas först." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        I den här versionen måste varje jobb köras flera gånger. </br></br>**Du måste börja med den äldsta säkerhets kopian från listan över säkerhets kopior som du vill migrera.** (markerat i bilden)</br></br>Du kör jobbet igen, så många gånger som du har valt säkerhets kopieringar, varje gång mot en progressivt nyare säkerhets kopiering.
        </br></br>
        > [!CAUTION]
        > Det är absolut nödvändigt att köra migreringsjobbet med den äldsta säkerhets kopian valt först och sedan igen, varje gång med en progressivt nyare säkerhets kopiering. Du måste alltid upprätthålla ordningen på dina säkerhets kopior manuellt – från äldsta till nyaste.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Sammanfattning av fas 3

I slutet av fas 3 har du kört minst ett av dina migreringsjobb från StorSimple-volymer till Azure-filresurser. Du kommer att ha kört samma migreringsjobb flera gång, från äldsta till nyaste säkerhets kopior som måste migreras. Du kan nu fokusera på att antingen konfigurera Azure File Sync för resursen (när migreringsjobb för en resurs har slutförts) eller dirigera resurs åtkomsten för dina informations anställda och appar till Azure-filresursen.

## <a name="phase-4-access-your-azure-file-shares"></a>Fas 4: få åtkomst till dina Azure-filresurser

Det finns två huvudsakliga strategier för att komma åt Azure-fil resurser:

* **Azure File Sync**: [Distribuera Azure File Sync](#deploy-azure-file-sync) till en lokal Windows Server-instans. Azure File Sync har alla fördelar med en lokal cache, precis som StorSimple.
* **Direkt delning – åtkomst**: [distribuera direkt delning – åtkomst](#deploy-direct-share-access). Använd den här strategin om ditt åtkomst scenario för en specifik Azure-filresurs inte drar nytta av lokal cachelagring, eller om du inte längre har möjlighet att vara värd för en lokal Windows Server-instans. Här kommer dina användare och appar fortsätta att komma åt SMB-resurser via SMB-protokollet. De här resurserna finns inte längre på en lokal server utan direkt i molnet.

Du bör redan ha bestämt vilket alternativ som passar dig bäst i [steg 1](#phase-1-prepare-for-migration) i den här hand boken.

Resten av det här avsnittet fokuserar på distributions anvisningar.

### <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync

Det är dags att distribuera en del av Azure File Sync.

1. Skapa Azure File Sync moln resurs.
1. Distribuera Azure File Sync agent på din lokala server.
1. Registrera servern med moln resursen.

Skapa inte några Sync-grupper ännu. Att konfigurera synkronisering med en Azure-filresurs bör bara ske efter att migreringen till en Azure-filresurs har slutförts. Om du började använda Azure File Sync innan migreringen har slutförts, skulle migreringen bli svår eftersom du inte kunde se när det var dags att initiera ett klipp.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuera Azure File Sync moln resurs

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Om du vill ändra Azure-regionen som dina data finns i när migreringen är färdig distribuerar du tjänsten för synkronisering av lagring i samma region som mål lagrings kontona för migreringen.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Distribuera en lokal Windows Server-instans

* Skapa Windows Server 2019 (minst 2012R2) som en virtuell dator eller fysisk server. Det finns också stöd för ett Windows Server-redundanskluster. Återanvänd inte servern fram till StorSimple 8100 eller 8600.
* Etablera eller Lägg till direktansluten lagring. Nätverksansluten lagring stöds inte.

Vi rekommenderar att du ger din nya Windows Server-instans en lika stor eller större mängd lagrings utrymme än din StorSimple 8100-eller 8600-installation har lokalt tillgänglig för cachelagring. Du använder Windows Server-instansen på samma sätt som du använde StorSimple-enheten. Om det finns samma mängd lagrings utrymme som enheten bör cachelagring-upplevelsen vara liknande, om inte samma. Du kan lägga till eller ta bort lagrings utrymme från din Windows Server-instans på. Med den här funktionen kan du skala din lokala volym storlek och mängden lokal lagring som är tillgänglig för cachelagring.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Förbereda Windows Server-instansen för filsynkronisering

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurera Azure File Sync på Windows Server-instansen

Din registrerade lokala Windows Server-instans måste vara klar och ansluten till Internet för den här processen.

> [!IMPORTANT]
> Din StorSimple-migrering av filer och mappar till Azure-filresursen måste slutföras innan du fortsätter. Se till att det inte finns några fler ändringar som görs i fil resursen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Se till att aktivera moln nivåer. Moln nivåer är Azure File Sync funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men som har det fullständiga namn området tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb, lokal åtkomst prestanda. En annan orsak till att aktivera moln nivåer i det här steget är att vi inte vill synkronisera fil innehåll i det här skedet. Endast namn området ska flyttas för tillfället.

### <a name="deploy-direct-share-access"></a>Distribuera direkt delning – åtkomst

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Den här videon är en guide och en demonstration av hur du på ett säkert sätt kan exponera Azure-filresurser direkt till informations arbetare och appar i fem enkla steg.</br>
        En dedikerad dokumentation om video referenser för vissa ämnen:

* [Identitetsöversikt](storage-files-active-directory-overview.md)
* [Så här ansluter du till ett lagrings konto](storage-files-identity-auth-active-directory-enable.md)
* [Översikt över nätverk för Azure-filresurser](storage-files-networking-overview.md)
* [Konfigurera offentliga och privata slut punkter](storage-files-networking-endpoints.md)
* [Så här konfigurerar du en S2S VPN](storage-files-configure-s2s-vpn.md)
* [Så här konfigurerar du en Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Så här konfigurerar du en Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [Så här konfigurerar du DNS-vidarebefordran](storage-files-networking-dns.md)
* [Konfigurera DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Sammanfattning för fas 4

I den här fasen har du skapat och kört flera migreringsjobb i StorSimple Data Manager. Dessa jobb har migrerat dina filer och mappar till Azure-filresurser. Du har också distribuerat Azure File Sync eller för beredde nätverks-och lagrings konton för direkt delnings åtkomst.

## <a name="phase-5-user-cut-over"></a>Fas 5: överhuggen av användare

Den här fasen är allt om att figursätta migreringen:

* Planera stillestånds tiden.
* Kom igång med alla ändringar som dina användare och appar har producerat på StorSimple-sidan medan migreringsjobb i fas 3 har körts.
* Användarna får inte till gång till den nya Windows Server-instansen med Azure File Sync eller Azure-filresurser via direkt delning-åtkomst.

### <a name="plan-your-downtime"></a>Planera din stillestånds tid

Den här metoden för migrering kräver vissa stillestånds tider för dina användare och appar. Målet är att hålla stillestånds tiden minst. Följande överväganden kan hjälpa dig:

* Se till att dina StorSimple-volymer är tillgängliga när du kör dina migreringsjobb.
* När du har kört dina datamigreringar för en resurs är det dags att ta bort användar åtkomsten (minst Skriv åtkomst) från StorSimple-volymerna eller-resurserna. En slutgiltig RoboCopy kommer att fånga upp Azure-filresursen. Sedan kan du klippa över dina användare. Var du kör RoboCopy beror på om du väljer att använda Azure File Sync eller direkt delnings åtkomst. Det kommande avsnittet om RoboCopy omfattar detta ämne.
* När du har slutfört RoboCopy-fångsten är du redo att exponera den nya platsen för dina användare genom antingen Azure-filresursen direkt eller en SMB-resurs på en Windows Server-instans med Azure File Sync. En DFS-N-distribution hjälper ofta att göra en klipps över snabbt och effektivt. Den behåller dina befintliga resurs adresser konsekvent och pekar på en ny plats som innehåller dina migrerade filer och mappar.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Avgöra när ditt namn område har synkroniserats fullständigt till servern

När du använder Azure File Sync för en Azure-filresurs är det viktigt att du bestämmer att hela namn området har laddats ned till servern *innan* du påbörjar en lokal Robocopy. Hur lång tid det tar att ladda ned ditt namn område beror på antalet objekt i Azure-filresursen. Det finns två metoder för att avgöra om ditt namn område har anlänt på servern.

#### <a name="azure-portal"></a>Azure Portal

Du kan använda Azure Portal för att se när ditt namn område har anlänt.

* Logga in på Azure Portal och gå till din Sync-grupp. Kontrol lera synkroniseringsstatus för Sync-gruppen och Server slut punkten.
* Den intressanta riktningen är Ladda ned. Om Server slut punkten har nyligen allokerats visas den **inledande synkroniseringen**, som anger att namn området fortfarande kommer att avslutas.
När du har ändrat vad som helst men den **inledande synkroniseringen** fylls ditt namn område i fullständigt på servern. Nu kan du fortsätta med en lokal RoboCopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Loggboken

Du kan också använda Loggboken på Windows Server-instansen för att se när namn rummet har anlänt fullständigt.

1. Öppna **Loggboken** och gå till **program och tjänster**.
1. Gå till och öppna **Microsoft\FileSync\Agent\Telemetry**.
1. Leta efter den senaste **händelse 9102** som motsvarar en slutförd Sync-session.
1. Välj **information** och bekräfta att du tittar på en händelse där **SyncDirection** -värdet **hämtas**.
1. Under tiden där ditt namn område har laddats ned till servern, kommer det att finnas en enskild händelse med **scenario**, värdet **FullGhostedSync** och **HResult**  =  **0**.
1. Om du saknar den händelsen kan du också söka efter andra **9102-händelser** med **SyncDirection**  =  **nedladdning** och **scenario**  =  **"RegularSync"**. Att hitta någon av dessa händelser indikerar också att namn området har laddat ned och synkroniseringen har slutförts till vanliga synkroniseringar, oavsett om det finns något att synkronisera eller inte för tillfället.

### <a name="a-final-robocopy"></a>En slutgiltig RoboCopy

Nu finns det skillnader mellan den lokala Windows Server-instansen och StorSimple 8100-eller 8600-produkten.

1. Du måste komma igång med de ändringar som användare eller appar har producerat på StorSimple-sidan när migreringen var pågående.
1. I de fall där du använder Azure File Sync: StorSimple-installationen har en fylld cache jämfört med Windows Server-instansen med bara ett namn område utan fil innehåll som lagras lokalt för tillfället. Den slutliga RoboCopy kan hjälpa dig att komma igång med din lokala Azure File Sync cache genom att hämta lokalt cachelagrat fil innehåll så mycket som det är tillgängligt och får plats på Azure File Sync-servern.
1. Vissa filer kan ha lämnats kvar av migreringsjobbet på grund av ogiltiga tecken. I så fall, kopierar du dem till den Azure File Sync-aktiverade Windows Server-instansen. Senare kan du justera dem så att de kommer att synkroniseras. Om du inte använder Azure File Sync för en viss resurs är det bättre att byta namn på filerna med ogiltiga tecken på StorSimple-volymen. Kör sedan RoboCopy direkt mot Azure-filresursen.

> [!WARNING]
> Robocopy i Windows Server 2019 har för närvarande ett problem som gör att filer som skiktas av Azure File Sync på mål servern kopieras från källan och sedan överförs till Azure igen när funktionen/MIR i Robocopy används. Det är absolut nödvändigt att använda Robocopy på en Windows-Server som inte är 2019. Ett föredraget val är Windows Server 2016. Den här kommentaren kommer att uppdateras om problemet löses via Windows Update.

> [!WARNING]
> Du *får inte* starta Robocopy innan servern har namn området för en Azure-filresurs som hämtats fullständigt. Mer information finns i [bestämma när ditt namn område har laddats ned helt till servern](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Du vill bara kopiera filer som har ändrats efter att migreringsjobbet senast kördes och filer som inte har flyttats via dessa jobb tidigare. Du kan lösa problemet på så sätt att de inte flyttades senare på servern när migreringen är klar. Mer information finns i [Azure File Sync fel sökning](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy har flera parametrar. I följande exempel visas ett färdigt kommando och en lista över orsaker till att välja dessa parametrar.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Lägg

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy körs med flera trådar. Standardvärdet är 8 och det högsta värdet är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Matar ut status till logg filen som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsol fönstret. Används tillsammans med utdata i en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Utelämnar loggning av förloppet för att hålla loggen läsbar.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att endast överväga delta mellan källan (StorSimple-enheten) och mål (Windows Server-katalog).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Ser till att åter givningen bevaras i vissa speglings scenarier.</br>Exempel: mellan två Robocopy kör en fil som använder en ACL-ändring och en attributändringar, till exempel är den också markerad som *dold*. Utan/IT kan ACL-ändringen missas av Robocopy och överförs därför inte till mål platsen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Åter givningen av fil kopian (standard är/COPY: DAT), kopierings flaggor: D = data, A = attribut, T = tidsstämplar, S = Security = NTFS ACL: er, O = ägar information, U = gransknings information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiera ALL fil information (motsvarande/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Åter givning för kopian av kataloger (standard är/DCOPY: DA), kopierings flaggor: D = data, A = attribut, T = tidsstämplar.
   :::column-end:::
:::row-end:::

När du konfigurerar käll-och mål platserna för RoboCopy-kommandot, se till att du granskar strukturen för källan och målet för att se till att de matchar. Om du använde funktionen katalog mappning i migreringsjobbet kan rot Katalog strukturen skilja sig från strukturen på din StorSimple-volym. I så fall kan du behöva flera RoboCopy-jobb, ett för varje under katalog. Om du är osäker på om kommandot ska fungera som förväntat, kan du använda parametern */l* , som simulerar kommandot utan att faktiskt göra några ändringar.

Detta RoboCopy-kommando använder/MIR, så det går inte att flytta filer som är desamma (skiktade filer, till exempel). Men om du får fel källa och mål Sök väg, rensar/MIR även katalog strukturer på din Windows Server-instans eller Azure-filresurs som inte finns på käll Sök vägen för StorSimple. De måste matcha exakt för RoboCopy-jobbet för att uppnå det avsedda målet att uppdatera det migrerade innehållet med de senaste ändringarna som gjorts när migreringen pågår.

Se efter i RoboCopy-logg filen om filerna har varit kvar bakom. Om det finns problem korrigerar du dem och kör kommandot RoboCopy igen. Avetablera inte några StorSimple-resurser innan du åtgärdar utestående problem för filer eller mappar som du bryr dig om.

Om du inte använder Azure File Sync för att cachelagra den aktuella Azure-filresursen i fråga utan i stället vill du ha direkt delnings åtkomst:

1. [Montera Azure-filresursen](storage-how-to-use-files-windows.md#mount-the-azure-file-share) som en nätverks enhet på en lokal Windows-dator.
1. Utför RoboCopy mellan din StorSimple och den monterade Azure-filresursen. Om filerna inte kopieras, korrigerar du namnen på StorSimple-sidan för att ta bort ogiltiga tecken. Försök sedan RoboCopy igen. Det tidigare angivna RoboCopy-kommandot kan köras flera gånger utan att orsaka onödig återställning till StorSimple.

### <a name="user-cut-over"></a>Användaren klipps över

Om du använder Azure File Sync måste du förmodligen skapa SMB-resurserna på den Azure File Sync-aktiverade Windows Server-instans som matchar de resurser du hade på StorSimple-volymerna. Du kan läsa in det här steget och göra det tidigare så att du inte förlorar tid här. Men du måste se till att innan det här läget har ingen behörighet att göra ändringar i Windows Server-instansen.

Om du har en DFS-N-distribution kan du peka DFN-Namespaces till nya mappar för servermappar. Om du inte har en DFS-N-distribution, och du framförde din 8100-eller 8600-enhet lokalt med en Windows Server-instans, kan du ta bort servern från domänen. Sedan ansluter domänen till din nya Azure File Sync-aktiverade Windows Server-instans. Under den processen ger du servern samma server namn och resurs namn som den gamla servern så att stycknings listan förblir transparent för dina användare, en grup princip och dina skript.

Läs mer om [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Avetablera

När du avetablerar en resurs förlorar du åtkomsten till konfigurationen för den resursen och dess data. Det går inte att återställa avetableringen. Fortsätt inte förrän du har bekräftat att:

* Migreringen är klar.
* Det finns inga beroenden på de StorSimple-filer, mappar eller volym säkerhets kopior som du håller på att avetablera.

Innan du börjar är det bäst att studera din nya Azure File Sync distribution i produktion en stund. Den tiden ger dig möjlighet att åtgärda eventuella problem som kan uppstå. När du har observerat din Azure File Sync-distribution under minst några få dagar kan du börja avetablera resurser i den här ordningen:

1. Avetablera din StorSimple Data Manager-resurs via Azure Portal. Alla DTS-jobb tas bort med det. Du kan inte enkelt hämta kopierings loggarna. Om de är viktiga för dina poster kan du hämta dem innan du avetablerar.
1. Kontrol lera att dina StorSimple fysiska enheter har migrerats och avregistrera dem sedan. Fortsätt inte om du inte är helt säker på att de har migrerats. Om du avetablerar dessa resurser när de fortfarande är nödvändiga kan du inte återställa data eller deras konfiguration.<br>Om du vill kan du först avetablera StorSimple Volume-resursen, som kommer att rensa data på enheten. Detta kan ta flera dagar och **kommer inte att** forensically data på enheten. Om detta är viktigt för dig kan du hantera disk avskilt från resurs avetableringen och enligt dina principer.
1. Om det inte finns några fler registrerade enheter kvar i ett StorSimple-Enhetshanteraren kan du fortsätta att ta bort den Enhetshanteraren resursen.
1. Nu är det dags att ta bort StorSimple Storage-kontot i Azure. Stoppa och bekräfta att migreringen är klar och att ingenting och ingen är beroende av dessa data innan du fortsätter.
1. Dra ut den fysiska StorSimple från ditt data Center.
1. Om du äger StorSimple-installationen är du kostnads fri att återvinna datorn. Om din enhet lånas ut, så meddela leasegivaren och returnera enheten efter behov.

Migreringen är klar.

> [!NOTE]
> Har du fortfarande frågor eller påträffat eventuella problem?</br>
> Vi är här för att hjälpa dig AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Nästa steg

* Bekanta dig med [Azure File Sync: aka.MS/AFS](./storage-sync-files-planning.md).
* Förstå flexibiliteten i principer för [moln nivåer](storage-sync-cloud-tiering-overview.md) .
* [Aktivera Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) på dina Azure-filresurser för att schemalägga ögonblicks bilder och definiera scheman för kvarhållning av säkerhets kopior.
* Om du ser i Azure Portal att vissa filer inte synkroniseras permanent kan du läsa [fel söknings guiden](storage-sync-files-troubleshoot.md) för steg för att lösa problemen.
