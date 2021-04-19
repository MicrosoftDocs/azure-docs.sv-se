---
title: Migrering i StorSimple 8000-serien till Azure File Sync
description: Lär dig hur du migrerar en StorSimple 8100- eller 8600-enhet till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717359"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100- och 8600-migrering till Azure File Sync

StorSimple 8000-serien representeras av antingen 8100 eller 8600 fysiska, lokala installationer och deras molntjänstkomponenter. Det är möjligt att migrera data från någon av dessa apparater till en Azure File Sync miljö. Azure File Sync är den standard- och strategiska långsiktiga Azure-tjänst som StorSimple-installationer kan migreras till.

StorSimple 8000-serien går ut [i december](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 2022. Det är viktigt att du börjar planera migreringen så snart som möjligt. Den här artikeln innehåller nödvändiga bakgrundskunskaper och migreringssteg för en lyckad migrering till Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Fas 1: Förbereda för migrering

Det här avsnittet innehåller de steg som du bör vidta i början av migreringen från StorSimple-volymer till Azure-filresurser.

### <a name="inventory"></a>Inventering

När du börjar planera migreringen ska du först identifiera alla StorSimple-enheter och volymer som du behöver migrera. När du har gjort det kan du välja den bästa migreringsvägen för dig.

* Fysiska StorSimple-apparater (8000-serien) använder den här migreringsguiden.
* Virtuella installationer, [StorSimple 1200-serien, använder en annan migreringsguide.](storage-files-migration-storsimple-1200.md)

### <a name="migration-cost-summary"></a>Sammanfattning av migreringskostnad

Migreringar till Azure-filresurser från StorSimple-volymer via migreringsjobb StorSimple Data Manager en resurs är kostnadsfria. Andra kostnader kan tillkomma under och efter en migrering:

* **Utgående nätverkstrafik:** Dina StorSimple-filer finns på ett lagringskonto inom en specifik Azure-region. Om du etablerar Azure-filresurser som du migrerar till ett lagringskonto som finns i samma Azure-region sker ingen utgående kostnad. Du kan flytta dina filer till ett lagringskonto i en annan region som en del av migreringen. I så fall gäller utgående kostnader för dig.
* **Azure-filresurstransaktioner:** När filer kopieras till en Azure-filresurs (som en del av en migrering eller utanför en) tillämpas transaktionskostnaderna när filer och metadata skrivs. Bästa praxis är att starta din Azure-filresurs på den transaktionsoptimerade nivån under migreringen. Växla till önskad nivå när migreringen är klar. I följande faser visas detta vid lämplig tidpunkt.
* **Ändra en Azure-filresursnivå:** Ändra nivån för en Azure-filresurs kostnadstransaktioner. I de flesta fall är det mer kostnadseffektivt att följa rekommendationerna från föregående punkt.
* **Lagringskostnad:** När den här migreringen börjar kopiera filer till en Azure-filresurs Azure Files lagringsutrymmet förbrukas och faktureras. Migrerade säkerhetskopior blir ögonblicksbilder [av Azure-filresursen.](storage-snapshots-files.md) Ögonblicksbilder av filresurs förbrukar endast lagringskapacitet för de skillnader som de innehåller.
* **StorSimple:** Tills du har möjlighet att avetablera StorSimple-enheter och lagringskonton fortsätter StorSimple-kostnaden för lagring, säkerhetskopiering och installationer att ske.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direkt resursåtkomst jämfört med Azure File Sync

Azure-filresurser öppnar upp en helt ny värld av möjligheter att strukturera distributionen av filtjänster. En Azure-filresurs är bara en SMB-resurs i molnet som du kan konfigurera så att användarna får åtkomst direkt via SMB-protokollet med den välbekanta Kerberos-autentiseringen och befintliga NTFS-behörigheter (fil- och mapp-ACL:er) som fungerar inbyggt. Läs mer om [identitetsbaserad åtkomst till Azure-filresurser.](storage-files-active-directory-overview.md)

Ett alternativ till direktåtkomst är [Azure File Sync](./storage-sync-files-planning.md). Azure File Sync är en direkt analog för StorSimples möjlighet att cachelagra filer som används ofta lokalt.

Azure File Sync är en Microsoft-molntjänst som baseras på två huvudkomponenter:

* Filsynkronisering och molnnivåindelning för att skapa ett cacheminne för prestandaåtkomst på valfri Windows Server.
* Filresurser som intern lagring i Azure som kan nås via flera protokoll som SMB och fil-REST.

Azure-filresurser behåller viktiga filåtergivningsaspekter på lagrade filer som attribut, behörigheter och tidsstämplar. Med Azure-filresurser behöver du inte längre ett program eller en tjänst för att tolka de filer och mappar som lagras i molnet. Du kan komma åt dem inbyggt via välbekanta protokoll och klienter som Windows Utforskaren. Med Azure-filresurser kan du lagra allmänna filserverdata och programdata i molnet. Säkerhetskopiering av en Azure-filresurs är en inbyggd funktion som kan förbättras ytterligare av Azure Backup.

Den här artikeln fokuserar på migreringsstegen. Om du vill veta mer om Azure File Sync innan du migrerar kan du läsa följande artiklar:

* [Azure File Sync översikt](./storage-sync-files-planning.md "Översikt")
* [Azure File Sync distributionsguide](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Krypteringsnyckel för StorSimple-tjänstdata

När du först konfigurerade StorSimple-installationen genererade den en "krypteringsnyckel för tjänstdata" och instruerade dig att lagra nyckeln på ett säkert sätt. Den här nyckeln används för att kryptera alla data i det associerade Azure-lagringskontot där StorSimple-installationen lagrar dina filer.

"Krypteringsnyckeln för tjänstdata" är nödvändig för en lyckad migrering. Nu är ett bra tillfälle att hämta den här nyckeln från dina poster, en för var och en av apparaterna i inventeringen.

Om du inte hittar nycklarna i dina poster kan du generera en ny nyckel från installationen. Varje installation har en unik krypteringsnyckel.

#### <a name="change-the-service-data-encryption-key"></a>Ändra krypteringsnyckeln för tjänstdata

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> När du bestämmer hur du ska ansluta till StorSimple-installationen bör du tänka på följande:
>
> * Att ansluta via en HTTPS-session är det säkraste och rekommenderade alternativet.
> * Det är säkert att ansluta direkt till enhetens seriekonsol, men det är inte anslutet till seriekonsolen via nätverksväxlar.
> * HTTP-sessionsanslutningar är ett alternativ men *krypteras inte*. De rekommenderas inte om de inte används i ett stängt, betrott nätverk.

### <a name="storsimple-volume-backups"></a>Säkerhetskopior av StorSimple-volymer

StorSimple erbjuder differentiella säkerhetskopior på volymnivå. Azure-filresurser har också den här möjligheten, som kallas resursögonblicksbilder.
Dina migreringsjobb kan bara flytta säkerhetskopior, inte data från livevolymen. Den senaste säkerhetskopieringen bör därför alltid finnas med i listan över säkerhetskopior som har flyttats under en migrering.

Bestäm om du behöver flytta äldre säkerhetskopior under migreringen.
Bästa praxis är att hålla listan så liten som möjligt, så att migreringsjobben slutförs snabbare.

Om du vill identifiera kritiska säkerhetskopieringar som måste migreras, gör du en checklista över dina säkerhetskopieringsprinciper. Till exempel:
* Den senaste säkerhetskopian. (Obs! Den senaste säkerhetskopieringen bör alltid ingå i den här listan.)
* En säkerhetskopia per månad i 12 månader.
* En säkerhetskopia per år i tre år. 

Senare när du skapar migreringsjobb kan du använda den här listan för att identifiera de exakta StorSimple-säkerhetskopior som måste migreras för att uppfylla kraven i din lista.

> [!CAUTION]
> Det finns inte stöd för att välja fler än **50** StorSimple-säkerhetskopieringar.
> Dina migreringsjobb kan bara flytta säkerhetskopior, aldrig data från den direktsända volymen. Därför är den senaste säkerhetskopieringen närmast livedata och bör därför alltid vara en del av listan över säkerhetskopior som ska flyttas i en migrering.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mappa dina befintliga StorSimple-volymer till Azure-filresurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Antal lagringskonton

Migreringen kan förmodligen dra nytta av en distribution av flera lagringskonton som var och en innehåller ett mindre antal Azure-filresurser.

Om dina filresurser är mycket aktiva (används av många användare eller program) kan två Azure-filresurser nå prestandagränsen för ditt lagringskonto. Därför är bästa praxis att migrera till flera lagringskonton, var och en med sina egna enskilda filresurser, och vanligtvis inte mer än två eller tre resurser per lagringskonto.

Ett bra sätt är att distribuera lagringskonton med en filresurs vardera. Du kan samla flera Azure-filresurser i samma lagringskonto om du har arkivresurser i dem.

Dessa överväganden gäller mer för [direkt molnåtkomst](#direct-share-access-vs-azure-file-sync) (via en virtuell Azure-dator eller tjänst) än för Azure File Sync. Om du planerar att exklusivt använda Azure File Sync dessa resurser går det bra att gruppera flera i ett enda Azure Storage-konto. I framtiden kanske du vill lyfta och flytta en app till molnet som sedan skulle ha direkt åtkomst till en filresurs. Det här scenariot skulle ha nytta av högre IOPS och dataflöde. Eller så kan du börja använda en tjänst i Azure som också skulle ha nytta av högre IOPS och dataflöde.

Om du har skapat en lista över dina resurser mappar du varje resurs till lagringskontot där den kommer att finnas.

> [!IMPORTANT]
> Bestäm en Azure-region och se till att varje lagringskonto Azure File Sync resurs matchar den region som du har valt.
> Konfigurera inte nätverks- och brandväggsinställningar för lagringskontona nu. Att göra dessa konfigurationer i det här läget skulle göra migreringen omöjlig. Konfigurera dessa Azure Storage-inställningar när migreringen är klar.

### <a name="phase-1-summary"></a>Sammanfattning av fas 1

I slutet av Fas 1:

* Du har en bra översikt över dina StorSimple-enheter och volymer.
* Tjänsten Data Manager är redo att komma åt dina StorSimple-volymer i molnet eftersom du har hämtat din "krypteringsnyckel för tjänstdata" för varje StorSimple-enhet.
* Du har en plan för vilka volymer och säkerhetskopior (om det finns fler än de senaste) som behöver migreras.
* Du vet hur du mappar dina volymer till lämpligt antal Azure-filresurser och lagringskonton.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fas 2: Distribuera Azure-lagrings- och migreringsresurser

I det här avsnittet beskrivs överväganden kring distribution av de olika resurstyper som behövs i Azure. Vissa kommer att innehålla dina data efter migreringen, och vissa behövs endast för migreringen. Börja inte distribuera resurser förrän du har skapat distributionsplanen. Det är svårt, ibland omöjligt, att ändra vissa aspekter av dina Azure-resurser när de har distribuerats.

### <a name="deploy-storage-accounts"></a>Distribuera lagringskonton

Du behöver förmodligen distribuera flera Azure Storage-konton. Var och en kommer att innehålla ett mindre antal Azure-filresurser, enligt din distributionsplan, som slutfördes i föregående avsnitt i den här artikeln. Gå till Azure Portal för att [distribuera dina planerade lagringskonton.](../common/storage-account-create.md#create-a-storage-account) Överväg att följa följande grundläggande inställningar för alla nya lagringskonto.

> [!IMPORTANT]
> Konfigurera inte nätverks- och brandväggsinställningar för dina lagringskonton nu. Att göra dessa konfigurationer i det här läget skulle göra en migrering omöjlig. Konfigurera dessa Azure Storage-inställningar när migreringen är klar.

#### <a name="subscription"></a>Prenumeration

Du kan använda samma prenumeration som du använde för din StorSimple-distribution eller en annan. Den enda begränsningen är att din prenumeration måste finnas i Azure Active Directory klientorganisation som StorSimple-prenumerationen. Överväg att flytta StorSimple-prenumerationen till rätt klientorganisation innan du startar en migrering. Du kan bara flytta hela prenumerationen. Enskilda StorSimple-resurser kan inte flyttas till en annan klient eller prenumeration.

#### <a name="resource-group"></a>Resursgrupp

Resursgrupper hjälper till med organisering av resurser och administratörshanteringsbehörigheter. Lär dig mer om [resursgrupper i Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Lagringskontonamn

Namnet på ditt lagringskonto blir en del av en URL och har vissa teckenbegränsningar. I namngivningskonventionen bör du tänka på att lagringskontonamn måste vara unika i världen, endast tillåta gemener och siffror, kräva mellan 3 och 24 tecken och inte tillåta specialtecken som bindestreck eller understreck. Mer information finns i [Namngivningsregler för Azure Storage-resurser.](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)

#### <a name="location"></a>Location

Platsen eller Azure-regionen för ett lagringskonto är mycket viktig. Om du Azure File Sync lagringskonton måste alla dina lagringskonton finnas i samma region som resursen för tjänsten för synkronisering av lagring. Den Azure-region som du väljer bör vara nära eller central för dina lokala servrar och användare. När resursen har distribuerats kan du inte ändra dess region.

Du kan välja en annan region än den där dina StorSimple-data (lagringskontot) för närvarande finns.

> [!IMPORTANT]
> Om du väljer en annan region än din aktuella lagringsplats för StorSimple-lagringskontot [debiteras utgående avgifter](https://azure.microsoft.com/pricing/details/bandwidth) under migreringen. Data lämnar StorSimple-regionen och anger din nya lagringskontoregion. Inga bandbreddsavgifter tillkommer om du stannar i samma Azure-region.

#### <a name="performance"></a>Prestanda

Du kan välja Premium Storage (SSD) för Azure-filresurser eller standardlagring. Standard Storage innehåller [flera nivåer för en filresurs](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share). Standard Storage är rätt alternativ för de flesta kunder som migrerar från StorSimple.

Är du fortfarande osäker?

* Välj Premium Storage om du behöver prestanda [för en Premium Azure-filresurs.](understanding-billing.md#provisioned-model)
* Välj standardlagring för allmänna filserverarbetsbelastningar, som innehåller heta data och arkivdata. Välj även standardlagring om den enda arbetsbelastningen på resursen i molnet ska Azure File Sync.

#### <a name="account-kind"></a>Typ av konto

* För standardlagring väljer du *StorageV2 (generell användning v2).*
* För premiumfilresurser väljer du *ArkivLagring.*

#### <a name="replication"></a>Replikering

Det finns flera tillgängliga replikeringsinställningar. Läs mer om de olika replikeringstyperna.

Välj bara något av följande två alternativ:

* *Lokalt redundant lagring (LRS).*
* *Zonredundant lagring (ZRS),* som inte är tillgängligt i alla Azure-regioner.

> [!NOTE]
> Endast redundanstyperna LRS och ZRS är kompatibla med de stora Azure-filresurser på 100 TiB-kapacitet.

Geo-redundant lagring (GRS) stöds för närvarande inte i alla varianter. Du kan byta redundanstyp senare och växla till GRS när support för den kommer till Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Aktivera filresurser med 100 TiB-kapacitet

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="En bild som visar fliken Avancerat i Azure Portal för att skapa ett lagringskonto.":::
    :::column-end:::
    :::column:::
        Under avsnittet **Avancerat** i guiden för det nya lagringskontot i Azure Portal kan du aktivera stöd **för stora filresurser** i det här lagringskontot. Om det här alternativet inte är tillgängligt för dig har du förmodligen valt fel redundanstyp. Se till att du bara väljer LRS eller ZRS för att det här alternativet ska bli tillgängligt.
    :::column-end:::
:::row-end:::

Att välja de stora filresurser med 100 TiB-kapacitet har flera fördelar:

* Prestandan ökar avsevärt jämfört med de mindre filresurser på 5 TiB-kapacitet (till exempel 10 gånger IOPS).
* Migreringen slutförs betydligt snabbare.
* Du ser till att en filresurs har tillräckligt med kapacitet för att lagra alla data som du migrerar till den, inklusive de differentiella säkerhetskopieringar av lagringskapacitet som krävs.
* Framtida tillväxt omfattas.

### <a name="azure-file-shares"></a>Azure-filresurser

När dina lagringskonton har  skapats går du till avsnittet Filresurs i lagringskontot och distribuerar lämpligt antal Azure-filresurser enligt din migreringsplan från Fas 1. Överväg att följa följande grundläggande inställningar för dina nya filresurser i Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="En Azure Portal skärmbild som visar det nya användargränssnittet för filresursen.":::
    :::column-end:::
    :::column:::
        </br>**Namn**</br>Gemener, siffror och bindestreck stöds.</br></br>**Kvoten**</br>Kvoten här är jämförbar med en hård SMB-kvot på en Windows Server-instans. Det bästa sättet är att inte ange en kvot här eftersom migreringen och andra tjänster kommer att misslyckas när kvoten har nåtts.</br></br>**Nivåer**</br>Välj **Transaktionsoptimerad** för den nya filresursen. Under migreringen sker många transaktioner. Det är mer kostnadseffektivt att senare byta nivå till den nivå som passar bäst för din arbetsbelastning.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Den Azure-resurs som ska innehålla dina migreringsjobb kallas för **en StorSimple Data Manager**. Välj **Ny resurs** och sök efter den. Välj sedan **Skapa**.

Den här tillfälliga resursen används för orkestrering. Du avetablerar den när migreringen är klar. Den bör distribueras i samma prenumeration, resursgrupp och region som ditt StorSimple-lagringskonto.

### <a name="azure-file-sync"></a>Azure File Sync

Med Azure File Sync kan du lägga till lokal cachelagring av de mest använda filerna. Precis som cachelagringsförmågor i StorSimple erbjuder Azure File Sync-molnnivåindelad svarstid i kombination med bättre kontroll över den tillgängliga cachekapaciteten på Windows Server-instansen och synkronisering av flera webbplatser. Om du vill ha en lokal cache kan du i ditt lokala nätverk förbereda en virtuell Windows Server-dator (fysiska servrar och redundanskluster stöds också) med tillräcklig direktkopplad lagringskapacitet.

> [!IMPORTANT]
> Konfigurera inte en Azure File Sync ännu. Det är bäst att konfigurera Azure File Sync när migreringen av resursen har slutförts. Distribution Azure File Sync bör inte starta före fas 4 av en migrering.

### <a name="phase-2-summary"></a>Sammanfattning av fas 2

I slutet av Fas 2 har du distribuerat dina lagringskonton och alla Azure-filresurser på dem. Du har också en StorSimple Data Manager resurs. Du använder det senare i Fas 3 när du konfigurerar migreringsjobben.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fas 3: Skapa och köra ett migreringsjobb

I det här avsnittet beskrivs hur du ställer in ett migreringsjobb och mappar noggrant katalogerna på en StorSimple-volym som ska kopieras till den Azure-målfilresurs som du väljer. Kom igång genom att gå till StorSimple Data Manager, hitta **Jobbdefinitioner** på menyn och välja **+ Jobbdefinition**. Rätt mållagringstyp är standard: **Azure-filresurs**.

![Migreringsjobbtyper i StorSimple 8000-serien.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "En skärmbild av dialogrutan Jobbdefinitioner Azure Portal en dialogruta med nya jobbdefinitioner som frågar efter typ av jobb: Kopiera till en filresurs eller en blobcontainer.")

:::row:::
    :::column:::
        ![Migreringsjobb i StorSimple 8000-serien.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "En skärmbild av formuläret för att skapa ett nytt jobb för ett migreringsjobb.")
    :::column-end:::
    :::column:::
        **Jobbdefinitionsnamn**</br>Det här namnet bör ange den uppsättning filer som du flyttar. Det är en bra idé att ge den ett liknande namn som din Azure-filresurs. </br></br>**Plats där jobbet körs**</br>När du väljer en region måste du välja samma region som ditt StorSimple-lagringskonto eller, om det inte är tillgängligt, en region nära den. </br></br><h3>Källa</h3>**Källprenumeration**</br>Välj den prenumeration där du lagrar storsimple-Enhetshanteraren resursen. </br></br>**StorSimple-resurs**</br>Välj din StorSimple Enhetshanteraren som din enhet är registrerad med. </br></br>**Krypteringsnyckel för tjänstdata**</br>Kontrollera föregående [avsnitt i den här](#storsimple-service-data-encryption-key) artikeln om du inte kan hitta nyckeln i dina poster. </br></br>**Enhet**</br>Välj den StorSimple-enhet som innehåller den volym där du vill migrera. </br></br>**Volym**</br>Välj källvolymen. Senare bestämmer du om du vill migrera hela volymen eller underkatalogerna till Azure-målfilresursen.</br></br> **Volymsäkerhetskopior**</br>Du kan välja *Välj volymsäkerhetskopior* för att välja specifika säkerhetskopieringar som ska flyttas som en del av det här jobbet. I ett [kommande, dedikerat avsnitt i den](#selecting-volume-backups-to-migrate) här artikeln beskrivs processen i detalj.</br></br><h3>Mål</h3>Välj prenumerationen, lagringskontot och Azure-filresursen som mål för det här migreringsjobbet.</br></br><h3>Katalogmappning</h3>[I ett dedikerat avsnitt i](#directory-mapping)den här artikeln beskrivs all relevant information.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Välja volymsäkerhetskopior som ska migreras

Det finns viktiga aspekter när det gäller att välja säkerhetskopior som behöver migreras:

- Dina migreringsjobb kan bara flytta säkerhetskopior, inte data från en live-volym. Den senaste säkerhetskopieringen ligger därför närmast livedata och bör alltid finnas i listan över säkerhetskopior som har flyttats i en migrering.
- Kontrollera att den senaste säkerhetskopieringen är ny för att hålla delta till liveresursen så liten som möjligt. Det kan vara värt att utlösa och slutföra en annan volymsäkerhetskopiering manuellt innan du skapar ett migreringsjobb. En liten delta-till-live-resurs förbättrar migreringen. Om den här deltan kan vara noll = inga fler ändringar av StorSimple-volymen gjordes efter att den senaste säkerhetskopian har gjorts i din lista – så förenklas och påskyndas nästa fas 5: Användarskärningen förenklas drastiskt.
- Säkerhetskopior måste spelas upp till Azure-filresursen **från äldsta till nyaste**. En äldre säkerhetskopia kan inte "sorteras in" i listan över säkerhetskopior på Azure-filresursen när ett migreringsjobb har körts. Därför måste du se till att listan över säkerhetskopior är klar *innan* du skapar ett jobb. 
- Den här listan över säkerhetskopior i ett jobb kan inte ändras när jobbet har skapats – även om jobbet aldrig kördes. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="En skärmbild av formuläret för att skapa ett nytt jobb med information om den del där StorSimple-säkerhetskopior har valts för migrering." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Om du vill välja säkerhetskopior av StorSimple-volymen för migreringsjobbet väljer du *formuläret Välj volymsäkerhetskopior* i formuläret för jobbskapande.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="En bild som visar att den övre halvan av bladet för att välja säkerhetskopior visar en lista över alla tillgängliga säkerhetskopior. En vald säkerhetskopia är nedtonad i den här listan och läggs till i en andra lista på den nedre halvan av bladet. Där kan den också tas bort igen." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        När bladet för val av säkerhetskopiering öppnas är det indelade i två listor. I den första listan visas alla tillgängliga säkerhetskopior. Du kan expandera och begränsa resultatuppsättningen genom att filtrera efter ett visst tidsperioder. (se nästa avsnitt) </br></br>En vald säkerhetskopia visas som nedtonad och läggs till i en andra lista på den nedre halvan av bladet. I den andra listan visas alla säkerhetskopior som valts för migrering. En säkerhetskopia som valts i fel kan också tas bort igen.
        > [!CAUTION]
        > Du måste välja **alla** säkerhetskopior som du vill migrera. Du kan inte lägga till äldre säkerhetskopior senare. Du kan inte ändra jobbet om du vill ändra ditt val när jobbet har skapats.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="En skärmbild som visar valet av ett tidsintervall för bladet för val av säkerhetskopiering." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Som standard filtreras listan för att visa StorSimple-volymsäkerhetskopior under de senaste sju dagarna för att göra det enkelt att välja den senaste säkerhetskopian. Om du vill ha säkerhetskopior längre fram i tiden använder du filtret för tidsintervall längst upp på bladet. Du kan antingen välja från ett befintligt filter eller ange ett anpassat tidsperiod för att endast filtrera fram de säkerhetskopior som tagits under den här perioden.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Det finns inte stöd för att välja fler än 50 StorSimple-säkerhetskopieringar. Jobb med ett stort antal säkerhetskopieringar kan misslyckas.

### <a name="directory-mapping"></a>Katalogmappning

Katalogmappning är valfritt för migreringsjobbet. Om du lämnar avsnittet tomt *flyttas* alla filer och mappar i roten på StorSimple-volymen till roten för Din Azure-målfilresurs. I de flesta fall är det inte den bästa metoden att lagra en hel volyms innehåll i en Azure-filresurs. Det är ofta bättre att dela upp en volyms innehåll över flera filresurser i Azure. Om du inte redan har gjort en plan kan du börja [med att mappa storsimple-volymen till Azure-filresurser.](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Som en del av migreringsplanen kanske du har bestämt att mapparna på en StorSimple-volym måste delas upp mellan flera Azure-filresurser. Om så är fallet kan du utföra delningen genom att:

1. Definiera flera jobb för att migrera mapparna på en volym. Var och en har samma StorSimple-volymkälla men en annan Azure-filresurs som mål.
1. Ange exakt vilka mappar från StorSimple-volymen som måste migreras till den angivna filresursen med hjälp av avsnittet **Katalogmappning** i formuläret för att skapa jobb och följa den specifika [mappningssemantiken](#semantic-elements).

> [!IMPORTANT]
> Sökvägarna och mappningsuttrycken i det här formuläret kan inte verifieras när formuläret skickas. Om mappningar anges felaktigt kan ett jobb antingen misslyckas helt eller ge ett oönskat resultat. I så fall är det vanligtvis bäst att ta bort Azure-filresursen, skapa den på nytt och sedan åtgärda mappningsutsatserna i ett nytt migreringsjobb för resursen. Om du kör ett nytt jobb med fasta mappningsutdrag kan du åtgärda utelämnade mappar och ta dem till den befintliga resursen. Det är dock bara mappar som utelämnats på grund av felstavade sökvägar som kan åtgärdas på det här sättet.

#### <a name="semantic-elements"></a>Semantiska element

En mappning uttrycks från vänster till höger: [\källsökväg] \> [\målsökväg].

|Semantiskt tecken          | Innebörd  |
|:---------------------------|:---------|
| **\\**                     | Rotnivåindikator.       |
| **\>**                     | [Källa] och [målmappning] operator.     |
|**\|** eller RETUR (ny rad) | Avgränsare för två mappningsinstruktioner. </br>Du kan också utelämna det här tecknet och välja Retur **för** att hämta nästa mappningsuttryck på en egen rad.        |

### <a name="examples"></a>Exempel
Flyttar innehållet i mappen *Användardata till* roten på målfilresursen:
``` console
\User data > \
```
Flyttar hela volyminnehållet till en ny sökväg på målfilresursen:
``` console
\ > \Apps\HR tracker
```
Flyttar källmappens innehåll till en ny sökväg på målfilresursen:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sorterar flera källplatser i en ny katalogstruktur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantiska regler

* Ange alltid mappsökvägar i förhållande till rotnivån.
* Börja varje mappsökväg med en rotnivåindikator " \\ ".
* Inkludera inte enhetsbeteckningar.
* När du anger flera sökvägar får käll- eller målsökvägar inte överlappa:</br>
   Exempel på ogiltig källsökväg överlappar:</br>
    *\\folder\1 \\ > mapp*</br>
    *\\mapp \\ 1 \\ 2 > \\ folder2*</br>
   Exempel på ogiltig målsökväg överlappar:</br>
   *\\mapp > \\*</br>
   *\\folder2 > \\*</br>
* Källmappar som inte finns ignoreras.
* Mappstrukturer som inte finns på målet skapas.
* Precis som i Windows är mappnamnen inte lika känsliga som i fallbevarande.

> [!NOTE]
> Innehållet i *mappen \System Volume Information* och *$Recycle.Bin* på storSimple-volymen kopieras inte av migreringsjobbet.

### <a name="run-a-migration-job"></a>Köra ett migreringsjobb

Migreringsjobben visas under *Jobbdefinitioner* i Data Manager resurs som du har distribuerat till en resursgrupp.
I listan över jobbdefinitioner väljer du det jobb som du vill köra.

På jobbbladet som öppnas kan du se att jobbet körs i den nedre listan. Till en början är den här listan tom. Längst upp på bladet finns ett kommando med namnet Run job ( *Kör jobb).* Det här kommandot kör inte jobbet omedelbart, utan öppnar **bladet Jobbkörning:**

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="En bild som visar jobbkörningsbladet med en nedrullningsad listr listkontroll som visar de valda säkerhetskopior som ska migreras. Den äldsta säkerhetskopian är markerad, den måste väljas först." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        I den här versionen måste varje jobb köras flera gånger. </br></br>**Du måste börja med den äldsta säkerhetskopian från listan över säkerhetskopior som du vill migrera.** (markerat i bilden)</br></br>Du kör jobbet igen, så många gånger som du har valt säkerhetskopior, varje gång mot en progressivt nyare säkerhetskopia.
        </br></br>
        > [!CAUTION]
        > Det är viktigt att du kör migreringsjobbet med den äldsta säkerhetskopian vald först och sedan igen, varje gång med en progressivt nyare säkerhetskopia. Du måste alltid behålla ordningen på dina säkerhetskopior manuellt – från äldsta till nyaste.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Sammanfattning av fas 3

I slutet av Fas 3 kommer du att ha kört minst ett av dina migreringsjobb från StorSimple-volymer till Azure-filresurs(er). Du kommer att ha kört samma migreringsjobb flera gånger, från äldsta till senaste säkerhetskopieringar som måste migreras. Nu kan du fokusera på att antingen konfigurera Azure File Sync för resursen (när migreringsjobben för en resurs har slutförts) eller att dirigera resursåtkomst för dina informationsarbetare och appar till Azure-filresursen.

## <a name="phase-4-access-your-azure-file-shares"></a>Fas 4: Få åtkomst till dina Azure-filresurser

Det finns två huvudsakliga strategier för att komma åt dina Azure-filresurser:

* **Azure File Sync:** [Azure File Sync](#deploy-azure-file-sync) till en lokal Windows Server-instans. Azure File Sync har alla fördelar med en lokal cache, precis som StorSimple.
* **Direct-share-access:** [Distribuera direct-share-access](#deploy-direct-share-access). Använd den här strategin om ditt åtkomstscenario för en viss Azure-filresurs inte drar nytta av lokal cachelagring, eller om du inte längre har möjlighet att vara värd för en lokal Windows Server-instans. Här fortsätter dina användare och appar att komma åt SMB-resurser via SMB-protokollet. Dessa resurser finns inte längre på en lokal server utan direkt i molnet.

Du bör redan ha bestämt vilket alternativ som passar bäst för dig i [Fas 1 i](#phase-1-prepare-for-migration) den här guiden.

Resten av det här avsnittet fokuserar på distributionsinstruktioner.

### <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync

Det är dags att distribuera en del av Azure File Sync.

1. Skapa Azure File Sync molnresursen.
1. Distribuera Azure File Sync-agenten på din lokala server.
1. Registrera servern med molnresursen.

Skapa inte några synkroniseringsgrupper ännu. Synkronisering med en Azure-filresurs bör endast konfigureras när migreringsjobben till en Azure-filresurs har slutförts. Om du började använda Azure File Sync innan migreringen slutfördes skulle det göra migreringen onödigt svår eftersom du inte enkelt kunde se när det var dags att påbörja en övergång.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuera Azure File Sync molnresursen

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Om du vill ändra den Azure-region som dina data finns i när migreringen är klar distribuerar du tjänsten för synkronisering av lagring i samma region som mållagringskontona för migreringen.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Distribuera en lokal Windows Server-instans

* Skapa Windows Server 2019 (minst 2012R2) som en virtuell dator eller fysisk server. Ett Windows Server-redundanskluster stöds också. Återanvänd inte servern som använder StorSimple 8100 eller 8600.
* Etablera eller lägga till direktkopplad lagring. Nätverkskopplad lagring stöds inte.

Det är bästa praxis att ge den nya Windows Server-instansen en lika stor eller större mängd lagringsutrymme än din StorSimple 8100- eller 8600-installation har lokalt tillgänglig för cachelagring. Du använder Windows Server-instansen på samma sätt som du använde StorSimple-installationen. Om den har samma mängd lagringsutrymme som enheten bör cachelagringen vara liknande, om inte samma. Du kan lägga till eller ta bort lagring från din Windows Server-instans när du vill. Med den här funktionen kan du skala din lokala volymstorlek och mängden lokal lagring som är tillgänglig för cachelagring.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Förbereda Windows Server-instansen för filsynkronisering

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurera Azure File Sync på Windows Server-instansen

Din registrerade lokala Windows Server-instans måste vara redo och ansluten till Internet för den här processen.

> [!IMPORTANT]
> StorSimple-migreringen av filer och mappar till Azure-filresursen måste slutföras innan du fortsätter. Kontrollera att inga fler ändringar har gjorts i filresursen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Se till att aktivera molnnivåindelad lagring. Molnnivåindelad är Azure File Sync funktion som gör att den lokala servern kan ha mindre lagringskapacitet än vad som lagras i molnet, men ändå ha det fullständiga namnområdet tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb, lokal åtkomstprestanda. Ett annat skäl till att aktivera molnnivåindelning i det här steget är att vi inte vill synkronisera filinnehåll i det här skedet. Endast namnområdet bör flyttas just nu.

### <a name="deploy-direct-share-access"></a>Distribuera direct-share-access

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Den här videon är en guide och demo för hur du på ett säkert sätt exponerar Azure-filresurser direkt för informationsarbetare och appar i fem enkla steg.</br>
        Videon refererar till dedikerad dokumentation för vissa ämnen:

* [Identitetsöversikt](storage-files-active-directory-overview.md)
* [Så här domän ansluts du till ett lagringskonto](storage-files-identity-auth-active-directory-enable.md)
* [Nätverksöversikt för Azure-filresurser](storage-files-networking-overview.md)
* [Så här konfigurerar du offentliga och privata slutpunkter](storage-files-networking-endpoints.md)
* [Så här konfigurerar du ett S2S VPN](storage-files-configure-s2s-vpn.md)
* [Så här konfigurerar du en Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Så här konfigurerar du en Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [Så här konfigurerar du DNS-vidarebefordran](storage-files-networking-dns.md)
* [Konfigurera DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Sammanfattning av fas 4

I den här fasen har du skapat och kört flera migreringsjobb i din StorSimple Data Manager. Dessa jobb har migrerat dina filer och mappar till Azure-filresurser. Du har också distribuerat Azure File Sync eller förberett dina nätverks- och lagringskonton för direkt resursåtkomst.

## <a name="phase-5-user-cut-over"></a>Fas 5: Användarförseningen

Den här fasen handlar om att omsluta migreringen:

* Planera din stilleståndstid.
* Kom ikapp med eventuella ändringar som dina användare och appar skapade på StorSimple-sidan medan migreringsjobben i Fas 3 har körts.
* Växla över dina användare till den nya Windows Server-instansen med Azure File Sync eller Azure-filresurser via direkt resursåtkomst.

### <a name="plan-your-downtime"></a>Planera din stilleståndstid

Den här migreringsstrategin kräver viss stilleståndstid för dina användare och appar. Målet är att hålla neretiden till ett minimum. Följande överväganden kan vara till hjälp:

* Håll dina StorSimple-volymer tillgängliga medan du kör migreringsjobben.
* När du har kört datamigreringsjobben för en resurs är det dags att ta bort användaråtkomst (minst skrivåtkomst) från StorSimple-volymerna eller -resurser. En slutlig RoboCopy kommer ikapp Din Azure-filresurs. Sedan kan du klippa ut över dina användare. Var du kör RoboCopy beror på om du har valt Azure File Sync eller direkt resursåtkomst. Det kommande avsnittet om RoboCopy tar upp det ämnet.
* När du har slutfört RoboCopy-komma ikapp är du redo att exponera den nya platsen för användarna antingen via Azure-filresursen direkt eller en SMB-resurs på en Windows Server-instans med Azure File Sync. Ofta hjälper en DFS-N-distribution till att snabbt och effektivt utföra en snabb och effektiv utskärning. Det gör att dina befintliga resursadresser är konsekventa och ger en ny återpunkt till en ny plats som innehåller dina migrerade filer och mappar.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Fastställ när namnområdet har synkroniserats helt till servern

När du Azure File Sync för en Azure-filresurs är det viktigt att du ser att hela namnområdet har laddats ned till *servern* innan du startar en lokal RoboCopy. Hur lång tid det tar att ladda ned namnområdet beror på antalet objekt i Azure-filresursen. Det finns två metoder för att avgöra om namnområdet har anlänt helt till servern.

#### <a name="azure-portal"></a>Azure Portal

Du kan använda Azure Portal för att se när namnområdet har anlänt helt.

* Logga in på Azure Portal och gå till synkroniseringsgruppen. Kontrollera synkroniseringsstatusen för synkroniseringsgruppen och serverslutpunkten.
* Den intressanta riktningen är att ladda ned. Om serverslutpunkten nyligen har etablerats visas Inledande **synkronisering**, vilket indikerar att namnområdet fortfarande är i drift.
Efter det ändras till allt **utom Inledande** synkronisering , kommer ditt namnområde att vara helt ifylld på servern. Du kan nu fortsätta med en lokal RoboCopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Loggboken

Du kan också använda Loggboken på Windows Server-instansen för att se när namnområdet har anlänt helt.

1. Öppna **Loggboken** och gå till **Program och tjänster**.
1. Gå till och öppna **Microsoft\FileSync\Agent\Telemetry**.
1. Leta efter den senaste **händelsen 9102**, som motsvarar en slutförd synkroniseringssession.
1. Välj **Information** och bekräfta att du tittar på en händelse där **SyncDirection-värdet** är **Ladda ned**.
1. För den tid då namnområdet har laddats ned till servern finns det en enda händelse med **Scenario**, värdet **FullGhostedSync** och **HResult**  =  **0**.
1. Om du missar den händelsen kan du även leta efter andra **9102-händelser** med **SyncDirection**  =  **Download** och   =  **scenariot "RegularSync".** Om du hittar en av dessa händelser indikerar det också att namnområdet har laddats ned och synkroniserats till vanliga synkroniseringssessioner, oavsett om det finns något att synkronisera eller inte just nu.

### <a name="a-final-robocopy"></a>En slutlig RoboCopy

Nu finns det skillnader mellan din lokala Windows Server-instans och StorSimple 8100- eller 8600-installationen.

1. Du måste komma ikapp med de ändringar som användare eller appar skapade på StorSimple-sidan medan migreringen pågick.
1. För fall där du använder Azure File Sync: StorSimple-installationen har en ifylld cache jämfört med Windows Server-instansen med bara ett namnområde utan filinnehåll som lagras lokalt just nu. Den slutliga RoboCopy kan hjälpa dig att komma igång med din lokala Azure File Sync-cache genom att hämta lokalt cachelagrat filinnehåll så mycket som är tillgängligt och får plats på Azure File Sync servern.
1. Vissa filer kan ha lämnats kvar av migreringsjobbet på grund av ogiltiga tecken. I så fall kopierar du dem till den Azure File Sync Windows Server-instansen. Senare kan du justera dem så att de synkroniseras. Om du inte använder Azure File Sync för en viss resurs är det bättre att byta namn på filerna med ogiltiga tecken på StorSimple-volymen. Kör sedan RoboCopy direkt mot Azure-filresursen.

> [!WARNING]
> Robocopy i Windows Server 2019 har för närvarande ett problem som gör att filer som nivåindelats av Azure File Sync på målservern kopieras igen från källan och laddas upp igen till Azure när du använder funktionen /LTE i robocopy. Det är viktigt att du använder Robocopy på en annan Windows Server än 2019. Ett bra alternativ är Windows Server 2016. Den här anteckningen uppdateras om problemet löses via Windows Update.

> [!WARNING]
> Du *får inte* starta RoboCopy innan servern har namnområdet för en Azure-filresurs helt nedladdad. Mer information finns i [Avgöra när ditt namnområde har laddats ned helt till servern.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 Du vill bara kopiera filer som har ändrats efter att migreringsjobbet senast kördes och filer som inte har flyttats genom de här jobben tidigare. Du kan lösa problemet med varför de inte flyttas senare på servern när migreringen är klar. Mer information finns i [Azure File Sync felsökning.](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

RoboCopy har flera parametrar. I följande exempel visas ett färdigt kommando och en lista över orsaker till att välja dessa parametrar.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Bakgrund:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy kör flertrådiga. Standardvärdet är 8 och maxvärdet är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Utdatastatus till LOG-fil som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /KANT
   :::column-end:::
   :::column span="1":::
      Utdata till konsolfönstret. Används tillsammans med utdata till en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Utelämnar loggning av förlopp för att hålla loggen läsbar.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhetskopieringsprogram använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /KANT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy endast överväger delta mellan källan (StorSimple-installationen) och målet (Windows Server-katalogen).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Säkerställer att återgivning bevaras i vissa speglingsscenarier.</br>Exempel: Mellan två Robocopy kör en fil en ACL-ändring och en attributuppdatering, till exempel är den också markerad *som dold*. Utan /IT kan ACL-ändringen missas av Robocopy och därför inte överföras till målplatsen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelity of the file copy (default is /COPY:DAT), copy flags: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ALs, O=Owner information, U=aUditing information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      KOPIERA ALL filinformation (motsvarar /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Återgivning för kopian av kataloger (standard är /DCOPY:DA), kopiera flaggor: D = Data, A = attribut, T = tidsstämplar.
   :::column-end:::
:::row-end:::

När du konfigurerar käll- och målplatser för RoboCopy-kommandot kontrollerar du att du granskar strukturen för källan och målet för att säkerställa att de matchar. Om du använde katalogmappningsfunktionen i migreringsjobbet kan rotkatalogstrukturen vara annorlunda än strukturen för din StorSimple-volym. I så fall kan du behöva flera RoboCopy-jobb, ett för varje underkatalog. Om du är osäker på om kommandot kommer att fungera som förväntat kan du använda parametern */L,* som simulerar kommandot utan att faktiskt göra några ändringar.

Det här RoboCopy-kommandot använder /ROB, så det flyttar inte filer som är samma (t.ex. nivåindelade filer). Men om du får fel käll- och målsökväg rensar /PPEL även katalogstrukturer på din Windows Server-instans eller Azure-filresurs som inte finns på StorSimple-källsökvägen. De måste matcha exakt för RoboCopy-jobbet för att nå det avsedda målet att uppdatera det migrerade innehållet med de senaste ändringarna som gjorts medan migreringen pågår.

Titta i RoboCopy-loggfilen för att se om filerna har lämnats kvar. Om det finns problem åtgärdar du dem och kör RoboCopy-kommandot igen. Avetablera inte Några StorSimple-resurser innan du åtgärdar utestående problem för filer eller mappar som är viktiga för dig.

Om du inte använder Azure File Sync för att cachelagra den specifika Azure-filresursen i fråga utan istället väljer direkt resursåtkomst:

1. [Montera din Azure-filresurs](storage-how-to-use-files-windows.md#mount-the-azure-file-share) som en nätverksenhet på en lokal Windows-dator.
1. Utför RoboCopy mellan StorSimple och den monterade Azure-filresursen. Om filerna inte kopieras kan du korrigera deras namn på StorSimple-sidan för att ta bort ogiltiga tecken. Försök sedan igen med RoboCopy. Det tidigare listade RoboCopy-kommandot kan köras flera gånger utan att orsaka onödig återkallelse till StorSimple.

### <a name="user-cut-over"></a>Användarens överklipp

Om du använder Azure File Sync måste du förmodligen skapa SMB-resurser på den Azure File Sync-aktiverade Windows Server-instansen som matchar de resurser som du hade på StorSimple-volymerna. Du kan läsa in det här steget direkt och göra det tidigare för att inte förlora tid här. Men du måste se till att ingen tidigare har åtkomst till att orsaka ändringar i Windows Server-instansen.

Om du har en DFS-N-distribution kan du peka DFN-Namespaces till de nya servermappsplatserna. Om du inte har någon DFS-N-distribution och du fronted din 8100- eller 8600-installation lokalt med en Windows Server-instans, kan du ta den servern från domänen. Anslut sedan den nya Azure File Sync Windows Server-instansen. Under den här processen ger du servern samma servernamn och delar namn som den gamla servern så att utskärningen förblir transparent för dina användare, grupprinciper och skript.

Läs mer om [DFS-N.](/windows-server/storage/dfs-namespaces/dfs-overview)

## <a name="deprovision"></a>Avetablera

När du avetablera en resurs förlorar du åtkomsten till konfigurationen av resursen och dess data. Avetablering kan inte ångras. Fortsätt inte förrän du har bekräftat att:

* Migreringen är klar.
* Det finns inga som helst beroenden för de StorSimple-filer, mappar eller volymsäkerhetskopior som du håller på att avetablera.

Innan du börjar är det bästa praxis att observera den nya Azure File Sync i produktion ett tag. Den tiden ger dig möjlighet att åtgärda eventuella problem som kan uppstå. När du har observerat Azure File Sync distribution i minst ett par dagar kan du börja avetablera resurser i den här ordningen:

1. Avetablera din StorSimple Data Manager via Azure Portal. Alla dina DTS-jobb tas bort med dem. Du kan inte enkelt hämta kopieringsloggarna. Om de är viktiga för dina poster hämtar du dem innan du avetablera dem.
1. Kontrollera att dina fysiska StorSimple-enheter har migrerats och avregistrera dem sedan. Om du inte är helt säker på att de har migrerats ska du inte fortsätta. Om du avetablera dessa resurser medan de fortfarande är nödvändiga kan du inte återställa data eller deras konfiguration.<br>Du kan också först avetablera StorSimple-volymresursen, vilket rensar data i installationen. Det kan ta flera dagar **och kommer inte** att kriminaltekniskt nollställa data i apparaten. Om detta är viktigt för dig kan du hantera disk nollning separat från resursetablering och enligt dina principer.
1. Om det inte finns några fler registrerade enheter kvar i en StorSimple-Enhetshanteraren kan du fortsätta att ta bort den Enhetshanteraren själva resursen.
1. Nu är det dags att ta bort StorSimple-lagringskontot i Azure. Stoppa och bekräfta återigen att migreringen är klar och att ingenting och ingen är beroende av dessa data innan du fortsätter.
1. Koppla från den fysiska StorSimple-installationen från ditt datacenter.
1. Om du äger StorSimple-installationen kan du använda PC Recycle it (återanvänd den). Om enheten leasas informerar du uthyren och returnerar enheten efter behov.

Migreringen är klar.

> [!NOTE]
> Har du fortfarande frågor eller påträffat några problem?</br>
> Vi är här för att hjälpa till på AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure File Sync: aka.ms/AFS](./storage-sync-files-planning.md).
* Förstå flexibiliteten med [principer för molnnivåindelad](storage-sync-cloud-tiering-overview.md) lagring.
* [Aktivera Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) azure-filresurser för att schemalägga ögonblicksbilder och definiera scheman för kvarhållning av säkerhetskopior.
* Om du ser i Azure Portal att vissa filer permanent inte synkroniseras kan du läsa felsökningsguiden för att få anvisningar för att lösa dessa problem. [](storage-sync-files-troubleshoot.md)
