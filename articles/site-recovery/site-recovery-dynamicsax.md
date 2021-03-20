---
title: Haveri beredskap för Dynamics AX med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för Dynamics AX med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133826"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Konfigurera katastrof återställning för ett Dynamics AX-program med flera nivåer   




 Dynamics AX är en av de mest populära ERP-lösningar som används av företag för att standardisera processer mellan platser, hantera resurser och förenkla efterlevnad. Eftersom programmet är kritiskt för en organisation, bör programmet vara igång på kort tid, i händelse av en katastrof.

I dag tillhandahåller inte Dynamics AX några inaktuella funktioner för katastrof återställning. Dynamics AX består av många Server komponenter, t. ex. Windows program objekt Server, Azure Active Directory, Azure SQL Database, SharePoint Server och repor ting Services. För att hantera haveri beredskap för var och en av dessa komponenter manuellt är det inte bara dyrt utan också fel som kan uppstå.

Den här artikeln förklarar hur du kan skapa en katastrof återställnings lösning för Dynamics AX-programmet med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den täcker även planerade/oplanerade redundanstest genom att använda en återställnings plan med ett klick, konfigurationer som stöds och krav.



## <a name="prerequisites"></a>Förutsättningar

Att implementera haveri beredskap för Dynamics AX-program genom att använda Site Recovery kräver följande krav:

• Konfigurera en lokal Dynamics AX-distribution.

• Skapa ett Site Recovery-valv i en Azure-prenumeration.

• Om Azure är din återställnings plats kör du verktyget Azure Virtual Machine readiness Assessment på de virtuella datorerna. De måste vara kompatibla med Azure-Virtual Machines och Site Recovery-tjänster.

## <a name="site-recovery-support"></a>Site Recovery-stöd

För att kunna skapa den här artikeln använde vi virtuella VMware-datorer med Dynamics AX 2012 R3 på Windows Server 2012 R2 Enterprise. Eftersom Site Recovery replikeringen är en oberoende för program, förväntar vi rekommendationerna som finns här för följande scenarier.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Aktivera haveri beredskap för Dynamics AX-programmet genom att använda Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Skydda ditt Dynamics AX-program
För att aktivera fullständig programreplikering och återställning måste varje komponent i Dynamics AX skyddas.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. konfigurera Active Directory och DNS-replikering

Active Directory krävs på webbplatsen för haveri beredskap för att Dynamics AX-programmet ska fungera. Vi rekommenderar följande två alternativ beroende på hur komplex kundens lokala miljö är.

**Alternativ 1**

Kunden har ett litet antal program och en enda domänkontrollant för hela den lokala platsen och planerar att redundansväxla hela platsen. Vi rekommenderar att du använder Site Recovery replikering för att replikera domänkontrollantens dator till en sekundär plats (gäller för både plats-till-plats-och plats-till-Azure-scenarier).

**Alternativ 2**

Kunden har ett stort antal program och kör en Active Directory skog och planerar att redundansväxla några program i taget. Vi rekommenderar att du konfigurerar ytterligare en domänkontrollant på webbplatsen för haveri beredskap (en sekundär plats eller i Azure).

 Mer information finns i [göra en domänkontrollant tillgänglig på en katastrof återställnings plats](site-recovery-active-directory.md). I resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på återställnings platsen för haveri beredskap.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurera SQL Server replikering
