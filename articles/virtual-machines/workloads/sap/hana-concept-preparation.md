---
title: Principer för haveri beredskap och förberedelser för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Principer för haveri beredskap och förberedelser för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: babd7c1dcae9d83af1f6c41e756b663d92d6d486
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677130"
---
# <a name="disaster-recovery-principles"></a>Principer för haveri beredskap

HANA-stora instanser ger en haveri beredskap mellan HANA-stora instanser i olika Azure-regioner. Om du till exempel distribuerar HANA-stora instans enheter i regionen USA, västra i Azure kan du använda de stora instans enheterna i USA i regionen USA, östra som återställnings enheter för haveri beredskap. Som tidigare nämnts är haveri beredskap inte konfigurerad automatiskt, eftersom det kräver att du betalar för en annan HANA-stor instans enhet i DR-regionen. Installations programmet för haveri beredskap fungerar både för skalbarhet och skalbara inställningar. 

I scenarier som distribueras hittills använder kunderna enheten i DR-regionen för att köra icke-produktions system som använder en installerad HANA-instans. Den stora volymen HANA måste vara av samma SKU som den SKU som används i produktions syfte. Följande bild visar hur disk konfigurationen mellan server enheten i Azure-produktionsområdet och Disaster Recovery-regionen ser ut så här:

![Konfiguration av DR-konfiguration från disk punkten](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Som du ser i den här översikts bilden måste du beställa en andra uppsättning disk volymer. Mål disk volymerna har samma storlek som produktions volymerna för produktions instansen i Disaster Recovery-enheterna. Dessa disk volymer är kopplade till server enheten HANA-stor instans på webbplatsen för haveri beredskap. Följande volymer replikeras från produktions regionen till DR-platsen:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (innehåller/usr/SAP)

/Hana/log-volymen replikeras inte eftersom SAP HANA transaktions loggen inte behövs på samma sätt som återställningen från dessa volymer är färdig. 

De funktioner för haveri beredskap som erbjuds är de Storage Replication-funktioner som erbjuds av infrastrukturen HANA stor instans. De funktioner som används på lagrings sidan är inte en konstant ström med ändringar som replikeras på ett asynkront sätt när det sker ändringar i lagrings volymen. I stället är det en mekanism som förlitar sig på att ögonblicks bilder av volymerna skapas med jämna mellanrum. Delta mellan en redan replikerad ögonblicks bild och en ny ögonblicks bild som ännu inte har repliker ATS överförs sedan till katastrof återställnings platsen till mål disk volymer.  Dessa ögonblicks bilder lagras på volymerna och om det finns en redundansväxling av haveri beredskap måste den återställas till dessa volymer.  

Den första överföringen av volymens fullständiga data måste vara innan mängden data blir mindre än delta mellan ögonblicks bilder. Därför innehåller volymerna på DR-platsen var och en av ögonblicks bilderna som utfördes på produktions platsen. Slutligen kan du använda detta DR-system för att komma till en tidigare status för att återställa förlorade data utan att återställa produktions systemet.

Om det finns en MCOD-distribution med flera oberoende SAP HANA-instanser på en av en HANA stor instans enhet, förväntas det att alla SAP HANA instanser får lagring som replikeras till DR-sidan.

I de fall där du använder HANA-systemreplikering som hög tillgänglighets funktion på produktions platsen, och använder Storage-baserad replikering för DR-platsen, replikeras volymerna för båda noderna från den primära platsen till DR-instansen. Du måste köpa ytterligare lagrings utrymme (samma storlek som primär nod) på DR-platsen för att hantera replikering från både primär och sekundär till DR. 



>[!NOTE]
>Funktionen HANA stor instans Storage Replication speglas och replikerar lagrings ögonblicks bilder. Om du inte utför lagrings ögonblicks bilder som du har infört i avsnittet säkerhets kopiering och återställning i den här artikeln, kan det inte finnas någon replikering till webbplatsen för haveri beredskap. Lagring av ögonblicks bilds körning är en förutsättning för att utföra Storage-replikering till webbplatsen för haveri beredskap.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Förberedelse av katastrof återställnings scenariot
I det här scenariot har du ett produktions system som körs på HANA-stora instanser i Azure-regionen produktion. För de steg som följer antar vi att SID för det HANA-systemet är "PRD" och att du har ett icke-produktionssystem som körs på HANA-stora instanser i DR Azure-regionen. För den senare antar vi att dess SID är "TST". Följande bild visar den här konfigurationen:

![Start av DR-konfiguration](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Om Server instansen inte redan har beställts med den ytterligare lagrings volym uppsättningen, lägger SAP HANA på Azure Service Management till den ytterligare uppsättningen volymer som mål för produktions repliken till den HANA-stora instans enhet som du kör TST HANA-instansen på. För detta ändamål måste du ange SID för produktion HANA-instansen. När SAP HANA på Azure Service Management bekräftar den bifogade volymen för de volymerna måste du montera dessa volymer i den stora volymen HANA.

![Nästa steg i DR-installationen](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Nästa steg är att du ska installera den andra SAP HANA-instansen i den stora volymen HANA i den DR Azure-region där du kör TST HANA-instansen. Den nyligen installerade SAP HANA-instansen måste ha samma SID. De användare som skapats måste ha samma UID och grupp-ID som produktions instansen har. Läs [säkerhets kopiering och återställning](hana-backup-restore.md) för mer information. Om installationen lyckades måste du:

- Kör steg 2 i förberedelserna för lagrings ögonblicks bilder som beskrivs i [säkerhets kopiering och återställning](hana-backup-restore.md).
- Skapa en offentlig nyckel för DR-enheten HANA stor instans enhet om du ännu inte har gjort det. Se steg 3 i förberedelserna för lagrings ögonblicks bilder som beskrivs i [säkerhets kopiering och återställning](hana-backup-restore.md).
- Behåll *HANABackupCustomerDetails.txt* med den nya Hana-instansen och testa om anslutningen till lagringen fungerar som den ska.  
- Stoppa den nyligen installerade SAP HANA-instansen på volymen HANA stor instans i Azure-regionen DR.
- Demontera de här PRD-volymerna och kontakta SAP HANA på Azure Service Management. Volymerna kan inte vara monterade på enheten eftersom de inte kan nås under drift som mål för lagrings replikering.  

![Diagram som visar replikeringsrelationen mellan PRD-volymerna i Azure-regionen för produktion och PRD-volymerna i DR Azure-regionen.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Drift teamet upprättar replikeringsrelationen mellan PRD-volymerna i Azure-regionen för produktion och PRD-volymerna i DR Azure-regionen.

>[!IMPORTANT]
>/Hana/log-volymen replikeras inte eftersom det inte är nödvändigt att återställa den replikerade SAP HANA databasen till ett konsekvent tillstånd på webbplatsen för haveri beredskap.

Sedan ställer du in eller justerar schemat för säkerhets kopiering av lagrings ögonblicks bilder för att komma till din RTO och återställnings väskan i katastrofen. För att minimera återställnings punkt målet anger du följande replikeringsintervall i tjänsten HANA stor instans:
- För volymer som omfattas av den kombinerade ögonblicks bilden (ögonblicks bilds typen **Hana**), Ställ in att replikera var 15: e minut till motsvarande lagrings volym mål på webbplatsen för haveri beredskap.
- För säkerhets kopierings volymen för transaktions loggen (ögonblicks bilds **loggar**) ställer du in att replikera var 3: e minut till motsvarande lagrings volym mål på webbplatsen för haveri beredskap.

Konfigurera följande för att minimera återställnings punkt målet:
- Utför en ögonblicks bild av **Hana** -typ lagring (se "steg 7: utföra ögonblicks bilder") var 30: e minut till 1 timme.
- Utför SAP HANA säkerhets kopieringar av transaktions loggar var 5: e minut.
- Utför en ögonblicks bild av **loggar** typ lagring var 5-15: e minut. Med den här intervall perioden uppnår du en period på cirka 15-25 minuter.

Med den här installationen kan sekvensen av säkerhets kopior av transaktions loggen, lagrings ögonblicks bilder och replikering av HANA-säkerhetskopierings volym och/Hana/data, och/Hana/Shared (inklusive/usr/SAP) se ut som de data som visas i den här bilden:

 ![Förhållandet mellan en ögonblicks bild av en transaktions logg och en fäst spegel på en tids axel](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Om du vill uppnå en ännu bättre återställnings punkt i haveri beredskap kan du kopiera säkerhets kopiorna HANA-säkerhetskopiering från SAP HANA på Azure (stora instanser) till den andra Azure-regionen. Utför följande steg för att uppnå denna ytterligare återställnings nedsättning:

1. Säkerhetskopiera HANA transaktions logg så ofta som möjligt till/Hana/logbackups.
1. Använd rsync för att kopiera säkerhets kopiorna av transaktions loggen till NFS-resursen som är värd för virtuella Azure-datorer. De virtuella datorerna finns i virtuella Azure-nätverk i Azures produktions region och i DR-regionerna. Du måste ansluta både virtuella Azure-nätverk till kretsen som ansluter produktionen HANA stora instanser till Azure. Se avsnittet [om nätverks överväganden för haveri beredskap med stora instanser av Hana](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) . 
1. Behåll säkerhets kopiorna av transaktions loggen i regionen på den virtuella datorn som är kopplade till NFS-exporterad lagring.
1. I ett haveri ärende kan du komplettera de säkerhets kopior av transaktions loggen som du hittar på/Hana/logbackups-volymen med mer nyligen tagna säkerhets kopior av transaktions loggen på NFS-resursen på webbplatsen för haveri beredskap. 
1. Starta en säkerhets kopia av transaktions loggen för att återställa till den senaste säkerhets kopian som kan sparas över till DR-regionen.

När HANA-stora instans åtgärder verifierar konfigurationen av replikeringsrelationen och du startar säkerhets kopiorna av den kopierade lagrings platsen, börjar datareplikeringen.

![Installations steg för DR innan du upprättar replikering](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

När replikeringen pågår återställs inte ögonblicks bilderna på PRD-volymerna i DR Azure-regionerna. De lagras bara. Om volymerna monteras i ett sådant tillstånd representerar de det tillstånd i vilket du demonterade volymerna när PRD SAP HANA-instansen installerades i Server enheten i Azure-regionen DR. De representerar också de lagrings säkerhets kopior som ännu inte har återställts.

Om det finns en redundansväxling kan du också välja att återställa till en äldre lagrings ögonblicks bild i stället för den senaste ögonblicks bilden av lagringen.

## <a name="next-steps"></a>Nästa steg

- Mer information om [redundansväxling av haveri beredskap](hana-failover-procedure.md).
