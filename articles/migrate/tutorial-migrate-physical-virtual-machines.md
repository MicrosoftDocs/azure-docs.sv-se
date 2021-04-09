---
title: Migrera datorer som fysiska servrar till Azure med Azure Migrate.
description: I den här artikeln beskrivs hur du migrerar fysiska datorer till Azure med Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: aeeb810174ff5c21a81bcec8aa9265ff100edf91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626333"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrera datorer som fysiska servrar till Azure

Den här artikeln visar hur du migrerar datorer som fysiska servrar till Azure med hjälp av verktyget Azure Migrate: Migreringsverktyg för Server. Att migrera datorer genom att behandla dem som fysiska servrar är användbara i ett antal scenarier:

- Migrera lokala fysiska servrar.
- Migrera virtuella datorer som är virtualiserade med plattformar som xen, KVM.
- Migrera virtuella Hyper-V-eller VMware-datorer, om det av någon anledning inte går att använda standardmigreringen för [Hyper-v](tutorial-migrate-hyper-v.md)eller [VMware](server-migrate-overview.md) -migrering.
- Migrera virtuella datorer som körs i privata moln.
- Migrera virtuella datorer som körs i offentliga moln, till exempel Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar fysiska servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered för att använda Azure med Azure Migrate: Server-migrering.
> * Kontrol lera kraven för datorer som du vill migrera och Förbered en dator för Azure Migrate Replication-enheten som används för att identifiera och migrera datorer till Azure.
> * Lägg till verktyget Azure Migrate: Migreringsverktyg i Azure Migrate Hub.
> * Konfigurera replikerings enheten.
> * Installera mobilitets tjänsten på datorer som du vill migrera.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen how-TOS för Azure Migrate.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

[Granska](./agent-based-migration-architecture.md) migreringens arkitektur.

## <a name="prepare-azure"></a>Förbereda Azure

Förbered Azure för migrering med Azure Migrate: Server-migrering.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar-eller ägar behörighet för att [skapa ett nytt projekt](./create-manage-projects.md).
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst** och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.


### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela Azure-kontot rollen virtuell dator deltagare. Detta ger behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ett virtuellt Azure-nätverk (VNet). När du replikerar till Azure skapas och ansluts virtuella Azure-datorer till det virtuella Azure-nätverk som du anger när du konfigurerar migrering.

## <a name="prepare-for-migration"></a>Förbereda för migrering

För att förbereda för migrering av fysiska servrar måste du verifiera inställningarna för den fysiska servern och förbereda distributionen av en replikeringsfil.

### <a name="check-machine-requirements-for-migration"></a>Kontrol lera dator kraven för migrering

Kontrol lera att datorerna uppfyller kraven för migrering till Azure. 

> [!NOTE]
> När du migrerar fysiska datorer Azure Migrate: Server migrering använder samma replikeringsprincip som agentbaserade haveri beredskap i Azure Site Recovery-tjänsten, och vissa komponenter delar samma kodbas. En del innehåll kan länkas till Site Recovery-dokumentationen.

