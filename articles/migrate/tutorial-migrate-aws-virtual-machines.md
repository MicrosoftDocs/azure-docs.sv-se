---
title: Identifiera, utvärdera och migrera virtuella Amazon Web Services (AWS) EC2 till Azure
description: Den här artikeln beskriver hur du migrerar virtuella AWS-datorer till Azure med Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 4879c8370953a5ac8c6b46efe8010db9692d3052
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714514"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Upptäck, utvärdera och migrera virtuella AWS-datorer (Amazon Web Services) till Azure

Den här självstudien visar hur du identifierar, utvärderar och migrerar virtuella AWS-datorer (Amazon Web Services) till virtuella Azure-datorer med hjälp av Azure Migrate: Server Assessment och Azure Migrate: Server Migration Tools.

> [!NOTE]
> Du migrerar virtuella AWS-datorer till Azure genom att behandla dem som fysiska servrar.

I den här självstudien får du lära dig hur man:
> [!div class="checklist"]
>
> * Kontrollera förhandskrav för migrering.
> * Förbereda Azure-resurser med Azure Migrate: Servermigrering. Konfigurera behörigheter för ditt Azure-konto och dina resurser så att de fungerar med Azure Migrate.
> * Förbereda AWS EC2-instanser för migrering.
> * Lägg till Azure Migrate: Server Migration i Azure Migrate hubben.
> * Konfigurera replikeringsinstallationen och distribuera konfigurationsservern.
> * Installera tjänsten Mobility virtuella AWS-datorer som du vill migrera.
> * Aktivera replikering för virtuella datorer.
> * Spåra och övervaka replikeringsstatusen. 
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="discover-and-assess"></a>Identifiera och utvärdera

Innan du migrerar till Azure rekommenderar vi att du utför en identifiering och migrering av virtuella datorer. Den här utvärderingen hjälper dig att ändra storlek på dina virtuella AWS-datorer för migrering till Azure och uppskatta potentiella Azure-körningskostnader.

Konfigurera en utvärdering på följande sätt:

1. Följ [självstudien](./tutorial-discover-physical.md) för att konfigurera Azure och förbereda dina virtuella AWS-datorer för en utvärdering. Tänk på följande:

    - Azure Migrate använder lösenordsautentisering vid identifiering av AWS-instanser. AWS-instanser stöder inte lösenordsautentisering som standard. Innan du kan identifiera en instans måste du aktivera lösenordsautentisering.
        - För Windows-datorer tillåter du WinRM-port 5985 (HTTP). Detta tillåter fjärr-WMI-anrop.
        - För Linux-datorer:
            1. Logga in på varje Linux-dator.
            2. Öppna filen sshd_config : vi /etc/ssh/sshd_config
            3. Leta upp raden **PasswordAuthentication** i filen och ändra värdet till **ja.**
            4. Spara filen och stäng den. Starta om ssh-tjänsten.
    - Om du använder en rotanvändare för att identifiera dina virtuella Linux-datorer ser du till att rotinloggning tillåts på de virtuella datorerna.
        1. Logga in på varje Linux-dator
        2. Öppna filen sshd_config : vi /etc/ssh/sshd_config
        3. Leta upp raden **PermitRootLogin i filen** och ändra värdet till **ja.**
        4. Spara filen och stäng den. Starta om ssh-tjänsten.

2. Följ sedan den här [självstudien](./tutorial-assess-physical.md) för att konfigurera ett Azure Migrate och installation för att identifiera och utvärdera dina virtuella AWS-datorer.

Även om vi rekommenderar att du provar en utvärdering är det inte obligatoriskt att utföra en utvärdering för att kunna migrera virtuella datorer.



## <a name="prerequisites"></a>Förutsättningar 

