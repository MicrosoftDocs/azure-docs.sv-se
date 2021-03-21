---
title: Migrera virtuella Linux-datorer till Azure Premium Storage med Azure Site Recovery
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage med Site Recovery. Premium Storage erbjuder disk support med hög prestanda och låg latens för I/O-intensiva arbets belastningar som körs på Azure Virtual Machines.
author: luywang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 8688d278c40ba34b7c4344a73cb4717f3fd71368
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600079"
---
# <a name="use-site-recovery-to-migrate-to-premium-storage"></a>Använd Site Recovery för att migrera till Premium Storage

[Azure Premium-SSD](../disks-types.md) ger hög prestanda, låg latens disk support för virtuella datorer som kör I/O-intensiva arbets belastningar. Den här guiden hjälper dig att migrera dina virtuella dator diskar från ett standard lagrings konto till ett Premium Storage-konto med hjälp av [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery är en Azure-tjänst som bidrar till din strategi för affärs kontinuitet och haveri beredskap genom att dirigera replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt Data Center. När avbrott uppstår på den primära platsen växlar du över till den sekundära platsen för att hålla program och arbets belastningar tillgängliga. Du växlar tillbaka till den primära platsen när den återgår till normal drift. 

Site Recovery tillhandahåller redundanstest för att stödja haveri beredskap utan att påverka produktions miljöer. Du kan köra redundans med minimal data förlust (beroende på replikeringsfrekvens) för oväntade haverier. I scenariot med att migrera till Premium Storage kan du använda [redundansväxlingen i Site Recovery](../../site-recovery/site-recovery-failover.md) för att migrera mål diskar till ett Premium Storage-konto.

Vi rekommenderar att du migrerar till Premium Storage genom att använda Site Recovery eftersom det här alternativet ger minimal nedtid. Med det här alternativet undviks även manuell körning av att kopiera diskar och skapa nya virtuella datorer. Site Recovery kommer systematiskt att kopiera diskar och skapa nya virtuella datorer under redundansväxlingen. 

Site Recovery stöder ett antal typer av redundans med minimal eller ingen stillestånds tid. För att planera stillestånds tiden och beräkna data förlust, se [typerna av redundans i Site Recovery](../../site-recovery/site-recovery-failover.md). Om du [förbereder för att ansluta till virtuella Azure-datorer efter redundansväxlingen](../../site-recovery/vmware-azure-tutorial.md)bör du kunna ansluta till den virtuella Azure-datorn med hjälp av RDP efter redundansväxlingen.

![Disaster Recovery-diagram][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-komponenter

Dessa Site Recoverys komponenter är relevanta för det här migrerings scenariot:

* **Konfigurations servern** är en virtuell Azure-dator som samordnar kommunikationen och hanterar data replikerings-och återställnings processer. På den här virtuella datorn kör du en enda installations fil för att installera konfigurations servern och en ytterligare komponent, som kallas en processerver, som en gateway för replikering. Läs om [krav för konfigurations servern](../../site-recovery/vmware-azure-tutorial.md). Du konfigurerar bara konfigurations servern en gång, och du kan använda den för alla migreringar till samma region.

* **Processerver** är en gateway för replikering som: 

  1. Tar emot replikeringsdata från virtuella käll datorer.
  2. Optimerar data med cachelagring, komprimering och kryptering.
  3. Skickar data till ett lagrings konto. 

  Den hanterar också push-installation av mobilitets tjänsten för virtuella datorer och utför automatisk identifiering av virtuella käll datorer. Standard processervern är installerad på konfigurations servern. Du kan distribuera ytterligare fristående process servrar för att skala distributionen. Läs om [metod tips för distribution av process Server](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) och [distribution av ytterligare process servrar](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Du kan bara konfigurera processervern en gång, och du kan använda den för alla migreringar till samma region.

* **Mobilitets tjänsten** är en komponent som distribueras på varje standard-VM som du vill replikera. Den samlar in data skrivningar på den virtuella standard datorn och vidarebefordrar dem till processervern. Läs om [förutsättningar för replikerade datorer](../../site-recovery/vmware-azure-tutorial.md).

Den här bilden visar hur dessa komponenter interagerar:

![Interaktion av Site Recovery-komponenter][15]

> [!NOTE]
> Site Recovery stöder inte migrering av lagrings utrymmen diskar.

Ytterligare komponenter för andra scenarier finns i [scenario arkitektur](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Azure Essentials

Detta är Azure-kraven för det här migrerings scenariot:

* En Azure-prenumeration.
* Ett Azure Premium Storage-konto för att lagra replikerade data.
* Ett virtuellt Azure-nätverk som de virtuella datorerna ska ansluta till när de skapas vid en redundansväxling. Det virtuella Azure-nätverket måste finnas i samma region som det som Site Recovery körs i.
* Ett Azure standard Storage-konto för att lagra loggar för replikering. Detta kan vara samma lagrings konto för de virtuella dator diskar som migreras.

## <a name="prerequisites"></a>Förutsättningar

* Förstå relevanta komponenter för migrerings scenario i föregående avsnitt.
* Planera stillestånds tiden genom att lära dig om [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Installations-och migrerings steg

Du kan använda Site Recovery för att migrera virtuella Azure IaaS-datorer mellan regioner eller inom samma region. Följande anvisningar är skräddarsydda för det här migrerings scenariot från artikeln [Replikera virtuella VMware-datorer eller fysiska servrar till Azure](../../site-recovery/vmware-azure-tutorial.md). Följ länkarna för detaljerade anvisningar utöver anvisningarna i den här artikeln.

### <a name="step-1-create-a-recovery-services-vault"></a>Steg 1: skapa ett Recovery Services-valv

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **skapa en**  >    >  **säkerhets kopia** av resurs hantering och **Site Recovery (OMS)**. Alternativt kan du välja **Bläddra**  >  **Recovery Services valv**  >  **Lägg till**. 
3. Ange en region som de virtuella datorerna ska replikeras till. För migrering i samma region väljer du den region där dina virtuella käll datorer och käll lagrings konton är. 

### <a name="step-2-choose-your-protection-goals"></a>Steg 2: Välj skydds mål 

1. Öppna [Azure Portal](https://portal.azure.com)på den virtuella datorn där du vill installera konfigurations servern.
2. Gå till inställningar för **Recovery Services valv**  >    >  **Site Recovery**  >  **steg 1: Förbered infrastruktur**  >  **skydds mål**.

   ![Bläddra till rutan skydds mål][2]

3. I den första List rutan under **skydds mål** väljer **du Azure**. Välj **inte virtualiserad/övrigt** i den andra List rutan och välj sedan **OK**.

   ![Rutan skydds mål med ifyllda rutor][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Steg 3: Konfigurera käll miljön (konfigurations servern)

1. Ladda ned **Azure Site Recovery enhetliga installations** -och valv registrerings nyckeln genom att gå till fönstret **förbereda infrastruktur**  >  **förbereda källa**  >  **Lägg till Server** . 
 
   Du kommer att behöva valv registrerings nyckeln för att köra den enhetliga installationen. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Bläddra till fönstret Lägg till Server][4]

2. Lägg till en konfigurations server i fönstret **Lägg till Server** .

   ![Fönstret Lägg till server med vald konfigurations Server][5]

3. På den virtuella datorn som du använder som konfigurations server kör du en enhetlig installation för att installera konfigurations servern och processervern. Du kan [gå igenom skärm](../../site-recovery/vmware-azure-tutorial.md) bilderna för att slutföra installationen. Du kan referera till följande skärm dum par för steg som har angetts för det här scenariot för migrering.

   1. I **innan du börjar** väljer du **Installera konfigurations servern och processervern**.

      ![Innan du börjar Sidan][6]

   2. I **registrering**, bläddra och välj den registrerings nyckel som du laddade ned från valvet.

      ![Registrerings sida][7]

   3. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. I det här scenariot för migrering väljer du **Nej**.

      ![Miljö informations sida][8]

4. När installationen är klar gör du följande i fönstret **Microsoft Azure Site Recovery konfigurations Server** :
 
   1. Använd fliken **Hantera konton** för att skapa kontot som Site Recovery kan använda för automatisk identifiering. (I scenariot för att skydda fysiska datorer är det inte relevant att konfigurera kontot, men du behöver minst ett konto för att aktivera något av följande steg. I det här fallet kan du namnge kontot och lösen ordet som helst.) 
   2. Använd fliken **valv registrering** för att ladda upp filen med valvet.

      ![Fliken valv registrering][9]

### <a name="step-4-set-up-the-target-environment"></a>Steg 4: Konfigurera mål miljön

Välj **Förbered infrastruktur**  >  **mål** och ange den distributions modell som du vill använda för virtuella datorer efter redundansväxlingen. Du kan välja **klassisk** eller **Resource Manager**, beroende på ditt scenario.

![Mål fönster][10]

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. 

> [!NOTE]
> Om du använder ett Premium Storage-konto för replikerade data måste du ställa in ytterligare ett standard lagrings konto för att lagra loggar för replikering.

### <a name="step-5-set-up-replication-settings"></a>Steg 5: Konfigurera replikeringsinställningar

Om du vill kontrol lera att konfigurations servern har kopplats till den replikeringsprincip som du skapar följer du [inställningarna för Konfigurera replikering](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Steg 6: Planera kapaciteten

1. Använd [kapacitets planeraren](../../site-recovery/site-recovery-capacity-planner.md) för att korrekt beräkna nätverks bandbredd, lagring och andra krav för att uppfylla dina replikerings behov. 
2. När du är klar väljer du **Ja, jag har gjort det** **har du slutfört kapacitets planeringen?**.

   ![För att bekräfta att du har slutfört kapacitets planeringen][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Steg 7: installera mobilitets tjänsten och aktivera replikering

1. Du kan välja att [push-installera](../../site-recovery/vmware-azure-tutorial.md) till dina käll datorer eller [Installera mobilitets tjänsten manuellt](../../site-recovery/vmware-azure-install-mobility-service.md) på dina virtuella käll datorer. Du hittar kravet på att skicka installationen och sökvägen till det manuella installations programmet i den angivna länken. Om du utför en manuell installation kan du behöva använda en intern IP-adress för att hitta konfigurations servern.

   ![Sidan information om konfigurations Server][12]

   Den felande virtuella datorn har två tillfälliga diskar: en från den primära virtuella datorn och den andra som skapades under etableringen av den virtuella datorn i återställnings regionen. Om du vill undanta den temporära disken före replikeringen installerar du mobilitets tjänsten innan du aktiverar replikering. Mer information om hur du undantar den temporära disken finns i [undanta diskar från replikering](../../site-recovery/vmware-azure-tutorial.md).

2. Aktivera replikering på följande sätt:
   1. Välj **Replikera program**  >  **källa**. När du har aktiverat replikering för första gången väljer du **+ Replikera** i valvet för att aktivera replikering för ytterligare datorer.
   2. I steg 1 ställer du in **källa** som processerver.
   3. I steg 2 anger du distributions modellen efter redundansväxling, ett Premium Storage-konto som ska migreras till, ett standard lagrings konto för att spara loggar och ett virtuellt nätverk för att inte fungera.
   4. I steg 3 lägger du till skyddade virtuella datorer med IP-adress. (Du kan behöva en intern IP-adress för att hitta dem.)
   5. I steg 4 konfigurerar du egenskaperna genom att välja de konton som du konfigurerade tidigare på processervern.
   6. I steg 5 väljer du den replikeringsprincip som du skapade tidigare i "steg 5: Konfigurera replikeringsinställningar".
   7. Välj **OK**.

   > [!NOTE]
   > När en virtuell Azure-dator frigörs och startas om, finns det ingen garanti för att den får samma IP-adress. Om IP-adressen för konfigurations servern/processervern eller de skyddade virtuella Azure-datorerna ändras kanske inte replikeringen i det här scenariot fungerar korrekt.

   ![Aktivera fönstret replikering med vald källa][13]

När du utformar din Azure Storage-miljö rekommenderar vi att du använder separata lagrings konton för varje virtuell dator i en tillgänglighets uppsättning. Vi rekommenderar att du följer bästa praxis i lagrings lagret för att [använda flera lagrings konton för varje tillgänglighets uppsättning](../availability.md). Distribution av virtuella dator diskar till flera lagrings konton hjälper till att förbättra lagrings tillgängligheten och distribuerar I/O i Azure Storage-infrastrukturen.

Om dina virtuella datorer finns i en tillgänglighets uppsättning, i stället för att replikera diskar för alla virtuella datorer till ett lagrings konto, rekommenderar vi starkt att du migrerar flera virtuella datorer flera gånger. På så sätt delar inte de virtuella datorerna i samma tillgänglighets uppsättning ett enda lagrings konto. Använd fönstret **Aktivera replikering** för att ställa in ett mål lagrings konto för varje virtuell dator, en i taget.
 
Du kan välja en distributions modell efter redundans efter dina behov. Om du väljer Azure Resource Manager som distributions modell efter redundans, kan du redundansväxla en virtuell dator (Resource Manager) till en virtuell dator (Resource Manager) eller växla över en virtuell dator (klassisk) till en virtuell dator (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Steg 8: köra ett redundanstest

Du kan kontrol lera om din replikering är slutförd genom att välja din Site Recovery instans och sedan välja **Inställningar**  >  **replikerade objekt**. Du kommer att se status och procent av din replikeringsprincip. 

När den inledande replikeringen är klar kan du köra ett redundanstest för att verifiera din strategi för replikering. Detaljerade anvisningar om redundanstest finns i [köra ett redundanstest i Site Recovery](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Innan du kör en redundansväxling kontrollerar du att din VM-och Replikerings strategi uppfyller kraven. Mer information om att köra ett redundanstest finns i [testa redundans till Azure i Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Du kan se status för redundanstest i **Inställningar**  >  **jobb**  >  *YOUR_FAILOVER_PLAN_NAME*. I fönstret kan du se en analys av stegen och resultatet av lyckade/misslyckade resultat. Om redundanstestning Miss lyckas i något steg väljer du steget för att kontrol lera fel meddelandet. 

### <a name="step-9-run-a-failover"></a>Steg 9: köra en redundansväxling

När redundanstestning har slutförts kör du en redundansväxling för att migrera diskarna till Premium Storage och replikerar de virtuella dator instanserna. Följ de detaljerade stegen i [köra en redundansväxling](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Se till att välja **Stäng virtuella datorer och synkronisera senaste data**. Det här alternativet anger att Site Recovery ska försöka stänga av de skyddade virtuella datorerna och synkronisera data så att den senaste versionen av data kommer att Miss lyckas. Om du inte väljer det här alternativet eller om försöket inte lyckas kommer redundansväxlingen att vara från den senaste tillgängliga återställnings punkten för den virtuella datorn. 

Site Recovery skapar en VM-instans vars typ är samma som eller liknar en Premium Storage-kompatibel virtuell dator. Du kan kontrol lera prestanda och pris för olika VM-instanser genom att gå till [virtuella Windows-datorer priser](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [virtuella Linux-datorer prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Steg efter migreringen

1. **Konfigurera replikerade virtuella datorer till tillgänglighets uppsättningen om tillämpligt**. Site Recovery har inte stöd för migrering av virtuella datorer tillsammans med tillgänglighets uppsättningen. Gör något av följande beroende på distributionen av den replikerade virtuella datorn:
   * För en virtuell dator som skapats via den klassiska distributions modellen: Lägg till den virtuella datorn i tillgänglighets uppsättningen i Azure Portal. Detaljerade anvisningar finns i [lägga till en befintlig virtuell dator i en tillgänglighets uppsättning](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * För en virtuell dator som skapats via Resource Manager-distributions modellen: Spara din konfiguration av den virtuella datorn och ta sedan bort och återskapa de virtuella datorerna i tillgänglighets uppsättningen. Det gör du genom att använda skriptet i [ange Azure Resource Manager tillgänglighets uppsättning för virtuell dator](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Innan du kör det här skriptet bör du kontrol lera begränsningarna och planera stillestånds tiden.

2. **Ta bort gamla virtuella datorer och diskar**. Se till att Premium diskarna är konsekventa med käll diskar och att de nya virtuella datorerna utför samma funktion som de virtuella käll datorerna. Ta bort den virtuella datorn och ta bort diskarna från dina käll lagrings konton i Azure Portal. Om det finns ett problem där disken inte tas bort trots att du har tagit bort den virtuella datorn, se [Felsöka fel vid borttagning av lagrings resurs](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors).

3. **Rengör Azure Site Recovery-infrastrukturen**. Om Site Recovery inte längre behövs kan du rensa dess infrastruktur. Ta bort replikerade objekt, konfigurations servern och återställnings principen och ta sedan bort Azure Site Recovery-valvet.

## <a name="troubleshooting"></a>Felsökning

* [Övervaka och Felsök skydd för virtuella datorer och fysiska servrar](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Sidan Microsoft Q&en fråga för Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Nästa steg

Vissa scenarier för att migrera virtuella datorer finns i följande resurser:

* [Migrera Azure Virtual Machines mellan lagrings konton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Ladda upp en virtuell Linux-hårddisk](upload-vhd.md)
* [Migrera Virtual Machines från Amazon AWS till Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Välja en disktyp för virtuella IaaS-datorer](../disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png