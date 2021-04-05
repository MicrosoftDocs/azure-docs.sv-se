---
title: Konfigurera Azure Backup Server för Azure VMware-lösning
description: Konfigurera din Azure VMware-lösning för att säkerhetskopiera virtuella datorer med Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581490"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Konfigurera Azure Backup Server för Azure VMware-lösning

Azure Backup Server bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR). Med Azure VMware-lösningen kan du bara konfigurera en säkerhets kopia av en virtuell dator (VM-nivå) med hjälp av Azure Backup Server. 

Azure Backup Server kan lagra säkerhetskopierade data till:

- **Disk**: Azure Backup Server säkerhetskopierar data till lagringspooler för kortsiktig lagring.
- **Azure**: för både kortsiktiga och långsiktiga lagrings platser kan Azure Backup Server data som lagras i diskkonfigurationer säkerhets kopie ras till Microsoft Azure molnet med hjälp av Azure Backup.

Använd Azure Backup Server för att återställa data till källan eller en annan plats. På så sätt kan du, om ursprungliga data inte är tillgängliga på grund av planerade eller oväntade problem, återställa data till en annan plats.

Den här artikeln hjälper dig att förbereda din Azure VMware-lösning för att säkerhetskopiera virtuella datorer med hjälp av Azure Backup Server. Vi vägleder dig genom stegen för att: 

> [!div class="checklist"]
> * Bestäm vilken typ av disk och storlek för virtuell dator som ska användas.
> * Skapa ett Recovery Services-valv som lagrar återställnings punkterna.
> * Ange Storage-replikering för ett Recovery Services-valv.
> * Lägg till lagring till Azure Backup Server.

## <a name="supported-vmware-features"></a>VMware-funktioner som stöds

- **Återställning utan agent:** Azure Backup Server kräver inte att en agent installeras på vCenter-eller ESXi-servern för att säkerhetskopiera den virtuella datorn. Ange i stället IP-adressen eller det fullständigt kvalificerade domän namnet (FQDN) och de inloggnings uppgifter som används för att autentisera VMware-servern med Azure Backup Server.
- **Cloud-integrerad säkerhets kopiering:** Azure Backup Server skyddar arbets belastningar till disk och molnet. Arbets flödet för säkerhets kopiering och återställning av Azure Backup Server hjälper dig att hantera långsiktig kvarhållning och säkerhets kopiering på annan plats.
- **Identifiera och skydda virtuella datorer som hanteras av vCenter:** Azure Backup Server identifierar och skyddar virtuella datorer som distribueras på en vCenter-eller ESXi-Server. Azure Backup Server identifierar även virtuella datorer som hanteras av vCenter så att du kan skydda stora distributioner.
- **Automatiskt skydd på mappnivå:** vCenter gör det möjligt att organisera dina virtuella datorer i VM-mappar. Azure Backup Server identifierar mapparna. Du kan använda den för att skydda virtuella datorer på mappnivå, inklusive alla undermappar. När du skyddar mappar Azure Backup Server skyddar de virtuella datorerna i mappen och skyddar virtuella datorer senare. Azure Backup Server identifierar nya virtuella datorer dagligen och skyddar dem automatiskt. När du ordnar de virtuella datorerna i rekursiva mappar, Azure Backup Server automatiskt identifiera och skydda de nya virtuella datorerna som distribueras i de rekursiva mapparna.
- **Azure Backup Server fortsätter att skydda virtuella vMotioned-datorer i klustret:** Eftersom virtuella datorer är vMotioned för belastnings utjämning i klustret, identifierar Azure Backup Server automatiskt och fortsätter med VM-skyddet.
- **Återställ nödvändiga filer snabbare:** Azure Backup Server kan återställa filer eller mappar från en virtuell Windows-dator utan att återställa hela den virtuella datorn.

## <a name="limitations"></a>Begränsningar

- Samlad uppdatering 1 för Azure Backup Server v3 måste vara installerad.
- Du kan inte säkerhetskopiera ögonblicks bilder av användaren före den första Azure Backup Server säkerhets kopieringen. När Azure Backup Server har slutfört den första säkerhets kopieringen kan du säkerhetskopiera användar ögonblicks bilder.
- Azure Backup Server kan inte skydda virtuella VMware-datorer med direkt lagrings diskar och fysiska mappningar av RAW-enheter (pRDMs).
- Azure Backup Server kan inte identifiera eller skydda VMware-vApps.

