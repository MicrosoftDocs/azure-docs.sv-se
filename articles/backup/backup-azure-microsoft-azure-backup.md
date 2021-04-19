---
title: Använda Azure Backup Server för att arbetsbelastningarna
description: I den här artikeln får du lära dig hur du förbereder din miljö för att skydda och backa upp arbetsbelastningar med Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 144a5e26f5ad10d120a49f6a0385c3a310448dbc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713686"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installera och uppgradera Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Gäller för: MABS v3. (MABS v2 stöds inte längre. Om du använder en tidigare version än MABS v3 uppgraderar du till den senaste versionen.)

I den här artikeln förklaras hur du förbereder din miljö för att arbetsbelastningarna Microsoft Azure Backup Server (MABS). Med Azure Backup Server kan du skydda programarbetsbelastningar som virtuella Hyper-V-datorer, Microsoft SQL Server-, SharePoint Server-, Microsoft Exchange- och Windows-klienter från en enda konsol.

> [!NOTE]
> Azure Backup Server kan nu skydda virtuella VMware-datorer och ger förbättrade säkerhetsfunktioner. Installera produkten på det sätt som beskrivs i avsnitten nedan och den senaste Azure Backup Agent. Mer information om hur du serverar VMware-servrar med Azure Backup Server finns i artikeln Använda Azure Backup Server för att [servera en VMware-server.](backup-azure-backup-server-vmware.md) Mer information om säkerhetsfunktioner finns i dokumentationen [Azure Backup om säkerhetsfunktioner.](backup-azure-security-feature.md)
>
>

