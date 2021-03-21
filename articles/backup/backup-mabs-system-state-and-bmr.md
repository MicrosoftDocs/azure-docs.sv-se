---
title: Skydd av system tillstånd och återställning utan operativ system
description: Använd Azure Backup Server för att säkerhetskopiera system tillstånd och tillhandahålla BMR-skydd (Bare Metal Recovery).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021630"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Säkerhetskopiera system tillstånd och Återställ till Bare Metal genom att använda Azure Backup Server

Azure Backup Server säkerhetskopierar system tillstånd och ger BMR-skydd (Bare Metal Recovery).

* **Säkerhets kopiering av system tillstånd**: säkerhetskopierar operativsystemfiler. Med den här säkerhets kopian kan du återställa när en dator startar, men systemfiler och registret går förlorade. En säkerhets kopia av system tillstånd innehåller följande element:
  * Domänmedlem: startfiler, COM+-klassens registreringsdatabas, register
  * Domänkontrollant: Windows Server Active Directory (NTDS), startfiler, databas för COM+-klass registrering, register, system volym (SYSVOL)
  * Dator som kör kluster tjänster: kluster serverns metadata
  * Dator som kör certifikat tjänster: certifikat data
* **Bare Metal-säkerhetskopiering**: säkerhetskopierar operativsystemfiler och alla data på kritiska volymer, förutom användar data. Efter definition innehåller en BMR säkerhets kopia en säkerhets kopia av system tillstånd. Den ger skydd när en dator inte startar och du måste återställa allt.

I följande tabell sammanfattas vad du kan säkerhetskopiera och återställa. Information om app-versioner som system tillstånd och BMR kan skydda finns i [vad Azure Backup Server säkerhetskopiera?](backup-mabs-protection-matrix.md).

|Backup|Problem|Återställa från Azure Backup Server säkerhets kopia|Återställa systemtillståndssäkerhetskopia|Återställning utan operativsystem|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fildata**<br /><br />Regelbunden säkerhetskopiering av data<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorade fildata|Y|N|N|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad Server (data volymer är intakta)|N|N|Y|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (datavolymer förlorade)|Y|N|Y<br /><br />BMR, följt av regelbunden återställning av säkerhetskopierade fildata|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR-/systemtillståndssäkerhetskopia|Borttappad webbplats, listor, list objekt, dokument|Y|N|N|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR-/systemtillståndssäkerhetskopia|Haveriberedskap|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad VM|Y|N|N|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Saknad eller skadad operativsystem|N|Y|Y|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer orörda)|N|N|Y|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer förlorade)|N|N|Y<br /><br />BMR, följt av regelbunden Azure Backup Server återställning|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorade appdata|Y|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar orörda)|N|N|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar förlorade)|N|N|Y<br /><br />BMR-återställning, följt av regelbunden Azure Backup Server återställning|

## <a name="how-system-state-backup-works"></a>Hur säkerhetskopiering av systemtillstånd fungerar

När en säkerhets kopiering av system tillstånd körs kommunicerar backup-servern med Windows Server Backup för att begära en säkerhets kopia av serverns system tillstånd. Som standard använder säkerhets kopierings servern och Windows Server Backup den enhet som har mest ledigt utrymme. Information om den här enheten sparas i *PSDataSourceConfig.xml* -filen.

Du kan anpassa den enhet som säkerhets kopierings servern använder för säkerhets kopiering av system tillstånd:

1. Gå till *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources* på den skyddade servern.
1. Öppna *PSDataSourceConfig.xml* -filen för redigering.
1. Ändra värdet \<FilesToProtect\> för enhetsbeteckningen.
1. Spara och stäng filen.

Om en skydds grupp är inställd för att skydda datorns system tillstånd kör du en konsekvens kontroll. Om en avisering genereras väljer du **ändra skydds grupp** i aviseringen och slutför sedan sidorna i guiden. Kör sedan en annan konsekvenskontroll.

Om skydds servern finns i ett kluster kan en kluster enhet väljas som den enhet som har mest ledigt utrymme. Om ägarskapet växlas till en annan nod och en säkerhets kopiering av system tillstånd körs, är enheten inte tillgänglig och säkerhets kopieringen Miss lyckas. I det här scenariot ändrar du *PSDataSourceConfig.xml* så att den pekar på en lokal enhet.

