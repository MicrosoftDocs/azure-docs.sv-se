---
title: Installera Azure Backup Server på Azure Stack
description: I den här artikeln får du lära dig hur du använder Azure Backup Server för att skydda eller backa upp arbetsbelastningar i Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: d7c685a16db50e51a54387dde49ffb137fcfd626
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519484"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installera Azure Backup Server på Azure Stack

Den här artikeln förklarar hur du installerar Azure Backup Server på Azure Stack. Med Azure Backup Server kan du skydda IaaS-arbetsbelastningar (Infrastruktur som en tjänst), till exempel virtuella datorer som körs i Azure Stack. En fördel med att Azure Backup Server skydda dina arbetsbelastningar är att du kan hantera allt arbetsbelastningsskydd från en enda konsol.

> [!NOTE]
> Mer information om säkerhetsfunktioner finns i dokumentationen [Azure Backup säkerhetsfunktioner.](backup-azure-security-feature.md)
>

## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server

Azure Backup Server skyddar följande arbetsbelastningar Azure Stack virtuella datorer.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Windows Server Halvårskanal – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2016 | Servergrupp, databas, serverdel, webbserver |
| SharePoint 2013 | Servergrupp, databas, serverdel, webbserver |
| SharePoint 2010 | Servergrupp, databas, serverdel, webbserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Server miljö

Överväg rekommendationerna i det här avsnittet när du Azure Backup Server i din Azure Stack miljö. Installationsprogrammet Azure Backup Server kontrollerar att din miljö har de nödvändiga förutsättningarna, men du sparar tid genom att förbereda dig innan du installerar.

### <a name="determining-size-of-virtual-machine"></a>Fastställa storleken på den virtuella datorn

