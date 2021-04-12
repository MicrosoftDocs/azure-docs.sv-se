---
title: Skala VMware/fysisk haveri beredskap med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap till Azure för ett stort antal lokala virtuella VMware-datorer eller fysiska servrar med Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 869702c9eaac4a38f6b0e64311c772892ddd6d90
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580446"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurera katastrof återställning i skala för virtuella VMware-datorer/fysiska servrar

Den här artikeln beskriver hur du konfigurerar haveri beredskap till Azure för stora tal (> 1000) av lokala virtuella VMware-datorer eller fysiska servrar i produktions miljön med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="define-your-bcdr-strategy"></a>Definiera din BCDR-strategi

Som en del av din strategi för affärs kontinuitet och haveri beredskap (BCDR) definierar du återställnings punkt mål (återställnings punkter) och återställnings tid (återställnings tider) för dina affärsappar och arbets belastningar. RTO mäter varaktighet och service nivå inom vilken en affärsappar eller process måste återställas och vara tillgänglig för att undvika kontinuitets problem.
- Site Recovery ger kontinuerlig replikering för virtuella VMware-datorer och fysiska servrar och ett [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) för RTO.
- När du planerar för storskalig haveri beredskap för virtuella VMware-datorer och tar reda på vilka Azure-resurser du behöver kan du ange ett RTO-värde som ska användas för kapacitets beräkningar.


## <a name="best-practices"></a>Bästa praxis

Några allmänna metod tips för storskalig katastrof återställning. Dessa metod tips beskrivs i detalj i nästa avsnitt i dokumentet.

- **Identifiera mål krav**: beräkna ut kapacitet och resurs behov i Azure innan du konfigurerar haveri beredskap.
- **Planera för Site Recovery-komponenter**: ta reda på vad Site Recovery komponenter (konfigurations Server, process servrar) som du behöver för att uppfylla den uppskattade kapaciteten.
- **Konfigurera en eller flera skalbara process servrar**: Använd inte processervern som körs som standard på konfigurations servern. 
- **Kör de senaste uppdateringarna**: Site Recovery team frigör nya versioner av Site Recovery-komponenter regelbundet och du bör kontrol lera att du kör de senaste versionerna. Om du vill ha hjälp med det spårar du [vad som är nytt](site-recovery-whats-new.md) för uppdateringar och [aktiverar och installerar uppdateringar](service-updates-how-to.md) när de släpps.
- **Övervaka proaktivt**: när du får haveri beredskap igång bör du proaktivt övervaka status och hälsa för replikerade datorer och infrastruktur resurser.
- **Granskning av haveri beredskap**: du bör köra granskningar på haveri nivå regelbundet. De påverkar inte produktions miljön, men se till att redundansen till Azure fungerar som förväntat när det behövs.



## <a name="gather-capacity-planning-information"></a>Samla in information om kapacitets planering

Samla in information om din lokala miljö för att hjälpa till att utvärdera och uppskatta dina mål (Azure) kapacitets behov.
- För VMware kör du distributions planeraren för virtuella VMware-datorer för att göra detta.
- Samla in informationen manuellt för fysiska servrar.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Kör distributions planeraren för virtuella VMware-datorer

Med distributions planeraren kan du samla in information om din lokala VMware-miljö.

