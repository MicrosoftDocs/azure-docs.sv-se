---
title: HANA-redundanskonfiguration till en katastrof webbplats för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Så här utför du redundans till en katastrof återställnings plats för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a33340524556f5da1703cae3532f053fbe8ba13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670992"
---
# <a name="disaster-recovery-failover-procedure"></a>Redundans för haveriberedskap


>[!IMPORTANT]
>Den här artikeln ersätter inte SAP HANA administrations dokumentation eller SAP-anteckningar. Vi förutsätter att du har en heltäckande förståelse för och expertis i SAP HANA administration och åtgärder, särskilt för säkerhets kopiering, återställning, hög tillgänglighet och haveri beredskap (DR). I den här artikeln visas skärm bilder från SAP HANA Studio. Innehåll, struktur och beskaffenheten hos skärmarna i SAP-administrations verktyg och verktygen kan ändras från SAP HANA version till version.

Det finns två fall att tänka på när du växlar över till en DR-plats:

- Du behöver SAP HANA databasen för att gå tillbaka till den senaste statusen för data. I det här fallet finns det ett självbetjänings skript där du kan utföra redundansväxlingen utan att behöva kontakta Microsoft. För återställning efter fel måste du arbeta med Microsoft.
- Du vill återställa till en lagrings ögonblicks bild som inte är den senaste replikerade ögonblicks bilden. I det här fallet måste du arbeta med Microsoft. 

>[!NOTE]
>Följande steg måste utföras på volymen HANA stor instans som representerar DR-enheten. 
 
Om du vill återställa till de senaste ögonblicks bilderna med replikerad lagring följer du stegen i "utföra fullständig DR-redundans-azure_hana_dr_failover" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Om du vill att flera SAP HANA instanser ska växlas över kör du kommandot azure_hana_dr_failover flera gånger. Vid begäran anger du SAP HANA SID som du vill redundansväxla och återställa. 


Du kan testa DR-redundansväxlingen även utan att påverka den faktiska replikeringsrelationen. Om du vill utföra en redundanstest följer du stegen i avsnittet "utföra en test-DR-redundans-azure_hana_test_dr_failover" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>Kör *inte* några produktions transaktioner på instansen som du skapade på Dr-platsen genom att **testa en redundansväxling**. Kommandot azure_hana_test_dr_failover skapar en uppsättning volymer som inte har någon relation till den primära platsen. Därför går det *inte* att synkronisera tillbaka till den primära platsen. 

Om du vill att flera SAP HANA instanser ska testa kör du skriptet flera gånger. Vid begäran anger du SAP HANA SID för den instans som du vill testa för redundans. 

>[!NOTE]
>Om du behöver redundansväxla till DR-platsen för att rädda data som har tagits bort sedan och måste återställas till en tidigare ögonblicks bild, gäller den här proceduren. 

1. Stäng av instansen av HANA på haveri beredskaps enheten för HANA-stora instanser som du kör. En vilande HANA-produktions instans förinstalleras.
1. Kontrol lera att inga SAP HANAs processer körs. Använd följande kommando för den här kontrollen:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Utdata bör visa **hdbdaemon** -processen i ett stoppat tillstånd och inga andra Hana-processer i ett tillstånd som körs eller startas.
1. Bestäm till vilket ögonblicks bilds namn eller SAP HANA säkerhets kopierings-ID som du vill återställa återställnings platsen för haveri beredskap. I verkliga katastrof återställnings fall är den här ögonblicks bilden vanligt vis den senaste ögonblicks bilden. Om du behöver återställa förlorade data väljer du en tidigare ögonblicks bild.
1. Kontakta Azure-supporten via en support förfrågan med hög prioritet. Be om återställningen av ögonblicks bilden med namnet och datumet för ögonblicks bilden eller säkerhets kopierings-ID: t för HANA på DR-platsen. Standardvärdet är att drifts sidan bara återställer/Hana/data-volymen. Om du vill ha/Hana/logbackups-volymer måste du särskilt ange det. *Återställ inte/Hana/Shared-volymen.* I stället väljer du vissa filer som global.ini utanför **. snapshot** -katalogen och dess under kataloger efter att du monterat om/Hana/Shared-volymen för PRD. 

   På sidan åtgärder inträffar följande steg:

   a. Replikeringen av ögonblicks bilder från produktions volymen till Disaster Recovery-volymer stoppas. Det kan hända att störningen redan har inträffat om ett avbrott på produktions platsen är orsaken till att du behöver utföra en katastrof återställning.
   
   b. Namnet på ögonblicks bilden eller ögonblicks bilden med det säkerhets kopierings-ID som du valde återställs på haveri beredskaps volymerna.
   
   c. Efter återställningen är Disaster Recovery-volymerna tillgängliga för montering till de stora HANA-instans enheterna i Disaster Recovery-regionen.
      
