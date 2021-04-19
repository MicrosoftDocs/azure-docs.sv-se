---
title: Om Säkerhetskopiering av virtuella Azure-datorer
description: I den här artikeln får du lära Azure Backup tjänsten rekommendationer för att rekommendationer för virtuella Azure-datorer och hur du följer bästa praxis.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718457"
---
# <a name="an-overview-of-azure-vm-backup"></a>En översikt över säkerhetskopiering av virtuella Azure-datorer

Den här artikeln beskriver hur [Azure Backup-tjänsten](./backup-overview.md) backar upp virtuella Azure-datorer (VM).

Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstöring av data på dina virtuella datorer. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalning är enkla, säkerhetskopieringar optimeras och du kan enkelt återställa efter behov.

Som en del av [säkerhetskopieringsprocessen](#snapshot-creation)tas en ögonblicksbild och data överförs till Recovery Services-valvet utan att det påverkar produktionsarbetsbelastningar. Ögonblicksbilden ger olika konsekvensnivåer, enligt beskrivningen [här](#snapshot-consistency).

Azure Backup har även specialiserade erbjudanden för databasarbetsbelastningar som [SQL Server](backup-azure-sql-database.md) och [SAP HANA](sap-hana-db-about.md) som är arbetsbelastningsmedvetna, erbjuder 15 minuters RPO (mål för återställningspunkt) och tillåter säkerhetskopiering och återställning av enskilda databaser.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Så här slutför Azure Backup en säkerhetskopiering för virtuella Azure-datorer:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Kryptering av säkerhetskopior av virtuella Azure-datorer

När du krypterar virtuella Azure-datorer Azure Backup krypteras de virtuella datorerna i vila med Kryptering för lagringstjänst (SSE). Azure Backup kan också krypa tillbaka virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption.

**Kryptering** | **Information** | **Support**
--- | --- | ---
**Sse** | Med SSE Azure Storage kryptering i vila genom att data krypteras automatiskt innan de lagras. Azure Storage dekrypterar även data innan de hämtas. Azure Backup stöder säkerhetskopiering av virtuella datorer med två typer av Kryptering för lagringstjänst:<li> **SSE med plattform hanterade nycklar:** Den här krypteringen är som standard för alla diskar på dina virtuella datorer. Mer information [finns här.](../virtual-machines/disk-encryption.md#platform-managed-keys)<li> **SSE med kund hanterade nycklar**. Med CMK hanterar du de nycklar som används för att kryptera diskarna. Mer information [finns här.](../virtual-machines/disk-encryption.md#customer-managed-keys) | Azure Backup använder SSE för kryptering i vila av virtuella Azure-datorer.
**Azure Disk Encryption** | Azure Disk Encryption krypterar både OS- och datadiskar för virtuella Azure-datorer.<br/><br/> Azure Disk Encryption integreras med BitLocker-krypteringsnycklar (BEK), som skyddas i ett nyckelvalv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault nyckelkrypteringsnycklar (KEK). | Azure Backup har stöd för säkerhetskopiering av hanterade och ohanterade virtuella Azure-datorer som krypterats med ENDAST BEK:er, eller med BEK:er tillsammans med KEK:er.<br/><br/> Både BEK och KEK säkerhetskopieras och krypteras.<br/><br/> Eftersom KEK:er och BEK:er säkerhetskopieras kan användare med nödvändiga behörigheter återställa nycklar och hemligheter till nyckelvalvet om det behövs. Dessa användare kan också återställa den krypterade virtuella datorn.<br/><br/> Krypterade nycklar och hemligheter kan inte läsas av obehöriga användare eller av Azure.

För hanterade och ohanterade virtuella Azure-datorer stöder Backup både virtuella datorer som krypterats med ENDAST BEK eller virtuella datorer som krypterats med BEK:er tillsammans med KEK:er.

Säkerhetskopierade BEK:er (hemligheter) och KEK:er (nycklar) krypteras. De kan bara läsas och användas när de återställs till nyckelvalvet av behöriga användare. Varken obehöriga användare eller Azure kan läsa eller använda säkerhetskopierade nycklar eller hemligheter.

BEK:er säkerhetskopieras också. Om BEK:erna försvinner kan behöriga användare återställa BEK:erna till nyckelvalvet och återställa de krypterade virtuella datorerna. Det är bara användare med den behörighetsnivå som krävs som kan kryptera och återställa krypterade virtuella datorer eller nycklar och hemligheter.

## <a name="snapshot-creation"></a>Skapa ögonblicksbild

Azure Backup tar ögonblicksbilder enligt säkerhetskopieringsschemat.

- **Virtuella Windows-datorer:** För virtuella Windows-datorer samordnar Backup-tjänsten med VSS för att ta en app-konsekvent ögonblicksbild av VM-diskarna.  Som standard Azure Backup en fullständig VSS-säkerhetskopiering (den trunkerar loggarna för programmet, till exempel SQL Server vid tidpunkten för säkerhetskopieringen för att få konsekvent säkerhetskopiering på programnivå).  Om du använder en SQL Server på säkerhetskopiering av virtuella Azure-datorer kan du ändra inställningen för att göra en VSS-kopieringssäkerhetskopia (för att bevara loggar). Mer information finns i [den här artikeln](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Virtuella Linux-datorer:** Om du vill ta appkonsekventa ögonblicksbilder av virtuella Linux-datorer använder du linux-ramverket för förskript och efterskript för att skriva egna anpassade skript för att säkerställa konsekvens.

  - Azure Backup endast de för-/efterskript som skrivits av dig.
  - Om förskripten och efterskripten körs korrekt markerar Azure Backup återställningspunkten som program-konsekvent. Men när du använder anpassade skript är du i slutändan ansvarig för programkonsekvensen.
  - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.

## <a name="snapshot-consistency"></a>Konsekvens för ögonblicksbilder

I följande tabell beskrivs de olika typerna av konsekvens för ögonblicksbilder:

**Ögonblicksbild** | **Information** | **Återställande** | **Att tänka på**
--- | --- | --- | ---
**Program konsekvent** | App-konsekventa säkerhetskopieringar samlar in minnesinnehåll och väntande I/O-åtgärder. Appkonsekventa ögonblicksbilder använder en VSS-skrivare (eller för-/efterskript för Linux) för att säkerställa konsekvensen för appdata innan en säkerhetskopiering sker. | När du återställer en virtuell dator med en app-konsekvent ögonblicksbild startar den virtuella datorn. Inga data skadas eller går förlorade. Apparna startar i ett konsekvent tillstånd. | Windows: Alla VSS-skrivare lyckades<br/><br/> Linux: För-/efterskript har konfigurerats och lyckats
**Filsystems konsekvent** | Filsystemkonsekventa säkerhetskopior ger konsekvens genom att ta en ögonblicksbild av alla filer samtidigt.<br/><br/> | När du återställer en virtuell dator med en filsystems konsekvent ögonblicksbild startas den virtuella datorn. Inga data skadas eller går förlorade. Appar måste implementera en egen "korrigeringsmekanism" för att säkerställa att återställda data är konsekventa. | Windows: Vissa VSS-skrivare misslyckades <br/><br/> Linux: Standard (om för-/efterskript inte har konfigurerats eller misslyckats)
**Krasch konsekvent** | Krasch konsekventa ögonblicksbilder inträffar vanligtvis om en virtuell Azure-dator stängs av vid tidpunkten för säkerhetskopieringen. Endast de data som redan finns på disken vid tidpunkten för säkerhetskopieringen samlas in och säkerhetskopieras. | Börjar med den virtuella datorns startprocess följt av en diskkontroll för att åtgärda fel. Minnesbaserade data eller skrivåtgärder som inte överfördes till disken innan kraschen går förlorade. Appar implementerar sin egen dataverifiering. En databasapp kan till exempel använda sin transaktionslogg för verifiering. Om transaktionsloggen innehåller poster som inte finns i databasen, återställs transaktionerna av databasprogramvaran tills data är konsekventa. | Den virtuella datorn är i avstängningstillstånd (stoppad/frisallokerad).

>[!NOTE]
> Om etableringstillståndet **har lyckats tar** Azure Backup säkerhetskopieringar som är konsekventa med filsystemet. Om etableringstillståndet inte **är tillgängligt** **eller misslyckades** tas kraschkom samma säkerhetskopieringar. Om etableringstillståndet **skapar eller** tar **bort** innebär det Azure Backup försöker utföra åtgärderna igen.

## <a name="backup-and-restore-considerations"></a>Överväganden för säkerhetskopiering och återställning

**Att tänka på** | **Information**
--- | ---
**Disk** | Säkerhetskopieringen av VM-diskar är parallell. Om en virtuell dator till exempel har fyra diskar försöker Backup-tjänsten säkerhetskopiera alla fyra diskar parallellt. Säkerhetskopieringen är inkrementell (endast ändrade data).
**Schemaläggning** |  Du kan minska säkerhetskopieringstrafiken genom att säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen och se till att tiderna inte överlappar varandra. Säkerhetskopiering av virtuella datorer samtidigt orsakar trafikstockningar.
**Förbereda säkerhetskopior** | Tänk på den tid som krävs för att förbereda säkerhetskopieringen. Förberedelsetiden omfattar installation eller uppdatering av säkerhetskopieringstillägget och utlösning av en ögonblicksbild enligt säkerhetskopieringsschemat.
**Dataöverföring** | Överväg den tid som krävs för att Azure Backup att identifiera de inkrementella ändringarna från föregående säkerhetskopia.<br/><br/> I en stegvis säkerhetskopiering fastställer Azure Backup ändringarna genom att beräkna kontrollsumman för blocket. Om ett block har ändrats markeras det för överföring till valvet. Tjänsten analyserar de identifierade blocken för att försöka minimera mängden data som ska överföras. När du har utvärderat alla ändrade block överför Azure Backup ändringarna till valvet.<br/><br/> Det kan finnas en fördröjning mellan att ta ögonblicksbilden och kopiera den till valvet. Vid hög belastning kan det ta upp till åtta timmar innan ögonblicksbilderna överförs till valvet. Säkerhetskopieringstiden för en virtuell dator är mindre än 24 timmar för den dagliga säkerhetskopieringen.
**Den första säkerhetskopieringen** | Trots att den totala säkerhetskopieringstiden för stegvisa säkerhetskopior är mindre än 24 timmar. Det kanske inte är fallet för den första säkerhetskopian. Tiden som krävs för den inledande säkerhetskopian beror på storleken på data och när säkerhetskopian bearbetas.
**Återställ kö** | Azure Backup återställer jobb från flera lagringskonton samtidigt och placerar återställningsbegäranden i en kö.
**Återställ kopia** | Under återställningsprocessen kopieras data från valvet till lagringskontot.<br/><br/> Den totala återställningstiden beror på I/O-åtgärderna per sekund (IOPS) och dataflödet för lagringskontot.<br/><br/> Om du vill minska kopieringstiden väljer du ett lagringskonto som inte har lästs in med andra program skrivningar och läsningar.

### <a name="backup-performance"></a>Prestanda vid säkerhetskopiering

Dessa vanliga scenarier kan påverka den totala säkerhetskopieringstiden:

- **Lägga till en ny disk till en skyddad virtuell Azure-dator:** Om en virtuell dator genomgår inkrementell säkerhetskopiering och en ny disk läggs till, ökar säkerhetskopieringstiden. Den totala säkerhetskopieringstiden kan ta mer än 24 timmar på grund av den inledande replikeringen av den nya disken, tillsammans med deltareplikering av befintliga diskar.
- **Fragmenterade diskar:** Säkerhetskopieringen går snabbare när diskändringarna är sammanhängande. Om ändringarna är utspridda och fragmenterade på en disk går säkerhetskopieringen långsammare.
- **Diskomsättning:** Om skyddade diskar som genomgår inkrementell säkerhetskopiering har en daglig omsättning på mer än 200 GB kan säkerhetskopieringen ta lång tid (mer än åtta timmar) att slutföra.
- **Säkerhetskopieringsversioner:** Den senaste versionen av Backup (kallas för versionen för omedelbar återställning) använder en mer optimerad process än en jämförelse av kontrollsummerna för att identifiera ändringar. Men om du använder omedelbar återställning och har tagit bort en ögonblicksbild av säkerhetskopian växlar säkerhetskopieringen till jämförelse av kontrollsumma. I det här fallet överskrider säkerhetskopieringen 24 timmar (eller misslyckas).

### <a name="restore-performance"></a>Återställa prestanda

Dessa vanliga scenarier kan påverka den totala återställningstiden:

- Den totala återställningstiden beror på in- och utdataåtgärderna per sekund (IOPS) och dataflödet för lagringskontot.
- Den totala återställningstiden kan påverkas om mållagringskontot har lästs in med andra läs- och skrivåtgärder för programmet. Om du vill förbättra återställningen väljer du ett lagringskonto som inte har lästs in med andra programdata.

## <a name="best-practices"></a>Bästa praxis

När du konfigurerar säkerhetskopieringar av virtuella datorer föreslår vi att du följer metoderna nedan:

- Ändra de standardinställda schematiderna som anges i en princip. Om standardtiden till exempel är 12:00 ökar du tiden stegvis så att resurserna används optimalt.
- Om du återställer virtuella datorer från ett enda valv rekommenderar vi starkt att du använder olika [v2-lagringskonton](../storage/common/storage-account-upgrade.md) för generell användning för att säkerställa att mållagringskontot inte begränsas. Till exempel måste varje virtuell dator ha ett annat lagringskonto. Om till exempel 10 virtuella datorer återställs använder du 10 olika lagringskonton.
- För säkerhetskopiering av virtuella datorer som använder premiumlagring med omedelbar återställning rekommenderar vi *att du allokerar 50 %* ledigt utrymme av det totala allokerade lagringsutrymmet, vilket endast krävs för den första säkerhetskopieringen.  50 % ledigt utrymme är inte ett krav för säkerhetskopieringar efter att den första säkerhetskopieringen har slutförts
- Gränsen för antalet diskar per lagringskonto är i förhållande till hur mycket diskarna används av programmen som körs på en virtuell dator med infrastruktur som en tjänst (IaaS). Som allmän praxis, om 5 till 10 diskar eller mer finns på ett enda lagringskonto balanserar du belastningen genom att flytta några diskar till separata lagringskonton.
- Om du vill återställa virtuella datorer med hanterade diskar med hjälp av PowerShell anger du ytterligare ***parametern TargetResourceGroupName*** för att ange resursgruppen som hanterade diskar ska återställas till. [Läs mer här](./backup-azure-vms-automation.md#restore-managed-disks).

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Virtuella Azure-datorer som säkerhetskopieras med Azure Backup omfattas av [Azure Backup prissättning.](https://azure.microsoft.com/pricing/details/backup/)

Faktureringen startar inte förrän den första lyckade säkerhetskopieringen är klar. I det här läget börjar faktureringen för både lagring och skyddade virtuella datorer. Faktureringen fortsätter så länge säkerhetskopierade data för den virtuella datorn lagras i ett valv. Om du stoppar skyddet för en virtuell dator, men säkerhetskopierade data för den virtuella datorn finns i ett valv, fortsätter faktureringen.

Faktureringen för en angiven virtuell dator stoppas bara om skyddet har stoppats och alla säkerhetskopierade data tas bort. När skyddet stoppas och det inte finns några aktiva säkerhetskopieringsjobb blir storleken på den senaste lyckade vm-säkerhetskopieringen den skyddade instansstorlek som används för månadsfakturan.

Storleksberäkningen för skyddade instanser baseras på den *virtuella* datorns faktiska storlek. Den virtuella datorns storlek är summan av alla data i den virtuella datorn, exklusive den tillfälliga lagringen. Prissättningen baseras på faktiska data som lagras på datadiskarna, inte på den maximala storleken som stöds för varje datadisk som är ansluten till den virtuella datorn.

På samma sätt baseras fakturan för lagring av säkerhetskopior på mängden data som lagras i Azure Backup, vilket är summan av faktiska data i varje återställningspunkt.

Ta till exempel en virtuell dator av A2-standardstorlek som har två ytterligare datadiskar med en maximal storlek på 32 TB vardera. I följande tabell visas faktiska data som lagras på var och en av dessa diskar:

**Disk** | **Maximal storlek** | **Faktiska data som finns**
--- | --- | ---
OS-disk | 32 TB | 17 GB
Lokal/tillfällig disk | 135 GB | 5 GB (ingår inte för säkerhetskopiering)
Datadisk 1 | 32 TB| 30 GB
Datadisk 2 | 32 TB | 0 GB

Den faktiska storleken på den virtuella datorn i det här fallet är 17 GB + 30 GB + 0 GB = 47 GB. Storleken på den skyddade instansen (47 GB) blir grunden för den månatliga fakturan. När mängden data i den virtuella datorn växer ändras storleken på den skyddade instansen som används för faktureringen så att den matchar.

## <a name="next-steps"></a>Nästa steg

- [Förbered för säkerhetskopiering av virtuella Azure-datorer.](backup-azure-arm-vms-prepare.md)