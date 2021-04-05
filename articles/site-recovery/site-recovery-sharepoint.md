---
title: Haveri beredskap för en SharePoint-app med flera nivåer med hjälp av Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar haveri beredskap för ett SharePoint-program med flera nivåer med hjälp av Azure Site Recovery-funktioner.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013759"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Konfigurera katastrof återställning för ett SharePoint-program med flera nivåer för haveri beredskap med hjälp av Azure Site Recovery

Den här artikeln beskriver i detalj hur du skyddar ett SharePoint-program med hjälp av  [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Översikt

Microsoft SharePoint är ett kraftfullt program som kan hjälpa en grupp eller avdelning att organisera, samar beta och dela information. SharePoint kan tillhandahålla intranät portaler, dokument-och fil hantering, samarbete, sociala nätverk, extra nät, webbplatser, Enterprise Search och Business Intelligence. Den har också funktioner för system integrering, process integrering och automatisering av arbets flöde. Organisationer anser vanligt vis att det är ett nivå 1-program som är känsligt för stillestånds tid och data förlust.

Idag tillhandahåller Microsoft SharePoint inte några färdiga funktioner för katastrof återställning. Oavsett typ och skala för en katastrof, innebär återställningen att du använder ett vänte läges Data Center som du kan återställa Server gruppen till. Vänte läges Data Center krävs för scenarier där lokala redundanta system och säkerhets kopieringar inte kan återställas från strömavbrottet vid det primära data centret.

En felfri lösning för haveri beredskap bör möjliggöra modellering av återställnings planer runt komplexa program arkitekturer som SharePoint. Det bör också ha möjlighet att lägga till anpassade steg för att hantera program mappningar mellan olika nivåer och därmed tillhandahålla en enkel klickning med en lägre RTO i händelse av en katastrof.

Den här artikeln beskriver i detalj hur du skyddar ett SharePoint-program med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskriver metod tips för att replikera ett SharePoint-program på tre nivåer till Azure, hur du kan göra en granskning av haveri beredskap och hur du kan redundansväxla programmet till Azure.

Du kan titta på videon nedan om hur du återställer ett program på flera nivåer till Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du förstår följande:

1. [Replikera en virtuell dator till Azure](./vmware-azure-tutorial.md)
2. Så här [skapar du ett återställnings nätverk](./concepts-on-premises-to-azure-networking.md)
3. [Utföra en redundanstest till Azure](site-recovery-test-failover-to-azure.md)
4. [Göra en redundansväxling till Azure](site-recovery-failover.md)
5. Så här [replikerar du en domänkontrollant](site-recovery-active-directory.md)
6. Så här [replikerar du SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-arkitektur

SharePoint kan distribueras på en eller flera servrar med hjälp av nivåbaserade topologier och Server roller för att implementera en server grupps design som uppfyller vissa mål och mål. En typisk, omfattande SharePoint-servergrupp med hög begäran som stöder ett stort antal samtidiga användare och ett stort antal innehålls objekt använder tjänst grupper som en del av deras skalbarhets strategi. Den här metoden omfattar att köra tjänster på dedikerade servrar, gruppera dessa tjänster tillsammans och sedan skala ut servrarna som en grupp. Följande topologi illustrerar tjänst-och Server grupperingen för en SharePoint-servergrupp på tre nivåer. Detaljerad information om olika SharePoint-topologier finns i dokumentation och produkt linje arkitekturer i SharePoint. Du hittar mer information om SharePoint 2013-distribution i [det här dokumentet](/SharePoint/sharepoint-server).



![Distributions mönster 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

Site Recovery är program-oberoende och bör fungera med alla versioner av SharePoint som körs på en dator som stöds. För att skapa den här artikeln användes virtuella VMware-datorer med Windows Server 2012 R2 Enterprise. SharePoint 2013 Enterprise Edition och SQL Server 2014 Enterprise Edition användes.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja
**Azure** | Ej tillämpligt | Ja


### <a name="things-to-keep-in-mind"></a>Saker att tänka på

Om du använder ett delat disk-baserat kluster som vilken nivå som helst i ditt program kan du inte använda Site Recovery replikering för att replikera de virtuella datorerna. Du kan använda intern replikering som tillhandahålls av programmet och sedan använda en [återställnings plan](site-recovery-create-recovery-plans.md) för att redundansväxla alla nivåer.

## <a name="replicating-virtual-machines"></a>Replikera virtuella datorer

Följ [den här vägledningen](./vmware-azure-tutorial.md) för att påbörja replikering av den virtuella datorn till Azure.

* När replikeringen är klar, se till att du går till varje virtuell dator för varje nivå och välj samma tillgänglighets uppsättning i replikerat objekt > inställningar > egenskaper > beräkning och nätverk. Om din webb nivå till exempel har tre virtuella datorer, se till att alla de 3 virtuella datorerna är konfigurerade att ingå i samma tillgänglighets uppsättning i Azure.

    ![Ange tillgänglighets uppsättning](./media/site-recovery-sharepoint/select-av-set.png)

* Anvisningar om hur du skyddar Active Directory och DNS finns i [skydda Active Directory och DNS-](site-recovery-active-directory.md) dokument.

* Information om hur du skyddar databas nivån som körs på SQL Server finns i [skydda SQL Server](site-recovery-sql.md) -dokument.

## <a name="networking-configuration"></a>Nätverkskonfiguration

### <a name="network-properties"></a>Nätverks egenskaper

* Konfigurera nätverks inställningar i Azure Portal så att de virtuella datorerna blir anslutna till rätt DR-nätverk efter redundansväxlingen.

    ![Välj nätverk](./media/site-recovery-sharepoint/select-network.png)


* Om du använder en statisk IP-adress anger du den IP-adress som du vill att den virtuella datorn ska ta i **mål-IP-** fältet

    ![Ange statisk IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS och trafik dirigering

För webbplatser som riktas mot Internet [skapar du en Traffic Manager profil med prioritets typ](../traffic-manager/quickstart-create-traffic-manager-profile.md) i Azure-prenumerationen. Och konfigurera sedan din DNS-och Traffic Manager-profil på följande sätt.


| **Vilken** | **Källa** | **Mål**|
| --- | --- | --- |
| Offentligt DNS | Offentlig DNS för SharePoint-webbplatser <br/><br/> Till exempel: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokal DNS | sharepointonprem.contoso.com | Offentlig IP-adress i den lokala gruppen |


[Skapa de primära och återställnings slut punkterna](../traffic-manager/traffic-manager-configure-priority-routing-method.md)i Traffic Manager profilen. Använd den externa slut punkten för lokal slut punkt och offentlig IP för Azure-slutpunkt. Se till att prioriteten är högre till den lokala slut punkten.

Var värd för en testsida på en speciell port (till exempel 800) på SharePoint-webbnivån för att Traffic Manager automatiskt identifiera tillgänglighet efter redundans. Det här är en lösning om du inte kan aktivera anonym autentisering på någon av dina SharePoint-webbplatser.

[Konfigurera Traffic Manager profilen](../traffic-manager/traffic-manager-configure-priority-routing-method.md) med inställningarna nedan.

* Routningsmetod-prioritet
* TTL-värde (Time to Live) för DNS – 30 sekunder
* Inställningar för slut punkts övervakning – om du kan aktivera anonym autentisering kan du ge en speciell webbplats slut punkt. Du kan också använda en testsida på en angiven port (till exempel 800).

## <a name="creating-a-recovery-plan"></a>Skapa en återställnings plan

En återställnings plan gör det möjligt att sekvensera redundansväxlingen av olika nivåer i ett program på flera nivåer, och därmed underhålla programmets konsekvens. Följ stegen nedan när du skapar en återställnings plan för ett webb program med flera nivåer. [Lär dig mer om att skapa en återställnings plan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för redundans

1. Skapa en återställnings plan genom att lägga till app-och webb nivåns virtuella datorer.
2. Klicka på Anpassa för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i "grupp 1".

    ![Anpassa RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Skapa en annan grupp (grupp 2) och flytta webb nivåns virtuella datorer till den nya gruppen. De virtuella datorerna på din app-nivå måste vara en del av "grupp 1" och virtuella datorer i virtuella datorer ska ingå i "grupp 2". Detta är för att säkerställa att app-Tier VM: ar startas först och sedan virtuella datorer på virtuella datorer.


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen

Du kan distribuera de vanligaste Azure Site Recovery-skripten till ditt Automation-konto genom att klicka på knappen distribuera till Azure nedan. När du använder ett publicerat skript, se till att du följer anvisningarna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett skript för för åtgärd i gruppen 1 i SQL-tillgänglighetsgruppen för redundans. Använd skriptet "ASR-SQL-FailoverAG" publicerat i exempel skripten. Se till att du följer anvisningarna i skriptet och gör de nödvändiga ändringarna i skriptet på lämpligt sätt.

    ![Lägg till-AG-skript-steg-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Lägg till-AG-skript-steg-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Lägg till ett inläggs åtgärds skript för att koppla en belastningsutjämnare på den misslyckade över virtuella datorerna på webb nivån (grupp 2). Använd skriptet "ASR-AddSingleLoadBalancer" publicerat i exempel skripten. Se till att du följer anvisningarna i skriptet och gör de nödvändiga ändringarna i skriptet på lämpligt sätt.

    ![Add-LB-script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Lägg till en manuell åtgärd för att uppdatera DNS-posterna så att de pekar på den nya server gruppen i Azure.

    * För webbplatser som riktas mot Internet krävs inga DNS-uppdateringar efter redundansväxlingen. Följ stegen som beskrivs i avsnittet "nätverks vägledning" för att konfigurera Traffic Manager. Om Traffic Manager profilen har kon figurer ATS enligt beskrivningen i föregående avsnitt lägger du till ett skript för att öppna dummy-porten (800 i exemplet) på den virtuella Azure-datorn.

    * För interna riktade platser lägger du till ett manuellt steg för att uppdatera DNS-posten så att den pekar på den nya IP-adressen för belastningsutjämnaren för den virtuella webb nivån.

4. Lägg till en manuell åtgärd för att återställa sökprogrammet från en säkerhets kopia eller starta en ny Sök tjänst.

5. För att återställa Sök tjänst programmet från en säkerhets kopia följer du stegen nedan.

    * Den här metoden förutsätter att en säkerhets kopia av Search Service programmet utfördes före den oåterkalleliga händelsen och att säkerhets kopian är tillgänglig på DR-platsen.
    * Detta kan enkelt uppnås genom att schemalägga säkerhets kopieringen (till exempel en gång per dag) och använda en kopierings procedur för att placera säkerhets kopian på DR-platsen. Kopierings procedurer kan innehålla skriptbaserade program som AzCopy (Azure Copy) eller för att ställa in DFSR (Distributed File Services-replikering).
    * Nu när SharePoint-servergruppen körs går du till Central administration, säkerhets kopiering och återställning och väljer Återställ. Återställningen uppdaterar den angivna säkerhets kopierings platsen (du kan behöva uppdatera värdet). Välj den Search Service program säkerhets kopia som du vill återställa.
    * Sökningen har återställts. Tänk på att återställningen förväntar sig att hitta samma topologi (samma antal servrar) och samma hård disks beteckningar som tilldelats dessa servrar. Mer information finns i dokumentet [restore search service app in SharePoint 2013](/SharePoint/administration/restore-a-search-service-application) .


6. För att börja med ett nytt Sök tjänst program följer du stegen nedan.

    * Den här metoden förutsätter att en säkerhets kopia av databasen "Sök administration" är tillgänglig på DR-webbplatsen.
    * Eftersom de andra Search Service program databaserna inte replikeras måste de skapas på nytt. Det gör du genom att gå till Central administration och ta bort det Search Service programmet. Ta bort indexfilerna på alla servrar som är värdar för Sök indexet.
    * Återskapa Search Service-programmet och skapa databaserna på nytt. Vi rekommenderar att du har ett för berett skript som återskapar det här tjänst programmet eftersom det inte går att utföra alla åtgärder via det grafiska användar gränssnittet. Det går till exempel att ställa in platsen för index enheten och konfigurera söktopologin med hjälp av SharePoint PowerShell-cmdlets. Använd Windows PowerShell-cmdleten Restore-SPEnterpriseSearchServiceApplication och ange den loggade och replikerade Sök administrations databasen Search_Service__DB. Denna cmdlet ger Sök konfigurationen, schemat, de hanterade egenskaperna, reglerna och källorna och skapar en standard uppsättning av de andra komponenterna.
    * När Search Service programmet har återskapats måste du starta en fullständig crawlning för varje innehålls källa för att återställa Search Service. Du förlorar lite analys information från den lokala server gruppen, till exempel Sök rekommendationer.

7. När alla steg har slutförts sparar du återställnings planen och den slutliga återställnings planen kommer att se ut så här.

    ![Spara RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Utföra en redundanstest
Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) för att utföra ett redundanstest.

1.  Gå till Azure Portal och välj ditt Recovery Service-valv.
2.  Klicka på återställnings planen som skapats för SharePoint-programmet.
3.  Klicka på testa redundans.
4.  Välj återställnings punkt och Azure Virtual Network för att starta processen för redundanstest.
5.  När den sekundära miljön är igång kan du utföra dina verifieringar.
6.  När verifieringen är klar kan du klicka på Rensa redundanstest i återställnings planen och testa redundansväxlingen.

Information om hur du testar redundans för AD och DNS finns i avsnittet [testa redundansväxling för AD och DNS-](site-recovery-active-directory.md#test-failover-considerations) dokument.

Information om hur du testar redundans för SQL Always ON-tillgänglighetsgrupper finns i [utföra Application Dr med Azure Site Recovery och utföra dokument för redundanstest](site-recovery-sql.md#disaster-recovery-of-an-application) .

## <a name="doing-a-failover"></a>Utföra en redundansväxling
Följ [den här vägledningen](site-recovery-failover.md) för att utföra en redundansväxling.

1.  Gå till Azure Portal och välj Recovery Services-valvet.
2.  Klicka på återställnings planen som skapats för SharePoint-programmet.
3.  Klicka på redundans.
4.  Välj återställnings punkt för att starta redundansväxlingen.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om att [Replikera andra program](site-recovery-workload.md) med hjälp av Site Recovery.