1. Montera Disaster Recovery-volymerna på den stora instans enheten i HANA på haveri beredskaps webbplatsen. 
1. Starta den vilande SAP HANA produktions instansen.
1. Om du väljer att kopiera logg böckerna för transaktions loggen för att minska återställnings tiden kan du sammanfoga transaktions logg säkerhets kopiorna i den nyligen monterade DR/Hana/logbackups-katalogen. Skriv inte över befintliga säkerhets kopior. Kopiera nyare säkerhets kopior som inte repliker ATS med den senaste replikeringen av en lagrings ögonblicks bild.
1. Du kan också återställa enskilda filer från ögonblicks bilder som inte har repliker ATS till/hana/shared/PRD-volymen i Azure-regionen DR.

Följande steg visar hur du återställer SAP HANA produktions instansen baserat på den återställda ögonblicks bilden och de säkerhets kopior av transaktions loggen som är tillgängliga.

1. Ändra platsen för säkerhets kopian till **/Hana/logbackups** med hjälp av SAP HANA Studio.

   ![Ändra säkerhets kopierings platsen för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA söker igenom platser för säkerhets kopiering och föreslår den senaste säkerhets kopian av transaktions loggen att återställa till. Genomsökningen kan ta några minuter tills en skärm som liknar följande visas:

   ![Lista över säkerhets kopior av transaktions logg för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Justera några av standardinställningarna:

      - Rensa **Använd delta-säkerhets kopieringar**.
      - Välj **initiera logg områden**.

   ![Ange zon för initierings logg](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Välj **Slutför**.

   ![Slutför DR-återställningen](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

En förlopps fönster, som det som visas här, ska visas. Kom ihåg att exemplet är en återställning av haveri beredskap för en skalbar SAP HANA konfiguration med tre noder.

![Återställnings förlopp](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Om återställningen slutar svara på skärmen **Slutför** och inte visar förlopps skärmen, kontrollerar du att alla SAP HANA instanser på arbetsnoderna körs. Om det behövs startar du SAP HANA instanserna manuellt.


## <a name="failback-from-a-dr-to-a-production-site"></a>Återställning efter fel från en DR till en produktions plats
Du kan växla tillbaka från en DR till en produktions plats. Nu ska vi titta på ett scenario där redundansväxlingen till webbplatsen för haveri beredskap orsakades av problem i Azure-regionen för produktion och inte genom att du behöver återställa förlorade data. 

Du har kört din arbets belastning för SAP-produktion för en stund på webbplatsen för haveri beredskap. När problemen i produktions platsen är lösta vill du växla tillbaka till produktions platsen. Eftersom du inte kan förlora data inkluderar steget tillbaka till produktions platsen flera steg och nära samarbete med SAP HANA i Azure Operations-teamet. Det är upp till dig att utlösa drifts teamet för att börja synkronisera tillbaka till produktions platsen när problemen har lösts.

Följ de här stegen:

1. SAP HANA på Azure Operations-teamet hämtar utlösaren för att synkronisera produktions lagrings volymerna från lagrings volymerna för haveri beredskap, som nu representerar produktions status. I det här läget stängs den stora instans enheten HANA på produktions platsen.
1. SAP HANA i Azure Operations-teamet övervakar replikeringen och ser till att den fångas upp innan de informerar dig.
1. Du stänger av programmen som använder produktion HANA-instansen på webbplatsen för haveri beredskap. Du utför sedan en säkerhets kopiering av HANA-transaktions logg. Sedan stoppar du HANA-instansen som körs på de stora instanser i HANA-instansen på webbplatsen för haveri beredskap.
1. När HANA-instansen som körs i den stora instans enheten HANA i haveri beredskap stängs av, synkroniserar drift teamet manuellt disk volymerna igen.
1. SAP HANA på Azure Operations-teamet startar den stora volymen HANA i produktions platsen igen. De lämnar dem till dig. Du ser till att SAP HANA instansen är i ett stängnings tillstånd vid start tiden för den stora instans enheten i HANA.
1. Du utför samma databas återställnings steg som du gjorde när du tidigare har redundansväxlats till webbplatsen för haveri beredskap.

## <a name="monitor-disaster-recovery-replication"></a>Övervaka replikering av haveri beredskap

Kör skriptet för att övervaka statusen för din Storage Replication-förlopp `azure_hana_replication_status` . Det här kommandot måste köras från en enhet som kör på återställnings platsen för haverier att fungera som förväntat. Kommandot fungerar oavsett om replikering är aktiv. Kommandot kan köras för varje HANA-stor instans enhet av din klient på haveri beredskaps platsen. Den kan inte användas för att hämta information om start volymen. 

Mer information om kommandot och dess utdata finns i "Hämta status för DR-replikering-azure_hana_replication_status" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Nästa steg
- Se [övervaka och Felsök från Hana-sidan](hana-monitor-troubleshoot.md).
