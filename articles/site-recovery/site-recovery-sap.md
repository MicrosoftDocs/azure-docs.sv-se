---
title: Konfigurera haveri beredskap för SAP NetWeaver med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för SAP NetWeaver med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711951"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurera haveri beredskap för en distribution med flera nivåer av SAP NetWeaver-appar

I de flesta stora och medel stora SAP-distributioner används någon form av katastrof återställnings lösning. Vikten av robusta och testable katastrof återställnings lösningar har ökat allteftersom fler viktiga affärs processer flyttas till program som SAP. Azure Site Recovery har testats och integrerats med SAP-program. Site Recovery överstiger funktionerna i de flesta lokala katastrof återställnings lösningar och till en lägre total ägande kostnad än konkurrerande lösningar.

Med Site Recovery kan du:
* Aktivera skydd av SAP-NetWeaver och icke-NetWeaver produktions program som körs lokalt genom att replikera komponenter till Azure.
* Aktivera skydd av SAP-NetWeaver och icke-NetWeaver produktions program som körs på Azure genom att replikera komponenter till ett annat Azure-datacenter.
* Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
* Förenkla SAP-projektets uppgraderingar, testning och prototyp genom att skapa en produktions klon på begäran för att testa SAP-program.

Du kan skydda SAP NetWeaver program distributioner med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskriver metod tips för att skydda en SAP NetWeaver-distribution på tre nivåer i Azure när du replikerar till ett annat Azure-datacenter med hjälp av Site Recovery. I artikeln beskrivs scenarier och konfigurationer som stöds och hur du utför redundanstest (haveri beredskap) och faktiska redundans.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att du vet hur du gör följande uppgifter:

* [Replikera en virtuell dator till Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Utforma ett återställnings nätverk](./azure-to-azure-about-networking.md)
* [Gör ett redundanstest till Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera en SQL Server instans](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan använda Site Recovery för att implementera en katastrof återställnings lösning i följande scenarier:
* Du har SAP-system som körs i ett Azure-datacenter och du replikerar dem till ett annat Azure-datacenter (Azure-till-Azure haveri beredskap). 
   Mer information finns i [arkitektur för Azure-till-Azure-replikering](./azure-to-azure-architecture.md).
* Du har SAP-system som körs på VMware-servrar (eller fysiska) lokalt. Du replikerar också SAP-systemen till en katastrof återställnings plats i ett Azure-datacenter (haveri beredskap från VMware till Azure). 
   Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för VMware-till-Azure-replikering](./vmware-azure-architecture.md).
* Du har SAP-system som körs på Hyper-V lokalt. Du replikerar också SAP-systemen till en katastrof återställnings plats i ett Azure-datacenter (haveri beredskap från Hyper-V till Azure).
   Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för Hyper-V-till-Azure-replikering](./hyper-v-azure-architecture.md).

I den här artikeln använder vi ett scenario för haveri beredskap i **Azure till Azure** . I scenariot visas funktionerna i SAP Disaster Recovery i Site Recovery. Eftersom Site Recovery replikering inte är programspecifikt förväntas den process som beskrivs även gälla för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga grund tjänster
I det scenario vi diskuterar i den här artikeln, är följande grundläggande tjänster distribuerade:
* Azure ExpressRoute eller Azure VPN Gateway
* Minst en Azure Active Directory-domänkontrollant och DNS-server som körs i Azure

Vi rekommenderar att du upprättar den här infrastrukturen innan du distribuerar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Referens för SAP-program

Den här referens arkitekturen kör SAP NetWeaver i en Windows-miljö i Azure med hög tillgänglighet. Den här arkitekturen distribueras med vissa storlekar för virtuella datorer (VM) som du kan ändra så att de passar din organisations behov.

