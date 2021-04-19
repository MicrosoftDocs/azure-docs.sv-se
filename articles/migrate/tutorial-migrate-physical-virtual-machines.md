---
title: Migrera datorer som fysisk server till Azure med Azure Migrate.
description: I den här artikeln beskrivs hur du migrerar fysiska datorer till Azure med Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: 1563543dec0a27094c00e446a205e94535e54229
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713541"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrera datorer som fysiska servrar till Azure

Den här artikeln visar hur du migrerar datorer som fysiska servrar till Azure med hjälp Azure Migrate: Server Migration. Att migrera datorer genom att behandla dem som fysiska servrar är användbart i ett antal scenarier:

- Migrera lokala fysiska servrar.
- Migrera virtuella datorer som virtualiserats av plattformar som Xen, KVM.
- Migrera virtuella Hyper-V- eller VMware-datorer om du av någon anledning inte kan använda standardmigreringsprocessen för [Hyper-V](tutorial-migrate-hyper-v.md)eller [VMware-migrering.](server-migrate-overview.md)
- Migrera virtuella datorer som körs i privata moln.
- Migrera virtuella datorer som körs i offentliga moln som Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


Den här självstudien är den tredje i en serie som visar hur du utvärderar och migrerar fysiska servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered för att använda Azure med Azure Migrate: Server Migration.
> * Kontrollera kraven för datorer som du vill migrera och förbered en dator för Azure Migrate replikeringsinstallation som används för att identifiera och migrera datorer till Azure.
> * Lägg till Azure Migrate: Server Migration i Azure Migrate hubben.
> * Konfigurera replikeringsinstallationen.
> * Installera tjänsten Mobility på datorer som du vill migrera.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudierna visar den enklaste distributionsvägen för ett scenario så att du snabbt kan konfigurera ett konceptbevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i Anvisningar för Azure Migrate.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

[Granska](./agent-based-migration-architecture.md) migreringsarkitekturen.

## <a name="prepare-azure"></a>Förbereda Azure

Förbereda Azure för migrering med Azure Migrate: Servermigrering.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar- eller ägarbehörighet för [att skapa ett nytt projekt.](./create-manage-projects.md)
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst hittar** du relevant konto och klickar på det för att visa behörigheter.
3. Du bör ha **behörighet som** deltagare **eller** ägare.
    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.


### <a name="assign-azure-account-permissions"></a>Tilldela Behörigheter för Azure-konto