Därefter skapar Windows Server Backup en mapp med namnet *WindowsImageBackup* i roten i mappen Restore. När Windows Server Backup skapar säkerhets kopian placeras alla data i den här mappen. När säkerhets kopieringen är klar överförs filen till datorn för säkerhets kopierings servern. Notera följande information:

* Den här mappen och dess innehåll rensas inte när säkerhets kopieringen eller överföringen är klar. Det bästa sättet att tänka på detta är att utrymmet är reserverat för nästa gång en säkerhets kopiering slutförs.
* Mappen skapas för varje säkerhets kopiering. Tids-och datumstämpeln visar tiden för den senaste säkerhets kopieringen av system tillstånd.

## <a name="how-bmr-backup-works"></a>Så här fungerar BMR backup

För BMR (inklusive en säkerhets kopia av system tillstånd) sparas säkerhets kopierings jobbet direkt till en resurs på datorn för säkerhets kopierings servern. Den sparas inte i en mapp på den skyddade servern.

Backup Server anropar Windows Server Backup och delar ut replik volymen för den BMR säkerhets kopian. I det här fallet behöver det inte Windows Server Backup att använda den enhet som har mest ledigt utrymme. I stället används den resurs som skapades för jobbet.

När säkerhets kopieringen är klar överförs filen till datorn för säkerhets kopierings servern. Loggarna lagras i *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

* BMR stöds inte för datorer som kör Windows Server 2003 eller för datorer som kör ett klient operativ system.

* Du kan inte skydda BMR och systemtillstånd för samma dator i olika skyddsgrupper.

* En säkerhets kopierings Server dator kan inte skydda sig själv för BMR.

* Kortsiktigt skydd till band (disk till band eller D2T) stöds inte för BMR. Långsiktig lagring till band (disk till disk till band eller D2D2T) stöds.

* För BMR-skydd måste Windows Server Backup installeras på den skyddade datorn.

* För BMR-skydd, till skillnad från system tillstånds skydd, har backup server inga utrymmes krav på den skyddade datorn. Windows Server Backup överför säkerhets kopiorna direkt till säkerhets kopierings servern. Överförings jobbet för säkerhets kopiering visas inte i vyn säkerhets kopierings Server **jobb** .

* Backup server reserverar 30 GB utrymme på replik volymen för BMR. Du kan ändra den här utrymmes tilldelningen på sidan **diskallokering** i guiden Ändra skydds grupp. Eller så kan du använda PowerShell-cmdletarna Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation. På återställnings punkt volymen kräver BMR-skydd cirka 6 GB för kvarhållning av fem dagar.
  * Du kan inte minska storleken på replik volymen till mindre än 15 GB.
  * Säkerhets kopierings servern beräknar inte storleken på data källan BMR. Den förutsätter 30 GB för alla servrar. Ändra värdet baserat på storleken på BMR säkerhets kopieringar som du förväntar dig i din miljö. Du kan ungefär beräkna storleken på en BMR säkerhets kopia som summan av använt utrymme på alla kritiska volymer. Kritiska volymer = start volym + system volym + volym värd system tillstånds data, till exempel Active Directory.

* Om du ändrar från system tillstånds skydd till BMR-skydd kräver BMR-skydd mindre utrymme på *återställnings punkt volymen*. Det extra utrymmet på volymen frigörs dock inte. Du kan minska volym storleken manuellt på sidan **Ändra diskallokering** i guiden Ändra skydds grupp. Eller så kan du använda PowerShell-cmdletarna Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation.

    Om du ändrar från system tillstånds skydd till BMR-skydd kräver BMR-skydd mer utrymme på *replik volymen*. Volymen utökas automatiskt. Om du vill ändra standard utrymmes tilldelningarna använder du Modify-DiskAllocation PowerShell-cmdleten.

* Om du ändrar från BMR-skydd till skydd av system tillstånd behöver du mer utrymme på återställnings punkt volymen. Säkerhets kopierings servern kan försöka öka volymen automatiskt. Om lagringspoolen inte har tillräckligt med utrymme uppstår ett fel.

    Om du ändrar från BMR-skydd till skydd av system tillstånd behöver du utrymme på den skyddade datorn. Du behöver ett utrymme eftersom skydd av system tillstånd först skriver repliken till den lokala datorn och sedan överför repliken till säkerhets kopierings servern.

