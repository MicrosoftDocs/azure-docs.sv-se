---
title: Felsöka Azure Backup agenten
description: I den här artikeln får du lära dig hur du felsöker installationen och registreringen av Azure Backup agenten.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518192"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Felsöka Microsoft Azure Recovery Services -agenten (MARS)

Den här artikeln beskriver hur du löser fel som kan visas under konfiguration, registrering, säkerhetskopiering och återställning.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du kontrollerar följande innan du börjar felsöka Microsoft MARS-agenten (Azure Recovery Services):

- [Kontrollera att MARS-agenten är uppdaterad.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att du har nätverksanslutning mellan MARS-agenten och Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Kontrollera att MARS körs (i tjänstkonsolen). Starta om och försök igen om det behövs.
- [Se till att 5 % till 10 % ledigt volymutrymme är tillgängligt på platsen för den scratch-mappen.](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Kontrollera om någon annan process eller ett antivirusprogram stör Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Om säkerhetskopieringsjobbet slutfördes med varningar kan du se [Säkerhetskopieringsjobb slutförda med varning](#backup-jobs-completed-with-warning)
- Om schemalagd säkerhetskopiering misslyckas men manuell säkerhetskopiering fungerar, [se Säkerhetskopieringar körs inte enligt schema](#backups-dont-run-according-to-schedule).
- Kontrollera att operativsystemet har de senaste uppdateringarna.
- [Se till att enheter och filer som inte stöds med attribut som inte stöds undantas från säkerhetskopieringen.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Se till att klockan på det skyddade systemet är konfigurerad till rätt tidszon.
- [Kontrollera .NET Framework 4.5.2 eller senare är installerat på servern](https://www.microsoft.com/download/details.aspx?id=30653).
- Om du försöker registrera om servern till ett valv:
  - Se till att agenten avinstalleras på servern och att den tas bort från portalen.
  - Använd samma lösenfras som ursprungligen användes för att registrera servern.
- För offlinesäkerhetskopior måste Azure PowerShell 3.7.0 installeras på både källdatorn och kopieringsdatorn innan du startar säkerhetskopieringen.
- Om Backup-agenten körs på en virtuell Azure-dator kan du läsa den [här artikeln.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

**Felmeddelande: Ogiltiga** valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)

| Orsak | Rekommenderade åtgärder |
| ---     | ---    |
| **Valvautentiseringsuppgifter är inte giltiga** <br/> <br/> Valvaudentialfiler kan vara skadade, ha upphört att gälla eller ha ett annat *filnamnstillägg än .vaultCredentials*. (De kan till exempel ha laddats ned mer än 10 dagar före registreringen.)| [Ladda ned nya autentiseringsuppgifter](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) från Recovery Services-valvet på Azure Portal. Gör sedan följande efter behov: <ul><li> Om du redan har installerat och registrerat MARS öppnar du MICROSOFT AZURE BACKUP MMC-agentkonsolen. Välj sedan **Registrera server** i fönstret **Åtgärder** för att slutföra registreringen med de nya autentiseringsuppgifterna. <br/> <li> Om den nya installationen misslyckas kan du försöka installera om med de nya autentiseringsuppgifterna.</ul> **Obs!** Om flera valvaudentialfiler har laddats ned är endast den senaste filen giltig under de kommande 10 dagarna. Vi rekommenderar att du laddar ned en ny valvaudentialfil.
| **Proxyserver/brandvägg blockerar registrering** <br/>eller <br/>**Ingen Internetanslutning** <br/><br/> Om datorn eller proxyservern har begränsad Internetanslutning och du inte garanterar åtkomst för nödvändiga URL:er, misslyckas registreringen.| Gör så här:<br/> <ul><li> Samarbeta med IT-teamet för att se till att systemet är anslutet till Internet.<li> Om du inte har en proxyserver ser du till att proxyalternativet inte är markerat när du registrerar agenten. [Kontrollera proxyinställningarna](#verifying-proxy-settings-for-windows).<li> Om du har en brandväggs-/proxyserver kan du samarbeta med nätverksteamet för att se till att dessa URL:er och IP-adresser har åtkomst:<br/> <br> **Webbadresser**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP-adresser**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Försök att registrera dig igen när du har slutfört föregående felsökningssteg.<br></br> Om anslutningen är via Azure ExpressRoute kontrollerar du att inställningarna är konfigurerade enligt beskrivningen i [Azure ExpressRoute stöder](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Antivirusprogram blockerar registrering** | Om du har ett antivirusprogram installerat på servern lägger du till nödvändiga undantagsregler i antivirusgenomsökningen för dessa filer och mappar: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Den scratch-mappen. Standardplatsen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Mappen bin på C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Ytterligare rekommendationer

- Gå till C:/Windows/Temp och kontrollera om det finns fler än 60 000 eller 65 000 filer med filnamnstillägget .tmp. Om det finns det tar du bort dessa filer.
- Kontrollera att datorns datum och tid matchar den lokala tidszonen.
- Se [till att dessa](install-mars-agent.md#verify-internet-access) platser läggs till på dina betrodda platser i Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifiera proxyinställningar för Windows

1. Ladda ned PsExec från [sysinternals-sidan.](/sysinternals/downloads/psexec)
1. Kör `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` från en upphöjd kommandotolk.

   Det här kommandot öppnar Internet Explorer.
1. Gå till **Verktyg**  >  **Internetalternativ**  >  **Anslutningar**  >  **LAN-inställningar**.
1. Kontrollera proxyinställningarna för systemkontot.
1. Om ingen proxy har konfigurerats och proxyinformation anges tar du bort informationen.
1. Om en proxy har konfigurerats och proxyinformationen är felaktig ser du till att **proxy-IP-** **och portinformationen** är korrekta.
1. Stäng Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Det går inte att ladda ned valvaudentialfilen

| Fel   | Rekommenderade åtgärder |
| ---     | ---    |
|Det gick inte att ladda ned valvaudentialfilen. (ID: 403) | <ul><li> Prova att ladda ned autentiseringsuppgifterna för valvet med hjälp av en annan webbläsare eller gör följande: <ul><li> Starta Internet Explorer. Välj F12. </li><li> Gå till fliken **Nätverk** och rensa cacheminnet och cookies. </li> <li> Uppdatera sidan.<br></li></ul> <li> Kontrollera om prenumerationen är inaktiverad/har upphört att gälla.<br></li> <li> Kontrollera om någon brandväggsregel blockerar nedladdningen. <br></li> <li> Se till att du inte har förbrukat gränsen för valvet (50 datorer per valv).<br></li>  <li> Se till att användaren har de Azure Backup behörigheter som krävs för att ladda ned valvautentiseringsuppgifter och registrera en server med valvet. Se [Använda rollbaserad åtkomstkontroll i Azure för att hantera Azure Backup återställningspunkter.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Fel  | Möjlig orsak | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br /><ul><li>Det Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrollera nätverksinställningarna och se till att du kan ansluta till Internet.<li>(407) Proxyautentisering krävs. |En proxy blockerar anslutningen. |  <ul><li>I Internet Explorer du till **Verktyg**  >  **Internetalternativ**  >  **Säkerhet**  >  **Internet**. Välj **Anpassad nivå** och rulla ned till avsnittet **Filhämtning.** Välj **Aktivera**.<p>Du kan också behöva lägga till [URL:er och IP-adresser](install-mars-agent.md#verify-internet-access) till dina betrodda platser i Internet Explorer.<li>Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<li> Om datorn har begränsad Internetåtkomst ska du kontrollera att brandväggsinställningarna på datorn eller proxyservern tillåter dessa [URL:er och IP-adresser.](install-mars-agent.md#verify-internet-access) <li>Om du har ett antivirusprogram installerat på servern ska du undanta dessa filer från antivirusgenomsökningen: <ul><li>CBEngine.exe (i stället för dpmra.exe).<li>CSC.exe (relaterat till .NET Framework). Det finns en CSC.exe för varje .NET Framework version som är installerad på servern. Undanta CSC.exe filer för alla versioner av .NET Framework på den berörda servern. <li>Den scratch-mapp eller cacheplats. <br>Standardplatsen för den scratch-mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Mappen bin på C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Den angivna valvaudentialfilen kan inte användas eftersom den inte laddas ned från valvet som är associerat med den här servern

| Fel  | Möjlig orsak | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| Den angivna valvaudentialfilen kan inte användas eftersom den inte laddas ned från valvet som är associerat med den här servern. (ID: 100110) Ange lämpliga autentiseringsuppgifter för valvet. | Valvaudentialfilen kommer från ett annat valv än det som servern redan är registrerad på. | Kontrollera att måldatorn och källdatorn är registrerade i samma Recovery Services-valv. Om målservern redan har registrerats i ett annat valv använder du **alternativet Registrera server** för att registrera till rätt valv.  

## <a name="backup-jobs-completed-with-warning"></a>Säkerhetskopieringsjobb slutfördes med varning

- När MARS-agenten itererar över filer och mappar under säkerhetskopieringen kan det uppstå olika villkor som kan göra att säkerhetskopieringen markeras som slutförd med varningar. Under dessa förhållanden visas ett jobb som slutfört med varningar. Det är ok, men det innebär att minst en fil inte kunde säkerhetskopieras. Jobbet överhoppade alltså filen, men säkerhetskopierade alla andra filer i fråga på datakällan.

  ![Säkerhetskopieringsjobbet slutfördes med varningar](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Villkor som kan göra att säkerhetskopior hoppar över filer är:
  - Filattribut som inte stöds (till exempel i en OneDrive-mapp, komprimerad dataström,parse-punkter). Den fullständiga listan finns i [supportmatrisen](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Ett filsystemproblem
  - En annan process stör (till exempel: antivirusprogram som innehåller referenser till filer kan hindra MARS-agenten från att komma åt filerna)
  - Filer som är låsta av ett program  

- Säkerhetskopieringstjänsten markerar dessa filer som misslyckade i loggfilen med följande namngivningskonvention: *LastBackupFailedFilesxxxx.txt* under mappen *C:\Program Files\Microsoft Azure Recovery Service Agent\temp.*
- Lös problemet genom att granska loggfilen för att förstå problemet:

  | Felkod             | Skäl                                             | Rekommendationer                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Filen eller katalogen är skadad och oläslig. | Kör **chkdsk** på källvolymen.                             |
  | 0x80070002, 0x80070003 | Systemet kan inte hitta den angivna filen.         | [Se till att den scratch-mappen inte är full](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Kontrollera om volymen där det scratch-utrymmet har konfigurerats finns (inte borttagna)  <br><br>   [Se till att MARS-agenten undantas från det antivirusprogram som är installerat på datorn](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Åtkomst nekas                                    | [Kontrollera om antivirusprogram eller annan programvara från tredje part blockerar åtkomst](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Åtkomst till molnfilen nekas.                | OneDrive-filer, Git-filer eller andra filer som kan vara i offline-tillstånd på datorn |

- Du kan använda [Lägg till undantagsregler](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) i en befintlig princip för att undanta filer som inte stöds, saknas eller har tagits bort från säkerhetskopieringsprincipen för att säkerställa att säkerhetskopiorna lyckas.

- Undvik att ta bort och återskapa skyddade mappar med samma namn i mappen på den översta nivån. Detta kan resultera i att säkerhetskopieringen slutförs med varningar med felet *A critical inconsistency wasected , därför* går det inte att replikera ändringar.  Om du behöver ta bort och återskapa mappar bör du överväga att göra det i undermappar under den skyddade mappen på den översta nivån.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering

| Fel | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br />Det gick inte att ange krypteringsnyckeln för säkra säkerhetskopieringar. Aktiveringen lyckades inte helt, men krypteringsfrasen sparades i följande fil. |<li>Servern har redan registrerats med ett annat valv.<li>Under konfigurationen skadades lösenfrasen.| Avregistrera servern från valvet och registrera den igen med en ny lösenfras.

## <a name="the-activation-did-not-complete-successfully"></a>Aktiveringen slutfördes inte

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|<br />Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår.     | <li> Den scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den tillfälligt mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.         | <li>Uppgradera till den [senaste versionen av](https://aka.ms/azurebackup_agent) MARS-agenten.<li>Flytta den scratch-mappen eller cacheplatsen till en volym med ledigt utrymme som är mellan 5 % och 10 % av den totala storleken på säkerhetskopierade data. Information om hur du flyttar cacheplatsen på rätt sätt finns i stegen i [Vanliga frågor om att lagra filer och mappar.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den scratch-mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Krypteringslösenfrasen är inte korrekt konfigurerad

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
| <br />Fel 34506. Krypterings lösenfrasen som lagras på den här datorn är inte korrekt konfigurerad.    | <li> Den scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den tillfälligt mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.        | <li>Uppgradera till den [senaste versionen av](https://aka.ms/azurebackup_agent) MARS-agenten.<li>Flytta den scratch-mappen eller cachelagringsplatsen till en volym med ledigt utrymme som är mellan 5 % och 10 % av den totala storleken på säkerhetskopierade data. Om du vill flytta cacheplatsen på rätt sätt kan du läsa stegen i [Vanliga frågor om att backa upp filer och mappar.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den scratch-mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Säkerhetskopieringar körs inte enligt schemat

Om schemalagda säkerhetskopieringar inte utlöses automatiskt men manuella säkerhetskopieringar fungerar korrekt kan du prova följande åtgärder:

- Se till att schemat för säkerhetskopiering av Windows Server inte står i konflikt med schemat för säkerhetskopiering av Azure-filer och -mappar.

- Kontrollera att onlinesäkerhetskopieringsstatus är inställd **på Aktivera.** Så här kontrollerar du statusen:

  1. I Schemaläggaren expanderar du **Microsoft** och väljer **Online Backup**.
  1. Dubbelklicka på **Microsoft-OnlineBackup** och gå till **fliken Utlösare.**
  1. Kontrollera om statusen är **aktiverad.** Om den inte är det väljer **du Redigera,** **Aktiverad** och sedan **OK.**

- Kontrollera att det användarkonto som valts för att köra uppgiften antingen är **gruppen SYSTEM** **eller Lokala** administratörer på servern. Kontrollera användarkontot genom att gå till **fliken Allmänt** och kontrollera **säkerhetsalternativen.**

- Kontrollera att PowerShell 3.0 eller senare är installerat på servern. Kontrollera PowerShell-versionen genom att köra det här kommandot och kontrollera att `Major` versionsnumret är 3 eller senare:

  `$PSVersionTable.PSVersion`

- Se till att den här sökvägen är en del av `PSMODULEPATH` miljövariabeln:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Om PowerShell-körningsprincipen för `LocalMachine` är inställd på kan den `restricted` PowerShell-cmdlet som utlöser säkerhetskopieringsaktiviteten misslyckas. Kör dessa kommandon i upphöjd läge för att kontrollera och ange körningsprincipen till antingen `Unrestricted` eller `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Se till att inga PowerShell-modul MSOnlineBackup-filer saknas eller är skadade. Om det finns några filer som saknas eller är skadade gör du följande:

  1. Kopiera MAPPEN MSOnlineBackup från C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules från en dator som har en MARS-agent som fungerar korrekt.
  1. På den problematiska datorn klistrar du in de kopierade filerna på samma mappplats (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Om det redan finns en MSOnlineBackup-mapp på datorn klistrar du in filerna i den eller ersätter eventuella befintliga filer.

> [!TIP]
> För att säkerställa att ändringarna tillämpas konsekvent startar du om servern när du har utfört föregående steg.

## <a name="resource-not-provisioned-in-service-stamp"></a>Resursen har inte etablerats i tjänststämpeln

Fel | Möjliga orsaker | Rekommenderade åtgärder
--- | --- | ---
Den aktuella åtgärden misslyckades på grund av det interna tjänstfelet "Resursen har inte etablerats i tjänststämpeln". Försök igen efter en stund. (ID: 230006) | Den skyddade servern har bytt namn. | <li> Byt namn på servern tillbaka till det ursprungliga namnet som registrerats med valvet. <br> <li> Registrera servern på nytt till valvet med det nya namnet.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Det gick inte att starta jobbet eftersom ett annat jobb pågår

Om du ser ett varningsmeddelande i **MARS-konsolen** Jobbhistorik med meddelandet "Jobbet kunde inte startas eftersom ett annat jobb pågår" kan det vara på grund av en dubblettinstans av jobbet som utlöstes  >  av Schemaläggaren.

![Det gick inte att starta jobbet eftersom ett annat jobb pågår](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Lös problemet så här:

1. Starta Schemaläggaren snapin-modulen genom *att skriva taskschd.msc* i fönstret Kör
1. I den vänstra rutan går du till **Schemaläggaren Microsoft**  ->    ->  **OnlineBackup**.
1. Dubbelklicka på uppgiften för varje aktivitet i det här biblioteket för att öppna egenskaperna och utföra följande steg:
    1. Växla till **fliken** Inställningar.

         ![Fliken Inställningar](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Ändra alternativet för **Om aktiviteten redan körs gäller följande regel**. Välj **Starta inte en ny instans.**

         ![Ändra regeln så att den inte startar någon ny instans](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Felsöka återställningsproblem

Azure Backup kanske inte montera återställningsvolymen, även efter flera minuter. Och du kan få felmeddelanden under processen. Börja återställa normalt genom att göra följande:

1. Avbryt monteringsprocessen om den har körts i flera minuter.

2. Kontrollera om du har den senaste versionen av Backup-agenten. Om du vill kontrollera versionen går du **till rutan Åtgärder** i MARS-konsolen och väljer Om Microsoft Azure Recovery **Services-agenten**. Bekräfta att **Versionsnumret** är lika med eller högre än den version som anges i [den här artikeln.](https://go.microsoft.com/fwlink/?linkid=229525) Välj den här länken för [att ladda ned den senaste versionen](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Gå till **Enhetshanteraren**  >  **Storage-styrenheter** och leta upp **Microsoft iSCSI-initieraren**. Om du hittar den går du direkt till steg 7.

4. Om du inte kan hitta Microsoft iSCSI-initierartjänsten kan du försöka hitta en post under **Enhetshanteraren** Storage-styrenheter med namnet Okänd enhet med maskinvaru-ID  >    **ROOT\ISCSIPRT.**

5. Högerklicka på Okänd **enhet och** välj **Uppdatera drivrutinsprogram.**

6. Uppdatera drivrutinen genom att välja alternativet sök **automatiskt efter uppdaterad drivrutinsprogramvara.** Den här uppdateringen bör **ändra Okänd enhet** till Microsoft **iSCSI-initierare:**

    ![Skärmbild av Azure Backup Enhetshanteraren, med Lagringsstyrenheter markerade](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Gå till **Aktivitetshanteraren**  >  **Services (lokal)**  >  **Microsoft iSCSI-initierartjänst:**

    ![Skärmbild av Azure Backup Aktivitetshanteraren, med Tjänster (lokalt) markerat](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Starta om Microsoft iSCSI-initierartjänsten. Det gör du genom att högerklicka på tjänsten och välja **Stoppa**. Högerklicka sedan på den igen och välj **Starta**.

9. Försök att återställa igen med hjälp [av Omedelbar återställning.](backup-instant-restore-capability.md)

Om återställningen fortfarande misslyckas startar du om servern eller klienten. Om du inte vill starta om, eller om återställningen fortfarande misslyckas även efter att du har startat om servern, kan du prova [att återställa från en annan dator.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Felsöka cacheproblem

Säkerhetskopieringen kan misslyckas om cachelagringsmappen (kallas även för en tillfälligt mapp) är felaktigt konfigurerad, saknar förutsättningar eller har begränsad åtkomst.

### <a name="prerequisites"></a>Förutsättningar

För att MARS-agentåtgärder ska lyckas måste cachemappen uppfylla följande krav:

- [Se till att det finns 5 % till 10 % ledigt utrymme på den scratch-mappen](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Kontrollera att platsen för den scratch-mappen är giltig och tillgänglig](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Kontrollera att filattribut i cachemappen stöds](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Se till att det allokerade skuggkopielagringsutrymmet är tillräckligt för säkerhetskopieringsprocessen](#increase-shadow-copy-storage)
- [Se till att det inte finns några andra processer (t.ex. antivirusprogram) som begränsar åtkomsten till cachemappen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Öka lagringen av skuggkopior

Säkerhetskopieringsåtgärder kan misslyckas om det inte finns tillräckligt med lagringsutrymme för skuggkopior som krävs för att skydda datakällan. Lös problemet genom att öka lagringsutrymmet för skuggkopior på den skyddade volymen med **hjälp av vssadmin** enligt nedan:

- Kontrollera det aktuella skugglagringsutrymmet från den upphöjda kommandotolken:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Öka skugglagringsutrymmet med följande kommando:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>En annan process eller ett antivirusprogram som blockerar åtkomsten till cachemappen

Om du har ett antivirusprogram installerat på servern lägger du till nödvändiga undantagsregler i antivirusgenomsökningen för dessa filer och mappar:  

- Den scratch-mappen. Standardplatsen är `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Bin-mappen på `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Vanliga problem

Det här avsnittet beskriver vanliga fel som uppstår när du använder MARS-agenten.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Felmeddelande | Rekommenderad åtgärd
--|--
Microsoft Azure Recovery Services-agenten kunde inte komma åt kontrollsumman för säkerhetskopian på den tillfälliga platsen | Lös problemet genom att utföra följande steg och starta om servern <br/> - [Kontrollera om det finns ett antivirusprogram eller andra processer som låser de tillfälligt platsfilerna](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Kontrollera om den scratch-platsen är giltig och tillgänglig för MARS-agenten.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Felmeddelande | Rekommenderad åtgärd
--|--
Microsoft Azure Recovery Services-agenten kunde inte komma åt den tillfälliga platsen för att initiera VHD | Lös problemet genom att utföra följande steg och starta om servern <br/> - [Kontrollera om antivirus eller andra processer låser de scratch-platsfilerna](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Kontrollera om den scratch-platsen är giltig och tillgänglig för MARS-agenten.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Felmeddelande | Rekommenderad åtgärd
--|--
Säkerhetskopieringen misslyckades på grund av otillräcklig lagring i volymen där den scratch-mappen finns | Lös problemet genom att kontrollera följande steg och försök igen:<br/>- [Kontrollera att MARS-agenten är den senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verifiera och lösa lagringsproblem som påverkar det scratch-utrymmet för säkerhetskopiering](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Felmeddelande | Rekommenderad åtgärd
--|--
Det går inte att hitta ändringar i en fil. Detta kan bero på olika orsaker. Försök att utföra åtgärden igen. | Lös problemet genom att kontrollera följande steg och försök igen:<br/> - [Kontrollera att MARS-agenten är den senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verifiera och lösa lagringsproblem som påverkar det scratch-utrymmet för säkerhetskopiering](#prerequisites)

## <a name="next-steps"></a>Nästa steg

- Få mer information om [hur du serverar Windows Server med Azure Backup agenten](tutorial-backup-windows-server-to-azure.md).
- Om du behöver återställa en säkerhetskopia kan du se Återställa [filer till en Windows-dator.](backup-azure-restore-windows-server.md)