Om du Azure Backup Server på en Azure Stack virtuell dator använder du storlek A2 eller större. Om du vill ha hjälp med att välja storlek på en virtuell dator laddar du [ned Azure Stack storlekskalkylatorn för virtuella datorer.](https://www.microsoft.com/download/details.aspx?id=56832)

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på Azure Stack virtuella datorer

Alla virtuella datorer som används i en Azure Stack arbetsbelastning måste tillhöra samma virtuella Azure-nätverk och Azure-prenumeration.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server vm-prestanda

Om de delas med andra virtuella datorer påverkar lagringskontots storlek och IOPS Azure Backup Server vm-prestanda. Därför bör du använda ett separat lagringskonto för den virtuella Azure Backup Server datorn. Agenten Azure Backup som körs på Azure Backup Server behöver tillfällig lagring för:

- dess egen användning (en cacheplats),
- data som återställts från molnet (lokalt mellanlagringsområde)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup tillfällig disklagring

Varje Azure Stack virtuell dator levereras med tillfällig disklagring, som är tillgänglig för användaren som `D:\` volym. Det lokala mellanlagringsområdet som krävs Azure Backup kan konfigureras att finnas i `D:\` och cacheplatsen kan placeras på `C:\` . På så sätt behöver ingen lagring tas bort från de datadiskar som är anslutna till den Azure Backup Server virtuella datorn.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhetskopierade data på en lokal disk och i Azure

Azure Backup Server säkerhetskopierade data på Azure-diskar som är anslutna till den virtuella datorn för driftåterställning. När diskarna och lagringsutrymmet är anslutna till den virtuella datorn Azure Backup Server du lagringen åt dig. Mängden lagringsutrymme för säkerhetskopierade data beror på antalet och storleken på diskar som är anslutna till varje [Azure Stack virtuella datorn](/azure-stack/user/azure-stack-storage-overview). Varje storlek Azure Stack virtuell dator har ett maximalt antal diskar som kan kopplas till den virtuella datorn. Till exempel är A2 fyra diskar. A3 är åtta diskar. A4 är 16 diskar. Återigen avgör storleken och antalet diskar den totala lagringspoolen för säkerhetskopior.

> [!IMPORTANT]
> Du bör **inte** behålla data för driftåterställning (säkerhetskopiering) Azure Backup Server anslutna diskarna i mer än fem dagar.
>

Genom att lagra säkerhetskopierade data i Azure minskar säkerhetskopieringsinfrastrukturen på Azure Stack. Om data är äldre än fem dagar bör de lagras i Azure.

Om du vill lagra säkerhetskopierade data i Azure skapar eller använder du ett Recovery Services-valv. När du förbereder för att Azure Backup Server arbetsbelastningen [konfigurerar du Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Varje gång ett säkerhetskopieringsjobb körs skapas en återställningspunkt i valvet när den har konfigurerats. Varje Recovery Services-valv innehåller upp till 9 999 återställningspunkter. Beroende på antalet återställningspunkter som skapats och hur länge de bevaras kan du behålla säkerhetskopierade data i många år. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.

### <a name="scaling-deployment"></a>Skalningsdistribution

Om du vill skala distributionen har du följande alternativ:

- Skala upp – Öka storleken på den virtuella Azure Backup Server från A-serien till D-serien och öka den lokala lagringen enligt de virtuella [Azure Stack-instruktionerna.](/azure-stack/user/azure-stack-manage-vm-disks)
- Avlasta data – skicka äldre data till Azure och behåll endast de senaste data i lagringen som är kopplade till Azure Backup Server.
- Skala ut – Lägg till fler Azure Backup-servrar för att skydda arbetsbelastningarna.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 eller senare måste installeras på den virtuella datorn.

### <a name="joining-a-domain"></a>Ansluta till en domän

Den Azure Backup Server virtuella datorn måste vara ansluten till en domän. En domänanvändare med administratörsbehörighet måste installera Azure Backup Server på den virtuella datorn.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Använda en virtuell IaaS-dator i Azure Stack

När du väljer en server Azure Backup Server du en Windows Server 2012 R2 Datacenter- eller Windows Server 2016 Datacenter-galleriavbildning. Artikeln Skapa [din första virtuella Windows-dator](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)i Azure Portal innehåller en självstudiekurs för att komma igång med den rekommenderade virtuella datorn. De rekommenderade minimikraven för den virtuella serverdatorn (VM) bör vara: A2 Standard med två kärnor och 3,5 GB RAM-minne.

Att skydda arbetsbelastningar Azure Backup Server har många nyanser. [Skyddsmatrisen för MABS](./backup-mabs-protection-matrix.md) hjälper till att förklara dessa nyanser. Läs den här artikeln helt innan du distribuerar datorn.

> [!NOTE]
> Azure Backup Server är utformat för att köras på en dedikerad virtuell dator med ett enda syfte. Du kan inte installera Azure Backup Server på:
>
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Anslut alltid Azure Backup Server till en domän. Om du behöver flytta Azure Backup Server till en annan domän installerar du först Azure Backup Server och ansluter den sedan till den nya domänen. När du Azure Backup Server kan du inte flytta den till en ny domän.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Med alternativet för replikering av Recovery Services-valvlagring kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv geo-redundant lagring. Om det här valvet är ditt primära valv låter du lagringsalternativet vara inställt på geo-redundant lagring. Välj lokalt redundant lagring om du vill ha ett billigare alternativ som är mindre beständigt. Läs mer om [alternativen geo-redundant,](../storage/common/storage-redundancy.md#geo-redundant-storage)lokalt [redundant](../storage/common/storage-redundancy.md#locally-redundant-storage)och [zonredundant](../storage/common/storage-redundancy.md#zone-redundant-storage) i [översikten Azure Storage replikering.](../storage/common/storage-redundancy.md)

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna valvets instrumentpanel och menyn Inställningar. Om menyn **Inställningar** inte öppnas väljer du Alla inställningar **på valvets** instrumentpanel.
2. På menyn **Inställningar väljer** du Konfiguration av **säkerhetskopieringsinfrastruktur för**  >  **säkerhetskopiering** för att öppna menyn Konfiguration av **säkerhetskopiering.** På menyn **Konfiguration av säkerhetskopiering** väljer du alternativet för lagringsreplikering för valvet.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Ladda ned Azure Backup Server installationsprogram

Det finns två sätt att ladda ned Azure Backup Server installationsprogrammet. Du kan ladda ned Azure Backup Server installationsprogrammet från [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=55269) Du kan också ladda Azure Backup Server installationsprogrammet när du konfigurerar ett Recovery Services-valv. Följande steg går igenom hur du laddar ned installationsprogrammet från Azure Portal när du konfigurerar ett Recovery Services-valv.

1. Logga in Azure Stack Azure-prenumerationen [i den virtuella Azure Portal](https://portal.azure.com/).
2. I den vänstra menyn väljer du **Alla tjänster.**

    ![Välj alternativet Alla tjänster på huvudmenyn](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. I dialogrutan **Alla tjänster** skriver du *Recovery Services*. När du börjar skriva filtrerar dina indata listan över resurser. När du ser det väljer du **Recovery Services-valv**.

    ![Skriv Recovery Services i dialogrutan Alla tjänster](./media/backup-mabs-install-azure-stack/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. I listan över Recovery Services-valv väljer du ditt valv för att öppna dess instrumentpanel.

    ![Välj ditt valv för att öppna instrumentpanelen](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. I valvets meny Komma igång säkerhetskopiering **för** att öppna Komma igång guiden.

    ![Kom igång med säkerhetskopiering](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Säkerhetskopieringsmenyn öppnas.

    ![Säkerhetskopieringsmål öppnas som standard](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. På säkerhetskopieringsmenyn går du **till menyn Where is your workload running** (Var körs din arbetsbelastning) och **väljer On-premises (Lokalt).** I **listrutan Vad vill du säkerhetskopiera?** väljer du de arbetsbelastningar som du vill skydda med Azure Backup Server. Om du inte är säker på vilka arbetsbelastningar du ska välja väljer du **Hyper-V-Virtual Machines** sedan **Förbered infrastruktur.**

    ![lokalt och arbetsbelastningar som mål](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Menyn **Förbered** infrastruktur öppnas.

7. På menyn **Förbered infrastruktur** väljer du Ladda **ned för** att öppna en webbsida för att ladda ned Azure Backup Server installationsfiler.

    ![Komma igång ändring i guiden](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Microsoft-webbsidan som är värd för de nedladdningsbara filerna för Azure Backup Server öppnas.

8. På nedladdningssidan Microsoft Azure Backup Server väljer du ett språk och sedan Ladda **ned.**

    ![Download Center öppnas](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Installationsprogrammet Azure Backup Server består av åtta filer – ett installationsprogram och sju .bin-filer. Markera **Filnamn för** att markera alla nödvändiga filer och välj **Nästa.** Ladda ned alla filer till samma mapp.

    ![Download Center, valda filer](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Hämtningsstorleken för alla installationsfiler är större än 3 GB. På en nedladdningslänk på 10 Mbit/s kan det ta upp till 60 minuter att ladda ned alla installationsfiler. Filerna laddas ned till den angivna nedladdningsplatsen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahera Azure Backup Server installera filer

När du har laddat ned alla filer till Azure Stack virtuella datorn går du till nedladdningsplatsen. Den första fasen i installationen Azure Backup Server är att extrahera filerna.

![Ladda ned MABS-installationsprogrammet](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Starta installationen genom att i listan över nedladdade filer välja **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Minst 4 GB ledigt utrymme krävs för att extrahera installationsfilerna.
    >

2. I guiden Azure Backup Server väljer du **Nästa för** att fortsätta.

    ![Microsoft Azure Backup Installationsguiden](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Välj sökvägen för Azure Backup Server och välj **Nästa.**

   ![Välj mål för filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Kontrollera extraheringsplatsen och välj **Extrahera.**

   ![Verifiera extraheringsplats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Guiden extraherar filerna och läser installationsprocessen.

   ![Guiden extraherar filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. När extraheringsprocessen är klar väljer du **Slutför.** Som standard **är Kör setup.exe** markerat. När du väljer **Slutför** Setup.exe installerar Microsoft Azure Backup Server på den angivna platsen.

   ![Installationsprogrammet extraherar Microsoft Azure Backup Server-filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installera programvarupaketet

I föregående steg väljer du Slutför **för att** avsluta extraheringsfasen och startar Azure Backup Server installationsguiden.

![Microsoft Azure Backup Installationsguiden startar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server delar kod med Data Protection Manager. Du ser referenser till Data Protection Manager DPM i Azure Backup Server installationsprogrammet. Även Azure Backup Server och Data Protection Manager är separata produkter är dessa produkter nära relaterade.

1. Starta installationsguiden genom att välja **Microsoft Azure Backup Server**.

   ![Välj Microsoft Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. På **välkomstskärmen** väljer du **Nästa.**

    ![Azure Backup Server – Välkommen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. På skärmen **Kravkontroller** väljer du **Kontrollera för** att avgöra om maskin- och programvarukraven för Azure Backup Server har uppfyllts.

    ![Azure Backup Server – Kravkontroll](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Om din miljö har de nödvändiga förutsättningarna visas ett meddelande som anger att datorn uppfyller kraven. Välj **Nästa**.  

    ![Azure Backup Server – Kravkontrollen har godkänts](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Om din miljö inte uppfyller de nödvändiga förutsättningarna anges problemen. Kraven som inte uppfylldes visas också i DpmSetup.log. Lös de nödvändiga felen och kör sedan **Kontrollera igen.** Installationen kan inte fortsätta förrän alla krav är uppfyllda.

    ![Azure Backup Server – installationsförutsättningar är inte uppfyllda](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server kräver SQL Server. Installationspaketet Azure Backup Server paketeras med lämpliga binärfiler SQL Server paketet. Om du vill använda en egen SQL-installation kan du göra det. Det rekommenderade alternativet är dock att låta installationsprogrammet lägga till en ny instans av SQL Server. För att säkerställa att ditt val fungerar med din miljö väljer **du Kontrollera och installera**.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärrinstans SQL Server instans. Den instans som används av Azure Backup Server måste vara lokal.
   >

    ![Azure Backup Server – SQL-inställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Om den virtuella datorn har de nödvändiga förutsättningarna för att installera Azure Backup Server efter kontrollen väljer du **Nästa.**

    ![Azure Backup Server – kraven uppfylls](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Om ett fel inträffar med en rekommendation att starta om datorn startar du om datorn. När du har startat om datorn startar du om installationsprogrammet och när du kommer till **skärmen SQL-inställningar** väljer du **Kontrollera igen.**

5. I **Installationsinställningar anger** du en plats för installationen av Microsoft Azure Backup och väljer **Nästa.**

    ![Ange plats för installation av filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Den scratch-platsen krävs för att backa upp till Azure. Se till att storleken på den scratch-platsen motsvarar minst 5 % av de data som planeras att säkerhetskopieras till Azure. För diskskydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [Förbereda datalagring.](/system-center/dpm/plan-long-and-short-term-data-storage)

6. På skärmen **Säkerhetsinställningar** anger du ett starkt lösenord för begränsade lokala användarkonton och väljer **Nästa.**

    ![Skärmen Säkerhetsinställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. På Microsoft Update **väljer du** om du vill använda Microsoft Update *för* att söka efter uppdateringar och väljer **Nästa.**

   > [!NOTE]
   > Vi rekommenderar att Windows Update omdirigeras till Microsoft Update, som erbjuder säkerhetsuppdateringar och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >

    ![Microsoft Update Opt-In skärm](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Granska *sammanfattningen av inställningar* och välj **Installera.**

    ![Sammanfattning av inställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    När Azure Backup Server har installerats startar installationsprogrammet omedelbart Microsoft Azure installationsprogrammet för Recovery Services-agenten.

9. Installationsprogrammet Microsoft Azure Recovery Services Agent öppnas och söker efter Internetanslutning. Om Internetanslutningen är tillgänglig fortsätter du med installationen. Om det inte finns någon anslutning anger du proxyinformation för att ansluta till Internet. När du har angett proxyinställningarna väljer du **Nästa.**

    ![Proxykonfiguration](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Om du vill Microsoft Azure Recovery Services-agenten väljer du **Installera**.

    ![Agentinstallation](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Den Microsoft Azure Recovery Services-agenten, som även kallas Azure Backup agent, konfigurerar Azure Backup Server till Recovery Services-valvet. När du har Azure Backup Server data alltid till samma Recovery Services-valv.

11. När Microsoft Azure Recovery Services-agenten har installerats  väljer du Nästa för att starta nästa fas: Azure Backup Server med Recovery Services-valvet.

    ![Agentinstallationen har slutförts](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Installationsprogrammet startar guiden **Registrera server.**

12. Växla till din Azure-prenumeration och recovery Services-valvet. På menyn **Förbered infrastruktur väljer du** Ladda ned för att ladda **ned** autentiseringsuppgifter för valvet. Om knappen **Ladda** ned i steg 2 inte är aktiv väljer du Redan nedladdad eller använder **den senaste Azure Backup Server för** att aktivera knappen. Autentiseringsuppgifterna för valvet laddas ned till den plats där du lagrar nedladdningar. Tänk på den här platsen eftersom du behöver den för nästa steg.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. På menyn **Valvidentifiering** väljer du Bläddra **för att** hitta autentiseringsuppgifterna för Recovery Services-valvet.

    ![Meny för valvidentifiering](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    I dialogrutan **Välj valvautentiseringsuppgifter** går du till nedladdningsplatsen, väljer autentiseringsuppgifterna för valvet och **väljer Öppna.**

    Sökvägen till autentiseringsuppgifterna visas på menyn Valvidentifiering. Välj **Nästa** för att gå till **Krypteringsinställningar.**

14. I dialogrutan **Krypteringsinställning** anger du en lösenfras för säkerhetskopieringskryptering och en plats där lösenfrasen ska lagras och väljer **Nästa.**

    ![Krypteringsinställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Du kan ange din egen lösenfras eller använda lösenfrasgeneratorn för att skapa en åt dig. Lösenfrasen är din och Microsoft sparar eller hanterar inte den här lösenfrasen. Förbered för en katastrof genom att spara lösenfrasen på en tillgänglig plats.

    När du väljer **Nästa** registreras Azure Backup Server med Recovery Services-valvet. Installationsprogrammet fortsätter att SQL Server och Azure Backup Server.

    ![Installationsprogrammet installerar SQL och Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. När installationsprogrammet är klart visar **Status** att all programvara har installerats.

    ![Programvaran har installerats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    När installationen är klar skapas Azure Backup Server-konsolen och Azure Backup Server PowerShell-ikonerna på serverdatorn.

## <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior

Den första säkerhetskopian sparas på lagring som är ansluten till den Azure Backup Server datorn. Mer information om hur du lägger till diskar finns i [Lägga till modern backuplagring.](/system-center/dpm/add-storage)

> [!NOTE]
> Du måste lägga till lagringsutrymme för säkerhetskopiering även om du planerar att skicka data till Azure. I den Azure Backup Server arkitekturen innehåller Recovery  Services-valvet den andra kopian av data medan den lokala lagringen innehåller den första (och obligatoriska) säkerhetskopieringskopian.
>
>

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup Server kräver anslutning till Azure Backup tjänsten för att produkten ska fungera korrekt. Om du vill verifiera om datorn har anslutning till Azure använder du ```Get-DPMCloudConnection``` cmdleten i Azure Backup Server PowerShell-konsolen. Om cmdletens utdata är TRUE finns anslutningen, annars finns det ingen anslutning.

Samtidigt måste Azure-prenumerationen vara i felfritt tillstånd. Logga in på prenumerationsportalen för att ta reda på statusen för din prenumeration och [hantera den.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

När du vet tillståndet för Azure-anslutningen och Azure-prenumerationen kan du använda tabellen nedan för att ta reda på hur funktionerna för säkerhetskopiering/återställning påverkar.

| Anslutningstillstånd | Azure Subscription (Azure-prenumeration) | Säkerhetskopiera till Azure | Back up to disk ( Backa upp till disk) | Återställa från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Aktiv |Tillåts |Tillåts |Tillåts |Tillåts |
| Ansluten |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Ansluten |Avetablerad |Stoppad |Stoppad |Stoppade och Azure-återställningspunkter togs bort |Stoppad |
| Förlorad anslutning > 15 dagar |Aktiv |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Avetablerad |Stoppad |Stoppad |Stoppade och Azure-återställningspunkter togs bort |Stoppad |

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


När anslutningen till Azure har återställts till Azure Backup Server anger Azure-prenumerationens tillstånd vilka åtgärder som kan utföras. När servern är **ansluten använder** du tabellen i [Nätverksanslutning för att](backup-mabs-install-azure-stack.md#network-connectivity) se tillgängliga åtgärder.

### <a name="handling-subscription-states"></a>Hantera prenumerations tillstånd

Det går att ändra en Azure-prenumeration från *statusen Upphört eller* *Avetablerat* till *Aktivt* tillstånd. Medan prenumerationstillståndet inte är *aktivt:*

- När en prenumeration *avetablerar* förlorar den funktioner. När du återställer prenumerationen *till Aktiv* återställs funktionerna för säkerhetskopiering/återställning. Om säkerhetskopierade data på den lokala disken har kvarhålls med en tillräckligt lång kvarhållningsperiod kan säkerhetskopierade data hämtas. Säkerhetskopierade data i Azure går dock oåterkalleligt förlorade när prenumerationen har *avetableats.*
- När en prenumeration *har upphört att* gälla förlorar den funktioner. Schemalagda säkerhetskopieringar körs inte när en prenumeration har upphört *att gälla.*

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning) kan du se [felkoderna i dokumentet](https://support.microsoft.com/kb/3041338).
Du kan också Azure Backup [vanliga frågor och svar](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Nästa steg

Artikeln Förbereda [din miljö för DPM](/system-center/dpm/prepare-environment-for-dpm)innehåller information om Azure Backup Server konfigurationer.

Du kan använda följande artiklar för att få en djupare förståelse för arbetsbelastningsskydd med hjälp Microsoft Azure Backup Server.

- [SQL Server säkerhetskopiering](./backup-mabs-sql-azure-stack.md)
- [Säkerhetskopiering av SharePoint-server](./backup-mabs-sharepoint-azure-stack.md)
- [Alternativ serversäkerhetskopiering](backup-azure-alternate-dpm-server.md)