![Diagram över ett typiskt SAP-distributions mönster](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

Vid haveri beredskap måste du kunna redundansväxla till en sekundär region. Varje nivå använder en annan strategi för att tillhandahålla katastrof återställnings skydd.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Virtuella datorer som kör SAP Web dispatcher-pooler

Webb dispatcher-komponenten fungerar som en belastningsutjämnare för SAP-trafik mellan SAP-programservrarna. För att uppnå hög tillgänglighet för webb dispatcher-komponenten kan Azure Load Balancer implementera installations programmet för parallell webb dispatcher. Webb dispatcher använder en konfiguration för resursallokering (Round Robin) för HTTP (S)-trafik distribution bland de tillgängliga webb sändningarna i poolerna för Utjämning av nätverks belastning.

#### <a name="vms-running-application-servers-pools"></a>Virtuella datorer som kör program Server grupper
SMLG-transaktionen hanterar inloggnings grupper för ABAP program servrar. Den använder funktionen belastnings utjämning i meddelande servern för centrala tjänster för att distribuera arbets belastningen mellan SAP Application Server-pooler för SAPGUIs-och RFC-trafik. Du kan replikera den här hanteringen med hjälp av Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Virtuella datorer som kör SAP Central Services-kluster
Den här referens arkitekturen kör centrala tjänster på virtuella datorer på program nivån. Centrala tjänster är en potentiell felpunkt vid en enskild virtuell dator. Typisk distribution och hög tillgänglighet är inte krav.

Om du vill implementera en lösning med hög tillgänglighet kan du antingen använda ett delat disk kluster eller ett fil resurs kluster. Om du vill konfigurera virtuella datorer för ett delat disk kluster använder du Windows Server-redundanskluster. Vi rekommenderar att du använder moln vittnet som ett kvorumlogg.

 > [!NOTE]
 > Eftersom Site Recovery inte replikerar moln vittnet, rekommenderar vi att du distribuerar moln vittnet i Disaster Recovery-regionen.

För att stödja kluster miljön för växling vid fel gör [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) funktionen för klusterdelade volymer. I funktionen replikerar SIOS DataKeeper-klustret oberoende diskar som ägs av klusternoderna. Eftersom Azure inte har inbyggt stöd för delade diskar, krävs det lösningar som tillhandahålls av SIOS.

Du kan också hantera kluster genom att implementera ett fil resurs kluster. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ändrade nyligen distributions mönstret för central tjänster för att få åtkomst till globala/sapmnt-kataloger via en UNC-sökväg. Vi rekommenderar fortfarande att du ser till att/sapmnt UNC-resurs har hög tillgänglighet. Du kan kontrol lera din instans av central tjänster. Använd Windows Server-redundanskluster med skalbar fil server (SOFS) och funktionen Lagringsdirigering (S2D) i Windows Server 2016.

 > [!NOTE]
 > Site Recovery stöder för närvarande endast krasch punkt replikering av virtuella datorer som använder lagrings dirigering och den passiva noden för SIOS-DataKeeper.


## <a name="more-disaster-recovery-considerations"></a>Mer information om haveri beredskap

Du kan använda Site Recovery för att dirigera redundansväxlingen av fullständig SAP-distribution i Azure-regioner.
Nedan följer stegen för att konfigurera haveri beredskap:

1. Replikera virtuella datorer
1. Utforma ett återställnings nätverk
1. Replikera en domänkontrollant
1. Replikera data bas nivån
1. Utföra ett redundanstest
1. Göra en redundansväxling

Följande är en rekommendation för haveri beredskap för varje nivå som används i det här exemplet.

 **SAP-nivåer** | **Rekommendation**
 --- | ---
**SAP Web dispatcher-pool** |  Replikera med hjälp av Site Recovery 
**SAP Application Server-pool** |  Replikera med hjälp av Site Recovery 
**SAP-kluster för central tjänster** |  Replikera med hjälp av Site Recovery 
**Active Directory-virtuella datorer** |  Använd Active Directory-replikering 
**SQL Database-servrar** |  Använd SQL Server Always on Replication

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla virtuella SAP-program till data centret för Azure haveri beredskap följer du rikt linjerna i [replikera en virtuell dator till Azure](./azure-to-azure-tutorial-enable-replication.md).

* Information om hur du skyddar Active Directory och DNS finns i [så här skyddar du Active Directory och DNS](site-recovery-active-directory.md).

* Läs mer om [hur du skyddar SQL Server genom att](site-recovery-sql.md)läsa mer om hur du skyddar databas nivån som körs på SQL Server.

## <a name="networking-configuration"></a>Nätverkskonfiguration

Om du använder en statisk IP-adress kan du ange den IP-adress som du vill att den virtuella datorn ska ta. Om du vill ange IP-adressen går du till nätverkskortet **beräknings-och nätverks inställningar**  >  .

![Skärm bild som visar hur du ställer in en privat IP-adress i fönstret för nätverkskortet Site Recovery nätverkskort](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

En återställnings plan stöder sekvenseringen av olika nivåer i ett program med flera nivåer under en redundansväxling. Sekvenseringen bidrar till att upprätthålla programmets konsekvens. När du skapar en återställnings plan för ett webb program med flera nivåer slutför du stegen som beskrivs i [skapa en återställnings plan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägg till virtuella datorer i grupper för redundans

1. Skapa en återställnings plan genom att lägga till program servern, webb sändningen och de virtuella SAP-tjänsterna.
1. Välj **Anpassa** för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i grupp 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen
För att dina program ska fungera korrekt kan du behöva utföra några åtgärder på de virtuella Azure-datorerna. Utför dessa åtgärder efter redundansväxlingen eller under ett redundanstest. Du kan också automatisera vissa åtgärder efter redundans. Du kan till exempel uppdatera DNS-posten och ändra bindningar och anslutningar genom att lägga till motsvarande skript i återställnings planen.

Du kan distribuera de mest använda Site Recovery-skripten till ditt Azure Automation-konto genom att välja **distribuera till Azure**. När du använder ett publicerat skript följer du anvisningarna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett skript före åtgärd i grupp 1 om du vill redundansväxla SQL Server tillgänglighets gruppen. Använd skriptet ASR-SQL-FailoverAG som publicerats i exempel skripten. Följ anvisningarna i skriptet och gör de nödvändiga ändringarna i skriptet på lämpligt sätt.
1. Lägg till ett skript för efter åtgärd för att koppla en belastningsutjämnare till de misslyckade virtuella datorerna på webb nivån (grupp 1). Använd ASR-AddSingleLoadBalancer-skriptet som publicerats i exempel skripten. Följ anvisningarna i skriptet och gör nödvändiga ändringar i skriptet efter behov.

![SAP-återställnings plan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I Azure Portal väljer du Recovery Services valvet.
1. Välj den återställnings plan som du skapade för SAP-program.
1. Välj **Testa redundans**.
1. Starta processen för redundanstest genom att välja återställnings punkten och det virtuella Azure-nätverket.
1. Utföra verifieringar när den sekundära miljön är igång.
1. När valideringen är klar rensar du redundansväxlingen genom att välja **rensning** av redundanstest.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. I Azure Portal väljer du Recovery Services valvet.
1. Välj den återställnings plan som du skapade för SAP-program.
1. Välj **Redundans**.
1. Starta redundansväxlingen genom att välja återställnings punkten.

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att skapa en katastrof återställnings lösning för SAP NetWeaver-distributioner med hjälp av Site Recovery. Se nedladdnings bara white paper [SAP-NetWeaver: skapa en lösning för katastrof återställning med Site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). White paper diskuterar rekommendationer för olika SAP-arkitekturer. Du kan se program och VM-typer som stöds för SAP på Azure. Det finns också alternativ för att testa din katastrof återställnings lösning.
* Lär dig mer om att [Replikera andra arbets belastningar](site-recovery-workload.md) med hjälp av Site Recovery.