MABS som distribueras på en virtuell Azure-dator kan säkerhetskopiera virtuella datorer i Azure, men de bör finnas i samma domän för att aktivera säkerhetskopiering. Processen för att jäsa en virtuell Azure-dator förblir densamma som när du backar upp virtuella datorer lokalt, men distributionen av MABS i Azure har vissa begränsningar. Mer information om begränsningar finns i [DPM som en virtuell Azure-dator.](/system-center/dpm/install-dpm#setup-prerequisites)

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/management/deployment-models.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribuerats med hjälp av Resource Manager modellen.
>
>

Azure Backup Server ärver en stor del av funktionerna för säkerhetskopiering av arbetsbelastningar från Data Protection Manager (DPM). Den här artikeln länkar till DPM-dokumentationen för att förklara några av de delade funktionerna. Även Azure Backup Server delar mycket av samma funktioner som DPM, Azure Backup Server säkerhetskopierar inte till band och integrerar inte heller med System Center.

## <a name="choose-an-installation-platform"></a>Välj en installationsplattform

Det första steget mot att Azure Backup Server komma igång är att konfigurera en Windows Server. Servern kan finnas i Azure eller lokalt.

* För att skydda lokala arbetsbelastningar måste MABS-servern finnas lokalt och vara ansluten till en domän.
* För att skydda arbetsbelastningar som körs på virtuella Azure-datorer måste MABS-servern finnas i Azure, köras som en virtuell Azure-dator och vara ansluten till en domän.

### <a name="using-a-server-in-azure"></a>Använda en server i Azure

När du väljer en server för Azure Backup Server bör du börja med en galleriavbildning av Windows Server 2016 Datacenter eller Windows Server 2019 Datacenter. Artikeln Skapa din första virtuella [Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)innehåller en självstudiekurs för att komma igång med den rekommenderade virtuella datorn i Azure, även om du aldrig har använt Azure tidigare. De rekommenderade minimikraven för den virtuella serverdatorn (VM) bör vara: Standard_A4_v2 med fyra kärnor och 8 GB RAM-minne.

Att skydda arbetsbelastningar Azure Backup Server har många nyanser. [Skyddsmatrisen för MABS](./backup-mabs-protection-matrix.md) hjälper till att förklara dessa nyanser. Läs den här artikeln helt innan du distribuerar datorn.

### <a name="using-an-on-premises-server"></a>Använda en lokal server

Om du inte vill köra basservern i Azure kan du köra servern på en virtuell Hyper-V-dator, en virtuell VMware-dator eller en fysisk värd. De rekommenderade minimikraven för servermaskinvaran är två kärnor och 8 GB RAM-minne. De operativsystem som stöds visas i följande tabell:

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitars |Standard, Datacenter, Essentials |
| Windows Server 2016 och senaste SPs |64-bitars |Standard, Datacenter, Essentials  |

Du kan deduplicera DPM-lagringen med Hjälp av Windows Server-deduplicering. Läs mer om hur [DPM och deduplicering](/system-center/dpm/deduplicate-dpm-storage) fungerar tillsammans när de distribueras i virtuella Hyper-V-datorer.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad server för ett enda syfte. Du kan inte installera Azure Backup Server på:
>
> * En dator som körs som en domänkontrollant
> * En dator där programserverrollen är installerad
> * En dator som är en System Center Operations Manager-hanteringsserver
> * En dator som kör Exchange Server
> * En dator som är en nod i ett kluster
>
> Installation av Azure Backup Server stöds inte på Windows Server Core eller Microsoft Hyper-V Server.

Anslut alltid Azure Backup Server till en domän. Det går inte Azure Backup Server att flytta en befintlig Azure Backup Server till en ny domän *efter distributionen.*

Oavsett om du skickar säkerhetskopierade data till Azure eller behåller dem lokalt Azure Backup Server vara registrerade med ett Recovery Services-valv.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Ange lagringsreplikering

Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv geo-redundant lagring. Om det här valvet är ditt primära valv låter du lagringsalternativet vara inställt på geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om [alternativen geo-redundant,](../storage/common/storage-redundancy.md#geo-redundant-storage)lokalt [redundant](../storage/common/storage-redundancy.md#locally-redundant-storage)och [zonredundant](../storage/common/storage-redundancy.md#zone-redundant-storage) i [översikten Azure Storage replikering.](../storage/common/storage-redundancy.md)

Så här redigerar du inställningen för lagringsreplikering:

1. I fönstret **Recovery Services-valv** väljer du det nya valvet. Under avsnittet **Inställningar** väljer du **Egenskaper.**
2. Under **Konfiguration av** **säkerhetskopiering i Egenskaper** väljer du **Uppdatera**.

3. Välj typ av lagringsreplikering och välj **Spara.**

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Programvarupaket

### <a name="downloading-the-software-package"></a>Ladda ned programvarupaketet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Om du redan har ett Öppet Recovery Services-valv fortsätter du till steg 3. Om du inte har något Recovery Services-valv öppet, men finns i Azure Portal väljer du Bläddra på **huvudmenyn.**

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** väljer du det.

     ![Skapa Recovery Services-valv steg 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Instrumentpanel för valv](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Fönstret **Inställningar** öppnas som standard. Om den är stängd väljer du **Inställningar** för att öppna inställningsfönstret.

    ![Fönstret Inställningar](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Välj **Säkerhetskopiera** för att Komma igång guiden.

    ![Kom igång med säkerhetskopiering](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    I **fönstret Komma igång säkerhetskopiering** som öppnas väljs **Säkerhetskopieringsmål** automatiskt.

    ![Säkerhetskopieringsmål – öppnad som standard](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. I fönstret **Säkerhetskopieringsmål** går du till **menyn Where is your workload running** (Var körs din arbetsbelastning) **och väljer On-premises (Lokalt).**

    ![lokalt och arbetsbelastningar som mål](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    I **listrutan Vad vill** du backa upp? väljer du de arbetsbelastningar som du vill skydda med Azure Backup Server och väljer sedan **OK.**

    Guiden **Komma igång säkerhetskopiering** växlar alternativet **Förbered infrastruktur för** att säkerhetskopiera arbetsbelastningar till Azure.

   > [!NOTE]
   > Om du bara vill backa upp filer och mappar rekommenderar vi att du använder Azure Backup-agenten och följer riktlinjerna i artikeln Först ska du [titta:](./backup-windows-with-mars-agent.md)back up files and folders . Om du ska skydda mer än filer och mappar, eller om du planerar att utöka skyddsbehoven i framtiden, väljer du dessa arbetsbelastningar.
   >
   >

    ![Komma igång ändring i guiden](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. I fönstret **Förbered infrastruktur** som öppnas väljer du nedladdningslänkarna **för Installera** Azure Backup Server och Ladda ned valvautentiseringsuppgifter. Du använder autentiseringsuppgifterna för valvet under registreringen Azure Backup Server till Recovery Services-valvet. Länkarna tar dig till Download Center där programvarupaketet kan laddas ned.

    ![Förbereda infrastrukturen för Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Markera alla filer och välj **Nästa.** Ladda ned alla filer som kommer från Microsoft Azure Backup nedladdningssidan och placera alla filer i samma mapp.

    ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Eftersom nedladdningsstorleken för alla filer tillsammans är > 3 GB kan det ta upp till 60 minuter innan nedladdningen är klar på en nedladdningslänk på 10 Mbit/s.

### <a name="extracting-the-software-package"></a>Extrahera programvarupaketet

När du har laddat ned alla filer väljer du **MicrosoftAzureBackupInstaller.exe**. När du gör det **Microsoft Azure Backup Installationsguiden du** extraherar installationsfilerna till en plats som du har angett. Fortsätt genom guiden och välj knappen **Extrahera för** att påbörja extraheringsprocessen.

> [!WARNING]
> Minst 4 GB ledigt utrymme krävs för att extrahera installationsfilerna.
>
>

![Installation av extrahering av filer för installation](./media/backup-azure-microsoft-azure-backup/extract/03.png)

När extraheringen är klar markerar du kryssrutan för att starta den *nyligen extraheradesetup.exe* börja installera Microsoft Azure Backup Server och väljer **knappen** Slutför.

### <a name="installing-the-software-package"></a>Installera programvarupaketet

1. Välj **Microsoft Azure Backup för** att starta installationsguiden.

    ![Microsoft Azure Backup Installationsguiden](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. På välkomstskärmen väljer du **knappen** Nästa. Då kommer du till *avsnittet Kravkontroller.* På den här skärmen väljer **du** Kontrollera för att avgöra om maskin- och programvarukraven för Azure Backup Server har uppfyllts. Om alla krav är uppfyllda visas ett meddelande om att datorn uppfyller kraven. Välj knappen **Nästa**.

    ![Azure Backup Server – Välkomst- och kravkontroll](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Installationspaketet Azure Backup Server paketeras med lämpliga SQL Server binärfiler som behövs. När du startar en Azure Backup Server installation väljer du alternativet Installera ny **instans av SQL Server** med den här installationen och väljer knappen Kontrollera **och** installera. När förutsättningarna har installerats väljer du **Nästa.**

    >[!NOTE]
    >Om du vill använda en egen SQL-server är de SQL Server versionerna SQL Server 2014 SP1 eller senare, 2016 och 2017.  Alla SQL Server versioner ska vara Standard eller Enterprise 64-bitars.
    >Azure Backup Server fungerar inte med en fjärrinstans SQL Server instans. Instansen som används av Azure Backup Server måste vara lokal. Om du använder en befintlig SQL-server för MABS stöder MABS-installationen endast användning av *namngivna instanser* av SQL Server.

    ![Azure Backup Server – SQL-kontroll](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Om ett fel inträffar med en rekommendation om att starta om datorn gör du det och väljer **Kontrollera igen.** Om det finns problem med SQL-konfiguration konfigurerar du om SQL enligt riktlinjerna för SQL och försöker installera/uppgradera MABS med hjälp av den befintliga SQL-instansen.

   **Manuell konfiguration**

   När du använder din egen instans av SQL ska du se till att lägga till builtin\Administrators till sysadmin-rollen i huvud-DB.

    **SSRS-konfiguration med SQL 2017**

    När du använder en egen instans av SQL 2017 måste du konfigurera SSRS manuellt. Efter SSRS-konfigurationen ser du till *att SSRS-egenskapen IsInitialized* har angetts till *True*. När detta är inställt på True förutsätter MABS att SSRS redan har konfigurerats och hoppar över SSRS-konfigurationen.

    Använd följande värden för SSRS-konfiguration:
    * Tjänstkonto: "Använd inbyggt konto" ska vara Nätverkstjänst
    * Webbtjänst-URL: "Virtual Directory" ska ReportServer_\<SQLInstanceName>
    * Databas: DatabaseName ska vara ReportServer$\<SQLInstanceName>
    * Webbadress till webbportalen: "Virtual Directory" bör Reports_\<SQLInstanceName>

    [Läs mer om](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) SSRS-konfiguration.

    > [!NOTE]
    > Licensiering för SQL Server som används som databas för MABS styrs av [Microsoft Villkor för Onlinetjänster](https://www.microsoft.com/licensing/product-licensing/products) (OST). Enligt OST kan SQL Server som paketeras med MABS endast användas som databas för MABS.

4. Ange en plats för installationen av Microsoft Azure Backup serverfiler och välj **Nästa.**

    ![Ange plats för installation av filer](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Den scratch-platsen är ett krav för att backa upp till Azure. Se till att den scratch-platsen är minst 5 % av de data som planeras att säkerhetskopieras till molnet. För diskskydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [Förbereda datalagring.](/system-center/dpm/plan-long-and-short-term-data-storage)

    Kapacitetskraven för disklagring beror främst på storleken på skyddade data, storleken på den dagliga återställningspunkten, förväntad volymtillväxt för data och mål för kvarhållningsintervall. Vi rekommenderar att du gör disklagringen dubbelt så stor som skyddade data. Detta förutsätter en storlek på den dagliga återställningspunkten som är 10 % av storleken på skyddade data och ett kvarhållningsintervall på 10 dagar. Om du vill få en bra uppskattning av storleken kan du [granska DPM-Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301). 

5. Ange ett starkt lösenord för begränsade lokala användarkonton och välj **Nästa.**

    ![Ange starkt lösenord](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Välj om du vill använda *Microsoft Update* för att söka efter uppdateringar och välj **Nästa.**

   > [!NOTE]
   > Vi rekommenderar att Windows Update omdirigeras till Microsoft Update, som erbjuder säkerhetsuppdateringar och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Granska *sammanfattningen av inställningar* och välj **Installera**.

    ![Sammanfattning av inställningar](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Installationen sker i faser. I den första fasen installeras Microsoft Azure Recovery Services-agenten på servern. Guiden söker även efter Internetanslutning. Om Internetanslutningen är tillgänglig kan du fortsätta med installationen. Om inte måste du ange proxyinformation för att ansluta till Internet.

    Nästa steg är att konfigurera Microsoft Azure Recovery Services-agenten. Som en del av konfigurationen måste du ange dina valvautentiseringsuppgifter för att registrera datorn till Recovery Services-valvet. Du tillhandahåller också en lösenfras för att kryptera/dekryptera data som skickas mellan Azure och dina lokala platser. Du kan generera en lösenfras automatiskt eller ange en egen lösenfras på minst 16 tecken. Fortsätt med guiden tills agenten har konfigurerats.

    ![Guiden Registrera server](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. När registreringen av Microsoft Azure Backup-servern har slutförts fortsätter den övergripande installationsguiden till installationen och konfigurationen av SQL Server och Azure Backup Server-komponenterna. När SQL Server är klar installeras Azure Backup Server komponenter.

    ![Azure Backup Server konfigurationsförloppet](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

När installationssteget har slutförts har även produktens skrivbordsikoner skapats. Dubbelklicka på ikonen för att starta produkten.

### <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior

Den första säkerhetskopian sparas på lagringen som är ansluten Azure Backup Server datorn. Mer information om hur du lägger till diskar finns i [Konfigurera lagringspooler och disklagring.](./backup-mabs-add-storage.md)

> [!NOTE]
> Du måste lägga till lagringsutrymme för säkerhetskopior även om du planerar att skicka data till Azure. I den aktuella arkitekturen Azure Backup Server innehåller Azure Backup den  andra kopian av data medan den lokala lagringen innehåller den första (och obligatoriska) säkerhetskopieringskopian.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installera och uppdatera Data Protection Manager-skyddsagenten

MABS använder System Center Data Protection Manager-skyddsagenten. [Här är stegen för](/system-center/dpm/deploy-dpm-protection-agent) att installera skyddsagenten på dina skyddsservrar.

I följande avsnitt beskrivs hur du uppdaterar skyddsagenter för klientdatorer.

1. I säkerhetskopieringsservern väljer Administratörskonsol **hanteringsagenter.**  >  

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten för.

   > [!NOTE]
   > Kolumnen **Agentuppdateringar** visar när en uppdatering av skyddsagenten är tillgänglig för varje skyddad dator. I fönstret **Åtgärder** är åtgärden **Uppdatera** endast tillgänglig när en skyddad dator har valts och uppdateringar är tillgängliga.
   >
   >

3. Om du vill installera uppdaterade skyddsagenter på de valda datorerna går **du till rutan** Åtgärder och väljer **Uppdatera**.

4. För en klientdator som inte är ansluten till nätverket visas statusen Uppdatering väntar i kolumnen **Agentstatus** förrän datorn är ansluten **till nätverket.**

   När en klientdator är ansluten till nätverket visar **kolumnen Agentuppdateringar** för klientdatorn **statusen Uppdatera**.

## <a name="move-mabs-to-a-new-server"></a>Flytta MABS till en ny server

Här är stegen om du behöver flytta MABS till en ny server, samtidigt som lagringen behålls. Detta kan bara göras om alla data finns på Modern Backup Storage.

  > [!IMPORTANT]
  >
  > * Det nya servernamnet måste ha samma namn som den ursprungliga Azure Backup Server instansen. Du kan inte ändra namnet på den nya Azure Backup Server-instansen om du vill använda den tidigare lagringspoolen och MABS Database (DPMDB) för att behålla återställningspunkter.
  > * Du måste ha en säkerhetskopia av MABS-databasen (DPMDB). Du behöver den för att återställa databasen.

1. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten för.
2. Stäng av den ursprungliga Azure Backup servern eller ta den offline.
3. Återställ datorkontot i Active Directory.
4. Installera Server 2016 på en ny dator och ge den samma datornamn som den ursprungliga Azure Backup servern.
5. Anslut till domänen.
6. Installera Azure Backup Server V3 eller senare (flytta MABS-lagringspooldiskar från en gammal server och importera).
7. Återställ den DPMDB som togs i steg 1.
8. Koppla lagringen från den ursprungliga säkerhetskopieringsservern till den nya servern.
9. Återställ DPMDB från SQL.
10. Kör CMD (som administratör) på den nya servern. Gå till Microsoft Azure Backup installationsplats och lagerplatsmapp.

    Sökvägsexempel: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Kör för att Azure Backup till `DPMSYNC -SYNC` .

    Om du har lagt till **nya diskar** i DPM-lagringspoolen i stället för att flytta de gamla kör du `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup Server kräver anslutning till Azure Backup tjänsten för att produkten ska fungera korrekt. Om du vill kontrollera om datorn har anslutningen till Azure använder du ```Get-DPMCloudConnection``` cmdleten i Azure Backup Server PowerShell-konsolen. Om cmdletens utdata är TRUE finns det en anslutning, annars finns det ingen anslutning.

Samtidigt måste Azure-prenumerationen vara i felfritt tillstånd. Logga in på prenumerationsportalen för att ta reda på statusen för din prenumeration och [hantera den.](https://account.windowsazure.com/Subscriptions)

När du vet tillståndet för Azure-anslutningen och Azure-prenumerationen kan du använda tabellen nedan för att ta reda på hur funktionerna för säkerhetskopiering/återställning påverkar.

| Anslutningstillstånd | Azure Subscription (Azure-prenumeration) | Säkerhetskopiera till Azure | Back up to disk ( Back up to disk) | Återställa från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Aktiv |Tillåts |Tillåts |Tillåts |Tillåts |
| Ansluten |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Ansluten |Avetablerad |Stoppad |Stoppad |Stoppad och Azure-återställningspunkter har tagits bort |Stoppad |
| Förlorad anslutning > 15 dagar |Aktiv |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Avetablerad |Stoppad |Stoppad |Stoppad och Azure-återställningspunkter har tagits bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställa från förlust av anslutning

Om datorn har begränsad Internetåtkomst ska du kontrollera att brandväggsinställningarna på datorn eller proxyservern tillåter följande URL:er och IP-adresser:

* Webbadresser
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

Om du använder ExpressRoute Microsoft-peering väljer du följande tjänster/regioner:

* Azure Active Directory (12076:5060)
* Microsoft Azure region (enligt platsen för Recovery Services-valvet)
* Azure Storage (enligt platsen för Recovery Services-valvet)

Mer information finns i [ExpressRoute-routningskrav.](../expressroute/expressroute-routing.md)

När anslutningen till Azure har återställts Azure Backup Server datorn bestäms de åtgärder som kan utföras av Azure-prenumerationens tillstånd. Tabellen ovan innehåller information om de åtgärder som tillåts när datorn är "Ansluten".

### <a name="handling-subscription-states"></a>Hantera prenumerations tillstånd

Det går att ta en Azure-prenumeration från tillståndet *Förfallen* eller *Avetablerat* till *aktivt* tillstånd. Detta påverkar dock produktbeteendet när tillståndet inte är *aktivt:*

* En *avetableerad* prenumeration förlorar funktioner för den period då den avetablerar. När du *slår* på Aktiv återställs produktfunktionerna i säkerhetskopiering/återställning. Säkerhetskopierade data på den lokala disken kan också hämtas om de har förvarats med en tillräckligt lång kvarhållningsperiod. Säkerhetskopierade data i Azure går dock oåterkalleligen förlorade när prenumerationen har *avetableats.*
* En *prenumeration som* har upphört att gälla förlorar endast funktioner tills den har gjorts *aktiv* igen. Säkerhetskopieringar som schemalagts för perioden då prenumerationen *har upphört att gälla* körs inte.

## <a name="upgrade-mabs"></a>Uppgradera MABS

Använd följande procedurer för att uppgradera MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uppgradera från MABS V2 till V3

> [!NOTE]
>
> MABS V2 är inte ett krav för att installera MABS V3. Du kan dock bara uppgradera till MABS V3 från MABS V2.

Använd följande steg för att uppgradera MABS:

1. Om du vill uppgradera från MABS V2 till MABS V3 uppgraderar du ditt operativsystem till Windows Server 2016 eller Windows Server 2019 om det behövs.

2. Uppgradera servern. Stegen liknar [installation](#install-and-upgrade-azure-backup-server). För SQL-inställningar får du dock ett alternativ för att uppgradera din SQL-instans till SQL 2017 eller använda din egen instans av SQL Server 2017.

   > [!NOTE]
   >
   > Avsluta inte medan din SQL-instans uppgraderas. Om du avslutar avinstalleras SQL Reporting-instansen, så ett försök att uppgradera MABS misslyckas.

   > [!IMPORTANT]
   >
   >  Som en del av SQL 2017-uppgraderingen säkerhetskopierar vi SQL-krypteringsnycklarna och avinstallerar Reporting Services. Efter uppgraderingen av SQL Server installeras Reporting Service(14.0.6827.4788) & krypteringsnycklarna återställs.
   >
   > När du konfigurerar SQL 2017 manuellt kan du läsa avsnittet *SSRS-konfiguration med SQL 2017* under Installationsanvisningar.

3. Uppdatera skyddsagenterna på de skyddade servrarna.
4. Säkerhetskopieringar bör fortsätta utan att du behöver starta om dina produktionsservrar.
5. Du kan börja skydda dina data nu. Om du uppgraderar till Modern Backup Storage, samtidigt som du skyddar, kan du även välja de volymer som du vill lagra säkerhetskopiorna i och söka efter under etablerat utrymme. [Läs mer](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning) kan du läsa mer i det här dokumentet [med](https://support.microsoft.com/kb/3041338)  felkoder.

Du kan också Azure Backup [vanliga frågor och svar.](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Nästa steg

Du kan få detaljerad information här om hur [du förbereder din miljö för DPM.](/system-center/dpm/prepare-environment-for-dpm) Den innehåller också information om konfigurationer som stöds Azure Backup Server kan distribueras och användas. Du kan använda en serie [PowerShell-cmdlets för](/powershell/module/dataprotectionmanager/) att utföra olika åtgärder.

Du kan använda de här artiklarna för att få en djupare förståelse för arbetsbelastningsskydd med hjälp Microsoft Azure Backup server.

* [SQL Server säkerhetskopiering](backup-azure-backup-sql.md)
* [Säkerhetskopiering av SharePoint-server](backup-azure-backup-sharepoint.md)
* [Alternativ serversäkerhetskopiering](backup-azure-alternate-dpm-server.md)
