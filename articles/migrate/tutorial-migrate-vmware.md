---
title: Migrera virtuella VMware-datorer utan agent Azure Migrate Server migrering
description: Lär dig hur du kör en agent lös migrering av virtuella VMware-datorer med Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: a858bcc0e6f6364a1eb5c37d555f5f1e7064b650
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879468"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrera virtuella VMware-datorer till Azure (utan agent)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) med återställning utan agent. Du kan också migrera virtuella VMware-datorer med hjälp av agent-baserad migrering. [Jämför](server-migrate-overview.md#compare-migration-methods) metoderna.

Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure. 

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. 


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till Azure-migrering: Migreringsverktyg för Server.
> * Identifiera virtuella datorer som du vill migrera.
> * Starta replikering av virtuella datorer.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering av virtuella datorer.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Slutför den första självstudien](./tutorial-discover-vmware.md) för att förbereda Azure och VMware för migrering.
2. Vi rekommenderar att du slutför den andra självstudien för att [utvärdera virtuella VMware-datorer](./tutorial-assess-vmware-azure-vm.md) innan du migrerar dem till Azure, men du behöver inte. 
3. Gå till det redan skapade projektet eller [skapa ett nytt projekt](./create-manage-projects.md)
4. Verifiera behörigheter för ditt Azure-konto – ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate Server-migrering kör en enkel VMware VM-enhet som används för identifiering, utvärdering och migrering utan agent för virtuella VMware-datorer. Om du följer [utvärderings guiden](./tutorial-assess-vmware-azure-vm.md)har du redan ställt in installationen. Om du inte gjorde det konfigurerar du det nu genom att använda någon av följande metoder:

- **Ägg-mall**: [Konfigurera](how-to-set-up-appliance-vmware.md) på en virtuell VMware-dator med en Hämtad områdesmall-mall.
- **Skript**: [Konfigurera](deploy-appliance-script.md) på en virtuell VMware-dator eller fysisk dator med hjälp av ett PowerShell-skript för installations programmet. Den här metoden ska användas om du inte kan konfigurera en virtuell dator med en behållar mall eller om du är i Azure Government.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.

## <a name="replicate-vms"></a>Replikera virtuella datorer

När du har konfigurerat installationen och slutfört identifieringen kan du påbörja replikering av virtuella VMware-datorer till Azure. 

- Du kan köra upp till 300 replikeringar samtidigt.
- I portalen kan du välja upp till 10 virtuella datorer på en gång för migrering. Om du vill migrera fler datorer lägger du till dem i grupper i grupp 10.

Aktivera replikering på följande sätt:

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering** klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **Lokal dator** väljer du namnet på den Azure Migrate-dator som du konfigurerar > **OK**. 

    ![Källinställningar](./media/tutorial-migrate-vmware/source-settings.png)

4. I **Virtuella datorer** väljer du de datorer som du vill replikera. Om du vill använda storleks ändring och disk typ för virtuell dator från en utvärdering om du har kört en, i **Importera inställningar för migrering från en Azure Migrate-utvärdering?** väljer du **Ja** och väljer den virtuella dator gruppen och utvärderings namnet. Om du inte använder bedömnings inställningar väljer du **Nej**.
   
    ![Välj utvärdering](./media/tutorial-migrate-vmware/select-assessment.png)

5. I **virtuella datorer** väljer du de virtuella datorer som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-vmware/select-vms.png)

6. I **mål inställningar** väljer du prenumerationen och mål regionen. Ange resurs gruppen där de virtuella Azure-datorerna finns efter migreringen.
7. I **Virtual Network** väljer du det virtuella Azure-nätverk/undernät som de virtuella Azure-datorerna ska ansluta till efter migreringen.
8. I **tillgänglighets alternativ** väljer du:
    -  Tillgänglighets zon för att fästa den migrerade datorn i en angiven tillgänglighets zon i regionen. Använd det här alternativet för att distribuera servrar som utgör en program nivå med flera noder i Tillgänglighetszoner. Om du väljer det här alternativet måste du ange den tillgänglighets zon som ska användas för var och en av de valda datorerna på fliken beräkning. Det här alternativet är bara tillgängligt om det valda mål området för migreringen stöder Tillgänglighetszoner
    -  Tillgänglighets uppsättning för att placera den migrerade datorn i en tillgänglighets uppsättning. Den valda mål resurs gruppen måste ha en eller flera tillgänglighets uppsättningar för att kunna använda det här alternativet.
    - Inget alternativ för infrastrukturs redundans krävs om du inte behöver någon av dessa tillgänglighets konfigurationer för de migrerade datorerna.
