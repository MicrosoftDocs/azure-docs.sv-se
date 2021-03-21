---
title: Metod tips för virtuell StorSimple-matris | Microsoft Docs
description: Beskriver de bästa metoderna för att distribuera och hantera den virtuella StorSimple-matrisen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964991"
---
# <a name="storsimple-virtual-array-best-practices"></a>Metodtips för StorSimple Virtual Array

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple virtuell matris är en integrerad lagrings lösning som hanterar lagrings aktiviteter mellan en lokal virtuell enhet som körs i en hypervisor och Microsoft Azure moln lagring. StorSimple Virtual Array är ett effektivt, kostnads effektivt alternativ till den fysiska matrisen 8000-serien. Den virtuella matrisen kan köras på din befintliga hypervisor-infrastruktur, stöder både iSCSI-och SMB-protokollen och lämpar sig väl för fjärran slutet av Office/filialer. Mer information om StorSimple-lösningarna finns i [Microsoft Azure StorSimple översikt](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Den här artikeln beskriver de bästa metoderna som implementeras under den första installationen, distributionen och hanteringen av den virtuella StorSimple-matrisen. Dessa metod tips ger validerade rikt linjer för installation och hantering av den virtuella matrisen. Den här artikeln är riktad mot IT-administratörer som distribuerar och hanterar virtuella matriser i sina data Center.

Vi rekommenderar en regelbunden granskning av de bästa metoderna för att se till att enheten fortfarande är kompatibel när ändringar görs i installationen eller åtgärds flödet. Om du stöter på problem när du implementerar dessa metod tips på din virtuella matris [kontaktar du Microsoft Support](storsimple-virtual-array-log-support-ticket.md) för att få hjälp.

## <a name="configuration-best-practices"></a>Rekommenderade konfigurations metoder
Dessa metod tips beskriver de rikt linjer som måste följas under den första installationen och distributionen av de virtuella matriserna. De här bästa metoderna är relaterade till etablering av den virtuella datorn, grup princip inställningar, storleks ändring, konfiguration av nätverk, konfiguration av lagrings konton och skapande av resurser och volymer för den virtuella matrisen. 

### <a name="provisioning"></a>Etablering
StorSimple Virtual Array är en virtuell dator (VM) som tillhandahålls på hypervisorn (Hyper-V eller VMware) på värd servern. När du konfigurerar den virtuella datorn måste du se till att värden kan tilldela tillräckligt med resurser. Mer information finns i [lägsta resurs krav](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) för att etablera en matris.

Implementera följande metod tips när du konfigurerar den virtuella matrisen:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ av virtuell dator** |**Generation 2** Virtuell dator för användning med Windows Server 2012 eller senare och en *. vhdx* -avbildning. <br></br> **Generation 1** Virtuell dator för användning med Windows Server 2008 eller senare och en *VHD* -avbildning. |Använd Virtual Machine version 8 när du använder *. vmdk* -avbildning. |
| **Minnes typ** |Konfigurera som **statiskt minne**. <br></br> Använd inte alternativet för **dynamiskt minne** . | |
| **Typ av datadisk** |Etablera **dynamiskt expanderande**.<br></br> **Fast storlek** tar lång tid. <br></br> Använd inte alternativet **differentiering** . |Använd alternativet **tunn allokering** . |
| **Ändring av data disk** |Det är inte tillåtet att utöka eller minska. Ett försök att göra detta leder till förlust av alla lokala data på enheten. |Det är inte tillåtet att utöka eller minska. Ett försök att göra detta leder till förlust av alla lokala data på enheten. |

### <a name="sizing"></a>Storlekar
Tänk på följande faktorer när du ändrar storlek på den virtuella StorSimple-matrisen:

* Lokal reservation för volymer eller resurser. Cirka 12% av utrymmet är reserverat på den lokala nivån för varje volym eller resurs som har allokerats på nivå. Ungefär 10% av utrymmet är också reserverat för en lokalt fäst volym för fil system.
* Overheadkostnad för bild. Ungefär 15% utrymme på den lokala nivån är reserverat för ögonblicks bilder.
* Behöver återställas. Om du återställer som en ny åtgärd ska storleks ändringen vara ett konto för det utrymme som behövs för att återställa. Återställningen görs till en resurs eller volym av samma storlek.
* Vissa buffertar ska tilldelas för oväntad tillväxt.

Enligt föregående faktorer kan storleks kraven representeras av följande ekvation:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

I följande exempel visas hur du kan ändra storlek på en virtuell matris utifrån dina behov.

#### <a name="example-1"></a>Exempel 1:
På den virtuella matrisen vill du kunna

* etablera en nivå volym eller resurs med 2 TB.
* etablera en nivå volym eller resurs med 1 TB.
* etablera en 300 GB lokalt fäst volym eller resurs.

För de föregående volymerna eller resurserna kan vi beräkna utrymmes kraven på den lokala nivån.

För det första, för varje volym/resurs på nivå, skulle lokal reservation vara lika med 12% av volym/resurs storleken. För lokalt fästa volym/resurs är lokal reservation 10% av den lokalt fästa volym/resurs storleken (utöver den etablerade storleken). I det här exemplet behöver du

* 240 GB lokal reservation (för en nivå volym/resurs på 2 TB)
* 120 GB lokal reservation (för en nivå volym/resurs på 1 TB)
* 330 – GB för lokalt fixerad volym eller resurs (lägger till 10% av lokal reservation till den allokerade storleken på 300 GB)

Det totala utrymmet som krävs på den lokala nivån är så långt: 240 GB + 120 GB + 330 GB = 690 GB.

För det andra behöver vi minst lika mycket utrymme på den lokala nivån som den största enkla reservationen. Den här extra mängden används om du behöver återställa från en ögonblicks bild av molnet. I det här exemplet är den största lokala reservationen 330 GB (inklusive reservation för fil system), så du kan lägga till den till 690 GB: 690 GB + 330 GB = 1020 GB.
Om vi utför efterföljande ytterligare återställningar kan vi alltid frigöra utrymmet från föregående återställnings åtgärd.

För det tredje behöver vi 15% av ditt totala lokala utrymme hittills för att lagra lokala ögonblicks bilder, så att endast 85% av det är tillgängligt. I det här exemplet är det cirka 1020 GB = 0,85 &ast; etablerade data disk TB. Den etablerade data disken skulle därför vara (1020 &ast; (1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (avrundat till närmaste kvartilen)

I takt med oväntad tillväxt och nya återställningar bör du etablera en lokal disk på cirka 1,25-1,5 TB.

> [!NOTE]
> Vi rekommenderar också att den lokala disken är tunt etablerad. Den här rekommendationen beror på att återställnings utrymmet bara behövs om du vill återställa data som är äldre än fem dagar. Med återställning på objekt nivå kan du återställa data under de senaste fem dagarna utan att kräva det extra utrymmet för återställning.


#### <a name="example-2"></a>Exempel 2:
På den virtuella matrisen vill du kunna

* etablera en nivå volym på 2 TB
* etablera en lokalt fäst volym på 300 GB

Baserat på 12% av lokalt utrymme med reservation för skiktade volymer/resurser och 10% för lokalt fästa volymer/resurser behöver vi

* 240 GB lokal reservation (för 2 TB nivå volym/resurs)
* 330 – GB för lokalt fixerad volym eller resurs (lägger till 10% av lokal reservation till det allokerade utrymmet på 300 GB)

Det totala utrymmet som krävs på den lokala nivån är: 240 GB + 330 GB = 570 GB

Det minsta lokala utrymmet som krävs för återställning är 330 GB.

15% av din totala disk används för att lagra ögonblicks bilder så att endast 0,85 är tillgängligt. Disk storleken är (900 &ast; (1/0.85)) = 1,06 TB ~ 1,25 TB (avrundat till närmaste kvartilen)

Vid en oväntad tillväxt kan du etablera en lokal disk på 1,25-1,5 TB.

### <a name="group-policy"></a>Grupprincip
Grupprincip är en infrastruktur som gör att du kan implementera vissa konfigurationer för användare och datorer. Grupprincip inställningar finns i grupprincip objekt (GPO) som är länkade till följande Active Directory Domain Services-behållare (AD DS): platser, domäner eller organisationsenheter (OU). 

Om din virtuella matris är domänansluten, kan grup princip objekt tillämpas på den. Dessa grup princip objekt kan installera program som en antivirus program vara som kan påverka driften av den virtuella StorSimple-matrisen.

Vi rekommenderar därför att du:

* Se till att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory.
* Se till att inga grup princip objekt (GPO) tillämpas på den virtuella matrisen. Du kan blockera arv för att se till att den virtuella matrisen (underordnad nod) inte automatiskt ärver grup princip objekt från den överordnade. Mer information finns i [Blockera arv](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

### <a name="networking"></a>Nätverk
Nätverks konfigurationen för din virtuella matris görs via det lokala webb gränssnittet. Ett virtuellt nätverks gränssnitt har Aktiver ATS via hypervisorn som den virtuella matrisen är etablerad i. Använd sidan [nätverks inställningar](storsimple-virtual-array-deploy3-fs-setup.md) om du vill konfigurera det virtuella nätverks gränssnittets IP-adress, undernät och gateway.  Du kan också konfigurera den primära och sekundära DNS-servern, tids inställningarna och valfria proxyinställningar för enheten. Det mesta av nätverks konfigurationen är en eng ång slö period. Granska [StorSimple nätverks krav](storsimple-ova-system-requirements.md#networking-requirements) innan du distribuerar den virtuella matrisen.

När du distribuerar den virtuella matrisen rekommenderar vi att du följer dessa rekommendationer:

* Kontrol lera att nätverket där den virtuella matrisen har distribuerats alltid har kapaciteten att dedikera 5-Mbit/s Internet bandbredd (eller mer).
  
  * Behovet av Internet bandbredd varierar beroende på dina arbets belastnings egenskaper och hur många data som ändras.
  * Data ändringen som kan hanteras är direkt proportionell mot din Internet bandbredd. Som exempel vid säkerhets kopiering kan en bandbredd på 5 Mbit/s hantera en data ändring på cirka 18 GB på 8 timmar. Med fyra gånger större bandbredd (20 Mbit/s) kan du hantera fyra gånger större data ändringar (72 GB).
* Se till att anslutning till Internet alltid är tillgänglig. Sporadisk eller otillförlitlig Internet anslutningar till enheterna kan leda till förlust av åtkomst till data i molnet och kan resultera i en konfiguration som inte stöds.
* Om du planerar att distribuera din enhet som en iSCSI-server:
  
  * Vi rekommenderar att du inaktiverar alternativet **Hämta IP-adress automatiskt** (DHCP).
  * Konfigurera statiska IP-adresser. Du måste konfigurera en primär och en sekundär DNS-server.
  * Om du definierar flera nätverks gränssnitt på den virtuella matrisen, kan endast det första nätverks gränssnittet (som standard är **Ethernet**) komma åt molnet. Du kan styra typen av trafik genom att skapa flera virtuella nätverks gränssnitt på din virtuella matris (konfigurerad som en iSCSI-server) och ansluta dessa gränssnitt till olika undernät.
* Om du vill begränsa moln bandbredden (används av den virtuella matrisen) konfigurerar du begränsning på routern eller brand väggen. Om du definierar begränsning i hypervisor-programmet kommer det att begränsa alla protokoll inklusive iSCSI och SMB i stället för bara moln bandbredden.
* Kontrol lera att tidssynkroniseringen för hypervisorer är aktive rad. Om du använder Hyper-V väljer du din virtuella matris i Hyper-V Manager, går till **inställningar &gt; integrerings tjänster** och kontrollerar att **tidssynkroniseringen** är markerad.

### <a name="storage-accounts"></a>Lagringskonton
StorSimple virtuella matris kan associeras med ett enda lagrings konto. Det här lagrings kontot kan vara ett automatiskt genererat lagrings konto, ett konto i samma prenumeration som tjänsten eller ett lagrings konto som är kopplat till en annan prenumeration. Mer information finns i [Hantera lagrings konton för den virtuella matrisen](storsimple-virtual-array-manage-storage-accounts.md).

Använd följande rekommendationer för lagrings konton som är kopplade till den virtuella matrisen.

* När du länkar flera virtuella matriser med ett enda lagrings konto, faktor i den maximala kapaciteten (64 TB) för en virtuell matris och den maximala storleken (500 TB) för ett lagrings konto. Detta begränsar antalet virtuella matriser i full storlek som kan associeras med lagrings kontot till ca 7.
* När du skapar ett nytt lagrings konto
  
  * Vi rekommenderar att du skapar den i den region som ligger närmast det fjärranslutna kontoret/avdelnings kontoret där din virtuella StorSimple-matris distribueras för att minimera fördröjning.
  * Tänk på att du inte kan flytta ett lagrings konto mellan olika regioner. Du kan inte heller flytta en tjänst mellan prenumerationer.
  * Använd ett lagrings konto som implementerar redundans mellan data Center. Geo-Redundant Storage (GRS), zon redundant lagring (ZRS) och lokalt redundant lagring (LRS) stöds för användning med den virtuella matrisen. Mer information om de olika typerna av lagrings konton finns i [Azure Storage-replikering](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Resurser och volymer
För din virtuella StorSimple-matris kan du etablera resurser när de är konfigurerade som en fil server och volymer när de konfigureras som en iSCSI-server. De bästa metoderna för att skapa resurser och volymer är relaterade till den storlek och den typ som kon figurer ATS.

#### <a name="volumeshare-size"></a>Volym/resurs storlek
På den virtuella matrisen kan du etablera resurser när de är konfigurerade som en fil server och volymer när de konfigureras som en iSCSI-server. De bästa metoderna för att skapa resurser och volymer är relaterade till den storlek och den typ som kon figurer ATS. 

Tänk på följande bästa metoder när du konfigurerar resurser eller volymer på den virtuella enheten.

* Fil storlekarna i förhållande till den allokerade storleken på en nivå resurs kan påverka prestanda på nivån. Att arbeta med stora filer kan resultera i en långsam nivå. När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3% av resursens storlek.
* Högst 16 volymer/resurser kan skapas på den virtuella matrisen. För storleks gränserna för lokalt fästa och skiktade volymer/resurser, finns alltid [StorSimple virtuella mat ris gränser](storsimple-ova-limits.md).
* När du skapar en volym ska du faktor i den förväntade data förbrukningen och framtida tillväxt. Volymen eller resursen kan inte expanderas senare.
* När volymen/resursen har skapats kan du inte minska storleken på volym/resurs på StorSimple.
* Vid skrivning till en nivå volym på StorSimple, när volym data når ett visst tröskelvärde (i förhållande till det lokala utrymme som reserver ATS för volymen), begränsas IO-värdet. Om du fortsätter att skriva till den här volymen minskar du i/o i/o avsevärt. Även om du kan skriva till en nivå volym utöver dess etablerade kapacitet (vi hindrar inte användaren från att skriva utöver den tillhandahållna kapaciteten) visas ett varnings meddelande om den effekt som du har överprenumererat på. När du ser aviseringen är det viktigt att du vidtar åtgärder som att ta bort volym data (volym expansion stöds inte för närvarande).
* För haveri beredskap används fall, eftersom antalet tillåtna resurser/volymer är 16 och det maximala antalet resurser/volymer som kan bearbetas parallellt är 16, så har antalet resurser/volymer inte något som är försett med din återställnings-och återställnings tider.

#### <a name="volumeshare-type"></a>Volym/resurs typ
StorSimple har stöd för två volym-/resurs typer baserat på användningen: lokalt fästs och skiktas. Lokalt fästa volymer/resurser är tjockt etablerade, medan de skiktade volymerna/resurserna är tunt etablerade. Du kan inte konvertera en lokalt fixerad volym/resurs till nivå eller *vice versa* när du har skapat.

Vi rekommenderar att du implementerar följande metod tips när du konfigurerar StorSimple-volymer/resurser:

* Identifiera volym typen baserat på de arbets belastningar som du tänker distribuera innan du skapar en volym. Använd lokalt fästa volymer för arbets belastningar som kräver lokala data garantier (även under ett moln avbrott) och som kräver låg moln fördröjning. När du har skapat en volym på den virtuella matrisen kan du inte ändra volym typen från lokalt fäst till en nivå eller *vice versa*. Du kan till exempel skapa lokalt fästa volymer när du distribuerar SQL-arbetsbelastningar eller arbets belastningar som är värdar för virtuella datorer (VM). Använd skiktade volymer för fil resurs arbets belastningar.


#### <a name="volume-format"></a>Volym format
När du har skapat StorSimple-volymer på iSCSI-servern måste du initiera, montera och formatera volymerna. Den här åtgärden utförs på den värd som är ansluten till din StorSimple-enhet. Följande metod tips rekommenderas när du monterar och formaterar volymer på StorSimple-värden.

* Utför en snabbformatering på alla StorSimple-volymer.
* När du formaterar en StorSimple volym bör du använda en storlek för allokeringsenhet (Australien) på 64 KB (Standardvärdet är 4 KB). 64 KB-Australien baseras på testning som har gjorts internt för vanliga StorSimple-arbetsbelastningar och andra arbets belastningar.
* När du använder den virtuella StorSimple-matrisen som kon figurer ATS som en iSCSI-server ska du inte använda utsträckta volymer eller dynamiska diskar eftersom dessa volymer eller diskar inte stöds av StorSimple.

#### <a name="share-access"></a>Dela åtkomst
Följ dessa rikt linjer när du skapar resurser på en virtuell mat ris fil Server:

* När du skapar en resurs ska du tilldela en användar grupp som en resurs administratör i stället för en enskild användare.
* Du kan hantera NTFS-behörigheterna när resursen har skapats genom att redigera resurserna via Utforskaren i Windows.

#### <a name="volume-access"></a>Volym åtkomst
När du konfigurerar iSCSI-volymerna på din virtuella StorSimple-matris är det viktigt att kontrol lera åtkomsten när det behövs. För att avgöra vilka värd servrar som kan komma åt volymer, skapa och associera åtkomst kontroll poster (ACR: er) med StorSimple-volymer.

Använd följande metod tips när du konfigurerar ACR: er för StorSimple-volymer:

* Associera alltid minst en ACR med en volym.

* När du tilldelar fler än en ACR till en volym bör du se till att volymen inte exponeras på ett sätt som kan användas samtidigt av fler än en icke-klustrad värd. Om du har tilldelat flera ACR: er till en volym visas ett varnings meddelande där du kan granska konfigurationen.

### <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering
Din virtuella StorSimple-matris har data säkerhets-och krypterings funktioner som garanterar konfidentialitet och integritet för dina data. När du använder dessa funktioner rekommenderar vi att du följer dessa rekommendationer: 

* Definiera en krypterings nyckel för moln lagring för att generera AES-256-kryptering innan data skickas från den virtuella matrisen till molnet. Den här nyckeln krävs inte om dina data är krypterade att börja med. Nyckeln kan genereras och hållas säker med ett nyckel hanterings system, till exempel [Azure Key Vault](../key-vault/general/overview.md).
* När du konfigurerar lagrings kontot via StorSimple Manager tjänsten ska du se till att aktivera TLS-läget för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet.
* Återskapa nycklar för dina lagrings konton (genom att komma åt Azure Storage tjänsten) med jämna mellanrum för att få åtkomst till eventuella ändringar av åtkomst baserat på den ändrade listan över administratörer.
* Data på den virtuella matrisen komprimeras och dedupliceras innan de skickas till Azure. Vi rekommenderar inte att du använder roll tjänsten datadeduplicering på Windows Server-värden.

## <a name="operational-best-practices"></a>Metodtips för drift
De bästa metoderna för drift är rikt linjer som bör följas under den dagliga hanteringen eller driften av den virtuella matrisen. Dessa metoder beskriver olika hanterings uppgifter, till exempel säkerhets kopiering, återställning från en säkerhets kopia, utförande av redundans, inaktive ring och borttagning av matrisen, övervakning av system användning och hälso tillstånd, samt körning av virus genomsökningar på den virtuella matrisen.

### <a name="backups"></a>Säkerhetskopior
Data på den virtuella matrisen säkerhets kopie ras till molnet på två sätt, en automatiserad standard säkerhets kopiering av hela enheten från 22:30 eller via en manuell säkerhets kopiering på begäran. Som standard skapar enheten automatiskt dagliga moln ögonblicks bilder av alla data som finns på den. Mer information finns i [säkerhetskopiera din virtuella StorSimple-matris](storsimple-virtual-array-backup.md).

Den frekvens och kvarhållning som är associerad med standard säkerhets kopieringarna kan inte ändras, men du kan konfigurera tiden då de dagliga säkerhets kopieringarna initieras varje dag. När du konfigurerar start tiden för de automatiska säkerhets kopieringarna rekommenderar vi att:

* Schemalägg säkerhets kopieringar för tider med låg belastning. Start tiden för säkerhets kopieringen bör inte sammanfalla med flera värden i/o.
* Starta en manuell säkerhets kopiering på begäran när du planerar att utföra en redundansväxling av enheten eller före underhålls perioden för att skydda data på den virtuella matrisen.

### <a name="restore"></a>Återställ
Du kan återställa från en säkerhets kopia på två sätt: Återställ till en annan volym eller dela eller utföra en återställning på objekt nivå (endast tillgängligt på en virtuell matris som kon figurer ATS som en fil Server). Med återställning på objekt nivå kan du göra en detaljerad återställning av filer och mappar från en moln säkerhets kopia av alla resurser på StorSimple-enheten. Mer information finns i [återställa från en säkerhets kopia](storsimple-virtual-array-clone.md).

När du utför en återställning bör du tänka på följande rikt linjer:

* Din virtuella StorSimple-matris har inte stöd för återställning på plats. Detta kan dock lätt uppnås genom en två stegs process: frigör utrymme på den virtuella matrisen och återställ sedan till en annan volym/resurs.
* När du återställer från en lokal volym bör du vara medveten om att återställningen kommer att vara en tids krävande åtgärd. Även om volymen kan komma att bli online, fortsätter data att behållas i bakgrunden.
* Volym typen förblir samma under återställnings processen. En nivå volym återställs till en annan nivå volym och en lokalt fäst volym till en annan lokalt fixerad volym.
* När du försöker återställa en volym eller en resurs från en säkerhets kopia kan en mål volym eller resurs fortfarande skapas i portalen om återställnings jobbet Miss lyckas. Det är viktigt att du tar bort den oanvända mål volymen eller resursen i portalen för att minimera eventuella framtida problem som uppstår på grund av det här elementet.

### <a name="failover-and-disaster-recovery"></a>Redundansväxling och haveri beredskap
Med en enhets växling kan du migrera data från en *käll* enhet i data centret till en annan *målenhet* på samma eller en annan geografisk plats. Redundansväxlingen för hela enheten. Under redundansväxlingen ändrar käll enhetens moln data ägande till den på mål enheten.

För din virtuella StorSimple-matris kan du bara redundansväxla till en annan virtuell matris som hanteras av samma StorSimple Manager tjänst. En redundansväxling till en enhet på 8000-serien eller en matris som hanteras av en annan StorSimple Manager tjänst (än den som används för käll enheten) är inte tillåten. Om du vill veta mer om redundansväxlingen går du till [krav för enhetens redundans](storsimple-virtual-array-failover-dr.md).

Tänk på följande när du utför en redundans för din virtuella matris:

* För en planerad redundansväxling rekommenderas bästa praxis att ta alla volymer/resurser offline innan redundansväxlingen initieras. Följ instruktionerna för operativ systemet för att ta volymerna/resurserna offline på värden först och ta sedan dem offline på den virtuella enheten.
* För en fil Server haveri beredskap (DR) rekommenderar vi att du ansluter mål enheten till samma domän som källan så att resurs behörigheterna automatiskt löses. Det finns bara stöd för redundans till en mål enhet i samma domän i den här versionen.
* När DR-filen har slutförts tas käll enheten bort automatiskt. Även om enheten inte längre är tillgänglig, kommer den virtuella dator som du har allokerat på värd systemet fortfarande att förbruka resurser. Vi rekommenderar att du tar bort den här virtuella datorn från värd systemet för att förhindra avgifter från att periodiseras.
* Observera att även om redundansväxlingen Miss lyckas **är data alltid säkra i molnet**. Överväg följande tre scenarier där redundansväxlingen inte slutfördes korrekt:
  
  * Ett fel uppstod i de inledande stegen i redundansväxlingen, till exempel när DR-kontrollerna utfördes. I den här situationen kan mål enheten fortfarande användas. Du kan försöka utföra redundansväxlingen på samma mål enhet igen.
  * Ett fel uppstod under den faktiska redundansväxlingen. I det här fallet markeras mål enheten som oanvändbar. Du måste etablera och konfigurera en annan virtuell mål mat ris och använda den för redundans.
  * Redundansväxlingen slutfördes efter vilken käll enheten togs bort, men mål enheten har problem och du kan inte komma åt några data. Informationen är fortfarande säker i molnet och kan enkelt hämtas genom att skapa en annan virtuell matris och sedan använda den som mål enhet för DR.

### <a name="deactivate"></a>Inaktivera
När du inaktiverar en virtuell StorSimple-matris kan du påverka anslutningen mellan enheten och motsvarande StorSimple Manager tjänst. Inaktive ringen är en **permanent** åtgärd och kan inte ångras. En inaktive rad enhet kan inte registreras med StorSimple Manager tjänsten igen. Mer information finns i [inaktivera och ta bort din virtuella StorSimple-matris](storsimple-virtual-array-deactivate-and-delete-device.md).

Tänk på följande rekommendationer när du inaktiverar din virtuella matris:

* Ta en moln ögonblicks bild av alla data innan du inaktiverar en virtuell enhet. När du inaktiverar en virtuell matris försvinner alla lokala enhets data. Genom att ta en moln ögonblicks bild kan du återställa data i ett senare skede.
* Innan du inaktiverar en virtuell StorSimple-matris måste du stoppa eller ta bort klienter och värdar som är beroende av den enheten.
* Ta bort en inaktive rad enhet om du inte längre använder så att den inte påförs några kostnader.

### <a name="monitoring"></a>Övervakning
För att säkerställa att din virtuella StorSimple-matris är i ett kontinuerligt felfritt tillstånd, måste du övervaka matrisen och se till att du får information från systemet, inklusive aviseringar. För att övervaka den övergripande hälso statusen för den virtuella matrisen, implementera följande bästa praxis:

* Konfigurera övervakning för att spåra disk användningen av din virtuella mat ris data disk samt OS-disken. Om du kör Hyper-V kan du använda en kombination av System Center Virtual Machine Manager (SCVMM) och System Center Operations Manager för att övervaka dina Virtualization-värdar.
* Konfigurera e-postaviseringar på den virtuella matrisen för att skicka aviseringar på vissa användnings nivåer.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Program för index sökning och virus genomsökning
En virtuell StorSimple-matris kan automatiskt Tier data från den lokala nivån till Microsoft Azure molnet. När ett program, till exempel en indexs ökning eller virus genomsökning används för att genomsöka data som lagras på StorSimple, måste du ta hänsyn till att moln data inte går att komma åt och kommer tillbaka till den lokala nivån.

Vi rekommenderar att du implementerar följande metod tips när du konfigurerar indexs ökningen eller virus genomsökningen på den virtuella matrisen:

* Inaktivera alla automatiskt konfigurerade fullständiga skannings åtgärder.
* Konfigurera indexs ökningen eller antivirus programmet för att utföra en stegvis genomsökning för volymer med flera nivåer. Detta genomsöker bara de nya data som troligt vis finns på den lokala nivån. Data som är i nivå av molnet kan inte nås under en stegvis åtgärd.
* Kontrol lera att rätt Sök filter och inställningar har kon figurer ATS så att endast de avsedda typerna av filer kan genomsökas. Bildfiler (JPEG, GIF och TIFF) och teknik ritningar bör till exempel inte genomsökas under den stegvisa eller fullständiga index återuppbyggnaden.

Följ dessa rikt linjer om du använder Windows indexerings process:

* Använd inte Windows-indexeraren för skiktade volymer eftersom den anropar stora mängder data (TBs) från molnet om indexet behöver återskapas ofta. Att återskapa indexet skulle hämta alla filtyper för att indexera innehållet.
* Använd indexerings processen i Windows för lokalt fästa volymer eftersom detta bara kommer åt data på de lokala nivåerna för att bygga indexet (det går inte att komma åt moln data).

### <a name="byte-range-locking"></a>Låsning av byte intervall
Program kan låsa ett angivet antal byte i filerna. Om låsning av byte intervall är aktiverat för de program som skriver till din StorSimple fungerar inte skiktning på den virtuella matrisen. För att nivån ska fungera måste alla områden i de filer som används låsas upp. Låsning av byte intervall stöds inte med skiktade volymer på den virtuella matrisen.

Rekommenderade åtgärder för att under lätta byte intervall låsning:

* Inaktivera byte intervall låsning i program logiken.
* Använd lokalt fästa volymer (i stället för på nivå av) för de data som är kopplade till det här programmet. Lokalt fästa volymer har inte en nivå i molnet.
* När du använder lokalt fästa volymer där låsning av byte intervall har Aktiver ATS kan volymen anslutas innan återställningen är klar. I dessa fall måste du vänta tills återställningen har slutförts.

## <a name="multiple-arrays"></a>Flera matriser
Flera virtuella matriser kan behöva distribueras för att kunna användas för en växande arbets uppsättning data som kan spilla på molnet och därmed påverka enhetens prestanda. I dessa fall är det bäst att skala enheter när arbets minnet växer. Detta kräver att en eller flera enheter läggs till i det lokala data centret. När du lägger till enheterna kan du:

* Dela den aktuella data uppsättningen.
* Distribuera nya arbets belastningar till nya enheter.
* Om du distribuerar flera virtuella matriser, rekommenderar vi att du från belastnings Utjämnings perspektiv distribuerar matrisen mellan olika hypervisor-värdar.
* Flera virtuella matriser (när de har kon figurer ATS som en fil server eller en iSCSI-server) kan distribueras i ett Distributed File System-namnområde. Detaljerade anvisningar finns i [Distributed File System namn områdes lösning med distributions guide för Hybrid moln lagring](https://www.microsoft.com/download/details.aspx?id=45507). Distributed File System replikering rekommenderas inte för användning med den virtuella matrisen. 

## <a name="see-also"></a>Se även
Lär dig hur du [administrerar din virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md) via tjänsten StorSimple Manager.