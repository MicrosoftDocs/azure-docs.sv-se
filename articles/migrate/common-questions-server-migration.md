---
title: Vanliga frågor om migrering av Azure Migrate Server
description: Få svar på vanliga frågor om att använda Azure Migrate Server-migrering för att migrera datorer.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548821"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrering av Azure Migrate Server: vanliga frågor

I den här artikeln besvaras vanliga frågor om verktyget Azure Migrate: Migreringsverktyg för Server. Om du har andra frågor kontrollerar du följande resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [identifiering, utvärdering och beroende visualisering](common-questions-discovery-assessment.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Konverterar Azure Migrate UEFI-baserade datorer till BIOS-baserade datorer och migrerar dem till Azure som virtuella Azure generation 1-datorer?
Azure Migrate: verktyget Migreringsverktyg migrerar alla UEFI-baserade datorer till Azure som virtuella Azure generation 2-datorer. Vi stöder inte längre konvertering av UEFI-baserade virtuella datorer till BIOS-baserade virtuella datorer. Observera att alla BIOS-baserade datorer migreras till Azure som Azure generation 1-virtuella datorer.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Hur kan jag migrera UEFI-baserade datorer till Azure som virtuella Azure generation 1-datorer?
Azure Migrate: verktyget Migreringsverktyg migrerar UEFI-baserade datorer till Azure som virtuella Azure generation 2-datorer. Om du vill migrera dem till virtuella datorer i Azure generation 1 konverterar du Start typen till BIOS innan du startar replikeringen och använder sedan verktyget Azure Migrate: Migreringsverktyg för att migrera till Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Vilka operativ system stöds för migrering av UEFI-baserade datorer till Azure?

| **Operativ system som stöds för UEFI-baserade datorer** | **Agent lös VMware till Azure**                                                                                                             | **Agent lös Hyper-V till Azure** | **Agent-baserade VMware, fysiska och andra moln till Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | Y<br>                 _RHEL 8. x kräver [manuell förberedelse](./prepare-for-migration.md#linux-machines)_   | Y                              | Y                                                          |
| % OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | Y<br>_% OS 8. x kräver [manuell förberedelse](./prepare-for-migration.md#linux-machines)_ | Y                              | Y                                                          |
| Oracle Linux 7,7, 7,7 – CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Kan jag använda Recovery Services-valvet som skapats av Azure Migrate för katastrof återställnings scenarier?
Vi rekommenderar inte att du använder Recovery Services-valvet som skapats av Azure Migrate för katastrof återställnings scenarier. Detta kan leda till att det inte går att starta replikeringar i Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Var bör jag installera Replication-enheten för agentbaserade migreringar?

Replikeringssystemet bör installeras på en dedikerad dator. Replikeringssystemet bör inte installeras på en käll dator som du vill replikera eller på Azure Migrate identifierings-och utvärderings installation som du kan ha installerat tidigare. Följ [själv studie kursen](./tutorial-migrate-physical-virtual-machines.md) om du vill ha mer information.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Hur kan jag migrera mina AWS EC2-instanser till Azure?

Läs den här [artikeln](./tutorial-migrate-aws-virtual-machines.md) för att identifiera, utvärdera och MIGRERA dina AWS EC2-instanser till Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Kan jag migrera AWS-virtuella datorer som kör Amazon Linux-operativsystemet?

Virtuella datorer som kör Amazon Linux kan inte migreras eftersom Amazon Linux OS bara stöds på AWS.
Om du vill migrera arbets belastningar som körs på Amazon Linux kan du skapa en virtuell dator med CentOS/RHEL i Azure och migrera arbets belastningen som körs på AWS Linux-datorn med en relevant metod för migrering av arbets belastning. Beroende på arbets belastningen kan det till exempel finnas arbets belastnings bara verktyg för att under lätta migreringen – till exempel för databaser eller distributions verktyg i händelse av webb servrar.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Vilka geografiska områden stöds för migrering med Azure Migrate?

Granska de geografiska områden som stöds för [offentliga moln](migrate-support-matrix.md#supported-geographies-public-cloud) och [myndighetsmoln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Kan vi använda samma Azure Migrate projekt för att migrera till flera regioner?

Även om du kan skapa utvärderingar för flera regioner i ett Azure Migrate projekt, kan ett Azure Migrate projekt endast användas för att migrera servrar till en Azure-region. Du kan skapa ytterligare Azure Migrate projekt för varje region som du behöver migrera till.

- För att få till gång till en agent utan en VMware-migrering är mål regionen låst när du aktiverar den första replikeringen.
- För agentbaserade migreringar (VMware, fysiska servrar och servrar från andra moln) är mål regionen låst när du klickar på knappen "skapa resurser" på portalen när du konfigurerar replikerings enheten.
- För agentbaserade Hyper-V-migreringar är mål regionen låst när du klickar på knappen "skapa resurser" på portalen när du konfigurerar Hyper-V-replikeringsprovidern.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Kan vi använda samma Azure Migrate projekt för att migrera till flera prenumerationer? 

Ja, du kan migrera till flera prenumerationer i samma mål region för ett Azure Migrate-projekt. Du kan välja mål prenumerationen samtidigt som du aktiverar replikering för en dator eller en uppsättning datorer. Mål regionen är låst publicera den första replikeringen för icke-replikerade VMware-migreringar och vid installation av Hyper-V-Provider för agentbaserade migreringar och virtuella Hyper-V-migreringar.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Vilka migrerings alternativ finns i Azure Migrate: Server migrering?

Verktyget Azure Migrate: Migreringsverktyg för Server innehåller två alternativ för att utföra migrering av dina käll servrar/VM: ar till Azure – agent lös migrering och agent-baserad migrering.

Oavsett vilket alternativ för migrering som valts, det första steget för att migrera en server med Azure migration: Server migrering är att aktivera replikering för servern. Detta utför en inledande replikering av dina VM/Server-data till Azure. När den inledande replikeringen har slutförts upprättas en pågående replikering (pågående delta-synkronisering) för att migrera stegvisa data till Azure. När åtgärden har nått steget delta-synkronisering kan du när som helst välja att migrera till Azure.  

Här är några saker som du bör tänka på när du bestämmer dig för migreringen.

Utan **agent-migrering** kräver att ingen program vara (agenter) distribueras på de virtuella käll datorerna/-servrarna som migreras. Alternativet utan agent dirigerar replikeringen genom att integrera med de funktioner som tillhandahålls av Virtualization-providern.
Alternativen för replikering utan agent är tillgängliga för virtuella [VMware-datorer](./tutorial-migrate-vmware.md) och [virtuella Hyper-V-datorer](./tutorial-migrate-hyper-v.md).

Med **agentbaserade migreringar** måste Azure Migrate program vara (agenter) installeras på de virtuella käll datorer/datorer som ska migreras. Det agentbaserade alternativet är inte beroende av virtualiseringsplattformen för replikering och kan därför användas med en server som kör en x86/x64-arkitektur och en version av ett operativ system som stöds av den agentbaserade replikeringsprincipen.

Alternativet för programbaserad migrering kan användas för [virtuella VMware-datorer](./tutorial-migrate-vmware-agent.md), virtuella [Hyper-V-datorer](./tutorial-migrate-physical-virtual-machines.md), [fysiska servrar](./tutorial-migrate-physical-virtual-machines.md), [virtuella datorer som körs på AWS](./tutorial-migrate-aws-virtual-machines.md), virtuella datorer som körs på GCP eller virtuella datorer som körs på en annan Virtualization-Provider. Den agentbaserade migreringen behandlar dina datorer som fysiska servrar i syfte att migrera.

Även om migreringen av agenten ger ytterligare bekvämlighet och enkelhet jämfört med de agentbaserade replikeringsalternativ för de scenarier som stöds (VMWare och Hyper-V), kanske du vill överväga att använda det agentbaserade scenariot för följande användnings fall:

- IOPS-begränsad miljö: en agent utan replikering använder ögonblicks bilder och använder lagrings-IOPS/bandbredd. Vi rekommenderar den agentbaserade migreringsprocessen om det finns begränsningar för lagring/IOPS i din miljö.
- Om du inte har en vCenter Server kan du behandla dina virtuella VMware-datorer som fysiska servrar och använda det agentbaserade migrerings flödet.

Läs mer i den här [artikeln](./server-migrate-overview.md) för att jämföra migrerings alternativ för VMware-migreringar.

## <a name="how-does-agentless-migration-work"></a>Hur fungerar agent lös migrering?

Azure Migrate: Server Migration tillhandahåller replikerings alternativ utan agent för migrering av virtuella VMware-datorer och virtuella Hyper-V-datorer som kör Windows eller Linux. Verktyget innehåller också ytterligare ett agentbaserade replikeringsalternativ för Windows-och Linux-servrar som kan användas för att migrera fysiska servrar, samt x86/x64-virtuella datorer på VMware, Hyper-V, AWS, GCP osv. Alternativet för den agentbaserade replikeringen kräver installation av agentprogram vara på den server/virtuella dator som migreras, men i alternativet utan agent måste ingen program vara installeras på de virtuella datorerna, vilket ger ytterligare bekvämlighet och enkelhet över alternativet för den agentbaserade replikeringen.

Alternativet för att replikera utan agent fungerar med hjälp av mekanismer som tillhandahålls av Virtualization Provider (VMware, Hyper-V). När det gäller virtuella VMware-datorer använder mekanismen för agent lös replikering VMware-ögonblicksbilder och VMware-ändrade block spårnings teknik för att replikera data från virtuella dator diskar. Den här mekanismen liknar den som används av många säkerhets kopierings produkter. När det gäller virtuella Hyper-V-datorer använder mekanismen för agent lös replikering VM-ögonblicksbilder och ändrings spårnings funktionen i Hyper-V-replikeringen för att replikera data från virtuella dator diskar.

När replikering har kon figurer ATS för en virtuell dator går den först igenom en inledande replikering. Under den inledande replikeringen tas en ögonblicks bild av en virtuell dator och en fullständig kopia av data från ögonblicks bild diskarna replikeras till hanterade diskar i din prenumeration. När den inledande replikeringen för den virtuella datorn är klar övergår replikeringen över till en stegvis replikering (delta replikering)-fas. I fasen för stegvis replikering replikeras de data ändringar som har inträffat sedan den senaste slutförda replikeringen regelbundet och tillämpas på de replik hanterade diskarna, vilket gör att replikeringen synkroniseras med ändringar som sker på den virtuella datorn. När det gäller virtuella VMware-datorer används den ändrade block spårnings tekniken i VMware för att hålla reda på ändringar mellan cykler. I början av replikeringen tas en VM-ögonblicksbild och ändrad block spårning används för att hämta ändringarna mellan den aktuella ögonblicks bilden och den senaste replikerade ögonblicks bilden. På så sätt måste endast data som har ändrats sedan den senaste slutförda replikeringen replikeras för att behålla replikeringen för den virtuella datorn synkroniserad. I slutet av varje replikeringscykel släpps ögonblicks bilden och ögonblicks bilds konsolideringen utförs för den virtuella datorn. För virtuella Hyper-V-datorer används på samma sätt för att spåra ändrings spårnings motorn för Hyper-V-replikering för att hålla reda på ändringar mellan flera olika replikerings-cykler.
När du utför migreringen på en replikerad virtuell dator har du möjlighet att stänga av den lokala virtuella datorn och utföra en slutgiltig stegvis replikering för att säkerställa att inga data går förlorade. När du utför alternativet migrera används de replik hanterade diskar som motsvarar den virtuella datorn för att skapa den virtuella datorn i Azure.

För att komma igång, se självstudierna [VMware agent-migrering](./tutorial-migrate-vmware.md) och [Hyper-V-migrering utan agent](./tutorial-migrate-hyper-v.md) .

## <a name="how-does-agent-based-migration-work"></a>Hur fungerar agent-baserad migrering?

Förutom alternativ för att migrera virtuella VMware-datorer och virtuella Hyper-V-datorer innehåller verktyget Migreringsverktyg ett agent alternativ för migrering för att migrera Windows-och Linux-servrar som körs på fysiska servrar, eller som körs som x86/x64-virtuella datorer på VMware, Hyper-V, AWS, Google Cloud Platform osv.

Den agentbaserade migreringsprocessen använder agentprogram vara som är installerad på den server som migreras för att replikera Server data till Azure. Vid replikeringen används en avlastnings arkitektur där agenten vidarebefordrar replikeringsdata till en dedikerad replikeringsfil som kallas för replikerings-eller konfigurations servern (eller till en skalbar Processerver). [Läs mer](./agent-based-migration-architecture.md) om hur det agentbaserade migrerings alternativet fungerar. 

Obs! replikerings enheten skiljer sig från den Azure Migrate identifierings enheten och måste installeras på en separat/dedikerad dator.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Hur gör jag för att mäta bandbredds kravet för mina migreringar?

Bandbredden för replikering av data till Azure beror på en mängd faktorer och är en funktion i hur snabbt den lokala Azure Migrates enheten kan läsa och replikera data till Azure. Replikeringen har två faser: inledande replikering och delta-replikering.

När replikeringen startar för en virtuell dator sker en inledande replikering där fullständiga kopior av diskarna replikeras. När den inledande replikeringen har slutförts schemaläggs stegvisa replikeringar (delta cykler) för att överföra eventuella ändringar som har inträffat sedan den föregående replikerings cykeln.

### <a name="agentless-vmware-vm-migration"></a>VM-migrering utan agent

Du kan arbeta med bandbredds kravet baserat på den mängd data som behövs för att flytta under den våg och tid inom vilken du vill att den inledande replikeringen ska slutföras (vi rekommenderar att den inledande replikeringen har slutförts minst 3-4 dagar före det faktiska flyttnings fönstret, så att du får tillräckligt med tid för att utföra en testmigrering före det faktiska fönstret och för att hålla stillestånds tiden

Du kan uppskatta bandbredden eller tiden som krävs för migrering av virtuella VMware-datorer med hjälp av följande formel:

Tid för att slutföra inledande replikering = {storlek på diskar (eller använt storlek om det är tillgängligt) * 0,7 (förutsatt att det finns 30 procents komprimerings genomsnitt – försiktig beräkning)}/Bandwidth tillgängliga för replikering.

### <a name="agent-based-vmware-vm-migration"></a>Agent-baserad virtuell VMware-migrering

För en agent-baserad metod för replikering kan distributions planeraren hjälpa till att profilera miljön för data omsättningen och förutsäga kraven för bandbredd som krävs. Om du vill veta mer kan du läsa den här [artikeln](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hur gör jag för att begränsar du replikeringen i med Azure Migrate-utrustning för att kunna använda utan agent?  

Du kan begränsa användningen av NetQosPolicy. Exempel:

AppNamePrefix som ska användas i NetQosPolicy är "GatewayWindowsService.exe". Du kan skapa en princip på Azure Migrate-enheten för att reglera replikeringstrafiken från installationen genom att skapa en princip som den här:

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Hur överförs data från lokal-miljön till Azure? Krypteras den före överföringen?

Den Azure Migrate-installationen i skift läges beredskapen för replikering komprimerar data och krypterar dem innan de överförs. Data överförs via en säker kommunikations kanal över https och använder TLS 1,2 eller senare. Dessutom krypteras dina data automatiskt i Azure Storage när de sparas i molnet (kryptering vid vila).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hur påverkar omsättnings takten för en misslyckad replikering?

Eftersom mikroreplikeringen är i data, är det *omsättnings mönster* som är viktigare än *omsättnings takten*. När en fil skrivs igen och återigen har hastigheten ingen effekt. Men ett mönster där varje annan sektor skrivs orsakar hög omsättning i nästa cykel. Eftersom vi minimerar mängden data som vi överför, tillåter vi att data viks så mycket som möjligt innan vi schemalägger nästa cykel.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hur ofta schemaläggs en replikeringscykel?

Formeln för att schemalägga nästa replikeringscykel är (föregående cykel tid/2) eller en timme, beroende på vilket som är högre.

Om en virtuell dator till exempel tar fyra timmar för en delta cykel, schemaläggs nästa cykel på två timmar och inte under nästa timma. Processen skiljer sig direkt efter den inledande replikeringen, när den första delta cykeln har schemalagts direkt.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Hur gör jag för att migrera Windows Server 2003 som körs på VMware/Hyper-V till Azure?

[Windows Server 2003 Extended support](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) slutade den 14 juli 2015.  Support teamet för Azure fortsätter att hjälpa till med fel sökning av problem som rör körning av Windows Server 2003 på Azure. Detta stöd är dock begränsat till problem som inte kräver fel sökning eller uppdateringar på operativ system nivå.
Att migrera dina program till Azure-instanser som kör en nyare version av Windows Server är den rekommenderade metoden för att säkerställa att du effektivt utnyttjar Azure-molnets flexibilitet och tillförlitlighet.

Men om du fortfarande väljer att migrera Windows Server 2003 till Azure kan du använda verktyget Azure Migrate: Migreringsverktyg för server om din Windows Server är en virtuell dator som körs på VMware eller Hyper-V går du igenom artikeln för att [förbereda dina Windows Server 2003-datorer för migrering](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Vad är skillnaden mellan åtgärderna testa migreringen och migrera?

Med testmigrering kan du testa och verifiera migreringar före den faktiska migreringen. Testa migreringen genom att låta dig skapa test kopior av replikering av virtuella datorer i en sandbox-miljö i Azure. Sandbox-miljön avgränsas av ett virtuellt test nätverk som du anger. Åtgärden testa migreringen är inte störd, och program fortsätter att köras vid källan samtidigt som du kan utföra tester på en klonad kopia i en isolerad sand Box miljö. Du kan utföra flera tester efter behov för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före den faktiska migreringen.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Kommer Windows Server 2008 och 2008 R2 att stödjas i Azure efter migreringen?

Du kan migrera dina lokala Windows Server 2008-och 2008 R2-servrar till Azure virtuella datorer och få utökade säkerhets uppdateringar i tre år efter att support datumen är slut utan ytterligare kostnad utöver kostnaden för att köra den virtuella datorn. Du kan använda verktyget Azure Migrate: Migreringsverktyg för att migrera dina Windows Server 2008-och 2008 R2-arbetsbelastningar.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Finns det ett återställnings alternativ för Azure Migrate?

Du kan använda alternativet testa migrering för att validera dina program funktioner och prestanda i Azure. Du kan utföra ett valfritt antal testmigreringar och utföra den slutliga migreringen när du har upprättat säkerheten genom att testa migreringen. En testmigrering påverkar inte den lokala datorn, som förblir i drift och fortsätter att replikeras tills du utför den faktiska migreringen. Om det fanns några fel under UAT för testmigrering kan du välja att skjuta upp den slutliga migreringen och hålla din virtuella käll dator/server igång och replikera till Azure. Du kan försöka utföra den senaste migreringen igen när du har löst felen.  
Obs! när du har genomfört en slutgiltig migrering till Azure och den lokala käll datorn stängts av kan du inte utföra en återställning från Azure till din lokala miljö.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Kan jag välja det Virtual Network och undernät som ska användas för att testa migreringar?

Du kan välja en Virtual Network för test-migreringar. Under nätet väljs automatiskt baserat på följande logik:

- Om ett mål under nät (annat än standard) angavs som indatatyp när replikeringen aktiverades, så Azure Migrate prioriteras med hjälp av ett undernät med samma namn i Virtual Network som valts för testmigreringen.
- Om under nätet med samma namn inte hittas väljer Azure Migrate det första under nätet i alfabetisk ordning som inte är ett Gateway-/Application Gateway-eller brand Väggs-/skydds-undernät.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Varför är knappen Testa migrering inaktive rad för min server?

Knappen Testa migreringen kan vara i ett inaktiverat tillstånd i följande scenarier:

- Det går inte att påbörja en testmigrering förrän en inledande replikering (IR) har slutförts för den virtuella datorn. Knappen Testa migreringen kommer att inaktive ras tills IR-processen har slutförts. Du kan utföra en testmigrering när den virtuella datorn är i ett Delta-Sync-steg.
- Knappen kan inaktive ras om en testmigrering redan har slutförts, men en rensning av en test-migrering utfördes inte för den virtuella datorn. Utför en rensning av testmigrering och försök igen.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Vad händer om jag inte rensar min testmigrering?

Testmigrering simulerar den faktiska migreringen genom att skapa en virtuell test-Azure-dator med replikerade data. Servern kommer att distribueras med en tidpunkts kopia av replikerade data till mål resurs gruppen (väljs vid aktivering av replikering) med suffixet "-test". Testmigreringar är avsedda för att verifiera Server funktioner så att problem med att publicera migrering minimeras. Om testmigreringen inte rensas efter test, kommer den virtuella test datorn fortsätta att köras i Azure och debiteras. Om du vill rensa inlägget publicerar du en testmigrering, går du till vyn replikerade datorer i Migreringsverktyg för Server och använder åtgärden rensa test migrering på datorn.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Kan jag migrera Active Directory-domänkontrollanter med Azure Migrate?

Verktyget Migreringsverktyg är Application oberoende och fungerar för de flesta program. När du migrerar en server med hjälp av Migreringsverktyg migreras alla program som är installerade på servern tillsammans med den. Men för vissa program kan alternativa migreringsåtgärder som inte är Server migrering vara bättre för migreringen.  För Active Directory, om det gäller hybrid miljöer där den lokala platsen är ansluten till din Azure-miljö, kan du utöka din katalog till Azure genom att lägga till ytterligare domänkontrollanter i Azure och konfigurera Active Directory replikering. Om du migrerar till en isolerad miljö i Azure som kräver sina egna domänkontrollanter (eller testar program i en sandbox-miljö) kan du migrera servrar med hjälp av verktyget Migreringsverktyg.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Vad händer om jag inte stoppar replikering efter migreringen?

När du stoppar replikeringen rensar verktyget Azure Migrate: Migreringsverktyg de hanterade diskarna i prenumerationen som har skapats för replikering. Om du inte stoppar replikering efter en migrering fortsätter du att debiteras för diskarna. Stoppa replikering påverkar inte diskarna som är anslutna till datorer som redan har migrerats.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Behöver jag VMware vCenter för att migrera virtuella VMware-datorer?

Om du vill [migrera virtuella VMware-datorer](server-migrate-overview.md) med hjälp av VMware agent-eller agent lös migrering måste ESXi-värdar som de virtuella datorerna finns på hanteras av vCenter Server. Om du inte har vCenter Server kan du migrera virtuella VMware-datorer genom att migrera dem som fysiska servrar. [Läs mer](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Kan jag uppgradera mitt operativ system vid migrering?

Verktyget Azure Migrate: Migreringsverktyg stöder för närvarande endast liknande migreringar. Verktyget stöder inte uppgradering av operativ systemets version under migreringen. Den migrerade datorn kommer att ha samma OS som käll datorn.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Jag har distribuerat två (eller fler) enheter för att identifiera virtuella datorer i mina vCenter Server. Men när jag försöker migrera de virtuella datorerna ser jag bara virtuella datorer som motsvarar någon av enheterna.

Om det finns flera installerade enheter får det inte finnas någon överlappning mellan de virtuella datorerna på de vCenter-konton som har angetts. En identifiering med sådan överlappning är ett scenario som inte stöds.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Hur gör jag för att vet du om den virtuella datorn har migrerats?

När du har migrerat din virtuella dator/server kan du Visa och hantera den virtuella datorn från sidan Virtual Machines. Anslut till den migrerade virtuella datorn för att verifiera.
Du kan också granska jobb status för åtgärden för att kontrol lera om migreringen har slutförts. Om du ser några fel löser du dem och försöker migrera igen.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Kan jag konsolidera flera virtuella käll datorer till en virtuell dator vid migrering?

Stöd för migrering av Azure Migrate server som till exempel migreringar. Vi stöder inte konsolidering av servrar eller uppgradering av operativ systemet som en del av migreringen. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hur påverkar agenten replikeringen VMware-servrar?

Replikeringen utan agent medför att vissa prestanda påverkas av VMware vCenter Server och VMware ESXi värdar. Eftersom en agent utan replikering använder ögonblicks bilder, förbrukar den IOPS på lagrings utrymme, så viss bandbredd för IOPS krävs. Vi rekommenderar inte att du använder replikering utan Agent om du har begränsningar på lagring eller IOPs i din miljö.


## <a name="next-steps"></a>Nästa steg

Läs [Azure Migrate översikt](migrate-services-overview.md).
