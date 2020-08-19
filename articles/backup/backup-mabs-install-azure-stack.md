---
title: Installera Azure Backup Server på Azure Stack
description: I den här artikeln lär du dig hur du använder Azure Backup Server för att skydda eller säkerhetskopiera arbets belastningar i Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5b6ae97f30bf704e5bc495cc8020e8bcb0e30ce3
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263069"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installera Azure Backup Server på Azure Stack

Den här artikeln förklarar hur du installerar Azure Backup Server på Azure Stack. Med Azure Backup Server kan du skydda arbets belastningar för infrastruktur som en tjänst (IaaS), till exempel virtuella datorer som körs i Azure Stack. En fördel med att använda Azure Backup Server för att skydda dina arbets belastningar är att du kan hantera alla arbets belastnings skydd från en enda konsol.

> [!NOTE]
> Mer information om säkerhetsfunktioner finns i [dokumentationen för Azure Backup Security features](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server

Azure Backup Server skyddar följande Azure Stack arbets belastningar för virtuella datorer.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Windows Server, halvårs kanal – Data Center/Enterprise/standard | Volymer, filer, mappar |
| Windows Server 2016-Data Center/Enterprise/standard | Volymer, filer, mappar |
| Windows Server 2012 R2-Data Center/Enterprise/standard | Volymer, filer, mappar |
| Windows Server 2012-Data Center/Enterprise/standard | Volymer, filer, mappar |
| Windows Server 2008 R2-Data Center/Enterprise/standard | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2016 | Server grupp, databas, klient del, webb server |
| SharePoint 2013 | Server grupp, databas, klient del, webb server |
| SharePoint 2010 | Server grupp, databas, klient del, webb server |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Servers miljön

Överväg rekommendationerna i det här avsnittet när du installerar Azure Backup Server i din Azure Stack-miljö. Installations programmet för Azure Backup Server kontrollerar att din miljö uppfyller de nödvändiga förutsättningarna, men du sparar tid genom att förbereda innan du installerar.

### <a name="determining-size-of-virtual-machine"></a>Bestämmer storleken på den virtuella datorn

Om du vill köra Azure Backup Server på en Azure Stack virtuell dator använder du storlek a2 eller större. Om du vill ha hjälp med att välja storlek för virtuell dator kan du ladda ned [kalkylatorn Azure Stack VM-storlek](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på Azure Stack virtuella datorer

Alla virtuella datorer som används i en Azure Stack arbets belastning måste tillhöra samma virtuella Azure-nätverk och Azure-prenumeration.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestanda

Om den delas med andra virtuella datorer påverkar lagrings kontots storlek och IOPS Azure Backup Server VM-prestanda. Därför bör du använda ett separat lagrings konto för den Azure Backup Server virtuella datorn. Azure Backup-agenten som körs på Azure Backup Server behöver tillfällig lagring för:

- eget bruk (en plats för cachelagring),
- data som återställs från molnet (lokalt mellanlagringsområde)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup temporär disk lagring

Varje Azure Stack virtuell dator levereras med temporär disk lagring, som är tillgänglig för användaren som volym `D:\` . Det lokala mellanlagringsområdet som krävs av Azure Backup kan konfigureras för att finnas i `D:\` och cache-platsen kan placeras på `C:\` . På så sätt måste ingen lagring vara hämtas bort från data diskarna som är anslutna till den virtuella datorn Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhets kopierings data på lokal disk och i Azure

Azure Backup Server lagrar säkerhets kopierings data på Azure-diskar som är anslutna till den virtuella datorn för drift återställning. När diskarna och lagrings utrymmet är kopplade till den virtuella datorn kan Azure Backup Server Hantera lagring åt dig. Mängden säkerhets kopierings data lagring beror på antalet och storleken på diskar som är anslutna till varje [Azure Stack virtuell dator](/azure-stack/user/azure-stack-storage-overview). Varje storlek Azure Stack virtuell dator har ett maximalt antal diskar som kan kopplas till den virtuella datorn. Till exempel är a2 fyra diskar. A3 är åtta diskar. A4 är 16 diskar. På nytt bestämmer storlek och antal diskar den totala lagringspoolen för säkerhets kopiering.

> [!IMPORTANT]
> Du bör **inte** behålla återställnings data (säkerhets kopiering) på Azure Backup Server anslutna diskar i mer än fem dagar.
>

Lagring av säkerhetskopierade data i Azure minskar säkerhets kopierings infrastrukturen på Azure Stack. Om data är fler än fem dagar gamla bör de lagras i Azure.

Skapa eller Använd ett Recovery Services valv för att lagra säkerhetskopierade data i Azure. När du förbereder att säkerhetskopiera Azure Backup Server arbets belastningen [konfigurerar du Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). När ett säkerhets kopierings jobb har kon figurer ATS skapas en återställnings punkt i valvet. Varje Recovery Services valv innehåller upp till 9999 återställnings punkter. Beroende på hur många återställnings punkter som har skapats och hur länge de behålls, kan du behålla säkerhetskopierade data i många år. Du kan till exempel skapa månads Visa återställnings punkter och spara dem i fem år.

### <a name="scaling-deployment"></a>Skala distribution

Om du vill skala distributionen har du följande alternativ:

- Skala upp – öka storleken på den Azure Backup Server virtuella datorn från en serie till D-serien och öka den lokala lagringen [enligt anvisningarna för den virtuella Azure Stack datorn](/azure-stack/user/azure-stack-manage-vm-disks).
- Avlasta data – skicka äldre data till Azure och Behåll bara de senaste data på lagrings platsen som är kopplad till Azure Backup Server.
- Skala ut – Lägg till fler Azure Backup-servrar för att skydda arbets belastningarna.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3,5 SP1 eller högre måste vara installerat på den virtuella datorn.

### <a name="joining-a-domain"></a>Ansluta till en domän

Den Azure Backup Server virtuella datorn måste vara ansluten till en domän. En domän användare med administratörs behörighet måste installera Azure Backup Server på den virtuella datorn.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Använda en virtuell IaaS-dator i Azure Stack

När du väljer en server för Azure Backup Server börjar du med en Windows Server 2012 R2 Data Center-eller Windows Server 2016 Data Center Gallery-avbildning. Artikeln [skapar din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)och innehåller en själv studie kurs för att komma igång med den rekommenderade virtuella datorn. De rekommenderade minimi kraven för virtuell Server-dator (VM) ska vara: a2 standard med två kärnor och 3,5 GB RAM.

Att skydda arbets belastningar med Azure Backup Server har många olika delarna. [Skydds matrisen för Mabs](./backup-mabs-protection-matrix.md) hjälper till att förklara dessa olika delarna. Innan du distribuerar datorn kan du läsa den här artikeln helt och hållet.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad virtuell dator med ett enda syfte. Du kan inte installera Azure Backup Server på:
>
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Anslut alltid Azure Backup Server till en domän. Om du behöver flytta Azure Backup Server till en annan domän måste du först installera Azure Backup Server och sedan ansluta den till den nya domänen. När du har distribuerat Azure Backup Server kan du inte flytta den till en ny domän.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Med alternativet Recovery Services valv lagrings replikering kan du välja mellan Geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv Geo-redundant lagring. Om valvet är ditt primära valv lämnar du lagrings alternativet inställt på Geo-redundant lagring. Välj lokalt redundant lagring om du vill ha ett billigare alternativ som är mindre varaktigt. Läs mer om alternativen för [Geo-redundant](../storage/common/storage-redundancy.md) och [lokalt redundant](../storage/common/storage-redundancy.md) lagring i [Översikt över Azure Storage replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrument panelen för valv och menyn Inställningar. Om menyn **Inställningar** inte öppnas väljer du **alla inställningar** på instrument panelen för valvet.
2. På menyn **Inställningar** väljer du säkerhets kopierings **infrastruktur**  >  **säkerhets kopierings konfiguration** för att öppna menyn **säkerhets kopierings konfiguration** . På menyn för **säkerhets kopierings konfiguration** väljer du alternativet Storage Replication för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Hämta Azure Backup Server installations program

Det finns två sätt att ladda ned installations programmet för Azure Backup Server. Du kan hämta installations programmet för Azure Backup Server från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55269). Du kan också hämta Azure Backup Server Installer när du konfigurerar ett Recovery Services-valv. Följande steg beskriver hur du hämtar installations programmet från Azure Portal när du konfigurerar ett Recovery Services-valv.

1. [Logga in på din Azure-prenumeration i Azure Portal](https://portal.azure.com/)från din Azure Stack virtuella dator.
2. Välj **alla tjänster**i den vänstra menyn.

    ![Välj alternativet alla tjänster i huvud menyn](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. I dialog rutan **alla tjänster** skriver du *Recovery Services*. När du börjar skriva filtrerar ditt inkommande listan över resurser. När du ser det väljer du **Recovery Services valv**.

    ![Skriv Recovery Services i dialog rutan alla tjänster](./media/backup-mabs-install-azure-stack/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. I listan över Recovery Services valv väljer du ditt valv för att öppna instrument panelen.

    ![Välj ditt valv för att öppna instrument panelen](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. I valvets Komma igång-meny väljer du **säkerhets kopiering** för att öppna guiden komma igång.

    ![Komma igång med säkerhets kopiering](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Säkerhets kopierings menyn öppnas.

    ![Säkerhets kopiering-mål – standard – öppnade](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. I säkerhets kopierings menyn väljer du **lokalt**på menyn **där är din arbets belastning som körs** . I list rutan **vad vill du säkerhetskopiera?** väljer du de arbets belastningar som du vill skydda med hjälp av Azure Backup Server. Om du inte är säker på vilka arbets belastningar du väljer väljer du **Hyper-V Virtual Machines** och väljer sedan **Förbered infrastruktur**.

    ![lokalt och arbets belastningar som mål](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Menyn **Förbered infrastruktur** öppnas.

7. I menyn **Förbered infrastruktur** väljer du **Ladda ned** för att öppna en webb sida för att ladda ned Azure Backup Server installationsfiler.

    ![Ändring av guiden Komma igång](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Microsoft-webbsidan som innehåller nedladdnings bara filer för Azure Backup Server öppnas.

8. På sidan Microsoft Azure Backup Server-hämtning väljer du ett språk och väljer **Hämta**.

    ![Hämtnings Center öppnas](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Installations programmet för Azure Backup Server består av åtta filer – ett installations-och sju. bin-filer. Kontrol lera **fil namnet** för att välja alla nödvändiga filer och välj **Nästa**. Ladda ned alla filer till samma mapp.

    ![Download Center, valda filer](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Hämtnings storleken för alla installationsfiler är större än 3 GB. På en hämtnings länk på 10 Mbit/s kan nedladdning av alla installationsfiler ta upp till 60 minuter. Filerna laddas ned till den angivna nedladdnings platsen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahera Azure Backup Server installera filer

När du har laddat ned alla filer till Azure Stack virtuella datorn går du till nedladdnings platsen. Det första steget med att installera Azure Backup Server är att extrahera filerna.

![Ladda ned MABS Installer](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Starta installationen genom att välja **MicrosoftAzureBackupserverInstaller.exe**i listan över hämtade filer.

    > [!WARNING]
    > Det krävs minst 4 GB ledigt utrymme för att extrahera installationsfilerna.
    >

2. I guiden Azure Backup Server väljer du **Nästa** för att fortsätta.

    ![Microsoft Azure Backup installations guiden](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Välj sökvägen till Azure Backup Server-filerna och välj **Nästa**.

   ![Välj mål för filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifiera extraherings platsen och välj **extrahera**.

   ![Verifiera extraherings plats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Guiden extraherar filerna och läser installations processen.

   ![Guiden extraherar filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. När extraherings processen har slutförts väljer du **Slutför**. Som standard är **kör setup.exe** markerat. När du väljer **slutför**Setup.exe installerar Microsoft Azure Backup server på den angivna platsen.

   ![Installations programmet extraherar Microsoft Azure Backup server-filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installera programpaketet

I föregående steg väljer du **Slutför** för att avsluta extraherings fasen och startar installations guiden för Azure Backup Server.

![Installations guiden för Microsoft Azure Backup startar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server delar kod med Data Protection Manager. Du ser referenser till Data Protection Manager och DPM i Azure Backup Server Installer. Även om Azure Backup Server och Data Protection Manager separata produkter är dessa produkter nära relaterade.

1. Välj **Microsoft Azure Backup Server**för att starta installations guiden.

   ![Välj Microsoft Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. På **välkomst** skärmen väljer du **Nästa**.

    ![Azure Backup Server – Välkommen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. På skärmen **krav kontroll** väljer du **kontrol lera** för att fastställa om maskin-och program varu kraven för Azure Backup Server har uppfyllts.

    ![Azure Backup Server-krav kontroll](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Om din miljö har de nödvändiga förutsättningarna visas ett meddelande som anger att datorn uppfyller kraven. Välj **Nästa**.  

    ![Azure Backup Server-krav kontroll godkändes](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Om din miljö inte uppfyller de nödvändiga kraven, anges problemen. De krav som inte uppfylldes visas också i DpmSetup. log. Lös de nödvändiga felen och kör sedan **kontrollen igen**. Installationen kan inte fortsätta förrän alla nödvändiga komponenter är uppfyllda.

    ![Azure Backup Server-installations krav uppfylls inte](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server kräver SQL Server. Installations paketet för Azure Backup Server levereras med rätt SQL Server binärfiler. Om du vill använda en egen SQL-installation kan du. Men det rekommenderade alternativet är att låta installations programmet lägga till en ny instans av SQL Server. Välj **kontrol lera och installera**för att se till att ditt val fungerar med din miljö.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärran sluten SQL Server instans. Instansen som används av Azure Backup Server måste vara lokal.
   >

    ![Azure Backup Server-SQL-inställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    När du har kontrollerat om den virtuella datorn uppfyller de nödvändiga förutsättningarna för att installera Azure Backup Server, väljer du **Nästa**.

    ![Azure Backup Server-krav uppfylls](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Om ett fel inträffar med en rekommendation för att starta om datorn startar du om datorn. När du har startat om datorn startar du om installations programmet och klickar på **kontrol lera igen**när du kommer till skärmen **SQL-inställningar** .

5. I **installations inställningarna**anger du en plats för installationen av Microsoft Azure Backup server-filer och väljer **Nästa**.

    ![Ange plats för installation av filer](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Det krävs en arbets plats för att säkerhetskopiera till Azure. Se till att den virtuella platsens storlek motsvarar minst 5% av de data som planeras att säkerhets kopie ras till Azure. För disk skydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [förbereda data lagring](/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019).

6. På sidan **säkerhets inställningar** anger du ett starkt lösen ord för begränsade lokala användar konton och väljer **Nästa**.

    ![Skärmen säkerhets inställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. På den **Microsoft Update opt-in** väljer du om du vill använda *Microsoft Update* för att söka efter uppdateringar och väljer **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att du Windows Update omdirigera till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >

    ![Microsoft Update valbar skärm](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Granska *sammanfattningen av inställningarna* och välj **Installera**.

    ![Sammanfattning av inställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    När Azure Backup Server har slutfört installationen startar installations programmet omedelbart Microsoft Azure Recovery Services agent-installationsprogrammet.

9. Installations programmet för Microsoft Azure Recovery Services agent öppnas och kontrollerar Internet anslutningen. Om Internet anslutning är tillgängligt fortsätter du med installationen. Om det inte finns någon anslutning anger du proxyinformation för att ansluta till Internet. När du har angett proxyinställningarna väljer du **Nästa**.

    ![Proxykonfiguration](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Välj **Installera**för att installera Microsoft Azure Recovery Services agenten.

    ![Agentinstallation](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure Recovery Services agent, som också kallas Azure Backup Agent, konfigurerar Azure Backup Server till Recovery Services-valvet. När den har kon figurer ATS kommer Azure Backup Server alltid säkerhetskopiera data till samma Recovery Services-valv.

11. När Microsoft Azure Recovery Services-agenten har slutfört installationen väljer du **Nästa** för att starta nästa fas: registrera Azure Backup Server med Recovery Services-valvet.

    ![Agent installationen har slutförts](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Installations programmet startar **guiden Registrera Server**.

12. Växla till din Azure-prenumeration och ditt Recovery Services-valv. I menyn **Förbered infrastruktur** väljer du **Ladda ned** för att hämta autentiseringsuppgifter för valvet. Om knappen **Ladda ned** i steg 2 inte är aktiv väljer du **redan Hämtad eller använder den senaste Azure Backup Server installationen** för att aktivera knappen. Valvets autentiseringsuppgifter laddas ned till den plats där du lagrar hämtningar. Var medveten om den här platsen eftersom du behöver den för nästa steg.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. På menyn **valv identifiering** väljer du **Bläddra** för att hitta Recovery Services valvets autentiseringsuppgifter.

    ![Menyn valv identifiering](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    I dialog rutan **Välj valv autentiseringsuppgifter** går du till nedladdnings platsen, väljer dina autentiseringsuppgifter för valvet och väljer **Öppna**.

    Sökvägen till autentiseringsuppgifterna visas på menyn valv identifiering. Välj **Nästa** för att gå vidare till **krypterings inställningarna**.

14. I dialog rutan **krypterings inställning** anger du en lösen fras för kryptering av säkerhets kopior och en plats där lösen frasen ska lagras. Välj **sedan nästa**.

    ![Krypteringsinställningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Du kan ange en egen lösen fras eller använda lösen frasen för att skapa en åt dig. Lösen frasen är din och Microsoft sparar eller hanterar inte den här lösen frasen. Spara lösen frasen på en tillgänglig plats för att förbereda för en katastrof.

    När du har valt **Nästa**är Azure Backup Server registrerad i Recovery Services-valvet. Installations programmet fortsätter att installera SQL Server och Azure Backup Server.

    ![Installations programmet installerar SQL och Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. När installations programmet har slutförts visar **statusen** att all program vara har installerats.

    ![Program varan har installerats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    När installationen är klar skapas Azure Backup Server-konsolen och Azure Backup Server PowerShell-ikonerna på server Skriv bordet.

## <a name="add-backup-storage"></a>Lägg till säkerhets kopierings lagring

Den första säkerhets kopian sparas på lagrings platsen som är ansluten till Azure Backup Server datorn. Mer information om hur du lägger till diskar finns i [Lägg till modern säkerhets kopierings lagring](/system-center/dpm/add-storage).

> [!NOTE]
> Du måste lägga till säkerhets kopierings lagring även om du planerar att skicka data till Azure. I Azure Backup Server arkitekturen innehåller Recovery Services valvet den *andra* kopian av data medan den lokala lagrings platsen innehåller den första (och obligatoriska) säkerhets kopian.
>
>

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup Server kräver anslutning till Azure Backups tjänsten för att produkten ska fungera korrekt. Du kan kontrol lera om datorn har anslutningen till Azure genom att använda ```Get-DPMCloudConnection``` cmdleten i Azure Backup Server PowerShell-konsolen. Om utdata från cmdleten är TRUE finns det en anslutning, annars finns det ingen anslutning.

På samma gången måste Azure-prenumerationen vara i felfritt tillstånd. Logga in på [prenumerations portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)för att ta reda på status för din prenumeration och hantera den.

När du känner till statusen för Azure-anslutningen och Azure-prenumerationen kan du använda tabellen nedan för att ta reda på hur de funktioner för säkerhets kopiering/återställning som erbjuds fungerar.

| Anslutnings tillstånd | Azure Subscription (Azure-prenumeration) | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställa från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Aktiv |Tillåts |Tillåts |Tillåts |Tillåts |
| Ansluten |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppade och Azure-återställnings punkter har tagits bort |Stoppad |
| Förlorad anslutning > 15 dagar |Aktiv |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Upphörd |Stoppad |Stoppad |Tillåts |Tillåts |
| Förlorad anslutning > 15 dagar |Avetableras |Stoppad |Stoppad |Stoppade och Azure-återställnings punkter har tagits bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställning från förlust av anslutning

Om en brand vägg eller en proxy förhindrar åtkomst till Azure lägger du till följande domän adresser i listan över tillåtna brand väggar/proxy-profiler:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

När anslutningen till Azure har återställts till Azure Backup Server, bestämmer Azure-prenumerationens status vilka åtgärder som kan utföras. När servern är **ansluten**använder du tabellen i [nätverks anslutningen](backup-mabs-install-azure-stack.md#network-connectivity) för att se tillgängliga åtgärder.

### <a name="handling-subscription-states"></a>Hantera prenumerations tillstånd

Det går att ändra en Azure-prenumeration från *förfallet* eller *avetablerat* tillstånd till *aktivt* tillstånd. Även om prenumerations statusen inte är *aktiv*:

- När en prenumeration har *avetablerats*förlorar den funktioner. Återställer prenumerationen till *aktiv*, revives säkerhets kopiering/återställning. Om säkerhets kopierings data på den lokala disken har behållits med en tillräckligt stor kvarhållningsperiod kan du Hämta säkerhetskopierade data. Säkerhets kopierings data i Azure är dock irretrievably förlorade när prenumerationen går in i *avetablerat* tillstånd.
- När en prenumeration har *upphört att gälla*förlorar den funktioner. Schemalagda säkerhets kopieringar körs inte när en prenumeration har *upphört att gälla*.

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup-servern Miss lyckas med fel under installations fasen (eller säkerhets kopiering eller återställning) går du till [dokumentet med fel koder](https://support.microsoft.com/kb/3041338).
Du kan också läsa [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

Artikeln och [förbereder din miljö för DPM](/system-center/dpm/prepare-environment-for-dpm)innehåller information om Azure Backup Server konfigurationer som stöds.

Du kan använda följande artiklar för att få en djupare förståelse för arbets belastnings skyddet med hjälp av Microsoft Azure Backup Server.

- [SQL Server säkerhets kopiering](./backup-mabs-sql-azure-stack.md)
- [Säkerhets kopiering av SharePoint Server](./backup-mabs-sharepoint-azure-stack.md)
- [Alternativ server säkerhets kopiering](backup-azure-alternate-dpm-server.md)
