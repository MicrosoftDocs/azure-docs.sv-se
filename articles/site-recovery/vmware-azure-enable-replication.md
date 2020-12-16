---
title: Aktivera virtuella VMware-datorer för haveri beredskap med Azure Site Recovery
description: I den här artikeln beskrivs hur du aktiverar VMware VM-replikering för haveri beredskap med hjälp av tjänsten Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: a1f4759bc40c4074f0dd618be8ac66ad088e848c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587765"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Aktivera replikering till Azure för virtuella VMware-datorer

I den här artikeln beskrivs hur du aktiverar replikering av lokala virtuella VMware-datorer (VM) till Azure.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att systemet uppfyller följande kriterier:

- [Konfigurera din lokala käll miljö](vmware-azure-set-up-source.md).
- [Konfigurera mål miljön i Azure](vmware-azure-set-up-target.md).
- [Kontrol lera krav och förutsättningar](vmware-physical-azure-support-matrix.md) innan du börjar. Viktiga saker att tänka på är:
  - [Operativ system som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) för replikerade datorer.
  - Stöd för [lagring/disk](vmware-physical-azure-support-matrix.md#storage) .
  - [Azure-krav](vmware-physical-azure-support-matrix.md#azure-vm-requirements) som lokala datorer ska uppfylla.

### <a name="resolve-common-issues"></a>Lös vanliga problem

- Varje disk ska vara mindre än 4 TB.
- Operativ system disken måste vara en standard disk, inte en dynamisk disk.
- För virtuella datorer i generation 2 är operativ system familjen Windows och start disken måste vara mindre än 300 GB.

## <a name="before-you-start"></a>Innan du börjar

Tänk på följande när du replikerar virtuella VMware-datorer:

- Ditt Azure-användarkonto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) för att aktivera replikering av en ny virtuell dator till Azure.
- Virtuella VMware-datorer identifieras var 15: e minut. Det kan ta 15 minuter eller mer för virtuella datorer att visas i Azure Portal efter identifieringen. När du lägger till en ny vCenter-Server eller vSphere-värd kan identifieringen ta 15 minuter eller mer.
- Det kan ta 15 minuter eller mer för miljö ändringar på den virtuella datorn som ska uppdateras i portalen. Till exempel installation av VMware-verktyg.
- Du kan kontrol lera den senaste identifierade tiden för virtuella VMware-datorer: se den **senaste kontakt vid** fältet på sidan **konfigurations servrar** för vCenter-servern/vSphere-värden.
- Om du vill lägga till virtuella datorer för replikering utan att vänta på den schemalagda identifieringen markerar du konfigurations servern (men klickar inte på den) och väljer **Uppdatera**.
- När du aktiverar replikering, om den virtuella datorn är för beredd, installerar processervern automatiskt Azure Site Recovery mobilitets tjänsten på den virtuella datorn.

## <a name="enable-replication"></a>Aktivera replikering

Innan du utför stegen i det här avsnittet kan du läsa följande information:

- Azure Site Recovery replikeras nu direkt till hanterade diskar för alla nya replikeringar. Processervern skriver replikeringsinställningarna till ett cache Storage-konto i mål regionen. De här loggarna används för att skapa återställnings punkter i replik Managed disks som har namngivnings konventionen `asrseeddisk` .
- PowerShell-stöd för replikering till hanterade diskar är tillgängligt från och med [AZ. RecoveryServices-modul version 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- Vid redundansväxlingen används den återställnings punkt som du väljer för att skapa den mål hanterade disken.
- Virtuella datorer som tidigare har kon figurer ATS för replikering till mål lagrings konton påverkas inte.
- Replikering till lagrings konton för en ny virtuell dator är endast tillgängligt via ett REST-API (Representational State Transfer) och PowerShell. Använd Azure REST API version 2016-08-10 eller 2018-01-10 för replikering till lagrings konton.

Följ dessa steg om du vill aktivera replikering:

1. Gå till **steg 2: replikera program**  >  **källan**. När du har aktiverat replikering för första gången väljer du **+ Replikera** i valvet för att aktivera replikering för ytterligare virtuella datorer.
1. På **käll** sidan > **källa** väljer du konfigurations servern.
1. För **dator typ** väljer du **Virtual Machines** eller **fysiska datorer**.
1. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden. Den här inställningen är inte relevant om du replikerar fysiska datorer.
1. Välj processervern. Om inga ytterligare process servrar skapas, kommer den inskapade processervern på konfigurations servern att vara tillgänglig i list menyn. Hälso statusen för varje processerver anges enligt rekommenderade gränser och andra parametrar. Välj en felfri processerver. Det går inte att välja en [kritisk](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver. Du kan antingen [Felsöka och lösa](vmware-physical-azure-troubleshoot-process-server.md) felen **eller** konfigurera en [skalbar processerver](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Aktivera fönstret replikeringskälla":::

   > [!NOTE]
   > Från och med [version 9,24](site-recovery-whats-new.md)införs ytterligare aviseringar för att förbättra hälso aviseringarna i processervern. Uppgradera Site Recovery-komponenter till version 9,24 eller senare för att alla aviseringar ska genereras.

1. För **mål** väljer du den prenumeration och resurs grupp där du vill skapa de misslyckade virtuella datorerna. Välj den distributions modell som du vill använda i Azure för de misslyckade virtuella datorerna.
1. Välj det Azure-nätverk och undernät som de virtuella Azure-datorerna ska ansluta till efter redundansväxlingen. Nätverket måste finnas i samma region som Site Recovery tjänst valvet.

   Välj **Konfigurera nu för valda datorer** om du vill tillämpa nätverks inställningen på alla virtuella datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket per virtuell dator. Om du inte har ett nätverk måste du skapa ett. Om du vill skapa ett nätverk med hjälp av Azure Resource Manager väljer du **Skapa nytt**. Välj ett undernät om det är tillämpligt och välj sedan **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Aktivera fönstret Replication Target":::

1. För **virtuella datorer**  >  **väljer du virtuella datorer** och väljer varje virtuell dator som du vill replikera. Du kan bara välja virtuella datorer för vilka replikering kan aktive ras. Välj sedan **OK**. Om du inte kan se eller välja en viss virtuell dator, se [käll datorn inte i Azure Portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) för att lösa problemet.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Aktivera replikering Välj Virtual Machines-fönstret":::

1. För **Egenskaper**  >  **Konfigurera egenskaper** väljer du det konto som processervern använder för att automatiskt installera Site Recovery mobilitets tjänsten på den virtuella datorn. Välj också vilken typ av mål för hanterad disk som ska användas för replikering baserat på dina data omsättnings mönster.
1. Som standard replikeras alla diskar för en virtuell käll dator. Om du vill utesluta diskar från replikering avmarkerar du kryss rutan **Inkludera** för diskar som du inte vill replikera. Välj sedan **OK**. Du kan ange ytterligare egenskaper senare. [Läs mer](vmware-azure-exclude-disk.md) om att utesluta diskar.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Aktivera fönstret Konfigurera egenskaper för replikering":::

1. I **replikeringsinställningar**  >  **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip är markerad. Du kan ändra inställningarna för replikeringsprincip i **Inställningar**  >    >  _principer för princip namn_  >  **Redigera inställningar**. Ändringar som tillämpas på en princip gäller även för replikering och nya virtuella datorer.
1. Aktivera **konsekvens för flera virtuella** datorer om du vill samla in virtuella datorer i en replikeringsgrupp. Ange ett namn för gruppen och välj sedan **OK**.

   > [!NOTE]
   > - Virtuella datorer i en replikeringsgrupp replikeras tillsammans och har delade krasch-konsekventa och programkonsekventa återställnings punkter när de växlar över.
   > - Samla in virtuella datorer och fysiska servrar så att de speglar dina arbets belastningar. Att aktivera konsekvens för flera virtuella datorer kan påverka arbets Belastningens prestanda. Gör bara detta om de virtuella datorerna kör samma arbets belastning och du behöver konsekvens.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Fönstret aktivera replikering":::

1. Välj **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** på **Inställningar**  >  **jobb**  >  **Site Recovery jobb**. När jobbet **Slutför skydd** har körts är den virtuella datorn klar för redundans.

## <a name="monitor-initial-replication"></a>Övervaka inledande replikering

När "Aktivera replikering" av det skyddade objektet är slutförd initierar Azure Site Recovery replikering (synonymt till synkronisering) av data från käll datorn till mål regionen. Under den här perioden skapas replik av käll diskar. När du har slutfört kopieringen av ursprungliga diskar kopieras delta ändringarna till mål regionen. Hur lång tid det tar att kopiera de ursprungliga diskarna beror på flera parametrar, till exempel:

- storlek på käll dator diskar
- bandbredd som är tillgänglig för överföring av data till Azure (du kan utnyttja distributions planeraren för att identifiera den optimala bandbredd som krävs)
- bearbeta server resurser som minne, ledigt disk utrymme, CPU som är tillgängliga för cachelagring & bearbeta data som tas emot från skyddade objekt (se till att processervern är [felfri](vmware-physical-azure-monitor-process-server.md#monitor-proactively))

Om du vill spåra förloppet för den inledande replikeringen går du till Recovery Services-valvet i Azure Portal-> replikerade objekt-> övervaka "status" kolumn värde för replikerat objekt. Status visar slut för ande procent för inledande replikering. Vid hovring över statusen är "total data överföring" tillgängligt. När du klickar på status öppnas en sammanhangsbaserad sida och följande parametrar visas:

- Senast uppdaterad vid – indikerar den senaste tiden då replikeringen av hela datorn har uppdaterats av tjänsten.
- Slutförd procent – anger procent andelen inledande replikering som slutförts för den virtuella datorn
- Totalt antal data som överförts – mängd data som överförts från virtuell dator till Azure

:::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="tillstånds-och replikering" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Synkroniseringens förlopp (för att spåra information på en disk nivå)
    - Tillstånd för replikering
      - Om replikeringen ännu inte startar så uppdateras statusen som "i kö". Under den inledande replikeringen replikeras bara 3 diskar i taget. Den här mekanismen följs för att undvika begränsning på processervern.
      - När replikeringen har startats uppdateras status som "pågår".
      - När den inledande replikeringen har slutförts markeras statusen som slutförd.        
   - Site Recovery läser genom den ursprungliga disken, överför data till Azure och avbildar förloppet på disk nivå. Observera att Site Recovery hoppar över replikeringen av diskens lediga storlek och lägger till den i slutförda data. Därför kanske summan av data som överförs på alla diskar inte läggs till i "totalt antal överförda data" på VM-nivå.
   - När du klickar på informations ballongen mot en disk kan du få information om när replikeringen (synonymt för synkronisering) har utlösts för disken, data som överförts till Azure under de senaste 15 min följt av den senast uppdaterade tidsstämpeln. Den här tidsstämpeln anger senaste tid då information togs emot av Azure-tjänsten från käll datorns :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="inledande replikering – info-pratbubbla-information" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - Hälso tillståndet för varje disk visas
      - Om replikeringen går långsammare än förväntat blir disk statusen i varning
      - Om replikeringen inte fortlöper, blir disk statusen i kritiskt

Om hälso tillståndet är i kritiskt/varnings tillstånd kontrollerar du att hälso tillståndet för datorn och [processens](vmware-physical-azure-monitor-process-server.md) hälso tillstånd är felfria. 

Så snart som aktiveringen av replikeringen är slutförd blir replikeringens förlopp 0% och den totala mängden data som överförs skulle bli saknas. När du klickar på visas data mot varje identifierad disk som "NA". Detta anger att replikeringen ännu är igång och Azure Site Recovery ännu inte får den senaste statistiken. Förloppet uppdateras med ett intervall på 30 minuter.

> [!NOTE]
> Se till att uppdatera konfigurations servrar, skalbara process servrar och mobilitets agenter till version 9,36 eller högre för att se till att korrekt förlopp samlas in och skickas till Site Recovery tjänster.


## <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Kontrol lera sedan egenskaperna för den virtuella käll datorn. Kom ihåg att namnet på den virtuella Azure-datorn måste följa kraven för den [virtuella Azure-datorn](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Gå till **Inställningar**  >  **replikerade objekt** och välj sedan den virtuella datorn. Sidan **Essentials** visar information om den virtuella datorns inställningar och status.
1. I **Egenskaper** kan du Visa information om replikering och redundans för den virtuella datorn.
1. I **beräknings-och nätverks**  >  **beräknings egenskaper** kan du ändra flera egenskaper för virtuella datorer.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Fönstret Egenskaper för beräkning och nätverk":::

   - **Namn på virtuell Azure-dator**: ändra namnet så att det uppfyller Azure-kraven, om det behövs.
   - **Storlek på virtuell måldator eller VM-typ**: standard storleken på virtuella datorer väljs utifrån parametrar som inkluderar disk antal, antal nätverkskort, antal processor kärnor, minne och tillgängliga storlekar för virtuella dator roller i Azure-regionen. Azure Site Recovery väljer den första tillgängliga VM-storleken som uppfyller alla villkor. Du kan välja en annan VM-storlek baserat på dina behov när som helst före redundansväxlingen. Storleken på den virtuella dator disken baseras också på käll diskens storlek och kan bara ändras efter redundansväxlingen. Lär dig mer om disk storlekar och IOPS-priser vid [skalbarhet och prestanda mål för VM-diskar i Windows](../virtual-machines/windows/disk-scalability-targets.md).
   - **Resurs grupp**: du kan välja en [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)där en virtuell dator blir en del av en post växling vid fel. Du kan ändra den här inställningen när som helst före redundansväxlingen. Efter redundansväxlingen, om du migrerar den virtuella datorn till en annan resurs grupp, avbryts skydds inställningarna för den virtuella datorn.
   - **Tillgänglighets uppsättning**: du kan välja en [tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md) om den virtuella datorn måste ingå i en post-redundansväxling. Tänk på följande när du väljer en tillgänglighets uppsättning:
     - Endast tillgänglighets uppsättningar som tillhör den angivna resurs gruppen visas.
     - Virtuella datorer som finns i olika virtuella nätverk kan inte ingå i samma tillgänglighets uppsättning.
     - Endast virtuella datorer av samma storlek kan ingå i en tillgänglighets uppsättning.

1. Du kan också lägga till information om mål nätverket, under nätet och IP-adressen som har tilldelats till den virtuella Azure-datorn.
1. I **diskar** kan du se operativ systemet och data diskarna på den virtuella datorn som kommer att replikeras.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurera nätverk och IP-adresser

Du kan ange mål-IP-adress:

- Om du inte anger en adress använder den misslyckade virtuella datorn DHCP.
- Om du anger en adress som inte är tillgänglig vid redundans fungerar inte redundansväxlingen.
- Om adressen är tillgänglig i nätverket för redundanstest kan du använda samma mål-IP-adress för redundanstest.

Antalet nätverkskort styrs av den storlek som du anger för den virtuella mål datorn enligt följande:

- Om antalet nätverkskort på den virtuella käll datorn är mindre än eller lika med antalet nätverkskort som tillåts för den virtuella mål datorns storlek, har målet samma antal kort som källan.
- Om antalet nätverkskort för den virtuella käll datorn överskrider det antal som tillåts för den virtuella mål datorns storlek, används Max storleken för målet. Om till exempel en virtuell käll dator har två nätverkskort och storleken på den virtuella mål datorn stöder fyra, har den virtuella mål datorn två kort. Om den virtuella käll datorn har två nätverkskort men mål storleken endast stöder ett, har den virtuella mål datorn bara ett kort.
- Om den virtuella datorn har flera nätverkskort ansluter alla till samma nätverk. Det första kortet som visas i listan blir också standard nätverkskortet på den virtuella Azure-datorn.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

Microsoft Software Assurance-kunder kan använda Azure Hybrid-förmån för att spara licens kostnader för Windows Server-datorer som migreras till Azure. Förmånen gäller även för Azure Disaster Recovery. Om du är berättigad kan du tilldela förmånen för den virtuella datorn som Site Recovery skapar om det finns en redundansväxling.

1. Gå till **dator-och nätverks egenskaperna** för den replikerade virtuella datorn.
1. Svara när du tillfrågas om du har en Windows Server-licens som gör att du är berättigad till Azure Hybrid-förmån.
1. Bekräfta att du har en giltig Windows Server-licens med Software Assurance som du kan använda för att tillämpa förmånen på den virtuella datorn som ska skapas vid en redundansväxling.
1. Spara inställningarna för den replikerade virtuella datorn.

[Läs mer](https://azure.microsoft.com/pricing/hybrid-benefit/) om Azure Hybrid-förmån.

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har nått ett skyddat tillstånd kan du prova med en [redundansväxling](site-recovery-failover.md) för att kontrol lera om programmet visas i Azure.

- [Läs mer](site-recovery-manage-registration-and-protection.md) om hur du rensar registrerings-och skydds inställningar för att inaktivera replikering.
- [Lär dig mer](vmware-azure-disaster-recovery-powershell.md) om hur du automatiserar replikering för dina virtuella datorer med hjälp av PowerShell.