## <a name="before-you-begin"></a>Innan du börjar

1. **Distribuera Azure Backup Server**. Kontrol lera att säkerhets kopierings servern är korrekt distribuerad. Mer information finns i:
    * [System krav för Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Skydds mat ris för säkerhets kopierings Server](backup-mabs-protection-matrix.md)

1. **Konfigurera lagring**. Du kan lagra säkerhets kopierings data på disk, på band och i molnet med Azure. Mer information finns i [förbereda data lagring](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Konfigurera skydds agenten**. Installera skydds agenten på den dator som du vill säkerhetskopiera. Mer information finns i [Distribuera DPM-skyddsagenten](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Säkerhetskopiera systemtillstånd och utan operativsystem (Bare Metal)

Säkerhetskopiera system tillstånd och Bare Metal:

1. Om du vill öppna sidan Skapa guiden Ny skyddsgrupp går du till säkerhets kopierings servern administratörskonsol och väljer **skydds**  >  **åtgärder**  >  **skapa skydds grupp**.

1. Välj **servrar** på sidan **Välj typ av skydds grupp** och välj sedan **Nästa**.

1. På sidan **Välj grupp medlemmar** expanderar du datorn och väljer sedan antingen **BMR** eller **system tillstånd**.

    Kom ihåg att du inte kan skydda både BMR och system tillstånd för samma dator i olika grupper. När du väljer BMR aktive ras också system tillstånd automatiskt. Mer information finns i [distribuera skydds grupper](/system-center/dpm/create-dpm-protection-groups).

1. På sidan **Välj data skydds metod** väljer du hur du vill hantera kortsiktig säkerhets kopiering och säkerhets kopiering på lång sikt.

    Kortsiktig säkerhets kopiering är alltid en disk först, med alternativet att säkerhetskopiera från disken till Azure med hjälp av Azure Backup (kortsiktig eller långsiktig). Ett alternativ till långsiktig säkerhets kopiering till molnet är att konfigurera långsiktig säkerhets kopiering till en fristående band enhet eller ett band bibliotek som är anslutet till säkerhets kopierings servern.

1. På sidan **välj Short-Term mål** väljer du hur du vill säkerhetskopiera till kortsiktig lagring på disk:
    * För **kvarhållningsintervall** väljer du hur länge du vill behålla data på disken.
    * I **Synkroniseringsfrekvens** väljer du hur ofta du vill köra en stegvis säkerhets kopiering på disk. Om du inte vill ange ett intervall för säkerhets kopiering kan du välja **precis före en återställnings punkt**. Säkerhets kopierings servern kör en fullständig snabb säkerhets kopiering precis innan varje återställnings punkt har schemalagts.

1. Om du vill lagra data på band för långsiktig lagring väljer du hur länge du vill behålla band data (1 till 99 år) på sidan **ange Long-Term mål** .
    1. För **säkerhets kopierings frekvens** väljer du hur ofta säkerhets kopiering ska köras till band. Frekvensen baseras på det kvarhållningsintervall som du har valt:
        * Om kvarhållningsintervallet är 1 till 99 år kan du säkerhetskopiera varje dag, varje vecka, varannan vecka, månads vis, kvartals vis, halvårs vis eller varje år.
        * När kvarhållningsintervallet är 1 till 11 månader kan du säkerhetskopiera varje dag, varje vecka, varannan vecka eller varje månad.
        * Om kvarhållningsintervallet är 1 till 4 veckor kan du säkerhetskopiera varje dag eller varje vecka.

    1. På sidan **Välj band-och biblioteks information** väljer du det band och bibliotek som ska användas. Välj även om data ska komprimeras och krypteras.

1. På sidan **Granska diskallokering** granskar du disk utrymmet för lagringspoolen som är tillgängligt för skydds gruppen.

    * **Total data storlek** är storleken på de data som du vill säkerhetskopiera.
    * **Disk utrymme** som ska tillhandahållas på Azure Backup Server är det utrymme som säkerhets kopierings servern rekommenderar för skydds gruppen. Säkerhets kopierings servern använder de här inställningarna för att välja den idealiska säkerhets kopierings volymen. Du kan redigera säkerhets kopierings volymens val i **disk tilldelnings information**.
    * För arbets belastningar väljer du önskad lagring i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetablerat utrymme är mängden lagrings utrymme som säkerhets kopierings servern föreslår att du lägger till i volymen för att säkerställa smidig säkerhets kopiering.

1. På sidan **Välj metod för skapande av replik** väljer du hur du vill hantera den första fullständiga datareplikeringen.

    Om du väljer att replikera över nätverket rekommenderar vi att du väljer en tid med låg belastning. För stora mängder data eller för nätverks villkor som är mindre än optimala bör du överväga att replikera data offline med hjälp av flyttbara medier.

1. På sidan **Välj alternativ för konsekvens kontroll** väljer du hur du vill automatisera konsekvens kontroller.

    Du kan bara välja att köra en kontroll när replik data blir inkonsekventa eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvens kontroll kan du när som helst köra en manuell kontroll. Om du vill köra en manuell kontroll högerklickar du på skydds gruppen i **skydds** delen på säkerhets kopierings servern administratörskonsol och väljer sedan **utför konsekvens kontroll**.

1. Om du väljer att säkerhetskopiera till molnet med hjälp av Azure Backup väljer du de arbets belastningar som du vill säkerhetskopiera till Azure på sidan **ange data för onlineskydd** .

1. På sidan **Ange schema för onlinesäkerhetskopiering** väljer du hur ofta säkerhets kopieringen ska gå stegvis till Azure.

    Du kan schemalägga säkerhets kopieringar så att de körs varje dag, vecka, månad och år. Du kan också välja den tid och det datum då säkerhets kopieringarna ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång en säkerhets kopiering körs skapas en data återställnings punkt i Azure från kopian av de säkerhets kopierings data som lagras på säkerhets kopierings Server disken.

1. På sidan **Ange bevarande princip för online** väljer du hur återställnings punkterna som skapas från de dagliga, vecko Visa, månatliga och årliga säkerhets kopiorna ska behållas i Azure.

1. På sidan **Välj replikering online** väljer du hur den första fullständiga replikeringen av data ska ske.

    Du kan replikera över nätverket eller säkerhetskopiera offline (offline-dirigering). En offline-säkerhetskopiering använder Azure import-funktionen. Mer information finns i [arbets flöde för säkerhets kopiering offline i Azure Backup](offline-backup-azure-data-box.md).

1. På sidan  **Sammanfattning** granskar du inställningarna. När du har valt **Skapa grupp** sker inledande replikering av data. När datareplikeringen har slutförts på sidan **status** är skydds gruppens status **OK**. Säkerhets kopieringarna sker sedan i enlighet med inställningarna för skydds gruppen.

## <a name="recover-system-state-or-bmr"></a>Återställa systemtillstånd eller BMR

Du kan återställa BMR eller systemtillstånd på en nätverksplats. Om du säkerhetskopierade BMR kan du använda Windows Recovery Environment (WinRE) för att starta systemet och ansluta det till nätverket. Använd sedan Windows Server Backup för att återställa från nätverksplatsen. Om du säkerhetskopierade system tillstånd använder du bara Windows Server Backup för att återställa från nätverks platsen.

### <a name="restore-bmr"></a>Återställa BMR

Köra återställning på datorn för säkerhets kopierings servern:

1. Leta upp den dator som du vill återställa i **återställnings** fönstret. Välj sedan **återställning utan operativ system**.

1. Tillgängliga återställningspunkter anges i fetstil i kalendern. Välj datum och tid för den återställnings punkt som du vill använda.

1. På sidan  **Välj återställnings typ** väljer **du kopiera till en nätverksmapp**.

1. På sidan **Ange mål** väljer du målet för de kopierade data.

    Kom ihåg att målet måste ha tillräckligt med utrymme för data. Vi rekommenderar att du skapar en ny mapp för målet.

1. På sidan **Ange återställnings alternativ** väljer du säkerhets inställningar. Välj sedan om du vill använda storage area network (SAN)-baserade ögonblicks bilder av maskin vara för snabbare återställning. Det här alternativet är bara tillgängligt om:
    * Du har ett SAN som tillhandahåller den här funktionen.
    * Du kan skapa och dela en klon för att göra den skrivbar.
    * Datorn med den skyddade datorn och säkerhets kopierings servern är ansluten till samma nätverk.

1. Konfigurera meddelande alternativ.
1. På sidan **bekräftelse** väljer du **Återställ**.

Så här konfigurerar du resurs platsen:

1. På återställnings platsen går du till den mapp där säkerhets kopian finns.

1. Dela mappen som är en nivå över *WindowsImageBackup* så att roten i den delade mappen är mappen *WindowsImageBackup* .

    Om du inte delar den här mappen hittar inte säkerhets kopian i Restore. För att ansluta med hjälp av WinRE behöver du en resurs som du kan komma åt i WinRE med rätt IP-adress och autentiseringsuppgifter.

Så här återställer du systemet:

1. Starta datorn där du vill återställa avbildningen med hjälp av Windows-DVD: n för systemet som du återställer.

1. På den första sidan kontrollerar du inställningarna för språk och nationella inställningar. På sidan **Installera** väljer du **Reparera datorn**.

1. På sidan **alternativ för system återställning** väljer **du Återställ datorn med hjälp av en system avbildning som du skapade tidigare**.

1. På sidan **Välj en säkerhets kopia av system avbildning** väljer du **Välj en system** avbildning  >  **Avancerad**  >  **sökning för en system avbildning i nätverket**. Välj **Ja** om en varning visas. Gå till resurs Sök vägen, ange autentiseringsuppgifterna och välj sedan återställnings punkten. Systemet söker efter de säkerhets kopior som är tillgängliga i återställnings punkten. Välj den återställnings punkt som du vill använda.

1. På sidan **Välj hur du vill återställa säkerhets kopian** väljer du **Formatera och partitionera om diskar**. Verifiera inställningarna på nästa sida.

1. Starta återställningen genom att välja **Slutför**. En omstart krävs.

### <a name="restore-system-state"></a>Återställa systemtillståndet

Köra återställning på säkerhets kopierings Server:

1. I rutan **återställning** letar du reda på den dator som du vill återställa och väljer sedan **Bare Metal Recovery**.

1. Tillgängliga återställningspunkter anges i fetstil i kalendern. Välj datum och tid för den återställnings punkt som du vill använda.

1. På sidan **Välj återställnings typ** väljer **du kopiera till en nätverksmapp**.

1. På sidan **Ange mål** väljer du var du vill kopiera data.

    Kom ihåg att den destination du väljer måste ha tillräckligt med utrymme för data. Vi rekommenderar att du skapar en ny mapp för målet.

1. På sidan **Ange återställnings alternativ** väljer du säkerhets inställningar. Välj sedan om du vill använda SAN-baserade ögonblicks bilder av maskin vara för snabbare återställning. Det här alternativet är bara tillgängligt om:
    * Du har ett SAN som tillhandahåller den här funktionen.
    * Du kan skapa och dela en klon för att göra den skrivbar.
    * Den skyddade datorn och servern för säkerhets kopiering är anslutna till samma nätverk.

1. Konfigurera meddelande alternativ.
1. På sidan **bekräftelse** väljer du **Återställ**.

Köra Windows Server Backup:

1. Välj **åtgärder**  >  **Återställ**  >  **den här servern**  >  **Nästa**.

1. Välj **en annan server**, Välj sidan **Ange plats typ** och välj sedan **delad fjärrmapp**. Ange sökvägen till den mapp som innehåller återställnings punkten.

1. På sidan **Välj återställnings typ** väljer du **system tillstånd**.

1. På sidan **Välj plats för återställning av system tillstånd** väljer du  **ursprunglig plats**.

1. På sidan **bekräftelse** väljer du **Återställ**.

1. Starta om servern efter återställningen.

Du kan också köra återställning av system tillstånd i en kommando tolk:

1. Starta Windows Server Backup på den dator som du vill återställa.

1. Hämta versions identifierare genom att ange följande i en kommando tolk:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Använd versions-ID: n för att starta återställning av system tillstånd. Skriv följande i kommandotolken:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Bekräfta att du vill starta återställningen. Du kan se processen i kommando tolkens fönster. En återställningslogg skapas.

1. Starta om servern efter återställningen.