Om du vill konfigurera Azure Backup Server för Azure VMware-lösningen måste du slutföra följande steg:

- Konfigurera förutsättningarna och miljön.
- Skapa ett Recovery Services-valv.
- Hämta och installera Azure Backup Server.
- Lägg till lagring till Azure Backup Server.

### <a name="deployment-architecture"></a>Distributions arkitektur

Azure Backup Server distribueras som en virtuell dator i Azure Infrastructure as a Service (IaaS) för att skydda virtuella datorer i Azure VMware-lösningen.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server distribueras som en virtuell dator i Azure Infrastructure as a Service (IaaS) för att skydda virtuella datorer i Azure VMware-lösningen." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Servers miljön

Överväg rekommendationerna i det här avsnittet när du installerar Azure Backup Server i din Azure-miljö.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Se till att du [konfigurerar nätverk för ditt privata VMware-moln i Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>Välja storlek på den virtuella datorn

Följ anvisningarna i guiden [skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md) själv studie kursen.  Du skapar den virtuella datorn i det virtuella nätverket som du skapade i föregående steg. Börja med en galleri bild av Windows Server 2019 Data Center för att köra Azure Backup Server. 

Tabellen sammanfattar det maximala antalet skyddade arbets belastningar för varje Azure Backup Server VM-storlek. Informationen baseras på interna prestanda och skalningstester med kanoniska värden för arbetsbelastningsstorlek och omsättning. Den faktiska arbets belastnings storleken kan vara större men bör hanteras av diskarna som är anslutna till den virtuella datorn Azure Backup Server.

| Maximalt antal skyddade arbets belastningar | Genomsnittlig arbetsbelastningsstorlek | Genomsnittlig omsättning av arbetsbelastning (dagligen) | Minsta lagrings-IOPS | Rekommenderad disktyp/storlek      | Rekommenderad storlek på virtuell dator |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Netto 5 % omsättning                   | 2 000             | Standard HDD (8 TB eller över storlek per disk)  | A4V2       |
| 40                      | 150 GB                | Netto 10 % omsättning                  | 4 500             | Premium SSD * (1 TB eller över storlek per disk) | DS3_V2     |
| 60                      | 200 GB                | Netto 10 % omsättning                  | 10 500            | Premium SSD * (8 TB eller över storlek per disk) | DS3_V2     |

* Om du vill hämta de nödvändiga IOPs använder du minst diskar som rekommenderas eller högre. Mindre IOPs är lägre än disk storleken.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad server med ett enda syfte. Du kan inte installera Azure Backup Server på en dator som:
> * Körs som en domänkontrollant.
> * Har program Server rollen installerad.
> * Är en System Center Operations Manager hanterings Server.
> * Kör Exchange Server.
> * Är en nod i ett kluster.

### <a name="disks-and-storage"></a>Diskar och lagring

Azure Backup Server kräver diskar för installation. 

| Krav                      | Rekommenderad storlek  |
|----------------------------------|-------------------------|
| Azure Backup Server installation                | Installations plats: 3 GB<br />Databasfilenhet: 900 MB<br />Systemen het: 1 GB för SQL Server installation<br /><br />Du behöver också utrymme för Azure Backup Server för att kopiera fil katalogen till en tillfällig installations plats när du arkiverar.      |
| Disk för lagringspoolen<br />(Använder enkla volymer, får inte finnas på en dynamisk disk) | Två till tre gånger den skyddade data storleken.<br />Detaljerad lagrings beräkning finns i [DPM-Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

Information om hur du ansluter en ny hanterad datadisk till en befintlig virtuell Azure-dator finns i [bifoga en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> En enda Azure Backup Server har en mjuk gräns på 120 TB för lagringspoolen.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhets kopierings data på lokal disk och i Azure

Lagring av säkerhetskopierade data i Azure minskar säkerhets kopierings infrastrukturen på den virtuella datorn Azure Backup Server. Vid drifts återställning (säkerhets kopiering) lagrar Azure Backup Server säkerhets kopierings data på Azure-diskar som är anslutna till den virtuella datorn. När diskarna och lagrings utrymmet är anslutna till den virtuella datorn kan Azure Backup Server hantera lagringen åt dig. Lagrings utrymmet beror på antalet och storleken på diskar som är anslutna till varje virtuell Azure-dator. Varje storlek på den virtuella Azure-datorn har ett maximalt antal diskar som kan anslutas. Till exempel är a2 fyra diskar, A3 är åtta diskar och A4 är 16 diskar. På nytt bestämmer storlek och antal diskar den totala kapaciteten för säkerhets kopiering av lagringspool.

> [!IMPORTANT]
> Du bör *inte* behålla drift återställnings data på Azure Backup Server anslutna diskar i mer än fem dagar. Om data är fler än fem dagar gamla lagrar du dem i ett Recovery Services valv.

Skapa eller Använd ett Recovery Services valv för att lagra säkerhetskopierade data i Azure. När du förbereder för att säkerhetskopiera Azure Backup Server arbets belastningen [konfigurerar du Recovery Services-valvet](#create-a-recovery-services-vault). När en säkerhets kopiering online körs när ett jobb har kon figurer ATS skapas en återställnings punkt i valvet. Varje Recovery Services valv innehåller upp till 9 999 återställnings punkter. Beroende på hur många återställnings punkter som skapats och hur länge de är sparade, kan du behålla säkerhetskopierade data i många år. Du kan till exempel skapa månads Visa återställnings punkter och behålla dem i fem år.

> [!IMPORTANT]
> Oavsett om du skickar säkerhets kopierings data till Azure eller om du behåller dem lokalt måste du registrera Azure Backup Server med ett Recovery Services-valv.

### <a name="scale-deployment"></a>Skala distribution

Om du vill skala distributionen har du följande alternativ:

- **Skala upp**: öka storleken på den virtuella datorn Azure Backup Server från en serie till DS3-serien och öka den lokala lagringen.
- **Avlasta data**: skicka äldre data till Azure och Behåll bara de senaste data på lagrings platsen som är kopplad till Azure Backup Server datorn.
- **Skala ut**: Lägg till fler Azure Backup Server datorer för att skydda arbets belastningarna.

### <a name="net-framework"></a>.NET Framework

Den virtuella datorn måste ha .NET Framework 3,5 SP1 eller senare installerat.

### <a name="join-a-domain"></a>Ansluta till en domän

Den virtuella datorn Azure Backup Server måste vara ansluten till en domän. En domän användare med administratörs behörighet på den virtuella datorn måste installera Azure Backup Server.

Azure Backup Server som distribueras i en virtuell Azure-dator kan säkerhetskopiera arbets belastningar på de virtuella datorerna i Azure VMware-lösningen. Arbets belastningarna måste vara i samma domän för att säkerhets kopieringen ska kunna utföras.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en lagrings enhet som lagrar återställnings punkterna som skapats med tiden. Den innehåller också säkerhets kopierings principer som är associerade med skyddade objekt.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

1. På menyn till vänster väljer du **Alla tjänster**.

   ![På menyn till vänster väljer du Alla tjänster.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. I dialog rutan **alla tjänster** anger **Recovery Services** och väljer **Recovery Services valv** i listan.

   ![Ange och välj Recovery Services valv.](../backup/media/backup-create-rs-vault/all-services.png)

   Listan över Recovery Services-valv i prenumerationen visas.

1. På instrumentpanelen **Recovery Services-valv** väljer du **Lägg till**.

   ![Lägg till ett Recovery Services-valv.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Dialogrutan **Recovery Services-valv** öppnas.

1. Ange värden för **namn**, **prenumeration**, **resurs grupp** och **plats**.

   ![Konfigurera Recovery Services-valvet.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två men högst 50 tecken. Namnet måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda, använder du standardprenumerationen (den föreslagna). Du kan bara välja mellan flera alternativ om ditt arbets- eller skolkonto är associerat med mer än en Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill se en lista med tillgängliga resursgrupper i prenumerationen, väljer du **Använd befintliga** och en resurs i listrutan. Skapa en ny resursgrupp genom att välja **Skapa ny** och ange namnet.
   - **Plats**: Välj ett geografiskt område för valvet. För att skapa ett valv för att skydda virtuella datorer i Azure VMware-lösningen *måste* valvet vara i samma region som Azure VMware-lösningens privata moln.

1. När du är redo att skapa Recovery Services-valvet väljer du **Skapa**.

   ![Skapa Recovery Services-valvet.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När du har skapat valvet visas det i listan över Recovery Services valv. Om du inte ser valvet väljer du **Uppdatera**.

   ![Uppdatera listan över säkerhets kopierings valv.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Ange Storage-replikering

Med alternativet lagrings replikering kan du välja mellan Geo-redundant lagring (standard) och lokalt redundant lagring. Geo-redundant lagring kopierar data i ditt lagrings konto till en sekundär region, vilket gör dina data varaktiga. Lokalt redundant lagring är ett billigare alternativ som inte är lika beständigt. Mer information om alternativ för Geo-redundant och lokalt redundant lagring finns i [Azure Storage redundans](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Det går inte att ändra inställningen för **lagrings typ lokalt, redundant/Geo-redundant** för ett Recovery Services valv måste göras innan du konfigurerar säkerhets kopieringar i valvet. När du har konfigurerat säkerhets kopior är alternativet för att ändra det inaktiverat och du kan inte ändra typen av lagrings replik.

1. Välj det nya valvet från **Recovery Services-valv**. 

1. Under **Inställningar** väljer du **Egenskaper**. Under **säkerhets kopierings konfiguration** väljer du **Uppdatera**.

1. Välj typ av lagrings replikering och välj **Spara**.

## <a name="download-and-install-the-software-package"></a>Hämta och installera programpaketet

Följ stegen i det här avsnittet för att ladda ned, extrahera och installera program varu paketet.

### <a name="download-the-software-package"></a>Ladda ned program varu paketet

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Om du redan har ett Recovery Services-valv öppet kan du fortsätta till nästa steg. Om du inte har ett Recovery Services-valv öppet och du är i Azure Portal väljer du **Bläddra** i huvud menyn.

   1. Ange **Recovery Services** i listan över resurser.

   1. När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services valv** väljer du det.

   ![Skapa Recovery Services Vault steg 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Välj ett valv i listan över Recovery Services-valv.

   Instrumentpanelen för det valda valvet öppnas.

   ![Instrumentpanelen för det valda valvet öppnas.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Alternativet **Inställningar** öppnas som standard. Om det stängs väljer du **Inställningar** för att öppna den.

   ![Alternativet Inställningar öppnas som standard. Om det stängs väljer du inställningar för att öppna den.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Välj **säkerhets kopiering** för att öppna guiden **komma igång** .

   ![Välj säkerhets kopiering för att öppna guiden Komma igång.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. I fönstret som öppnas:

   1. På menyn **där körs din arbets belastning? väljer du** **lokalt**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Var körs din arbets belastning?":::

   1. På menyn **vad vill du säkerhetskopiera?** väljer du de arbets belastningar som du vill skydda med hjälp av Azure Backup Server.

   1. Välj **Förbered infrastruktur** för att ladda ned och installera Azure Backup Server och autentiseringsuppgifterna för valvet.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Förbered infrastruktur":::

1. I fönstret **Förbered infrastruktur** öppnas:

   1. Välj länken **Hämta** för att installera Azure Backup Server.

   1. Välj **redan laddat ned eller Använd den senaste Azure Backup Server installationen** och **Ladda ned** för att ladda ned autentiseringsuppgifterna för valvet. Du använder dessa autentiseringsuppgifter när du registrerar Azure Backup Server i Recovery Services-valvet. Länkarna tar dig till Download Center där du laddar ned program varu paketet.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Förbered infrastruktur – Azure Backup Server":::

1. På sidan Hämta väljer du alla filer och väljer **Nästa**.

   > [!NOTE]
   > Du måste ladda ned alla filer till samma mapp. Eftersom fil hämtnings storleken är större än 3 GB kan det ta upp till 60 minuter innan nedladdningen är klar. 

   ![På sidan Hämta väljer du alla filer och väljer Nästa.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Extrahera programpaketet

Om du har laddat ned program varu paketet till en annan server kopierar du filerna till den virtuella dator som du skapade för att distribuera Azure Backup Server.

> [!WARNING]
> Det krävs minst 4 GB ledigt utrymme för att extrahera installationsfilerna.

1. När du har laddat ned alla filer dubbelklickar du på **MicrosoftAzureBackupInstaller.exe** för att öppna installations guiden för **Microsoft Azure Backup** och väljer sedan **Nästa**.

1. Välj platsen där filerna ska extraheras och välj **Nästa**.

1. Starta extraherings processen genom att välja **extrahera** .

   ![Starta extraherings processen genom att välja extrahera.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. När du har extraherat väljer du alternativet för att **köra setup.exe** och väljer sedan **Slutför**.

> [!TIP]
> Du kan också leta upp setup.exe-filen från den mapp där du extraherade program varu paketet.

### <a name="install-the-software-package"></a>Installera programpaketet

1. I installations fönstret under **Installera** väljer du **Microsoft Azure Backup** för att öppna installations guiden.

   ![I installations fönstret under installera väljer du Microsoft Azure Backup för att öppna installations guiden.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. På **välkomst** skärmen väljer du **Nästa** för att fortsätta till sidan **krav kontroll** .

1. Välj **kontrol lera igen** för att avgöra om maskin varan och program varan uppfyller kraven för Azure Backup Server. Välj **Nästa** om det är uppfyllt.

   ![ Välj kontrol lera igen för att avgöra om maskin varan och program varan uppfyller kraven för Azure Backup Server. Välj nästa om det är uppfyllt.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Installations paketet för Azure Backup Server levereras med lämpliga SQL Server binärfiler som behövs. När du startar en ny Azure Backup Server-installation väljer du alternativet **installera ny instans av SQL Server med detta installations** alternativ. Välj sedan **kontrol lera och installera**.

   ![Installations paketet för Azure Backup Server levereras med lämpliga SQL Server binärfiler som behövs.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Om du vill använda en egen SQL Server-instans är de SQL Server-versioner som stöds SQL Server 2014 SP1 eller högre, 2016 och 2017. Alla SQL Server-versioner ska vara standard eller Enterprise 64-bitars. Instansen som används av Azure Backup Server får endast vara lokal. den kan inte vara fjärran sluten. Om du använder en befintlig SQL Server-instans för Azure Backup Server, stöder installations programmet endast användningen av *namngivna instanser* av SQL Server.

   Om ett fel inträffar med en rekommendation för att starta om datorn gör du det och väljer **sedan kontrol lera igen**. Konfigurera om SQL Server enligt SQL Server rikt linjer för SQL Server konfigurations problem. Försök sedan att installera eller uppgradera Azure Backup Server med hjälp av den befintliga instansen av SQL Server.

   **Manuell konfiguration**

   När du använder en egen SQL Server-instans kontrollerar du att du lägger till builtin\Administrators i sysadmin-rollen till Master-databasens sysadmin-roll.

   **Konfigurera repor ting Services med SQL Server 2017**

   Om du använder din instans av SQL Server 2017 måste du konfigurera SQL Server 2017 repor ting Services (SSRS) manuellt. När du har konfigurerat SSRS, se till att ange egenskapen **IsInitialized** för SSRS till **True**. Om värdet är **True** förutsätter Azure Backup server att SSRS redan har kon figurer ATS och hoppar över SSRS-konfigurationen.

   Kontrol lera status för SSRS-konfigurationen genom att köra:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Använd följande värden för SSRS-konfiguration:

   * **Tjänst konto**: **Använd det inbyggda kontot** ska vara **nätverks tjänst**.
   * **Webb tjänst-URL**: den **virtuella katalogen** ska vara **ReportServer_ \<SQLInstanceName>**.
   * **Databas**: **databasename** ska vara **reportserver $ \<SQLInstanceName>**.
   * **Webb portal-URL**: den **virtuella katalogen** ska vara **Reports_ \<SQLInstanceName>**.

   [Läs mer](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) om SSRS-konfiguration.

   > [!NOTE]
   > [Microsoft Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products) (ost) styr licensieringen för SQL Server som används som databas för Azure Backup Server. Enligt OST används endast SQL Server paketerat med Azure Backup Server som databas för Azure Backup Server.

1. När installationen har slutförts väljer du **Nästa**.

1. Ange en plats för att installera Microsoft Azure Backup server-filer och välj **Nästa**.

   > [!NOTE]
   > Det krävs en arbets plats för att säkerhetskopiera till Azure. Se till att arbets platsen är minst 5% av de data som planeras för säkerhets kopiering till molnet. För disk skydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i Konfigurera lagringspooler [och disk lagring](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Ange en plats för installationen av Microsoft Azure Backup server-filer och välj Nästa.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Ange ett starkt lösen ord för begränsade lokala användar konton och välj **Nästa**.

   ![Ange ett starkt lösen ord för begränsade lokala användar konton och välj Nästa.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Välj om du vill använda Microsoft Update för att söka efter uppdateringar och välj **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att du Windows Update omdirigera till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Azure Backup Server.

   ![Välj om du vill använda Microsoft Update för att söka efter uppdateringar och välj Nästa.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Granska **sammanfattningen av inställningarna** och välj **Installera**.

   Installationen sker i faser. 
   - Den första fasen installerar Microsoft Azure Recovery Services agenten.
   - Den andra fasen kontrollerar Internet anslutningen. Om det är tillgängligt kan du fortsätta med installationen. Om detta inte är tillgängligt måste du ange information om proxyn för att ansluta till Internet. 
   - Den sista fasen kontrollerar den nödvändiga program varan. Om det inte är installerat installeras eventuell program vara som saknas, tillsammans med Microsoft Azure Recovery Services agenten.

1. Välj **Bläddra** för att hitta dina autentiseringsuppgifter för valvet för att registrera datorn i Recovery Services-valvet och välj sedan **Nästa**.

1. Välj en lösen fras för att kryptera eller dekryptera data som skickas mellan Azure och dina lokaler.

   > [!TIP]
   > Du kan generera en lösen fras automatiskt eller ange lösen frasen med minst 16 bokstäver.

1. Ange platsen där du vill spara lösen frasen och välj sedan **Nästa** för att registrera servern.

   > [!IMPORTANT]
   > Spara lösen frasen på en annan säker plats än den lokala servern. Vi rekommenderar starkt att du använder Azure Key Vault för att lagra lösen frasen.

   När installationen av Microsoft Azure Recovery Services-agenten är klar flyttas installations steget vidare till installationen och konfigurationen av SQL Server och Azure Backup Server-komponenterna.

   ![När installationen av Microsoft Azure Recovery Services-agenten är klar flyttas installations steget vidare till installationen och konfigurationen av SQL Server och Azure Backup Server-komponenterna.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. När installations steget har slutförts väljer du **Stäng**.

### <a name="install-update-rollup-1"></a>Installera Samlad uppdatering 1

Installation av Samlad uppdatering 1 för Azure Backup Server v3 är obligatoriskt innan du kan skydda arbets belastningarna.  Du hittar fel korrigeringar och installationsinstruktioner i [Knowledge Base-artikeln](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Lägga till lagringsutrymme på Azure Backup Server

Azure Backup Server v3 stöder Modern Backup Storage som erbjuder:

-  Lagrings besparingar på 50%.
-  Säkerhets kopieringar som är tre gånger snabbare.
-  Mer effektiv lagring.
-  Arbets belastnings medveten lagring.

### <a name="volumes-in-azure-backup-server"></a>Volymer i Azure Backup Server

Lägg till data diskarna med den Azure Backup Server VM: s nödvändiga lagrings kapacitet om den inte redan har lagts till.

Azure Backup Server v3 accepterar bara lagrings volymer. När du lägger till en volym, Azure Backup Server formaterar volymen till elastiskt fil system (ReFS) som Modern Backup Storage kräver.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Lägg till volymer i Azure Backup Server disk lagring

1. I **hanterings** fönstret, Genomsök lagringen på nytt och välj sedan **Lägg till**. 

1. Välj bland de tillgängliga volymer som ska läggas till i lagringspoolen. 

1. När du har lagt till de tillgängliga volymerna får du ett eget namn som hjälper dig att hantera dem. 

1. Välj **OK** för att formatera volymerna till ReFS så att Azure Backup Server kan använda modern backup Storage förmåner.


## <a name="next-steps"></a>Nästa steg

Nu när du har gått igenom hur du konfigurerar Azure Backup Server för Azure VMware-lösningen kanske du vill lära dig mer om:

- [Konfigurera säkerhets kopior för dina virtuella datorer med Azure VMware-lösningen](backup-azure-vmware-solution-virtual-machines.md).
- [Skydda dina virtuella datorer med Azure VMware-lösningen med Azure Security Center-integrering](azure-security-integration.md).