Teknisk vägledning för det rekommenderade alternativet för att skydda SQL-nivån finns i [Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Aktivera skydd för de virtuella datorerna för Dynamics AX-klienten och program objekt servern
Utför relevant Site Recovery konfiguration baserat på om de virtuella datorerna distribueras på [Hyper-V](./hyper-v-azure-tutorial.md) eller [VMware](./vmware-azure-tutorial.md).

> [!TIP]
> Vi rekommenderar att du konfigurerar den kraschbaserade frekvensen till 15 minuter.
>

Följande ögonblicks bild visar skydds status för virtuella datorer i Dynamics-komponenter i ett VMware-scenario för plats-till-Azure-skydd.

![Skyddade objekt](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurera nätverk
**Konfigurera beräknings-och nätverks inställningar för virtuella datorer**

För de virtuella datorerna i Dynamics AX-klienten och program objekt servern konfigurerar du nätverks inställningar i Site Recovery så att de virtuella dator nätverken blir anslutna till rätt haveri beredskap efter redundansväxlingen. Se till att Disaster Recovery-nätverket för dessa nivåer är flyttbara till SQL-nivån.

Du kan välja den virtuella datorn i de replikerade objekten för att konfigurera nätverks inställningarna, som du ser i följande ögonblicks bild:

* För program objekt Server servrar väljer du rätt tillgänglighets uppsättning.

* Om du använder en statisk IP-adress anger du den IP-adress som du vill att den virtuella datorn ska ta i text rutan **mål-IP** .

    ![Nätverksinställningar](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. skapa en återställnings plan

Du kan skapa en återställnings plan i Site Recovery för att automatisera redundansväxlingen. Lägg till en app-nivå och en webb nivå i återställnings planen. Ordna dem i olika grupper så att klient delen stängs av innan app-nivån.

1. Välj Site Recovery valv i prenumerationen och välj panelen **återställnings planer** .

2. Välj **+ återställnings plan** och ange ett namn.

3. Välj **källa** och **mål**. Målet kan vara Azure eller en sekundär plats. Om du väljer Azure måste du ange distributions modellen.

    ![Skapa en återställningsplan](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Välj program objekt servern och de virtuella klient datorerna för återställnings planen och välj ✓.

    ![Välj objekt](./media/site-recovery-dynamics-ax/selectvms.png)

    Exempel på återställnings plan:

    ![Information om återställningsplan](./media/site-recovery-dynamics-ax/recoveryplan.png)

Du kan anpassa återställnings planen för Dynamics AX-programmet genom att lägga till följande steg. Den tidigare ögonblicks bilden visar hela återställnings planen när du har lagt till alla steg.


* **SQL Server redundans**: Mer information om återställnings steg som är speciella för SQL Server finns i [program för replikering med SQL Server och Azure Site Recovery](site-recovery-sql.md).

* **Redundans grupp 1**: redundansväxla de virtuella datorerna i program objekt servern.
Se till att den valda återställnings punkten är så nära databasens depå som möjligt, men inte före den.

* **Skript**: Lägg till belastningsutjämnare (endast E-A).
Lägg till ett skript (via Azure Automation) efter att program objekt serverns VM-grupp har registrerats för att lägga till en belastningsutjämnare till den. Du kan använda ett skript för att utföra den här uppgiften. Mer information finns i [så här lägger du till en belastningsutjämnare för haveri beredskap för program](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)i flera nivåer.

* **Redundans grupp 2**: redundansväxla virtuella Dynamics AX-klientdatorer. Redundansväxla de virtuella datorerna på webb nivå som en del av återställnings planen.


### <a name="perform-a-test-failover"></a>Utför ett redundanstest

Mer information om hur du Active Directory under redundanstest finns i hand boken "Active Directory katastrof återställnings lösning".

Mer information om SQL Server under redundanstest finns i [Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure Portal och välj Site Recovery valvet.

2. Välj den återställnings plan som skapats för Dynamics AX.

3. Välj **Testa redundans**.

4. Välj det virtuella nätverket för att starta processen för redundanstest.

5. När den sekundära miljön är upp kan du utföra dina verifieringar.

6. När verifieringen är klar väljer du **valideringar som slutförts** och testa redundansväxlingen är rensad.

Mer information om hur du utför redundanstest finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Utföra en redundansväxling

1. Gå till Azure Portal och välj Site Recovery valvet.

2. Välj den återställnings plan som skapats för Dynamics AX.

3. Välj **redundans** och välj **redundans**.

4. Välj mål nätverket och välj **✓** för att starta redundansväxlingen.

Mer information om hur du utför en växling vid fel finns [i redundansväxlingen i Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Utföra en återställning efter fel

Information om hur du SQL Server under återställning efter fel finns i [Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure Portal och välj Site Recovery valvet.

2. Välj den återställnings plan som skapats för Dynamics AX.

3. Välj **redundans** och välj **redundans**.

4. Välj **ändra riktning**.

5. Välj lämpliga alternativ: datasynkronisering och skapande av virtuell dator.

6. Välj **✓** för att starta processen för återställning efter fel.


Mer information om hur du utför en återställning efter fel finns i [återställning av virtuella VMware-datorer från Azure till lokala platser](./vmware-azure-failback.md).

## <a name="summary"></a>Sammanfattning
Med hjälp av Site Recovery kan du skapa en fullständig automatiserad katastrof återställnings plan för ditt Dynamics AX-program. I händelse av ett avbrott kan du initiera redundansväxlingen inom några sekunder från var som helst och få programmet igång på några minuter.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar företags arbets belastningar med Site Recovery finns i [vilka arbets belastningar kan jag skydda?](site-recovery-workload.md).