1. [Kontrol lera](migrate-support-matrix-physical-migration.md#physical-server-requirements) krav för fysisk server.
2. Kontrol lera att lokala datorer som du replikerar till Azure uppfyller [kraven för virtuella Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements)-datorer.
3. Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.
    - För vissa operativ system gör Azure Migrate dessa ändringar automatiskt. 
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure. Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikerings enheten

Azure Migrate: Server-migreringen använder en replikeringsfil för att replikera datorer till Azure. Replikeringssystemet kör följande komponenter.

- **Konfigurations** Server: konfigurations servern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cache Storage-konto i Azure. 

Förbered distribution av installationer enligt följande:

- Du förbereder en dator för att vara värd för replikerings enheten. [Granska](migrate-replication-appliance.md#appliance-requirements) maskin varu kraven.
- Replikerings enheten använder MySQL. Granska [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
- Granska de Azure-URL: er som krävs för att replikeringssystemet ska kunna komma åt [offentliga](migrate-replication-appliance.md#url-access) och [offentliga](migrate-replication-appliance.md#azure-government-url-access) moln.
- Granska [port](migrate-replication-appliance.md#port-access) åtkomst kraven för replikerings enheten.

> [!NOTE]
> Replikeringssystemet bör inte installeras på en käll dator som du vill replikera eller på Azure Migrate identifierings-och utvärderings installation som du kan ha installerat tidigare.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikerings enheten

Det första steget i migreringen är att konfigurera replikerings enheten. Om du vill konfigurera installations programmet för fysisk server-migrering, laddar du ned installations filen för installationen och kör den sedan på den [dator som du för beredde](#prepare-a-machine-for-the-replication-appliance). När du har installerat installationen registrerar du den med Azure Migrate: Server-migrering.


### <a name="download-the-replication-appliance-installer"></a>Ladda ned installations programmet för replikerings enheten

1. I **Azure Migrate: Server-migrering** i Azure Migrate Project >- **servrar** klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **inte virtualiserad/annan**.
4. I **mål region** väljer du den Azure-region som du vill migrera datorerna till.
5. Välj **Bekräfta att mål regionen för migrering är regions namn**.
6. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden.
    - Om du redan har konfigurerat migreringen med Azure Migrate: Server-migreringen kan du inte konfigurera mål alternativet eftersom resurserna har kon figurer ATS tidigare.    
    - Du kan inte ändra mål region för projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

7. I vill **du installera en ny replikeringsprincip? väljer du** **installera en replikeringsprincip**.
9. I **Hämta och installera installations programmet för replikering** laddar du ned installations programmet för installationen och registrerings nyckeln. Du behöver nyckeln för att kunna registrera installationen. Nyckeln är giltig i fem dagar efter att den har laddats ned.

    ![Hämta Provider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopiera installations filen för installationen och nyckel filen till Windows Server 2016-datorn som du skapade för installationen.
11. När installationen är klar startas konfigurations guiden för enheten automatiskt (du kan också starta guiden manuellt genom att använda cspsconfigtool-genvägen som skapas på Skriv bordet på enheten). Använd fliken Hantera konton i guiden för att lägga till konto information som ska användas för push-installation av mobilitets tjänsten. I den här självstudien kommer vi att manuellt installera mobilitets tjänsten på virtuella käll datorer som ska replikeras, så skapa ett dummy-konto i det här steget och fortsätt. Du kan ange följande information för att skapa vår dummy-konto – "gäst" som eget namn, "username" som användar namn och "lösen ord" som lösen ord för kontot. Du kommer att använda det här dummy-kontot i steget aktivera replikering. 

12. När installationen har startats om efter installationen går du till **identifiera datorer**, väljer den nya installationen i **Välj konfigurations Server** och klickar på **Slutför registrering**. Genom att slutföra registreringen utförs några slutliga uppgifter för att förbereda replikeringen.

    ![Slutför registrering](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Det kan ta lite tid efter att registreringen har slutförts tills identifierade datorer visas i Azure Migrate: Server-migrering. När virtuella datorer identifieras ökar antalet **identifierade servrar** .

![Identifierade servrar](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

På datorer som du vill migrera måste du installera mobilitets tjänst agenten. Agent installationerna är tillgängliga på replikerings enheten. Du hittar rätt installations program och installerar agenten på varje dator som du vill migrera. Gör på följande sätt:

1. Logga in på replikerings enheten.
2. Navigera till **%programdata%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installations programmet för datorns operativ system och version. Granska [operativ system som stöds](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Kopiera installations filen till den dator som du vill migrera.
5. Kontrol lera att du har den lösen fras som genererades när du distribuerade enheten.
    - Lagra filen i en tillfällig textfil på datorn.
    - Du kan hämta lösen frasen på replikerings enheten. Från kommando raden kör **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** för att visa den aktuella lösen frasen.
    - Återskapa inte lösen frasen. Detta bryter anslutningen och du måste registrera om replikerings enheten.

> [!NOTE]
> I */Platform* -parametern anger du *VMware* om du migrerar virtuella VMware-datorer eller fysiska datorer.

### <a name="install-on-windows"></a>Installera i Windows

1. Extrahera innehållet i installations filen till en lokal mapp (till exempel C:\Temp) på datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Kör installations programmet för mobilitets tjänsten:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Registrera agenten med replikerings enheten:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installera på Linux

1. Extrahera innehållet i installations tarball till en lokal mapp (till exempel/tmp/MobSvcInstaller) på datorn enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Kör installations skriptet:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Registrera agenten med replikerings enheten:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikera datorer

Välj datorer för migrering nu. 

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera mer replikerar du dem samtidigt i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering** klickar du på **Replikera**.

    ![Skärm bild av skärmen Azure Migrate-servrar som visar knappen replikera som marker ATS i Azure Migrate: migrering av Server under Migreringsverktyg.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera**, > **käll inställningar**  >  **att datorerna har virtualiserats?** väljer du **inte virtualiserad/övrigt**.
3. I **lokal** installation väljer du namnet på Azure Migrate-installationen som du konfigurerar.
4. I **processerver** väljer du namnet på replikerings enheten.
6. I **autentiseringsuppgifter för gäst** väljer du det dummy-konto som skapades tidigare under [installationen av installations programmet för replikering](#download-the-replication-appliance-installer) för att installera mobilitets tjänsten manuellt (push-installation stöds inte). Klicka sedan på **Nästa: virtuella datorer**.   

    ![Skärm bild av fliken käll inställningar på sidan replikera med fältet gäst autentiseringsuppgifter markerat.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. I **Virtual Machines**, i **Importera migreringsjobb från en utvärdering?**, lämnar du standardinställningen **Nej, jag anger inställningarna för migrering manuellt**.
8. Markera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
10. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
11. I **tillgänglighets alternativ** väljer du:
    -  Tillgänglighets zon för att fästa den migrerade datorn i en angiven tillgänglighets zon i regionen. Använd det här alternativet för att distribuera servrar som utgör en program nivå med flera noder i Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighets zon som ska användas för var och en av de valda datorerna på fliken beräkning. Det här alternativet är bara tillgängligt om det valda mål området för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighets uppsättning för att placera den migrerade datorn i en tillgänglighets uppsättning. Den valda mål resurs gruppen måste ha en eller flera tillgänglighets uppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturs redundans krävs om du inte behöver någon av dessa tillgänglighets konfigurationer för de migrerade datorerna.
    
12. I **disk krypterings typ** väljer du:
    - Kryptering – rest med plattforms hanterad nyckel
    - Kryptering – rest med kundhanterad nyckel
    - Double Encryption med plattforms hanterade och Kundhanterade nycklar

   > [!NOTE]
   > För att replikera virtuella datorer med CMK måste du [skapa en disk krypterings uppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) under mål resurs gruppen. Ett objekt med en disk krypterings uppsättning mappar Managed Disks till en Key Vault som innehåller den CMK som ska användas för SSE.
  
13. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-vmware/target-settings.png)

14. I **Compute** granskar du VM-namn, storlek, OS-disktyp och tillgänglighets konfiguration (om det valts i föregående steg). De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-storlek**: om du använder bedömnings rekommendationer visas den rekommenderade storleken i list rutan VM-storlek. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**.
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns.
    - **Tillgänglighets zon**: Ange den tillgänglighets zon som ska användas.
    - **Tillgänglighets uppsättning**: Ange den tillgänglighets uppsättning som ska användas.

![Beräknings inställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. I **diskar** anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium Managed Disks) i Azure. Klicka på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Disk inställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar, **Hantera**  >  **replikering av datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.

## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** startar du jobbet starta replikering. 
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.


Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.
![Övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I Server för **migrerings mål**  >    >  **Azure Migrate: Server migrering** klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar**  >  **Azure Migrate: Server-migrering** klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **migrera**  >  **Stäng virtuella datorer och utför en planerad migrering utan data förlust** väljer du **Ja**  >  **OK**.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
    
    Obs! för migrering av fysiska servrar är rekommendationen att sätta igång programmet som en del av migreringstabellen (låt inte programmen acceptera några anslutningar) och initiera sedan migreringen (servern måste vara igång, så återstående ändringar kan synkroniseras) innan migreringen har slutförts.

4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Det här gör följande:
    - Stoppar replikering för den lokala datorn.
    - Tar bort datorn från antalet **replikerade servrar** i Azure Migrate: Server-migrering.
    - Rensar information om replikeringstillståndet för datorn.
2. Installera [Azure VM-](../virtual-machines/extensions/agent-windows.md) eller [Linux](../virtual-machines/extensions/agent-linux.md) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](../security-center/security-center-just-in-time.md).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md).
    - Distribuera [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.