Tilldela rollen Virtuell datordeltagare till Azure-kontot. Detta ger behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera ett](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuellt Azure-nätverk (VNet). När du replikerar till Azure skapas och anslöts virtuella Azure-datorer till det virtuella Azure-nätverk som du angav när du konfigurerade migreringen.

## <a name="prepare-for-migration"></a>Förbereda för migrering

För att förbereda för fysisk servermigrering måste du kontrollera inställningarna för den fysiska servern och förbereda distributionen av en replikeringsinstallation.

### <a name="check-machine-requirements-for-migration"></a>Kontrollera datorkraven för migrering

Kontrollera att datorerna uppfyller kraven för migrering till Azure. 

> [!NOTE]
> När du migrerar fysiska datorer Azure Migrate: Server Migration använder samma replikeringsarkitektur som agentbaserad haveriberedskap i Azure Site Recovery-tjänsten, och vissa komponenter delar samma kodbas. En del innehåll kan länka till Site Recovery dokumentation.

1. [Kontrollera](migrate-support-matrix-physical-migration.md#physical-server-requirements) kraven för fysisk server.
2. Kontrollera att lokala datorer som du replikerar till Azure uppfyller kraven för [virtuella Azure-datorer.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)
3. Det krävs vissa ändringar på virtuella datorer innan du migrerar dem till Azure.
    - För vissa operativsystem gör Azure Migrate dessa ändringar automatiskt. 
    - Det är viktigt att göra dessa ändringar innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure. Granska [de Windows-](prepare-for-migration.md#windows-machines) [och Linux-ändringar](prepare-for-migration.md#linux-machines) du behöver göra.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikeringsinstallationen

Azure Migrate: Server Migration använder en replikeringsinstallation för att replikera datorer till Azure. Replikeringsinstallationen kör följande komponenter.

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan den lokala platsen och Azure och hanterar datareplikering.
- **Processerserver:** Processerpservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cachelagringskonto i Azure. 

Förbered distributionen av installationen på följande sätt:

- Du förbereder en dator som värd för replikeringsinstallationen. [Granska](migrate-replication-appliance.md#appliance-requirements) datorkraven.
- Replikeringsinstallationen använder MySQL. Granska [alternativen för](migrate-replication-appliance.md#mysql-installation) att installera MySQL på installationen.
- Granska de Azure-URL:er som krävs för att replikeringsinstallationen ska [få åtkomst till](migrate-replication-appliance.md#url-access) offentliga moln [och](migrate-replication-appliance.md#azure-government-url-access) myndighetsmoln.
- Granska [portåtkomstkraven](migrate-replication-appliance.md#port-access) för replikeringsinstallationen.

> [!NOTE]
> Replikeringsinstallationen bör inte installeras på en källdator som du vill replikera eller på den Azure Migrate identifierings- och utvärderingsinstallation som du kanske har installerat tidigare.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikeringsinstallationen

Det första steget i migreringen är att konfigurera replikeringsinstallationen. Om du vill konfigurera installationen för migrering av fysisk server laddar du ned installationsfilen för installationen och kör den sedan på den [dator som du förberedde.](#prepare-a-machine-for-the-replication-appliance) När du har installerat installationen registrerar du den med Azure Migrate: Server Migration.


### <a name="download-the-replication-appliance-installer"></a>Ladda ned installationsprogrammet för replikeringsinstallationen

1. I det Azure Migrate projektet > **Server** i **Azure Migrate: Server Migration klickar** du på **Identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. I **Identifiera datorer**  >  **Virtualiseras dina datorer? klickar du** på Inte **virtualiserad/Övrigt.**
3. I **Målregion** väljer du den Azure-region som du vill migrera datorerna till.
4. Välj **Bekräfta att målregionen för migrering är regionnamnet**.
5. Klicka **på Skapa resurser.** Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate: Server Migration går det inte att konfigurera målalternativet eftersom resurser har konfigurerats tidigare.    
    - Du kan inte ändra målregionen för det här projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen. 
    > [!NOTE]
    > Om du valde privat slutpunkt som anslutningsmetod för Azure Migrate-projektet när det skapades, konfigureras Recovery Services-valvet även för anslutning till privat slutpunkt. Kontrollera att de privata slutpunkterna kan nås från replikeringsinstallationen. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. I **Vill du installera en ny replikeringsinstallation?** väljer du Installera en **replikeringsinstallation.**
7. I **Ladda ned och installera programvaran för replikeringsinstallationen** laddar du ned installationsprogrammet och registreringsnyckeln. Du behöver nyckeln för att kunna registrera installationen. Nyckeln är giltig i fem dagar efter att den har laddats ned.

    ![Hämtningsprovider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Kopiera installationsfilen och nyckelfilen till den Windows Server 2016-dator som du skapade för installationen.
9. När installationen är klar startas installationskonfigurationsguiden automatiskt (Du kan också starta guiden manuellt med genvägen cspsconfigtool som skapas på skrivbordet i installationen). Använd fliken Hantera konton i guiden för att lägga till kontoinformation som ska användas för push-installation av tjänsten Mobility. I den här självstudien installerar vi mobilitetstjänsten manuellt på de virtuella käll-datorer som ska replikeras, så skapa ett dummykonto i det här steget och fortsätt. Du kan ange följande information för att skapa dummy-kontot – "gäst" som eget namn, "användarnamn" som användarnamn och "lösenord" som lösenord för kontot. Du kommer att använda det här dummy-kontot i fasen Aktivera replikering. 

10. När installationen har startats om efter installationen går du till Identifiera datorer, väljer den nya installationen i **Välj konfigurationsserver** och klickar **på Slutför registreringen.** Slutför registreringen utför några sista uppgifter för att förbereda replikeringsinstallationen.

    ![Slutför registreringen](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Det kan ta en stund efter att registreringen har slutförs tills identifierade datorer visas Azure Migrate: Server Migration. När virtuella datorer identifieras ökar **antalet identifierade** servrar.

![Identifierade servrar](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

På datorer som du vill migrera måste du installera tjänsten Mobility agenten. Agentinstallationerna är tillgängliga i replikeringsinstallationen. Du hittar rätt installationsprogram och installerar agenten på varje dator som du vill migrera. Gör på följande sätt:

1. Logga in på replikeringsinstallationen.
2. Gå till **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installationsprogrammet för datorns operativsystem och version. Granska [operativsystem som stöds](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Kopiera installationsfilen till den dator som du vill migrera.
5. Kontrollera att du har den lösenfras som genererades när du distribuerade installationen.
    - Lagra filen i en temporär textfil på datorn.
    - Du kan hämta lösenfrasen i replikeringsinstallationen. Från kommandoraden kör du **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v för** att visa den aktuella lösenfrasen.
    - Återskapa inte lösenfrasen. Detta bryter anslutningen och du måste omregistrera replikeringsinstallationen.

> [!NOTE]
> I *parametern /Platform* anger du *VMware om* du migrerar virtuella VMware-datorer eller fysiska datorer.

### <a name="install-on-windows"></a>Installera i Windows

1. Extrahera innehållet i installationsfilen till en lokal mapp (till exempel C:\Temp) på datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Kör installationsprogrammet för mobilitetstjänsten:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Registrera agenten med replikeringsinstallationen:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installera på Linux

1. Extrahera innehållet i installationsprogrammets tarball-fil till en lokal mapp (till exempel /tmp/MobSvcInstaller) på datorn enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Kör installationsskriptet:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Registrera agenten med replikeringsinstallationen:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikera datorer

Välj nu datorer för migrering. 

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera fler replikerar du dem samtidigt i batchar om 10.

1. I projektet Azure Migrate , **>,** **Azure Migrate: Server Migration,** klickar du på **Replikera**.

    ![Skärmbild av skärmen Azure Migrate – Servrar som visar knappen Replikera som valts Azure Migrate: Servermigrering under Migreringsverktyg.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera** väljer >   >  **Källinställningar Virtualiseras dina datorer?**, väljer **du Inte virtualiserad/Övrigt.**
3. I **Lokal installation väljer** du namnet på den Azure Migrate som du har ställt in.
4. I **Processer server** väljer du namnet på replikeringsinstallationen.
5. I **Gästautentiseringsuppgifter** väljer du det [](#download-the-replication-appliance-installer) dummy-konto som skapades tidigare under installationsprogrammet för replikering för att installera tjänsten Mobility manuellt (push-installation stöds inte). Klicka sedan **på Nästa: Virtuella datorer.**   

    ![Skärmbild av fliken Källinställningar på skärmen Replikera med fältet Gästautentiseringsuppgifter markerat.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

6. I **Virtual Machines** i **Importera migreringsinställningar** från en utvärdering? lämnar du standardinställningen Nej, jag anger **migreringsinställningarna manuellt.**
7. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan **på Nästa: Målinställningar.**

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


8. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
9. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.   
10. I  **Cachelagringskonto** behåller du standardalternativet för att använda cachelagringskontot som skapas automatiskt för projektet. Använd listrutan om du vill ange ett annat lagringskonto som ska användas som cachelagringskonto för replikering. <br/> 
    > [!NOTE]
    >
    > - Om du valde privat slutpunkt som anslutningsmetod för Azure Migrate-projektet beviljar du Recovery Services-valvet åtkomst till cachelagringskontot. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Om du vill replikera med ExpressRoute med privat peering skapar du en privat slutpunkt för cachelagringskontot. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
  
11. I **Tillgänglighetsalternativ** väljer du:
    -  Tillgänglighetszon för att fästa den migrerade datorn till en specifik tillgänglighetszon i regionen. Använd det här alternativet för att distribuera servrar som utgör en programnivå med flera noder över Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighetszon som ska användas för var och en av den valda datorn på fliken Beräkning. Det här alternativet är endast tillgängligt om målregionen som valts för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighetsuppsättning för att placera den migrerade datorn i en tillgänglighetsuppsättning. Den valda målresursgruppen måste ha en eller flera tillgänglighetsuppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturredundans krävs om du inte behöver någon av dessa tillgänglighetskonfigurationer för de migrerade datorerna.
    
12. I **Diskkrypteringstyp** väljer du:
    - Kryptering i vila med plattforms hanterad nyckel
    - Kryptering i vila med kund hanterad nyckel
    - Dubbel kryptering med plattformsbaserade och kund hanterade nycklar

   > [!NOTE]
   > Om du vill replikera virtuella datorer med CMK måste du [skapa en diskkrypteringsuppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) under målresursgruppen. Ett objekt för diskkrypteringsuppsättning mappar Managed Disks en Key Vault som innehåller den CMK som ska användas för SSE.
  
13. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

14. I **Compute** granskar du vm-namnet, storleken, OS-disktypen och tillgänglighetskonfigurationen (om du valde det i föregående steg). De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-storlek:** Om du använder utvärderingsrekommendationer visar listrutan VM-storlek den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**.
    - **OS-disk:** Ange OS-disken (startdisken) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns.
    - **Tillgänglighetszon:** Ange den tillgänglighetszon som ska användas.
    - **Tillgänglighetsuppsättning:** Ange den tillgänglighetsuppsättning som ska användas.

![Beräkningsinställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. I **Diskar anger** du om de virtuella datordiskarna ska replikeras till Azure och väljer disktypen (standard SSD/HDD eller premium-hanterade diskar) i Azure. Klicka på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskinställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar, **Hantera**  >  **replikeringsdatorer.** Det går inte att ändra inställningarna efter att replikeringen har startat.

## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** börjar jobbet Starta replikering. 
- När jobbet Starta replikering har slutförts påbörjar datorerna den inledande replikeringen till Azure.
- När den inledande replikeringen är klar påbörjas deltareplikeringen. Inkrementella ändringar av lokala diskar replikeras regelbundet till replikdiskarna i Azure.


Du kan spåra jobbstatus i portalmeddelandena.

Du kan övervaka replikeringsstatusen genom att **klicka på Replikera servrar** i **Azure Migrate: Server Migration**.
![Övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- När du kör en testmigrering kontrolleras att migreringen fungerar som förväntat, utan att de lokala datorerna påverkas, fortsätter att fungera och replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligtvis migrera till ett icke-produktions-VNet i din Azure-prenumeration).
- Du kan använda den replikerade virtuella Azure-testdatorn för att verifiera migreringen, utföra apptestning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål**  >  **Servrar**  >  **Azure Migrate: Servermigrering** klickar du **på Testa migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. I det Azure Migrate projektet > **Servers**  >  **Azure Migrate: Server Migration (Servermigrering)** **klickar du på Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera**  >  **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja**  >  **OK.**
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
    
    Obs! Vid fysisk servermigrering rekommenderar vi att du tar bort programmet som en del av migreringsfönstret (låt inte programmen acceptera några anslutningar) och sedan initiera migreringen (servern måste köras så att återstående ändringar kan synkroniseras) innan migreringen har slutförts.

4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella > **Stoppa migreringen.** Det här gör följande:
    - Stoppar replikeringen för den lokala datorn.
    - Tar bort datorn från antalet **replikerade servrar i** Azure Migrate: Servermigrering.
    - Rensar information om replikeringstillstånd för datorn.
2. Installera Azure VM [Windows-](../virtual-machines/extensions/agent-windows.md) eller [Linux-agenten](../virtual-machines/extensions/agent-linux.md) på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Minska trafik till den migrerade virtuella Azure-datorinstansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metodtips efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa åtkomsten till inkommande trafik [med Azure Security Center – Just-in-time-administration](../security-center/security-center-just-in-time.md).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md).
    - Distribuera [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [molnmigreringsresan](/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