- Kör distributions planeraren under en period som representerar en typisk omsättning för dina virtuella datorer. Då skapas mer exakta uppskattningar och rekommendationer.
- Vi rekommenderar att du kör distributions planeraren på Configuration Server-datorn eftersom Planner beräknar data flödet från den server där den körs. [Lär dig mer](site-recovery-vmware-deployment-planner-run.md#get-throughput) om Mät data flöde.
- Om du inte har konfigurerat någon konfigurations server än:
    - [Få en översikt](vmware-physical-azure-config-process-server-overview.md) över Site Recovery-komponenter.
    - [Konfigurera en konfigurations Server](vmware-azure-deploy-configuration-server.md)för att köra distributions planeraren på den.

Kör sedan planeraren på följande sätt:

1. [Lär dig mer om](site-recovery-deployment-planner.md) distributions planeraren. Du kan ladda ned den senaste versionen från portalen eller [Ladda ned den direkt](https://aka.ms/asr-deployment-planner).
2. Granska [kraven](site-recovery-deployment-planner.md#prerequisites) och de [senaste uppdateringarna](site-recovery-deployment-planner-history.md) för distributions planeraren och [Ladda ned och extrahera](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) verktyget.
3. [Kör distributions planeraren](site-recovery-vmware-deployment-planner-run.md) på konfigurations servern.
4. [Generera en rapport](site-recovery-vmware-deployment-planner-run.md#generate-report) för att sammanfatta uppskattningar och rekommendationer.
5. Analysera [rapport rekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md) och [kostnads uppskattningar](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Som standard är verktyget konfigurerat för att profilera och generera rapporter för upp till 1000 virtuella datorer. Du kan ändra den här gränsen genom att öka nyckel värdet nyckelvärdet i ASRDeploymentPlanner.exe.config-filen.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planera mål (Azure) krav och kapacitet

Med hjälp av dina insamlade uppskattningar och rekommendationer kan du planera för mål resurser och kapacitet. Om du körde distributions planeraren för virtuella VMware-datorer kan du använda ett antal [rapport rekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) för att hjälpa dig.

- **Kompatibla virtuella datorer**: Använd det här numret för att identifiera antalet virtuella datorer som är klara för haveri beredskap till Azure. Rekommendationer om nätverks bandbredd och Azure-kärnor baseras på det här antalet.
- **Nödvändig nätverks bandbredd**: anteckna vilken bandbredd du behöver för delta-replikering av kompatibla virtuella datorer. 
    - När du kör planeraren anger du önskad återställnings punkt på några minuter. Rekommendationerna visar vilken bandbredd som krävs för att uppfylla återställnings perioden 100% och 90% av tiden. 
    - Rekommendationerna för nätverks bandbredden tar hänsyn till den bandbredd som krävs för det totala antalet konfigurations servrar och process servrar som rekommenderas i Planner.
- **Nödvändiga Azure-kärnor**: Observera antalet kärnor du behöver i Azures Azure-region, baserat på antalet kompatibla virtuella datorer. Om du inte har tillräckligt många kärnor går det inte att skapa de virtuella Azure-datorer som krävs för redundans Site Recovery.
- **Rekommenderad storlek på virtuell dator**: den rekommenderade batchstorleken är baserad på möjligheten att slutföra den inledande replikeringen för batchen inom 72 timmar som standard, samtidigt som den uppfyller 100%. Värdet för timme kan ändras.

Du kan använda dessa rekommendationer för att planera för Azure-resurser, nätverks bandbredd och VM-batching.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planera Azure-prenumerationer och-kvoter

Vi vill se till att tillgängliga kvoter i mål prenumerationen räcker för att hantera redundans.

**Uppgift** | **Information** | **Åtgärd**
--- | --- | ---
**Kontrol lera kärnor** | Om kärnor i den tillgängliga kvoten inte är lika med eller överskrider det totala antalet mål vid tidpunkten för redundansväxlingen, kommer redundans att Miss Don. | För virtuella VMware-datorer kontrollerar du att det finns tillräckligt många kärnor i mål prenumerationen för att uppfylla distributions Planerarens kärn rekommendation.<br/><br/> För fysiska servrar kontrollerar du att Azure-kärnor uppfyller dina manuella uppskattningar.<br/><br/> Om du vill kontrol lera kvoterna klickar du på **användning + kvoter** i Azure Portal >- **prenumerationen**.<br/><br/> [Läs mer](../azure-portal/supportability/resource-manager-core-quotas-request.md) om att öka kvoterna.
**Kontrol lera begränsningar för redundans** | Antalet redundanser får inte överskrider gränsen för Site Recovery redundans. |  Om redundans överskrider gränserna kan du lägga till prenumerationer och redundansväxla till flera prenumerationer eller öka kvoten för en prenumeration. 


### <a name="failover-limits"></a>Begränsningar för redundans

Gränserna visar antalet redundanser som stöds av Site Recovery inom en timme, förutsatt att tre diskar per dator.

Vad innebär detta? För att starta en virtuell Azure-dator kräver Azure att vissa driv rutiner är i Start läge och tjänster som DHCP ska ställas in för att starta automatiskt.
- Datorer som uppfyller kraven har redan de här inställningarna på plats.
- För datorer som kör Windows kan du proaktivt kontrol lera efterlevnaden och göra dem kompatibla om det behövs. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux-datorer inträder endast i överensstämmelse vid redundansväxlingen.

**Är datorn kompatibel med Azure?** | **Azure VM-gränser (hanterad diskdiagnostik)**
--- | --- 
Yes | 2000
No | 1000

- Begränsningar förutsätter att minimala andra jobb pågår i mål regionen för prenumerationen.
- Vissa Azure-regioner är mindre och kan ha något lägre gränser.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planera infrastruktur och VM-anslutning

Efter redundansväxlingen till Azure behöver du dina arbets belastningar för att fungera som de var lokalt, och för att ge användare åtkomst till arbets belastningar som körs på virtuella Azure-datorer.

- [Lär dig mer](site-recovery-active-directory.md#test-failover-considerations) om att redundansväxla Active Directory eller lokal DNS-infrastruktur till Azure.
- [Lär dig mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) om att förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling.



## <a name="plan-for-source-capacity-and-requirements"></a>Planera för käll kapacitet och krav

Det är viktigt att du har tillräckligt med konfigurations servrar och skalbara process servrar för att uppfylla kapacitets kraven. När du påbörjar en storskalig distribution börjar du med en enda konfigurations Server och en skalbar processerver. Lägg till ytterligare servrar när du når de angivna gränserna.

>[!NOTE]
> För virtuella VMware-datorer gör distributions planeraren några rekommendationer om de konfigurations-och process servrar som du behöver. Vi rekommenderar att du använder tabellerna som ingår i följande procedurer, i stället för att följa rekommendationerna för distributions hanteraren. 


## <a name="set-up-a-configuration-server"></a>Konfigurera en konfigurations Server
 
Konfigurations serverns kapacitet påverkas av antalet datorer som replikeras, och inte av data omsättnings takten. Använd de här definierade gränserna för virtuella datorer för att ta reda på om du behöver ytterligare konfigurations servrar.

**Processor** | **Minne** | **Cachelagra disk** | **Gräns för replikerad dator**
 --- | --- | --- | ---
8 virtuella processorer<br> 2 Sockets * 4 kärnor @ 2,5 GHz | 16 GB | 600 GB | Upp till 550 datorer<br> Förutsätter att varje dator har tre diskar på 100 GB vardera.

- Dessa gränser baseras på en konfigurations server som kon figurer ATS med hjälp av en OVF-mall.
- Begränsningarna förutsätter att du inte använder processervern som körs som standard på konfigurations servern.

Om du behöver lägga till en ny konfigurations Server följer du dessa anvisningar:

- [Konfigurera en konfigurations Server](vmware-azure-deploy-configuration-server.md) för haveri beredskap för virtuella VMware-datorer med hjälp av en OVF-mall.
- [Konfigurera en konfigurations Server](physical-azure-set-up-source.md) manuellt för fysiska servrar, eller för VMware-distributioner som inte kan använda en OVF-mall.

Tänk på följande när du konfigurerar en konfigurations Server:

- När du konfigurerar en konfigurations Server är det viktigt att tänka på den prenumeration och det valv där det finns, eftersom dessa inte bör ändras efter installationen. Om du behöver ändra valvet måste du ta bort kopplingen mellan konfigurations servern och valvet och registrera det igen. Detta stoppar replikeringen av virtuella datorer i valvet.
- Om du vill konfigurera en konfigurations server med flera nätverkskort bör du göra det under Konfigurera. Du kan inte göra det efter att du registrerat konfigurations servern i valvet.

## <a name="set-up-a-process-server"></a>Konfigurera en processerver

Process serverns kapacitet påverkas av data omsättnings taxan och inte av antalet datorer som är aktiverade för replikering.

- För stora distributioner bör du alltid ha minst en skalbar processerver.
- Använd följande tabell för att avgöra om du behöver ytterligare servrar.
- Vi rekommenderar att du lägger till en server med den högsta specifikationen. 


**Processor** | **Minne** | **Cachelagra disk** | **Omsättnings pris**
 --- | --- | --- | --- 
12 virtuella processorer<br> 2 Sockets * 6 kärnor @ 2,5 GHz | 24 GB | 1 GB | Upp till 2 TB per dag

Konfigurera processervern enligt följande:

1. Granska [kraven](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installera-servern i [portalen](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)eller från [kommando raden](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurera replikerade datorer så att de använder den nya servern. Om du redan har datorer som replikeras:
    - Du kan [Flytta](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) en hel arbets belastning för process server till den nya processervern.
    - Alternativt kan du [Flytta](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) vissa virtuella datorer till den nya processervern.



## <a name="enable-large-scale-replication"></a>Aktivera storskalig replikering

När du har planerat kapaciteten och distribuerat nödvändiga komponenter och infrastruktur aktiverar du replikering för ett stort antal virtuella datorer.

1. Sortera datorer i batchar. Du aktiverar replikering för virtuella datorer i en batch och går sedan vidare till nästa batch.

    - För virtuella VMware-datorer kan du använda den [rekommenderade VM-batchstorleken](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) i rapporten distributions planering.
    - För fysiska datorer rekommenderar vi att du identifierar batchar baserat på datorer som har liknande storlek och mängd data, och på tillgängligt nätverks data flöde. Syftet är att batch-datorer ska kunna slutföra sin inledande replikering inom ungefär samma tid.
    
2. Om disk omsättningen för en dator är hög eller överskrider gränserna i distributionen thePlanner kan du flytta icke-kritiska filer som du inte behöver replikera (till exempel logg dum par eller temporära filer) på datorn. För virtuella VMware-datorer kan du flytta filerna till en separat disk och sedan [utesluta disken](vmware-azure-exclude-disk.md) från replikeringen.
3. Innan du aktiverar replikering bör du kontrol lera att datorerna uppfyller [kraven för replikering](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurera en replikeringsprincip för [virtuella VMware-datorer](vmware-azure-set-up-replication.md#create-a-policy) eller [fysiska servrar](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Aktivera replikering för [virtuella VMware-datorer](vmware-azure-enable-replication.md) eller [fysiska servrar](physical-azure-disaster-recovery.md#enable-replication). Detta avslutar den inledande replikeringen för de valda datorerna.

## <a name="monitor-your-deployment"></a>Övervaka distributionen

När du har startat replikeringen för den första batchen med virtuella datorer börjar du övervaka distributionen på följande sätt:  

1. Tilldela en katastrof återställnings administratör för att övervaka hälso status för replikerade datorer.
2. [Övervaka händelser](site-recovery-monitor-and-troubleshoot.md) för replikerade objekt och infrastrukturen.
3. [Övervaka hälsan](vmware-physical-azure-monitor-process-server.md) för dina skalbara process servrar.
4. Registrera dig för att få [e-postaviseringar](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) för händelser, för enklare övervakning.
5. Genomför regelbunden [haveri beredskap](site-recovery-test-failover-to-azure.md)för att se till att allt fungerar som förväntat.


## <a name="plan-for-large-scale-failovers"></a>Planera för storskalig redundans

I händelse av en katastrof kan du behöva redundansväxla ett stort antal datorer/arbets belastningar till Azure. Förbered dig för den här typen av händelse på följande sätt.

Du kan förbereda i förväg för redundans på följande sätt:

- [Förbered din infrastruktur och virtuella datorer](#plan-infrastructure-and-vm-connectivity) så att dina arbets belastningar blir tillgängliga efter redundansväxlingen och så att användarna kan komma åt de virtuella Azure-datorerna.
- Observera [växlings begränsningarna](#failover-limits) tidigare i det här dokumentet. Kontrol lera att redundansen kommer att falla inom dessa gränser.
- Kör regelbunden [haveri beredskap](site-recovery-test-failover-to-azure.md). Mer information om att:
    - Hitta luckor i distributionen före redundans.
    - Beräkna RTO från slut punkt till slut punkt för dina appar.
    - Beräkna slutpunkt till slut punkt för dina arbets belastningar.
    - Identifiera konflikter i IP-adressintervall.
    - När du kör övningar rekommenderar vi att du inte använder produktions nätverk för att öka detalj nivån och rensa redundanstest efter varje detalj nivå.

Vi rekommenderar följande för att köra en storskalig redundans:

1. Skapa återställnings planer för redundans av arbets belastning.
    - Varje återställnings plan kan utlösa redundans på upp till 100 datorer.
    - [Läs mer](recovery-plan-overview.md) om återställnings planer.
2. Lägg till Azure Automation Runbook-skript i återställnings planer för att automatisera eventuella manuella uppgifter i Azure. Vanliga uppgifter är konfiguration av belastningsutjämnare, uppdatering av DNS osv. [Läs mer](site-recovery-runbook-automation.md)
2. Förbered Windows-datorer så att de följer Azure-miljön före redundansväxlingen. [Växlings gränserna](#plan-azure-subscriptions-and-quotas) är högre för datorer som uppfyller kraven. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) om Runbooks.
4.  Utlös redundans med PowerShell [-cmdleten Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) tillsammans med en återställnings plan.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka Site Recovery](site-recovery-monitor-and-troubleshoot.md)
