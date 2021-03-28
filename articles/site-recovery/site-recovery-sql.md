---
title: Konfigurera katastrof återställning för SQL Server med Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar haveri beredskap för SQL Server med SQL Server och Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: ab2eb8a43fc75eea61a03bc25b2b6afc850d30aa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644401"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurera katastrof återställning för SQL Server

Den här artikeln beskriver hur du kan skydda SQL Server Server del i ett program. Du gör det genom att använda en kombination av SQL Server BCDR-teknik (affärs kontinuitet och haveri beredskap) och [Azure Site Recovery](site-recovery-overview.md).

Innan du börjar ska du kontrol lera att du förstår SQL Server haveri beredskap. Dessa funktioner är:

* Redundansklustring
* Always on-tillgänglighetsgrupper
* Databasspegling
* Loggöverföring
* Aktiv geo-replikering
* Automatiska redundansgrupper

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Kombinera BCDR-tekniker med Site Recovery

Ditt val av BCDR-teknik för att återställa SQL Server-instanser bör baseras på ditt återställnings tids mål (RTO) och återställnings punkt mål (återställnings punkt mål) enligt beskrivningen i följande tabell. Kombinera Site Recovery med redundansväxling av den valda tekniken för att dirigera återställning av hela programmet.

Distributions typ | BCDR-teknik | Förväntad RTO för SQL Server | Förväntad återställnings punkt för SQL Server |
--- | --- | --- | ---
SQL Server på en virtuell dator i Azure Infrastructure as a Service (VM) eller lokalt.| [Always On-tillgänglighetsgrupp](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) | Den tid det tar att göra den sekundära repliken som primär. | Eftersom replikeringen till den sekundära repliken är asynkron finns det vissa data förluster.
SQL Server på en virtuell Azure IaaS-dator eller lokalt.| [Kluster för växling vid fel (Always on FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Den tid det tar att redundansväxla noderna. | Eftersom Always on FCI använder delad lagring, är samma vy av lagrings instansen tillgänglig vid redundansväxling.
SQL Server på en virtuell Azure IaaS-dator eller lokalt.| [Databas spegling (högpresterande läge)](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Den tid det tar att framtvinga tjänsten, som använder speglings servern som en varm standby-Server. | Replikeringen är asynkron. Speglings databasen kan vara en fördröjning bakom huvud databasen. Fördröjningen är vanligt vis liten. Men den kan bli stor om huvud-eller speglings serverns system är hårt belastat.<br/><br/>Logg överföring kan vara ett tillägg till databas spegling. Det är ett fördelaktig alternativ till asynkron databas spegling.
SQL as Platform as a Service (PaaS) på Azure.<br/><br/>Den här distributions typen innehåller enkla databaser och elastiska pooler. | Aktiv geo-replikering | 30 sekunder efter att redundansväxlingen har utlösts.<br/><br/>När redundans aktive ras för en av de sekundära databaserna, länkas alla andra sekundära sekundära automatiskt till den nya primära. | Återställningen av fem sekunder.<br/><br/>Aktiv geo-replikering använder alltid teknik på SQL Server. Den replikerar allokerade transaktioner asynkront på den primära databasen till en sekundär databas med hjälp av ögonblicks bild isolering.<br/><br/>Sekundär data garanteras att aldrig ha partiella transaktioner.
SQL as-PaaS har kon figurer ATS med aktiv geo-replikering på Azure.<br/><br/>Den här distributions typen innehåller en hanterad instans, elastiska pooler och enskilda databaser. | Automatiska redundansgrupper | RTO på en timme. | Återställningen av fem sekunder.<br/><br/>Grupper för automatisk redundans ger gruppsemantiken ovanpå aktiv geo-replikering. Men samma mekanism för asynkron replikering används.
SQL Server på en virtuell Azure IaaS-dator eller lokalt.| Replikering med Azure Site Recovery | RTO är vanligt vis mindre än 15 minuter. Läs mer i [RTO-SLA som tillhandahålls av Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | En timme för program konsekvens och fem minuter för krasch konsekvens. Om du vill ha lägre återställnings punkt använder du andra BCDR-tekniker.

> [!NOTE]
> Några viktiga överväganden när du hjälper till att skydda SQL-arbetsbelastningar med Site Recovery:
> * Site Recovery är Application oberoende. Site Recovery kan skydda alla versioner av SQL Server som distribueras på ett operativ system som stöds. Mer information finns i [support mat ris för återställning](vmware-physical-azure-support-matrix.md#replicated-machines) av replikerade datorer.
> * Du kan välja att använda Site Recovery för alla distributioner på Azure, Hyper-V, VMware eller fysisk infrastruktur. Följ rikt linjerna i slutet av den här artikeln om [hur du skyddar ett SQL Server kluster](#how-to-help-protect-a-sql-server-cluster) med Site Recovery.
> * Se till att data ändrings takten på datorn ligger inom [Site Recovery gränser](vmware-physical-azure-support-matrix.md#churn-limits). Ändrings takten mäts i skrivna byte per sekund. För datorer som kör Windows kan du Visa den här ändrings hastigheten genom att välja fliken **prestanda** i aktivitets hanteraren. Observera skriv hastigheten för varje disk.
> * Site Recovery stöder replikering av instanser av redundanskluster på Lagringsdirigering. Mer information finns i [så här aktiverar du Lagringsdirigering replikering](azure-to-azure-how-to-enable-replication-s2d-vms.md).
> 
> När du migrerar din SQL-arbetsbelastning till Azure rekommenderar vi att du tillämpar [rikt linjerna för SQL Server på azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="disaster-recovery-of-an-application"></a>Katastrof återställning av ett program

Site Recovery dirigerar redundanstest och redundansväxlingen för hela programmet med hjälp av återställnings planer.

Det finns vissa krav för att se till att din återställnings plan är helt anpassad beroende på dina behov. En SQL Server distribution behöver vanligt vis en Active Directory-distribution. Den behöver också anslutning för program nivån.

### <a name="step-1-set-up-active-directory"></a>Steg 1: konfigurera Active Directory

Konfigurera Active Directory på den sekundära återställnings platsen för SQL Server att köras korrekt.

* **Litet företag**: du har ett litet antal program och en enda domänkontrollant för den lokala platsen. Om du vill redundansväxla hela platsen använder Site Recovery replikering. Den här tjänsten replikerar domänkontrollanten till det sekundära data centret eller till Azure.
* **Medel stora till stora företag**: du kan behöva konfigurera ytterligare domänkontrollanter.
  - Om du har ett stort antal program, har en Active Directory skog och vill växla över via program eller arbets belastning, konfigurerar du en annan domänkontrollant i det sekundära data centret eller i Azure.
  -  Om du använder Always on-tillgänglighetsgrupper för att återställa till en fjärrplats, konfigurerar du en annan domänkontrollant på den sekundära platsen eller i Azure. Den här domänkontrollanten används för den återställda SQL Server-instansen.

Anvisningarna i den här artikeln förutsätter att en domänkontrollant är tillgänglig på den sekundära platsen. Mer information finns i procedurerna för [att skydda Active Directory med Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Steg 2: kontrol lera anslutningen till andra nivåer

När databas nivån körs i Azure-regionen måste du kontrol lera att du har anslutning till program-och webb nivåerna. Vidta nödvändiga åtgärder i förväg för att verifiera anslutningen med redundanstest.

För att förstå hur du kan utforma program för anslutnings överväganden, se följande exempel:

* [Utforma ett program för haveri beredskap för molnet](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Katastrof återställnings strategier för elastisk pool](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Steg 3: samverka med Always on, aktiv geo-replikering och grupper för automatisk redundans

BCDR Technologies Always on, Active geo-replikering och autofailover-grupper har sekundära repliker av SQL Server som körs i Azures mål region. Det första steget för din programredundans är att ange den här repliken som primär. Det här steget förutsätter att du redan har en domänkontrollant i den sekundära. Steget kanske inte behövs om du väljer att göra en automatisk redundansväxling. Redundansväxla bara dina webb-och program nivåer när databasen har redundans slutförts.

> [!NOTE]
> Om du har hjälpt att skydda SQL-datorerna med Site Recovery behöver du bara skapa en återställnings grupp för dessa datorer och lägga till redundansväxlingen i återställnings planen.

[Skapa en återställnings plan](site-recovery-create-recovery-plans.md) med virtuella program och virtuella datorer på webben. Följande steg visar hur du lägger till redundans för databas nivån:

1. Importera skripten för att redundansväxla SQL tillgänglighets grupp i både en [virtuell Resource Manager-dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) och en [klassisk virtuell dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importera skripten till ditt Azure Automation-konto.

    [![Bild av en "distribuera till Azure"-logo typ](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till skriptet ASR-SQL-FailoverAG som en för åtgärd för den första gruppen i återställnings planen.

1. Följ instruktionerna som finns i skriptet för att skapa en Automation-variabel. Den här variabeln innehåller namnet på tillgänglighets grupperna.

### <a name="step-4-conduct-a-test-failover"></a>Steg 4: utför ett redundanstest

Vissa BCDR-tekniker, som SQL Always on, stöder inte redundanstest. Vi rekommenderar att du använder den här metoden *endast när du använder sådan teknik*.

1. Konfigurera [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) på den virtuella datorn som är värd för tillgänglighets grupps repliken i Azure.

1. Innan du aktiverar redundanstest för återställnings planen återställer du den virtuella datorn från säkerhets kopian som gjordes i föregående steg.

    ![Skärm bild som visar ett fönster för att återställa en konfiguration från Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Framtvinga ett kvorum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) på den virtuella datorn som har återställts från säkerhets kopian.

1. Uppdatera IP-adressen för lyssnaren så att den är en adress som är tillgänglig i nätverket för testning av redundans.

    ![Skärm bild av dialog rutan regel fönster och egenskaper för IP-adress](./media/site-recovery-sql/update-listener-ip.png)

1. Ta med lyssnaren online.

    ![Skärm bild av fönster med etiketten Content_AG visar Server namn och status](./media/site-recovery-sql/bring-listener-online.png)

1. Se till att belastningsutjämnaren i redundansklustret har en IP-adress, från den frontend-IP-adresspool som motsvarar varje tillgänglighets grupps lyssnare och med SQL Server VM i backend-poolen.

     ![Skärm bild av fönstret med rubriken "SQL-AlwaysOn-LB-frontend-IP-pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Skärm bild av fönstret med rubriken "SQL-AlwaysOn-LB-backend IP-adresspool](./media/site-recovery-sql/create-load-balancer2.png)

1. I senare återställnings grupper lägger du till redundans för program nivån följt av din webb nivå för den här återställnings planen.

1. Gör ett redundanstest för återställnings planen för att testa redundans från slut punkt till slut punkt för ditt program.

## <a name="steps-to-do-a-failover"></a>Steg för att utföra en redundansväxling

När du har lagt till skriptet i steg 3 och verifierar det i steg 4, kan du utföra en redundansväxling av återställnings planen som du skapade i steg 3.

Stegen för redundans för program-och webb nivåer bör vara samma i både redundanstest och återställnings planer för redundans.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Skydda ett SQL Server kluster

För ett kluster som kör SQL Server Standard Edition eller SQL Server 2008 R2 rekommenderar vi att du använder Site Recovery replikering för att skydda SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure till Azure och lokalt till Azure

Site Recovery ger inte gäst kluster stöd vid replikering till en Azure-region. SQL Server Standard Edition tillhandahåller inte heller en katastrof återställnings lösning med låg kostnad. I det här scenariot rekommenderar vi att du skyddar SQL Server-klustret till en fristående SQL Server instans på den primära platsen och återställer det i den sekundära.

1. Konfigurera ytterligare en fristående SQL Server instans på den primära Azure-regionen eller på den lokala platsen.

1. Konfigurera instansen så att den fungerar som en spegling för de databaser som du vill skydda. Konfigurera spegling i hög säkerhets läge.

1. Konfigurera Site Recovery på den primära platsen för virtuella [Azure](azure-to-azure-tutorial-enable-replication.md)-, [Hyper-V-](./hyper-v-azure-tutorial.md)eller [VMware-datorer och fysiska servrar](./vmware-azure-tutorial.md).

1. Använd Site Recovery replikering för att replikera den nya SQL Server-instansen till den sekundära platsen. Eftersom det är en hög säkerhets kopia kommer den att synkroniseras med det primära klustret men replikeras med hjälp av Site Recovery replikering.

   ![Bild av ett standard kluster som visar relationen och flödet mellan en primär plats, Site Recovery och Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Anmärkningar om återställning

För SQL Server Standard kluster kräver återställning efter fel efter en oplanerad redundansväxling en SQL Server säkerhets kopiering och återställning. Den här åtgärden görs från speglings instansen till det ursprungliga klustret med återetablering av speglingen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hur får SQL Server licensieras när de används med Site Recovery?

Site Recovery replikering för SQL Server omfattas av Disaster Recovery-förmånen för Software Assurance. Den här täckningen gäller för alla Site Recovery scenarier: lokalt till Azure haveri beredskap och Azure IaaS haveri beredskap över flera regioner. Mer information finns i [Azure Site Recovery priser](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>Kommer Site Recovery att stödja SQL Server-versionen?

Site Recovery är Application oberoende. Site Recovery kan skydda alla versioner av SQL Server som distribueras på ett operativ system som stöds. Mer information finns i [support mat ris för återställning](vmware-physical-azure-support-matrix.md#replicated-machines) av replikerade datorer.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Site Recovery arkitektur](./azure-to-azure-architecture.md).
* För SQL Server i Azure kan du läsa mer om [lösningar för hög tillgänglighet](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) för återställning i en sekundär Azure-region.
* För SQL Database kan du läsa mer om alternativen för [verksamhets kontinuitet](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) och [hög tillgänglighet](../azure-sql/database/high-availability-sla.md) för återställning i en sekundär Azure-region.
* För SQL Server datorer på plats kan du läsa mer om alternativen för [hög tillgänglighet](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) för återställning i Azure Virtual Machines.