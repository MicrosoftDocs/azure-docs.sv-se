---
title: Migrera virtuella VMware-datorer med agent-baserad Azure Migrate Server-migrering
description: Lär dig hur du kör en agent-baserad migrering av virtuella VMware-datorer med Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 480051680ea98b82627a9a2b3ea004d9472f7124
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797102"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrera virtuella VMware-datorer till Azure (agent-baserad)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) med agent-baserad migrering.  Du kan också migrera virtuella VMware-datorer med hjälp av en agent utan migrering. [Jämför](server-migrate-overview.md#compare-migration-methods) metoderna.


 I de här självstudierna får du lära dig att
> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered för agent-baserad migrering. Konfigurera ett VMware-konto så att Azure Migrate kan identifiera datorer för migrering. Konfigurera ett konto så att mobilitets tjänst agenten kan installeras på datorer som du vill migrera och Förbered en dator för att fungera som replikeringsfil.
> * Lägg till verktyget Azure Migrate: Migreringsverktyg för Server
> * Konfigurera replikerings enheten.
> * Replikera virtuella datorer.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien [granskar](./agent-based-migration-architecture.md) du VMware agent-baserad migrering.

## <a name="prepare-azure"></a>Förbereda Azure

Slutför uppgifterna i tabellen för att förbereda Azure för agent-baserad migrering.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar-eller ägar behörighet för att skapa ett projekt.
**Verifiera behörigheter för Azure-kontot** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.
**Skapa ett Azure-nätverk** | Konfigurera ett nätverk som virtuella Azure-datorer ska ansluta till efter migreringen.

### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt
Om du inte har ett Azure Migrate-projekt måste du kontrol lera behörigheterna för att skapa ett.


1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst** och klicka på det för att visa behörigheter.
3. Kontrol lera att du har behörighet som **deltagare** eller **ägare** .

    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.
    
### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela rollen virtuell dator deltagare till kontot så att du har behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till en Azure-hanterad disk. 


### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure Managed disks. När du växlar över till Azure för migrering skapas virtuella Azure-datorer från dessa hanterade diskar och ansluts till det Azure-nätverk som du har konfigurerat.

## <a name="prepare-for-migration"></a>Förbereda för migrering

Verifiera support krav och-behörigheter och Förbered för att distribuera en replikeringsprincip. 

### <a name="prepare-an-account-to-discover-vms"></a>Förbereda ett konto för att identifiera virtuella datorer

Azure Migrate Server-migrering behöver åtkomst till VMware-servrar för att identifiera virtuella datorer som du vill migrera. Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som **Azure_Migrate**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

#### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Uppgift** | **Roll/behörigheter** | **Information**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).
**Replikering** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, VMsa, nd-nätverk).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera.

- Azure Migrate Replication-enheten kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator, eller så kan du installera den manuellt eller använda installations verktyg.
- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För push-installation måste du förbereda ett konto som Azure Migrate Server-migrering kan använda för att få åtkomst till den virtuella datorn. Det här kontot används endast för push-installation om du inte installerar mobilitets tjänsten manuellt.

Förbered kontot enligt följande:

1. Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.
2. För virtuella Windows-datorer, om du inte använder ett domän konto, inaktiverar du åtkomst kontroll för fjärran vändare på den lokala datorn genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet i registret, under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Förbered ett rot konto på käll-Linux-servern för virtuella Linux-datorer.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikerings enheten

Enheten används för att replikera datorer till Azure. Installationen är enkel, lokal virtuell VMware-dator som är värd för dessa komponenter:

- **Konfigurations** Server: konfigurations servern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cache Storage-konto i Azure. Processervern installerar också mobilitets tjänst agenten på de virtuella datorer som du vill replikera och utför automatisk identifiering av lokala virtuella VMware-datorer.

Förbered för installationen enligt följande:

- [Granska kraven för produkten](migrate-replication-appliance.md#appliance-requirements). I allmänhet ställer du in replikeringstjänsten en virtuell VMware-dator med en Hämtad ägg fil. Mallen skapar en apparat som uppfyller alla krav.
- MySQL måste vara installerat på enheten. [Granska](migrate-replication-appliance.md#mysql-installation) installations metoder.
- Granska [URL: erna för offentliga moln](migrate-replication-appliance.md#url-access)och [Azure Government webb adresser](migrate-replication-appliance.md#azure-government-url-access) som datorn behöver åtkomst till.
- [Granska portarna](migrate-replication-appliance.md#port-access) som krävs för att datorn ska ha åtkomst till.



### <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Se till att VMware-servrar och virtuella datorer uppfyller kraven för migrering till Azure. 

1. [Kontrollera](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) VMware-serverkraven.
2. [Verifiera](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VM-krav för migrering.
3. Verifiera Azure-inställningar. Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för [virtuella Azure-datorer](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
    - Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.

> [!NOTE]
> Agent-baserad migrering med Azure Migrate Server-migrering baseras på funktionerna i tjänsten Azure Site Recovery. Vissa krav kan vara länkade till Site Recovery-dokumentationen.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikerings enheten

Den här proceduren beskriver hur du ställer in installationen med en Hämtad mall för öppen Virtualization-program (ägg). Om du inte kan använda den här metoden kan du konfigurera installationen [med hjälp av ett skript](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Ladda ned mallen för replikerings apparaten

Hämta mallen på följande sätt:

1. Klicka på **servrar** under **mål för migrering** i Azure Migrate projektet.
2. I **Azure Migrate-servrar**  >  **Azure Migrate: Server-migrering** klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **Ja, med VMware vSphere hypervisor**.
4. I **Hur vill du migrera? väljer du** **Använd agent-baserad replikering**.
5. I **mål region** väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att mål regionen för migrering är regions namn**.
7. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden. Du kan inte ändra mål regionen för projektet när du har klickat på den här knappen, och alla efterföljande migreringar är till den här regionen.

    ![Skapa Recovery Services-valv](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. I vill **du installera en ny replikeringsprincip? väljer du** **installera en replikeringsprincip**.
9. Klicka på **Hämta**. Detta laddar ned en OVF-mall.
    ![Hämta ägg](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Anteckna namnet på resurs gruppen och Recovery Services valvet. Du behöver dessa under installationen av produkten.


### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

När du har hämtat OVF-mallen importerar du den till VMware för att skapa replikeringssystemet på en virtuell VMware-dator som kör Windows Server 2016.

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMware vSphere-klienten.
2. I menyn **Arkiv** väljer du **distribuera OVF-mallen** för att starta **guiden Distribuera OVF-mall**. 
3. I **Välj källa** anger du platsen för den nedladdade OVF.
4. I **Granska information** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring**  >  **Välj virtuellt disk format** för bästa prestanda väljer du **tjock etablering Eager noll**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **klart att slutföra**, om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **ström när distributionen** är  >  **klar**.

   > [!TIP]
   > Om du vill lägga till ett extra nätverkskort rensar du **energi förbrukningen när distributionen** är  >  **klar**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

### <a name="start-appliance-setup"></a>Starta installationen av installationen

1. I VMware vSphere klient konsolen aktiverar du den virtuella datorn. Den virtuella datorn startas med en Windows Server 2016-installation.
2. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör med hjälp av administratörs lösen ordet. Första gången du loggar in startas installations verktyget för replikeringstjänsten (Azure Site Recovery konfigurations verktyget) inom några sekunder.
5. Ange ett namn som ska användas för att registrera enheten med Server migrering. Klicka på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.
7. Vänta tills verktyget har slutfört registreringen av en Azure AD-App för att identifiera installationen. Enheten startas om.
1. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="register-the-replication-appliance"></a>Registrera replikerings enheten

Slutför konfigurationen och registrera replik enheten.

1. I installations programmet för installationen väljer du **installations anslutning**.
2. Välj NÄTVERKSKORTet (som standard finns bara ett nätverkskort) som replikeringstjänsten använder för identifiering av virtuella datorer och för att göra en push-installation av mobilitets tjänsten på käll datorer.
3. Välj det nätverkskort som Replication-enheten använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har kon figurer ATS.
4. Om enheten finns bakom en proxyserver måste du ange proxyinställningar.
    - Ange namnet på proxyn som **http://ip-address** eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
5. När du tillfrågas om prenumerationen, resurs grupperna och valvet lägger du till den information som du antecknade när du laddade ned mallen installation.
6. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
7. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
8. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
9. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
10. Ange autentiseringsuppgifterna för det konto som du [skapade](#prepare-an-account-to-discover-vms) för VMware-identifiering. Välj **Lägg till**  >  **Fortsätt**.
11. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du de autentiseringsuppgifter som du [skapade](#prepare-an-account-for-mobility-service-installation) för push-installation av mobilitets tjänsten när du aktiverar replikering för virtuella datorer.  
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
12. Välj **Slutför konfigurationen** för att slutföra registreringen.


När replikeringstjänsten har registrerats ansluter Azure Migrate Server utvärderingen till VMware-servrar med de angivna inställningarna och identifierar virtuella datorer. Du kan visa identifierade virtuella datorer i **Hantera**  >  **identifierade objekt** på fliken **övrigt** .



## <a name="replicate-vms"></a>Replikera virtuella datorer

Välj virtuella datorer för migrering.

> [!NOTE]
> I portalen kan du välja upp till 10 datorer på en gång för replikering. Om du behöver replikera mer ska du gruppera dem i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering** klickar du på **Replikera**.

    ![Skärm bild av skärmen servrar i Azure Migrate. Knappen replikera är markerad i Azure Migrate: Server-migrering under Migreringsverktyg.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **lokal** installation väljer du namnet på Azure Migrate-installationen som du konfigurerar.
4. I **vCenter Server** anger du namnet på den vCenter-Server som hanterar de virtuella datorerna eller den vSphere-server som de virtuella datorerna finns på.
5. I **processerver** väljer du namnet på replikerings enheten.
6. I **autentiseringsuppgifter för gäst** anger du det administratörs konto för den virtuella datorn som ska användas för push-installation av mobilitets tjänsten. Klicka sedan på **Nästa: virtuella datorer**.

    ![Skärm bild av fliken käll inställningar på sidan replikera. Fältet gäst autentiseringsuppgifter är markerat och värdet är inställt på VM-admin-Account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. I **Virtual Machines** väljer du de datorer som du vill replikera.

    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.
8. I **tillgänglighets alternativ** väljer du:
    -  Tillgänglighets zon för att fästa den migrerade datorn i en angiven tillgänglighets zon i regionen. Använd det här alternativet för att distribuera servrar som utgör en program nivå med flera noder i Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighets zon som ska användas för var och en av de valda datorerna på fliken beräkning. Det här alternativet är bara tillgängligt om det valda mål området för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighets uppsättning för att placera den migrerade datorn i en tillgänglighets uppsättning. Den valda mål resurs gruppen måste ha en eller flera tillgänglighets uppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturs redundans krävs om du inte behöver någon av dessa tillgänglighets konfigurationer för de migrerade datorerna.
9. Markera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.
10. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
11. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
12. I **tillgänglighets alternativ** väljer du:
    -  Tillgänglighets zon för att fästa den migrerade datorn i en angiven tillgänglighets zon i regionen. Använd det här alternativet för att distribuera servrar som utgör en program nivå med flera noder i Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighets zon som ska användas för var och en av de valda datorerna på fliken beräkning. Det här alternativet är bara tillgängligt om det valda mål området för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighets uppsättning för att placera den migrerade datorn i en tillgänglighets uppsättning. Den valda mål resurs gruppen måste ha en eller flera tillgänglighets uppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturs redundans krävs om du inte behöver någon av dessa tillgänglighets konfigurationer för de migrerade datorerna.
    
13. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

14. I **Compute** granskar du VM-namn, storlek, OS-disktyp och tillgänglighets konfiguration (om det valts i föregående steg). De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **VM-storlek**: om du använder bedömnings rekommendationer visas den rekommenderade storleken i list rutan VM-storlek. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets zon**: Ange den tillgänglighets zon som ska användas.
    - **Tillgänglighets uppsättning**: Ange den tillgänglighets uppsättning som ska användas.

15. I **diskar** anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium Managed Disks) i Azure. Klicka på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

16. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar, **Hantera**  >  **replikering av datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.


## <a name="track-and-monitor"></a>Spåra och övervaka

1. Spåra jobb status i Portal meddelanden. 

    ![Spåra jobb](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.

    ![Övervaka replikering](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replikeringen sker på följande sätt:
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.


## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I Server för **migrerings mål**  >    >  **Azure Migrate: Server migrering** klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar**  >  **Azure Migrate: Server-migrering** klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **migrera**  >  **Stäng virtuella datorer och utför en planerad migrering utan data förlust** väljer du **Ja**  >  **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn för att säkerställa minsta möjliga data förlust. 
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Det här gör följande:
    - Stoppar replikering för den lokala datorn.
    - Tar bort datorn från antalet **replikerade servrar** i Azure Migrate: Server-migrering.
    - Rensar statusinformation om replikering för den virtuella datorn.
2. Installera [Azure VM-](../virtual-machines/extensions/agent-windows.md) eller [Linux](../virtual-machines/extensions/agent-linux.md) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- Lokalt
    - Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
    - Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
    - Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
    - Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.
- Justera inställningar för virtuella Azure-datorer efter migreringen:
    - [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security. När du migrerar virtuella VMare-datorer med agent-baserad migrering installerar mobilitets tjänstens installations program Azure VM-agent på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter migreringen.
    - Avinstallera mobilitets tjänsten manuellt från den virtuella Azure-datorn efter migreringen.
    - Avinstallera VMware-verktyg manuellt efter migreringen.
- I Azure:
    - Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
    - Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- Verksamhets kontinuitet/haveri beredskap
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