- Kontrollera att de virtuella AWS-datorer som du vill migrera kör en operativsystemversion som stöds. Virtuella AWS-datorer behandlas som fysiska datorer vid migreringen. Granska operativsystem [och kernelversioner som stöds för arbetsflödet](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för migrering av fysisk server. Du kan använda standardkommandon *som hostnamectl* eller *uname -a* för att kontrollera operativsystem- och kernel-versionerna för dina virtuella Linux-datorer.  Vi rekommenderar att du utför en testmigrering (testa redundans) för att verifiera om den virtuella datorn fungerar som förväntat innan du fortsätter med den faktiska migreringen.
- Kontrollera att dina virtuella AWS-datorer uppfyller de [konfigurationer som stöds](./migrate-support-matrix-physical-migration.md#physical-server-requirements) för migrering till Azure.
- Kontrollera att de virtuella AWS-datorerna som du replikerar till Azure uppfyller kraven för [virtuella Azure-datorer.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- Det krävs vissa ändringar på de virtuella datorerna innan du migrerar dem till Azure.
    - För vissa operativsystem gör Azure Migrate dessa ändringar automatiskt.
    - Det är viktigt att göra dessa ändringar innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
Granska [de Windows-](prepare-for-migration.md#windows-machines) [och Linux-ändringar](prepare-for-migration.md#linux-machines) du behöver göra.

### <a name="prepare-azure-resources-for-migration"></a>Förbereda Azure-resurser för migrering

Förbered Azure för migrering med Azure Migrate: Server Migration Tool.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto måste ha behörigheten Deltagare eller Ägare för [att skapa ett nytt projekt.](./create-manage-projects.md)
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en azure-hanterad disk.

### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst,** leta upp relevant konto och klicka på det för att visa behörigheter.
3. Du bör ha **behörighet som** deltagare **eller** ägare.
    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.

### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela rollen Virtuell datordeltagare till Azure-kontot. Detta ger behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera ett](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuellt Azure-nätverk (VNet). När du replikerar till Azure är de virtuella Azure-datorer som skapas ansluten till det virtuella Azure-nätverk som du anger när du konfigurerade migreringen.

## <a name="prepare-aws-instances-for-migration"></a>Förbereda AWS-instanser för migrering

För att förbereda för migrering från AWS till Azure måste du förbereda och distribuera en replikeringsinstallation för migrering.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikeringsinstallationen

Azure Migrate: Server Migration använder en replikeringsinstallation för att replikera datorer till Azure. Replikeringsinstallationen kör följande komponenter.

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan AWS-miljön och Azure och hanterar datareplikering.
- **Processerserver:** Processerpservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till ett cachelagringskonto i Azure.

Förbered distributionen av installationen på följande sätt:

- Konfigurera en separat virtuell EC2-dator som värd för replikeringsinstallationen. Den här instansen måste köra Windows Server 2012 R2 eller Windows Server 2016. [Granska](./migrate-replication-appliance.md#appliance-requirements) maskinvaru-, programvaru- och nätverkskraven för installationen.
- Installationen bör inte installeras på en virtuell källdatorn som du vill replikera eller på den Azure Migrate identifierings- och utvärderingsinstallation som du kanske har installerat tidigare. Den bör distribueras på en annan virtuell dator.
- De virtuella AWS-käll datorerna som ska migreras bör ha en nätverkslinje för replikeringsinstallationen. Konfigurera nödvändiga regler för säkerhetsgrupper för att aktivera detta. Vi rekommenderar att replikeringsinstallationen distribueras i samma VPC som de virtuella käll-datorer som ska migreras. Om replikeringsinstallationen måste finnas i en annan VPC måste VPC:erna anslutas via VPC-peering.
- De virtuella käll-AWS-datorerna kommunicerar med replikeringsinstallationen på portarna HTTPS 443 (kontrollkanalorkestrering) och TCP 9443 (datatransport) inkommande för replikeringshantering och överföring av replikeringsdata. Replikeringsinstallationen orkestrerar och skickar replikeringsdata till Azure via port HTTPS 443 utgående. Konfigurera de här reglerna genom att redigera reglerna för inkommande/utgående säkerhetsgrupp med lämpliga portar och käll-IP-information.

   ![AWS-säkerhetsgrupper ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Redigera säkerhetsinställningar ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- Replikeringsinstallationen använder MySQL. Granska [alternativen för](migrate-replication-appliance.md#mysql-installation) att installera MySQL på installationen.
- Granska de Azure-URL:er som krävs för att replikeringsinstallationen ska [få åtkomst till](migrate-replication-appliance.md#url-access) offentliga moln [och](migrate-replication-appliance.md#azure-government-url-access) myndighetsmoln.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikeringsinstallationen

Det första steget i migreringen är att konfigurera replikeringsinstallationen. Om du vill konfigurera installationen för migrering av virtuella AWS-datorer måste du ladda ned installationsfilen för installationen och sedan köra den på den virtuella dator [som du förberedde.](#prepare-a-machine-for-the-replication-appliance)

### <a name="download-the-replication-appliance-installer"></a>Ladda ned installationsprogrammet för replikeringsinstallationen

1. I det Azure Migrate projektet > **Server** i **Azure Migrate: Server Migration klickar** du på **Identifiera.**

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. I **Identifiera datorer**  >  **Virtualiseras dina datorer? klickar du** på Inte **virtualiserad/Övrigt.**
3. I **Målregion** väljer du den Azure-region som du vill migrera datorerna till.
4. Välj **Bekräfta att målregionen för migreringen <namnet på regionen>**.
5. Klicka **på Skapa resurser.** Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate Server Migration går det inte att konfigurera målalternativet eftersom resurser har konfigurerats tidigare.
    - Du kan inte ändra målregionen för det här projektet när du har klickat på den här knappen.
    - Om du vill migrera dina virtuella datorer till en annan region måste du skapa ett nytt/Azure Migrate projekt.  
    > [!NOTE]
    > Om du valde den privata slutpunkten som anslutningsmetod för Azure Migrate-projektet när det skapades, konfigureras Recovery Services-valvet även för anslutning till privat slutpunkt. Kontrollera att de privata slutpunkterna kan nås från replikeringsinstallationen. [**Läs mer**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. I **Vill du installera en ny replikeringsinstallation?** väljer du Installera en **replikeringsinstallation.**
7. I **Ladda ned och installera programvaran för replikeringsinstallationen** laddar du ned installationsprogrammet och registreringsnyckeln. Du behöver nyckeln för att kunna registrera installationen. Nyckeln är giltig i fem dagar efter att den har laddats ned.

    ![Nedladdningsprovider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Kopiera installationsfilen och nyckelfilen till den virtuella Windows Server 2016- eller Windows Server 2012 AWS-dator som du skapade för replikeringsinstallationen.
9. Kör installationsfilen för replikeringsinstallationen enligt beskrivningen i nästa procedur.  
    9.1. I **Innan du börjar** väljer du **Installera konfigurerationsservern och processervern** och väljer sedan **Nästa**.   
    9.2 **I Programvarulicens från** tredje part väljer du Jag godkänner **licensavtalet från** tredje part och väljer sedan **Nästa.**   
    9.3 I **Registrering** väljer du **Bläddra** och går sedan till den plats där du lägger nyckelfilen för valvregistrering. Välj **Nästa**.  
    9.4 I **Internetinställningar** väljer du **Anslut till Azure Site Recovery utan proxyserver** och väljer sedan **Nästa.**  
    9.5 **Sidan Kravkontroll** kör kontroller efter flera objekt. När den är klar väljer du **Nästa**.  
    9.6 I **MySQL-konfiguration** anger du ett lösenord för MySQL-databasen och väljer sedan **Nästa.**  
    9.7 I **Miljöinformation** väljer du **Nej**. Du behöver inte skydda dina virtuella datorer. Välj **Nästa**.  
    9.8 I **Installationsplats** väljer du **Nästa för** att acceptera standardinställningen.  
    9.9 I **Nätverksval** väljer du **Nästa för** att acceptera standardinställningen.  
    9.10 I **sammanfattning** väljer du **Installera**.   
    9.11 **Installationsförloppet** visar information om installationsprocessen. När den är klar väljer du **Avsluta**. Ett fönster visar ett meddelande om en omstart. Välj **OK**.   
    9.12 Ett fönster visar sedan ett meddelande om lösenfrasen för konfigurationsserverns anslutning. Kopiera lösenfrasen till Urklipp och spara lösenfrasen i en temporär textfil på de virtuella käll datorerna. Du behöver den här lösenfrasen senare under installationen av mobilitetstjänsten.
10. När installationen är klar startas installationskonfigurationsguiden automatiskt (Du kan också starta guiden manuellt med genvägen cspsconfigtool som skapas på skrivbordet i installationen). Använd fliken Hantera konton i guiden för att lägga till kontoinformation som ska användas för push-installation av tjänsten Mobility. I den här självstudien installerar vi mobilitetstjänsten manuellt på de virtuella käll-datorer som ska replikeras, så skapa ett dummykonto i det här steget och fortsätt. Du kan ange följande information för att skapa dummy-kontot – "gäst" som eget namn, "användarnamn" som användarnamn och "lösenord" som lösenord för kontot. Du kommer att använda det här dummy-kontot i fasen Aktivera replikering. 
11. När installationen har startats om efter installationen går du till Identifiera datorer, väljer den nya installationen i **Välj konfigurationsserver** och klickar **på Slutför registreringen.** Slutför registreringen utför några sista uppgifter för att förbereda replikeringsinstallationen.

    ![Slutför registreringen](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

En tjänsten Mobility agent måste vara installerad på de virtuella AWS-käll datorerna som ska migreras. Agentinstallationsprogram är tillgängliga i replikeringsinstallationen. Du hittar rätt installationsprogram och installerar agenten på varje dator som du vill migrera. Gör så här:

1. Logga in på replikeringsinstallationen.
2. Gå till **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Leta reda på installationsprogrammet för de virtuella AWS-käll datorernas operativsystem och version. Granska [operativsystem som stöds](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Kopiera installationsfilen till den virtuella AWS-källdatorn som du vill migrera.
5. Kontrollera att du har den sparade lösenfrastextfilen som skapades när du installerade replikeringsinstallationen.
    - Om du har glömt att spara lösenfrasen kan du visa lösenfrasen i replikeringsinstallationen med det här steget. Från kommandoraden kör du **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v för** att visa den aktuella lösenfrasen.
    - Kopiera nu den här lösenfrasen till Urklipp och spara den i en temporär textfil på de virtuella käll datorerna.

### <a name="installation-guide-for-windows-aws-vms"></a>Installationsguide för virtuella Windows AWS-datorer

1. Extrahera innehållet i installationsfilen till en lokal mapp (till exempel C:\Temp) på den virtuella AWS-datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Kör installationsprogrammet för mobilitetstjänsten:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Registrera agenten med replikeringsinstallationen:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Installationsguide för virtuella Linux AWS-datorer

1. Extrahera innehållet i installationsprogrammets tarball-fil till en lokal mapp (till exempel /tmp/MobSvcInstaller) på den virtuella AWS-datorn enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Kör installationsskriptet:
    ```
    sudo ./install -r MS -q
    ```  

3. Registrera agenten med replikeringsinstallationen:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-aws-vms"></a>Aktivera replikering för virtuella AWS-datorer

> [!NOTE]
> Via portalen kan du lägga till upp till 10 virtuella datorer för replikering samtidigt. Om du vill replikera fler virtuella datorer samtidigt kan du lägga till dem i batchar om 10.

1. I projektet Azure Migrate , **>,** **Azure Migrate: Server Migration,** klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera** väljer >   >  **källinställningar Virtualiseras dina datorer?**, väljer **du Inte virtualiserad/Övrigt.**
3. I **Lokal installation väljer** du namnet på den Azure Migrate som du har ställt in.
4. I **Processer server** väljer du namnet på replikeringsinstallationen. 
5. I **Gästautentiseringsuppgifter** väljer du det [](#download-the-replication-appliance-installer) dummy-konto som skapades tidigare under installationsprogrammet för replikering för att installera tjänsten Mobility manuellt (push-installation stöds inte). Klicka sedan **på Nästa: Virtuella datorer.**   
 
    ![Replikera inställningar](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
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
    -  Tillgänglighetszon för att fästa den migrerade datorn till en specifik tillgänglighetszon i regionen. Använd det här alternativet för att distribuera servrar som utgör en programnivå med flera noder över Tillgänglighetszoner. Om du väljer det här alternativet måste du ange vilken tillgänglighetszon som ska användas för var och en av den valda datorn på fliken Beräkning. Det här alternativet är endast tillgängligt om målregionen som valts för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighetsuppsättning för att placera den migrerade datorn i en tillgänglighetsuppsättning. Den valda målresursgruppen måste ha en eller flera tillgänglighetsuppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturredundans krävs om du inte behöver någon av dessa tillgänglighetskonfigurationer för de migrerade datorerna.
    
12. I **Diskkrypteringstyp** väljer du:
    - Kryptering i vila med plattformsstyrd nyckel
    - Kryptering i vila med kund hanterad nyckel
    - Dubbel kryptering med plattforms-hanterade och kund hanterade nycklar

   > [!NOTE]
   > Om du vill replikera virtuella datorer med CMK måste du [skapa en diskkrypteringsuppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) under målresursgruppen. Ett objekt för diskkrypteringsuppsättning mappar Managed Disks en Key Vault som innehåller den CMK som ska användas för SSE.
  
13. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-vmware/target-settings.png)

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

## <a name="track-and-monitor-replication-status"></a>Spåra och övervaka replikeringsstatus

- När du klickar på **Replikera** påbörjas ett jobb för att starta replikeringen.
- När jobbet Starta replikering har slutförts påbörjar de virtuella datorerna den inledande replikeringen till Azure.
- När den inledande replikeringen är klar påbörjas deltareplikeringen. Inkrementella ändringar av virtuella AWS-datordiskar replikeras regelbundet till replikdiskarna i Azure.

Du kan spåra jobbstatus i portalmeddelanden.

Du kan övervaka replikeringsstatusen genom att **klicka på Replikera servrar** i **Azure Migrate: Server Migration**.  

![Övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering

När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Testmigrering rekommenderas starkt och ger möjlighet att identifiera eventuella problem och åtgärda dem innan du fortsätter med själva migreringen. Vi rekommenderar att du gör detta minst en gång för varje virtuell dator innan du migrerar den.

- När du kör en testmigrering kontrolleras att migreringen fungerar som förväntat, utan att de virtuella AWS-datorerna påverkas, fortsätter att fungera och replikeras.
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligtvis migrera till ett icke-produktions-VNet i din Azure-prenumeration).
- Du kan använda den replikerade virtuella Azure-testdatorn för att verifiera migreringen, utföra apptestning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering på följande sätt:

1. I **Migreringsmål**  >  **Servrar**  >  **Azure Migrate: Servermigrering** klickar du **på Testa migrerade servrar.**

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-aws-vms"></a>Migrera virtuella AWS-datorer

När du har kontrollerat att testmigrering fungerar som förväntat kan du migrera de virtuella AWS-datorerna.

1. I det Azure Migrate projektet > **Servers**  >  **Azure Migrate: Server Migration (Servermigrering)** **klickar du på Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera**  >  **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja**  >  **OK.**
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej.**
4. Ett migreringsjobb startas för den virtuella datorn. Du kan visa jobbstatusen genom att klicka på klockikonen för meddelanden längst upp till höger på portalsidan eller genom att gå till jobbsidan i servermigreringsverktyget (klicka på Översikt på verktygspanelen > Välj jobb på den vänstra menyn).
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan Virtual Machines.

### <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella > **stoppa migreringen.** Det här gör följande:
    - Stoppar replikeringen för den virtuella AWS-datorn.
    - Tar bort den virtuella AWS-datorn från **antalet replikerade servrar** i Azure Migrate: Server Migration.
    - Rensar information om replikeringstillstånd för den virtuella datorn.
2. Installera [Linux-agenten](../virtual-machines/extensions/agent-linux.md) på de migrerade datorerna. Azure VM Windows-agenten är förinstallerad under migreringsprocessen.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Minska trafik till den migrerade virtuella Azure-datorinstansen.
6. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 




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



## <a name="troubleshooting--tips"></a>Felsökning/tips

**Fråga:** Jag kan inte se min virtuella AWS-dator i den identifierade listan över servrar för migrering   
**Svar:** Kontrollera om replikeringsinstallationen uppfyller kraven. Kontrollera att mobilitetsagenten är installerad på den virtuella källdatorn som ska migreras och är registrerad på konfigurationsservern. Kontrollera nätverksinställningen och brandväggsreglerna för att aktivera en nätverkssökväg mellan replikeringsinstallationen och de virtuella käll-AWS-datorerna.  

**Fråga:** Hur gör jag för att om den virtuella datorn har migrerats   
**Svar:** Efter migreringen kan du visa och hantera den virtuella datorn från Virtual Machines sidan. Anslut till den migrerade virtuella datorn för att verifiera.  

**Fråga:** Jag kan inte importera virtuella datorer för migrering från mina tidigare skapade serverutvärderingsresultat   
**Svar:** För närvarande stöder vi inte import av utvärdering för det här arbetsflödet. Som en tillfällig lösning kan du exportera utvärderingen och sedan manuellt välja rekommendationen för virtuella datorer under steget Aktivera replikering.
  
**Fråga:** Jag får felet "Det gick inte att hämta BIOS GUID" när jag försöker identifiera mina virtuella AWS-datorer   
**Svar:** Använd alltid rotinloggning för autentisering och inte någon pseudoanvändare. Granska även operativsystem som stöds för virtuella AWS-datorer.  

**Fråga:** Min replikeringsstatus fortskrider inte   
**Svar:** Kontrollera om replikeringsinstallationen uppfyller kraven. Kontrollera att du har aktiverat de portar som krävs på replikeringsinstallationen TCP-port 9443 och HTTPS 443 för dataöverföring. Kontrollera att det inte finns några inaktuella dubblettversioner av replikeringsinstallationen som är anslutna till samma projekt.   

**Fråga:** Jag kan inte identifiera AWS-instanser med Azure Migrate på grund av HTTP-statuskod 504 från windows fjärrhanteringstjänsten    
**Svar:** Se till att granska kraven för Azure Migrate-installationen och URL-åtkomstbehoven. Kontrollera att inga proxyinställningar blockerar installationens registrering.

**Fråga:** Måste jag göra några ändringar innan jag migrerar mina virtuella AWS-datorer till Azure?   
**Svar:** Du kan behöva göra dessa ändringar innan du migrerar dina virtuella EC2-datorer till Azure:

- Om du använder cloud-init för etableringen av den virtuella datorn kanske du vill inaktivera cloud-init på den virtuella datorn innan du replikerar den till Azure. Etableringsstegen som utförs av cloud-init på den virtuella datorn kanske är AWS-specifika och inte giltiga efter migreringen till Azure. 
- Om den virtuella datorn är en virtuell PV-dator (paravirtualiserad) och inte en virtuell HVM-dator kanske du inte kan köra den som den är i Azure eftersom paravirtualiserade virtuella datorer använder en anpassad startsekvens i AWS. Du kanske kan ta dig förbi den här utmaningen genom att avinstallera PV-drivrutiner innan du utför en migrering till Azure.  
- Vi rekommenderar alltid att du kör en testmigrering före den slutliga migreringen.  


**Fråga:** Kan jag migrera virtuella AWS-datorer som kör Amazon Linux-operativsystem?  
**Svar:** Virtuella datorer som kör Amazon Linux kan inte migreras som de är eftersom Amazon Linux OS endast stöds på AWS.
Om du vill migrera arbetsbelastningar som körs på Amazon Linux kan du starta en virtuell CentOS/RHEL-dator i Azure och migrera arbetsbelastningen som körs på AWS Linux-datorn med en relevant arbetsbelastningsmigreringsmetod. Beroende på arbetsbelastningen kan det till exempel finnas arbetsbelastningsspecifika verktyg för att underlätta migreringen, till exempel för databaser eller distributionsverktyg för webbservrar.

## <a name="next-steps"></a>Nästa steg

Undersök [molnmigreringsresan](/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.