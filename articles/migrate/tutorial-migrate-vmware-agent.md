---
title: Migrera virtuella VMware-datorer med agentbaserad Azure Migrate Server Migration
description: Lär dig hur du kör en agentbaserad migrering av virtuella VMware-datorer med Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 60b58f7cf67a22e019ff186e4e1811ff5b001d84
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714460"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrera virtuella VMware-datorer till Azure (agentbaserade)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp [av verktyget Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) med agentbaserad migrering.  Du kan också migrera virtuella VMware-datorer med agentlös migrering. [Jämför](server-migrate-overview.md#compare-migration-methods) metoderna.


 I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered för agentbaserad migrering. Konfigurera ett VMware-konto så att Azure Migrate kan identifiera datorer för migrering. Konfigurera ett konto så att tjänsten Mobility-agenten kan installeras på datorer som du vill migrera och förbereda en dator för att fungera som replikeringsinstallation.
> * Lägg till Azure Migrate:Server Migration
> * Konfigurera replikeringsinstallationen.
> * Replikera virtuella datorer.
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan konfigurera ett konceptbevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien [bör du](./agent-based-migration-architecture.md) granska VMware-agentbaserad migreringsarkitektur.

## <a name="prepare-azure"></a>Förbereda Azure

Slutför uppgifterna i tabellen för att förbereda Azure för agentbaserad migrering.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar- eller ägarbehörighet för att skapa ett projekt.
**Verifiera Azure-kontobehörigheter** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.
**Skapa ett Azure-nätverk** | Konfigurera ett nätverk som virtuella Azure-datorer ska ansluta till efter migreringen.

### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt
Om du inte har ett projekt Azure Migrate verifierar du behörigheterna för att skapa ett.


1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst,** leta upp relevant konto och klicka på det för att visa behörigheter.
3. Kontrollera att du har **behörigheten** Deltagare **eller** Ägare.

    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.
    
### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela rollen Virtuell datordeltagare till kontot så att du har behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till en Azure-hanterad disk. 


### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure-hanterade diskar. När du redundansar till Azure för migrering skapas virtuella Azure-datorer från dessa hanterade diskar och är ansluten till det Azure-nätverk som du har skapat.

## <a name="prepare-for-migration"></a>Förbereda för migrering

Kontrollera supportkraven och behörigheterna och förbered distributionen av en replikeringsinstallation. 

### <a name="prepare-an-account-to-discover-vms"></a>Förbereda ett konto för att identifiera virtuella datorer

Azure Migrate Server Migration behöver åtkomst till VMware-servrar för att identifiera virtuella datorer som du vill migrera. Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn, till exempel **Azure_Migrate**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

#### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Uppgift** | **Roll/behörigheter** | **Information**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill  begränsa åtkomsten  tilldelar du rollen Ingen åtkomst med Sprid till underobjekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).
**Replikering** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill  begränsa åtkomsten  tilldelar du rollen Ingen åtkomst med Sprid till underobjekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera.

- Den Azure Migrate replikeringsinstallationen kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator, eller så kan du installera den manuellt eller med hjälp av installationsverktyg.
- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För push-installation måste du förbereda ett konto som Azure Migrate Server Migration kan använda för att komma åt den virtuella datorn. Det här kontot används endast för push-installationen om du inte installerar tjänsten Mobility manuellt.

Förbered kontot enligt följande:

1. Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.
2. Om du inte använder ett domänkonto för virtuella Windows-datorer inaktiverar du kontrollen för fjärråtkomst på den lokala datorn genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy** med värdet i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. För virtuella Linux-datorer förbereder du ett rotkonto på Linux-källservern.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikeringsinstallationen

Installationen används för att replikering av datorer till Azure. Installationen är en enda lokal virtuell VMware-dator med hög tillgänglighet som är värd för dessa komponenter:

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan den lokala platsen och Azure och hanterar datareplikering.
- **Processerserver:** Processerpservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cachelagringskonto i Azure. Processervren installerar även mobilitetstjänstagenten på de virtuella datorer som du vill replikera och utför automatisk identifiering av lokala virtuella VMware-datorer.

Förbered för installationen på följande sätt:

- [Granska installationskraven](migrate-replication-appliance.md#appliance-requirements). I allmänhet ställer du in replikeringsinstallationen på en virtuell VMware-dator med hjälp av en nedladdad OVA-fil. Mallen skapar en apparat som uppfyller alla krav.
- MySQL måste vara installerat på enheten. [Granska](migrate-replication-appliance.md#mysql-installation) installationsmetoder.
- Granska [URL:erna för offentliga](migrate-replication-appliance.md#url-access)moln [och Azure Government url:er](migrate-replication-appliance.md#azure-government-url-access) som enhetens dator behöver åtkomst till.
- [Granska de portar](migrate-replication-appliance.md#port-access) som replikeringsinstallationsdatorn behöver åtkomst till.



### <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Kontrollera att VMware-servrar och virtuella datorer uppfyller kraven för migrering till Azure. 

1. [Kontrollera](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) VMware-serverkraven.
2. [Verifiera](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VM-krav för migrering.
3. Verifiera Azure-inställningarna. Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för [virtuella Azure-datorer.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)
4. Det krävs vissa ändringar på virtuella datorer innan du migrerar dem till Azure.
    - Det är viktigt att göra dessa ändringar innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
    - Granska [de Windows-](prepare-for-migration.md#windows-machines) [och Linux-ändringar](prepare-for-migration.md#linux-machines) du behöver göra.

> [!NOTE]
> Agentbaserad migrering med Azure Migrate Server Migration baseras på funktionerna i Azure Site Recovery tjänsten. Vissa krav kan länka till Site Recovery dokumentationen.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikeringsinstallationen

Den här proceduren beskriver hur du ställer in installationen med en nedladdad mall för Open Virtualization Application (OVA). Om du inte kan använda den här metoden kan du konfigurera installationen med [hjälp av ett skript](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Ladda ned mallen för replikeringsinstallationen

Ladda ned mallen på följande sätt:

1. Klicka Azure Migrate under **Migreringsmål** i **projektet**.
2. I **Azure Migrate – Servrar**  >  **Azure Migrate: Servermigrering** klickar du på **Identifiera.**

    ![Identifiera virtuella datorer](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. I **Identifiera datorer** Är dina datorer  >  **virtualiserade?** klickar du på **Ja, VMware vSphere hypervisor.**
4. I **Hur vill du migrera? väljer** du Använda **agentbaserad replikering**.
5. I **Målregion** väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att målregionen för migrering är regionnamnet**.
7. Klicka **på Skapa resurser.** Detta skapar Azure Site Recovery ett valv i bakgrunden. Du kan inte ändra målregionen för det här projektet när du har klickat på den här knappen och alla efterföljande migreringar är till den här regionen.

    ![Skapa Recovery Services-valv](./media/tutorial-migrate-vmware-agent/create-resources.png)  

    > [!NOTE]
    > Om du valde den privata slutpunkten som anslutningsmetod för Azure Migrate-projektet när det skapades, konfigureras Recovery Services-valvet även för anslutning till privat slutpunkt. Kontrollera att de privata slutpunkterna kan nås från replikeringsinstallationen: [ **Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)


8. I **Vill du installera en ny replikeringsinstallation?** väljer du Installera en **replikeringsinstallation.**
9. Klicka på **Hämta**. Då laddas en OVF-mall ned.
    ![Ladda ned OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Anteckna namnet på resursgruppen och Recovery Services-valvet. Du behöver dem under installationens distribution.


### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

När du har hämtat OVF-mallen importerar du den till VMware för att skapa replikeringsprogrammet på en virtuell VMware-dator som kör Windows Server 2016.

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMware vSphere klienten.
2. På **arkivmenyn** väljer du Distribuera **OVF-mall för** att starta **guiden Distribuera OVF-mall.** 
3. I **Välj källa** anger du platsen för den nedladdade OVF:en.
4. I **Granska information** väljer du **Nästa.**
5. I **Välj namn och mapp** och Välj **konfiguration** godkänner du standardinställningarna.
6. I **Välj lagring väljer** du  >  **virtuellt diskformat** för bästa prestanda genom att välja Thick Provision Eager **Zeroed**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klar att slutföra väljer** du Starta efter distributionen Slutför för att konfigurera den virtuella datorn med   >  **standardinställningarna.**

   > [!TIP]
   > Om du vill lägga till ytterligare ett nätverkskort avmarkerar du **Ström på efter**  >  **distributionen Slutför.** Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

### <a name="start-appliance-setup"></a>Starta installationen

1. I VMware vSphere-klientkonsolen aktiverar du den virtuella datorn. Den virtuella datorn startas med en Windows Server 2016-installation.
2. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör med administratörslösenordet. Första gången du loggar in startar konfigurationsverktyget för replikeringsinstallationen (Azure Site Recovery Configuration Tool) inom några sekunder.
5. Ange ett namn som ska användas för att registrera installationen med Server Migration. Klicka på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.
7. Vänta tills verktyget har registrerat en Azure AD-app för att identifiera installationen. Installationen startas om.
1. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="register-the-replication-appliance"></a>Registrera replikeringsinstallationen

Slutför installationen av och registreringen av replikeringsinstallationen.

1. I installationen väljer du **Konfigurera anslutning**.
2. Välj nätverkskortet (som standard finns det bara ett nätverkskort) som replikeringsinstallationen använder för identifiering av virtuella datorer och för att göra en push-installation av tjänsten Mobility på källdatorerna.
3. Välj det nätverkskort som replikeringsinstallationen använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
4. Om installationen finns bakom en proxyserver måste du ange proxyinställningar.
    - Ange proxynamnet som **http://ip-address** eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
5. När du tillfrågas om prenumeration, resursgrupper och valvinformation lägger du till den information som du antecknade när du laddade ned mallinstallationen.
6. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
7. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
8. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
9. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
10. Ange autentiseringsuppgifterna för det konto som du [skapade](#prepare-an-account-to-discover-vms) för VMware-identifiering. Välj **Lägg till**  >  **Fortsätt.**
11. I **Konfigurera autentiseringsuppgifter för** virtuell [](#prepare-an-account-for-mobility-service-installation) dator anger du de autentiseringsuppgifter som du skapade för push-installation av tjänsten Mobility när du aktiverar replikering för virtuella datorer.  
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
12. Välj **Slutför konfigurationen** för att slutföra registreringen.


När replikeringsinstallationen har registrerats ansluter Azure Migrate Server Assessment till VMware-servrar med de angivna inställningarna och identifierar virtuella datorer. Du kan visa identifierade virtuella datorer i  >  **Hantera identifierade objekt** på **fliken** Övrigt.



## <a name="replicate-vms"></a>Replikera virtuella datorer

Välj virtuella datorer för migrering.

> [!NOTE]
> I portalen kan du välja upp till 10 datorer samtidigt för replikering. Om du behöver replikera fler grupperar du dem i batchar om 10.

1. I projektet Azure Migrate , **>,** **Azure Migrate: Server Migration,** klickar du på **Replikera**.

    ![Skärmbild av skärmen Servrar i Azure Migrate. Knappen Replikera väljs i följande Azure Migrate: Servermigrering under Migreringsverktyg.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **Lokal installation väljer** du namnet på den Azure Migrate som du har ställt in.
4. I **vCenter-servern** anger du namnet på den vCenter-server som hanterar de virtuella datorerna eller den vSphere-server som de virtuella datorerna finns på.
5. I **Processer server** väljer du namnet på replikeringsinstallationen.
6. I **Gästautentiseringsuppgifter** anger du det VM-administratörskonto som ska användas för push-installation av tjänsten Mobility. Klicka sedan **på Nästa: Virtuella datorer.**

    ![Skärmbild av fliken Källinställningar på skärmen Replikera. Fältet Gästautentiseringsuppgifter är markerat och värdet är inställt på VM-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. I **Virtual Machines** väljer du de datorer som du vill replikera.

    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.
8. I **Tillgänglighetsalternativ** väljer du:
    -  Tillgänglighetszon för att fästa den migrerade datorn till en specifik tillgänglighetszon i regionen. Använd det här alternativet för att distribuera servrar som utgör en programnivå med flera noder över Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighetszon som ska användas för var och en av den valda datorn på fliken Beräkning. Det här alternativet är endast tillgängligt om målregionen som valts för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighetsuppsättning för att placera den migrerade datorn i en tillgänglighetsuppsättning. Den valda målresursgruppen måste ha en eller flera tillgänglighetsuppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturredundans krävs om du inte behöver någon av dessa tillgänglighetskonfigurationer för de migrerade datorerna.
9. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan **på Nästa: Målinställningar.**
10. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
11. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.  
12. I  **Cachelagringskonto** behåller du standardalternativet för att använda cachelagringskontot som skapas automatiskt för projektet. Använd listrutan om du vill ange ett annat lagringskonto som ska användas som cachelagringskonto för replikering. <br/> 
    > [!NOTE]
    >
    > - Om du valde privat slutpunkt som anslutningsmetod för Azure Migrate-projektet beviljar du Recovery Services-valvet åtkomst till cachelagringskontot. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Om du vill replikera med ExpressRoute med privat peering skapar du en privat slutpunkt för cachelagringskontot. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
13. I **Tillgänglighetsalternativ** väljer du:
    -  Tillgänglighetszon för att fästa den migrerade datorn till en specifik tillgänglighetszon i regionen. Använd det här alternativet för att distribuera servrar som utgör en programnivå med flera noder över Tillgänglighetszoner. Om du väljer det här alternativet måste du ange vilken tillgänglighetszon som ska användas för var och en av den valda datorn på fliken Beräkning. Det här alternativet är endast tillgängligt om målregionen som valts för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighetsuppsättning för att placera den migrerade datorn i en tillgänglighetsuppsättning. Den valda målresursgruppen måste ha en eller flera tillgänglighetsuppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturredundans krävs om du inte behöver någon av dessa tillgänglighetskonfigurationer för de migrerade datorerna.
14. I **Diskkrypteringstyp** väljer du:
    - Kryptering i vila med plattformsstyrd nyckel
    - Kryptering i vila med kund hanterad nyckel
    - Dubbel kryptering med plattforms-hanterade och kund-hanterade nycklar

   > [!NOTE]
   > Om du vill replikera virtuella datorer med CMK måste du [skapa en diskkrypteringsuppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) under målresursgruppen. Ett objekt för diskkrypteringsuppsättning mappar Managed Disks en Key Vault som innehåller den CMK som ska användas för SSE.
  
15. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-vmware/target-settings.png)

16. I **Compute** granskar du vm-namnet, storleken, OS-disktypen och tillgänglighetskonfigurationen (om du valde det i föregående steg). De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **VM-storlek:** Om du använder utvärderingsrekommendationer visar listrutan VM-storlek den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk:** Ange OS-disken (startdisken) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighetszon:** Ange den tillgänglighetszon som ska användas.
    - **Tillgänglighetsuppsättning:** Ange den tillgänglighetsuppsättning som ska användas.

17. I **Diskar anger** du om de virtuella datordiskarna ska replikeras till Azure och väljer disktypen (standard SSD/HDD eller premium-hanterade diskar) i Azure. Klicka på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

18. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar, **Hantera**  >  **replikeringsdatorer.** Det går inte att ändra inställningarna efter att replikeringen har startat.


## <a name="track-and-monitor"></a>Spåra och övervaka

1. Spåra jobbstatus i portalmeddelanden. 

    ![Spåra jobb](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Om du vill övervaka replikeringsstatus **klickar du på Replikera servrar** i **Azure Migrate: Server Migration**.

    ![Övervaka replikering](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replikeringen sker på följande sätt:
- När jobbet Starta replikering har slutförts påbörjar datorerna den inledande replikeringen till Azure.
- När den inledande replikeringen är klar påbörjas deltareplikeringen. Inkrementella ändringar av lokala diskar replikeras regelbundet till replikdiskarna i Azure.


## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- När du kör en testmigrering kontrolleras att migreringen fungerar som förväntat, utan att de lokala datorerna påverkas, som fortfarande fungerar och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligtvis migrera till ett icke-produktions-VNet i din Azure-prenumeration).
- Du kan använda den replikerade virtuella Azure-testdatorn för att verifiera migreringen, utföra apptestning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål**  >  **Servrar**  >  **Azure Migrate: Servermigrering** klickar du **på Testa migrerade servrar.**

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. I det Azure Migrate projektet > **Servers**  >  **Azure Migrate: Server Migration (Servermigrering)** **klickar du på Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera**  >  **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja**  >  **OK.**
    - Som standard Azure Migrate den lokala virtuella datorn för att säkerställa minsta möjliga dataförlust. 
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella > **stoppa migreringen.** Det här gör följande:
    - Stoppar replikeringen för den lokala datorn.
    - Tar bort datorn från antalet **replikerade servrar i** Azure Migrate: Servermigrering.
    - Rensar replikeringstillståndsinformation för den virtuella datorn.
2. Installera Azure VM [Windows-](../virtual-machines/extensions/agent-windows.md) eller [Linux-agenten](../virtual-machines/extensions/agent-linux.md) på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Minska trafik till den migrerade virtuella Azure-datorinstansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metodtips efter migreringen

- Lokalt
    - Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
    - Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
    - Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
    - Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.
- Justera inställningarna för virtuella Azure-datorer efter migreringen:
    - [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security. När du migrerar virtuella VMare-datorer med agentbaserad migrering installerar installationsprogrammet för mobilitetstjänsten Azure VM-agenten på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter migreringen.
    - Avinstallera programmet manuellt tjänsten Mobility den virtuella Azure-datorn efter migreringen.
    - Avinstallera VMware-verktyg manuellt efter migreringen.
- I Azure:
    - Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
    - Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- Affärskontinui/haveriberedskap
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