9. I **disk krypterings typ** väljer du:
    - Kryptering – rest med plattforms hanterad nyckel
    - Kryptering – rest med kundhanterad nyckel

   > [!NOTE]
   > För att replikera virtuella datorer med CMK måste du [skapa en disk krypterings uppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) under mål resurs gruppen. Ett objekt med en disk krypterings uppsättning mappar Managed Disks till en Key Vault som innehåller den CMK som ska användas för SSE.
  
10. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-vmware/target-settings.png)

11. I **Compute** granskar du VM-namn, storlek, typ av operativ system disk och tillgänglighets konfiguration (om du valde i föregående steg). De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **VM-storlek**: om du använder bedömnings rekommendationer visas den rekommenderade storleken i list rutan VM-storlek. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets zon**: Ange den tillgänglighets zon som ska användas.
    - **Tillgänglighets uppsättning**: Ange den tillgänglighets uppsättning som ska användas.

    > [!NOTE]
    > Om du vill välja ett annat tillgänglighets alternativ för en uppsättning virtuella datorer går du till steg 1 och upprepar stegen genom att välja olika tillgänglighets alternativ när du har startat replikering för en uppsättning virtuella datorer.


 ![Inställningar för VM-beräkning](./media/tutorial-migrate-vmware/compute-settings.png)

12. I **Diskar** anger du om VM-diskarna ska replikeras till Azure och disktypen (standard SSD/HDD eller premiumhanterade diskar) i Azure. Klicka på **Nästa**.
   
    ![Skärm bild som visar fliken diskar i dialog rutan replikera.](./media/tutorial-migrate-vmware/disks.png)

13. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar när som helst innan replikeringen startar (**Hantera**  >  **replikering av datorer**). Du kan inte ändra inställningarna när replikeringen har påbörjats.

### <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om det här är den första virtuella dator som du replikerar i projektet, etablerar Server migrering automatiskt dessa resurser i samma resurs grupp som projektet.

- **Service Bus**: Server-migreringen använder Service Bus för att skicka meddelanden om dirigering av replikering till enheten.
- **Gateway Storage-konto**: Server Migration använder Gateway Storage-kontot för att lagra statusinformation om de virtuella datorer som replikeras.
- **Logg lagrings konto**: Azure Migrate-installationen överför replik loggar för virtuella datorer till ett logg lagrings konto. Azure Migrate tillämpar replikeringsinformation på de replik hanterade diskarna.
- **Nyckel valv**: Azure Migrates enheten använder nyckel valvet för att hantera anslutnings strängar för Service Bus och åtkomst nycklar för de lagrings konton som används i replikeringen.

## <a name="track-and-monitor"></a>Spåra och övervaka

1. Spåra jobb status i Portal meddelanden.
2. Övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.

     ![Övervaka replikering](./media/tutorial-migrate-vmware/replicating-servers.png)

Replikeringen sker på följande sätt:
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- Under den inledande replikeringen skapas en VM-ögonblicksbild. Disk data från ögonblicks bilden replikeras till replik Managed disks i Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.

## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I Server för **migrerings mål**  >    >  **Azure Migrate: Server migrering** klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar**  >  **Azure Migrate: Server-migrering** klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **migrera**  >  **Stäng virtuella datorer och utför en planerad migrering utan data förlust** väljer du **Ja**  >  **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppar replikeringen**. Detta stoppar replikeringen för den lokala datorn och rensar information om replikeringstillståndet för den virtuella datorn.
2. Installera Azure VM [Linux](../virtual-machines/extensions/agent-linux.md) -agenten på de migrerade datorerna om datorn har Linux OS. Vi installerar automatiskt VM-agenten för virtuella Windows-datorer under migreringen.
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
-  Överväg att distribuera [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.