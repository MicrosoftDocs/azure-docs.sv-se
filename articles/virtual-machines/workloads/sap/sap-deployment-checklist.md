---
title: Check lista för SAP-arbetsbelastnings planering och distribution | Microsoft Docs
description: Check lista för planering av SAP-arbetsbelastnings distributioner till Azure och distribution av arbets belastningar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3402f4ff719752cca6e1d7a4aeeb3b7e12c3e28
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511150"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-arbetsbelastningar på Azure: planering och distribution check lista

Den här check listan är utformad för kunder som flyttar SAP NetWeaver-, S/4HANA-och hybris-program till Azure-infrastruktur som en tjänst. Under projektets varaktighet bör en kund-och/eller SAP-partner granska check listan. Det är viktigt att notera att många av kontrollerna har slutförts i början av projektet och under planerings fasen. När distributionen är färdig kan ändringar i de distribuerade Azure-infrastrukturen eller SAP-programutgåvor bli komplexa.

Granska check listan i viktiga mil stolpar under ditt projekt. På så sätt kan du identifiera små problem innan de blir stora problem. Du har också tillräckligt med tid för att återställa och testa nödvändiga ändringar. Tänk inte på att check listan har slutförts. Beroende på din situation kan du behöva utföra många fler kontroller.

Check listan innehåller inte uppgifter som är oberoende av Azure. Till exempel ändras SAP-programgränssnitten under en över gången till Azure-plattformen eller till en värd leverantör.

Den här check listan kan också användas för system som redan har distribuerats. Nya funktioner, till exempel Skrivningsaccelerator och Tillgänglighetszoner, och nya VM-typer kan ha lagts till sedan du distribuerade. Därför är det praktiskt att granska check listan regelbundet för att se till att du är medveten om nya funktioner i Azure-plattformen.

## <a name="project-preparation-and-planning-phase"></a>Projekt förberedelse och planerings fas
Under den här fasen planerar du migreringen av din SAP-arbetsbelastning till Azure-plattformen. Under den här fasen måste du minst skapa följande dokument och definiera och diskutera följande delar av migreringen:

1. Design dokument på hög nivå. Det här dokumentet ska innehålla:
    - Den aktuella inventeringen av SAP-komponenter och program och en mål program inventering för Azure.
    - En RACI (tilldelnings mat ris) som definierar ansvars områden och tilldelningar för de berörda parterna. Börja på hög nivå och arbeta med mer detaljerade nivåer genom att planera och de första distributionerna.
    - En hög nivå lösnings arkitektur.
    - Ett beslut om vilka Azure-regioner som ska distribueras till. Se [listan över Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). Information om vilka tjänster som är tillgängliga i varje region finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).
    - En nätverks arkitektur för att ansluta lokalt till Azure. Börja med att bekanta dig med den [virtuella Data Center skissen för Azure](/azure/architecture/vdc/).
    - Säkerhets principer för att köra affärs data med hög effekt i Azure. Om du vill veta mer om data säkerhet börjar du med [Azures säkerhets dokumentation](../../../security/index.yml).
2.  Tekniskt design dokument. Det här dokumentet ska innehålla:
    - Ett block diagram för lösningen.
    - Storleks ändring för beräknings-, lagrings-och nätverks komponenter i Azure. För SAP-storlek för virtuella Azure-datorer, se [SAP 
    -  Observera #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Affärs kontinuitet och katastrof återställnings arkitektur.
    - Detaljerad information om versioner av support paket för OS, DB, kernel och SAP. Det stämmer inte nödvändigt vis att varje OS-version som stöds av SAP NetWeaver eller S/4HANA stöds på virtuella Azure-datorer. Detsamma gäller för DBMS-versioner. Kontrol lera följande källor för att justera och vid behov uppgradera SAP-versioner, DBMS-versioner och OS-versioner för att säkerställa stöd för SAP och Azure. Du måste ha versions kombinationer som stöds av SAP och Azure för att få fullständig support från SAP och Microsoft. Vid behov måste du planera för att uppgradera vissa program varu komponenter. Mer information om stöd för SAP-, OS-och DBMS-programvara beskrivs här:
        - [#1928533 för SAP support NOTE](https://launchpad.support.sap.com/#/notes/1928533). Den här kommentaren definierar de lägsta OS-versioner som stöds på virtuella Azure-datorer. Den definierar också de minsta databas versioner som krävs för de flesta icke-HANA-databaser. Slutligen tillhandahåller den SAP-storlek för SAP-kompatibla Azure VM-typer.
        - [#2015553 för SAP support NOTE](https://launchpad.support.sap.com/#/notes/2015553). Den här anteckningen definierar stöd principer runt Azure Storage och support-relation som krävs med Microsoft.
        - [#2039619 för SAP support NOTE](https://launchpad.support.sap.com/#/notes/2039619). Den här kommentaren definierar Oracle support Matrix för Azure. Oracle stöder endast Windows och Oracle Linux som gäst operativ system på Azure för SAP-arbetsbelastningar. Den här support instruktionen gäller även för det SAP-program skikt som kör SAP-instanser. Oracle stöder dock inte hög tillgänglighet för SAP Central Services i Oracle Linux via pacemaker. Om du behöver hög tillgänglighet för ASCS på Oracle Linux måste du använda SIOS Protection Suite för Linux. Detaljerad information om SAP-certifiering finns i SAP support NOTE [#1662610-support information för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610). För Windows stöds den SAP-kompatibla Windows Server Failover Clustering-lösningen för SAP Central Services tillsammans med Oracle som DBMS-skiktet.
        - [#2235581 för SAP support NOTE](https://launchpad.support.sap.com/#/notes/2235581). Den här kommentaren innehåller en support mat ris för SAP HANA på olika OS-versioner.
        - SAP HANA-stödda virtuella Azure-datorer och [Hana-stora instanser](./hana-overview-architecture.md) visas på [SAP-webbplatsen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Produkt tillgänglighets mat ris för SAP](https://support.sap.com/en/).
        - [Stöd för SAP-support #2555629-SAP HANA 2,0 dynamisk Nivåing – hypervisor-och moln support](https://launchpad.support.sap.com/#/notes/2555629)
        - [Information om SAP-support #1662610-supportinformation för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-kommentarer för andra SAP-/regionsspecifika produkter.     
    - Användning av klusterkonfigurationer med flera-SID för SAP Central Services stöds på Windows-, SLES-och RHEL gäst operativ system på Azure. Tänk på att den förstärkta radien kan öka den mer ASCS/SCS som du placerar på ett sådant kluster med flera säkerhets-ID. Du hittar dokumentation för respektive gäst operativ system scenario i de här artiklarna:
        - [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med Windows Server-redundanskluster och delad disk i Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med kluster för växling vid fel i Windows Server och fil resurs på Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program med flera SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
        - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP-program med flera SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
    - Hög tillgänglighet och katastrof återställnings arkitektur.
        - Utifrån RTO och återställnings punkt definierar du vad arkitekturen för hög tillgänglighet och haveri beredskap måste se ut.
        - För hög tillgänglighet i en zon, kontrol lera vad det önskade DBMS: t har att erbjuda i Azure. De flesta DBMS-paket erbjuder synkrona metoder för synkron snabb växling, som vi rekommenderar för produktions system. Kontrol lera också den SAP-relaterade dokumentationen för olika databaser och börja med [att tänka på för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md) och relaterade dokument.
           Använd Windows Server-redundanskluster med en delad disk konfiguration för DBMS-skiktet som, till exempel, som [beskrivs för SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), inte stöds. Använd i stället lösningar som:
           - [Ständig aktivering av SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - För haveri beredskap i Azure-regioner granskar du de lösningar som erbjuds av olika DBMS-leverantörer. De flesta av dem stöder asynkron replikering eller logg överföring.
        - För SAP-program skiktet bestämmer du om du ska köra dina Business regression-testsystem, som är idealiska för repliker av dina produktions distributioner, i samma Azure-region eller i din DR-region. I det andra fallet kan du rikta in dig på Business regression-systemet som DR-mål för dina produktions distributioner.
        - Om du väljer att inte placera de icke-produktions systemen på DR-platsen kan du titta närmare på Azure Site Recovery som metod för att replikera SAP-programlagret till Azure DR-regionen. Mer information finns i [Konfigurera haveri beredskap för en distribution med flera nivåer av SAP NetWeaver-appar](../../../site-recovery/site-recovery-sap.md).
        - Om du väljer att använda en kombinerad HADR-konfiguration med hjälp av [Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md)bör du bekanta dig med de Azure-regioner där Tillgänglighetszoner är tillgängliga. Ta även hänsyn till vilka begränsningar som kan introduceras genom ökad nätverks fördröjning mellan två Tillgänglighetszoner.  
3.  En inventering av alla SAP-gränssnitt (SAP och icke-SAP).
4.  Design av grund tjänster. Den här designen bör innehålla följande objekt:
    - Active Directory-och DNS-design.
    - Nätverkstopologi i Azure och tilldelning av olika SAP-system.
    - [Azure-rollbaserad åtkomst kontrolls struktur (Azure RBAC)](../../../role-based-access-control/overview.md) för team som hanterar infrastruktur-och SAP-program i Azure.
    - Resurs gruppens topologi.
    - [Tagga strategin](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Namn konventioner för virtuella datorer och andra infrastruktur komponenter och/eller logiska namn.
5.  Microsoft Professional-eller Premier Support-avtal. Identifiera din Microsoft Technical Account Manager (TAM) om du har ett Premier support-avtal med Microsoft. Information om SAP-support finns i [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Antalet Azure-prenumerationer och kärn kvoten för prenumerationerna. [Öppna support förfrågningar för att öka kvoterna för Azure-prenumerationer](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) efter behov.
7.  Plan för data minskning och datamigrering för att migrera SAP-data till Azure. För SAP NetWeaver-system har SAP rikt linjer för hur du begränsar mängden stora mängder data. Se [den här SAP-guiden](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) om data hantering i SAP ERP-system. En del av innehållet gäller även för NetWeaver-och S/4HANA-system i allmänhet.
8.  En automatiserad distributions metod. Målet med automatiseringen av infrastruktur distributioner på Azure är att distribuera på ett deterministiskt sätt och få deterministiska resultat. Många kunder använder PowerShell-eller CLI-baserade skript. Men det finns flera tekniker med öppen källkod som du kan använda för att distribuera Azure-infrastrukturen för SAP och till och med installera SAP-programvara. Du kan hitta exempel på GitHub:
    - [Automatiserade SAP-distributioner i Azure-molnet](https://github.com/Azure/sap-hana)
    - [SAP HANA installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definiera en vanlig gransknings takt för design och distribution mellan dig som kunden, system integrerare, Microsoft och andra berörda parter.


## <a name="pilot-phase-strongly-recommended"></a>Pilot fas (rekommenderas starkt)
 
Du kan köra en pilot före eller under projekt planering och förberedelser. Du kan också använda pilot fasen för att testa metoder och design som görs under planerings-och förberedelse fasen. Och du kan expandera pilot fasen för att göra den till ett verkligt koncept bevis.

Vi rekommenderar att du ställer in och validerar en fullständig HADR-lösning och säkerhets design under en pilot distribution. Vissa kunder utför skalbarhets test under den här fasen. Andra kunder använder distributioner av system för SAP sandbox som en pilot fas. Vi förutsätter att du redan har identifierat ett system som du vill migrera till Azure för piloten.

1. Optimera data överföring till Azure. Det bästa valet är mycket beroende av det aktuella scenariot. Överföring från lokalt via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) är snabbast om ExpressRoute-kretsen har tillräckligt med bandbredd. I andra scenarier går det snabbare att överföra via Internet.
2. För en heterogen SAP Platform-migrering som omfattar export och import av data, testa och optimera export-och import faserna. För stora migreringar där SQL Server är mål plattformen kan du hitta [rekommendationer](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Du kan använda Migreringsverktyg/SWPM om du inte behöver en kombinerad versions uppgradering. Du kan använda [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -processen när du kombinerar migreringen med en uppgradering av SAP-utgåvor. För att göra det måste du uppfylla vissa krav för kombinationen av käll-och mål-DBMS-plattformen. Den här processen dokumenteras i [DMO (Database migration option) av SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportera till källa, exportera fil överföring till Azure och importera prestanda. Maximera överlappningen mellan export och import.
   2.  Utvärdera databasens volym på mål-och mål plattformarna med avseende på infrastruktur storlek.
   3.  Validera och optimera tids inställningen.
1. Teknisk validering.
   1. VM-typer.
        - Granska resurserna i SAP support Notes i SAP HANA maskin varu katalogen och i SAP PAM igen. Se till att det inte finns några ändringar i virtuella datorer som stöds för Azure, vilka OS-versioner som stöds för dessa VM-typer och vilka SAP-och DBMS-versioner som
        - Verifiera storleken på programmet och den infrastruktur som du distribuerar på Azure. Om du flyttar befintliga program kan du ofta härleda de nödvändiga SAPS från infrastrukturen du använder och [webb sidan för SAP-benchmark](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) och jämföra den med SAPS-numren som anges i [sap support NOTE #1928533](https://launchpad.support.sap.com/#/notes/1928533). Se även [den här artikeln om SAPS-klassificering](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) i åtanke.
        - Utvärdera och testa storleken på dina virtuella Azure-datorer med avseende på maximalt lagrings data flöde och nätverks data flöde för de VM-typer som du valde under planerings fasen. Du kan hitta data här:
           -  [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på det *maximala disk data flöde* som inte har cachelagrats för storleks ändring.
           -  [Storlekar för virtuella Linux-datorer i Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på det *maximala disk data flöde* som inte har cachelagrats för storleks ändring.
   2. Lagring.
        - Kontrol lera dokument [Azure Storage typer för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)
        - Använd minst [Azure standard SSD Storage](../../windows/disks-types.md#standard-ssd) för virtuella datorer som representerar SAP-programlager och för distribution av DBMS-objekt som inte är prestanda känsliga.
        - I allmänhet rekommenderar vi inte användningen av [Azure standard HDD-diskar](../../windows/disks-types.md#standard-hdd).
        - Använd [Azure Premium Storage](../../windows/disks-types.md#premium-ssd) för alla DBMS-VM: ar som är fjärrpresterade.
        - Använd [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Använd Azure Skrivningsaccelerator för DBMS-loggfiler med M-serien. Tänk på Skrivningsaccelerator gränser och användning, enligt beskrivningen i [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md).
        - För olika DBMS-typer kontrollerar du den [allmänna SAP-relaterade DBMS-dokumentationen](./dbms_guide_general.md) och den DBMS-specificerade dokumentation som det generiska dokumentet pekar på.
        - Mer information om SAP HANA finns i [SAP HANA infrastruktur konfiguration och åtgärder på Azure](./hana-vm-operations.md).
        - Montera aldrig Azure-datadiskarna till en virtuell Azure Linux-dator med hjälp av enhets-ID. Använd i stället den universella unika identifieraren (UUID). Var försiktig när du använder grafiska verktyg för att montera Azure Data disks, till exempel. Kontrol lera posterna i/etc/fstab för att se till att UUID används för att montera diskarna. Du hittar mer information i [den här artikeln](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Nätverk.
        - Testa och utvärdera din virtuella nätverks infrastruktur och distributionen av dina SAP-program över eller inom de olika virtuella Azure-nätverken.
        -  Utvärdera den nav-och-eker-arkitektur som det virtuella nätverket har eller för mikrosegmentering i ett enda virtuellt Azure-nätverk. Basera den här utvärderingen på:
               1. Kostnader för data utbyte mellan [peer-datorer i virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md). Mer information om kostnader finns [Virtual Network prissättning](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Fördelar med en snabb från koppling av peering mellan virtuella Azure-nätverk i stället för att ändra nätverks säkerhets gruppen för att isolera ett undernät i ett virtuellt nätverk. Den här utvärderingen är till för fall när program eller virtuella datorer som finns i ett undernät i det virtuella nätverket blev en säkerhets risk.
                3. Central loggning och granskning av nätverks trafik mellan lokalt, utanför världen och det virtuella Data Center som du skapade i Azure.
        - Utvärdera och testa data Sök vägen mellan SAP-program skiktet och SAP-DBMS-skiktet.
            -  Placering av [virtuella Azure-nätverksanslutningar](https://azure.microsoft.com/solutions/network-appliances/) i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i SAP-system baserat på SAP NetWeaver, hybris eller S/4HANA stöds inte.
            -  Placeringen av SAP-program skiktet och SAP-DBMS i olika virtuella Azure-nätverk som inte är peer-versioner stöds inte.
            -  Du kan använda [regler för program säkerhets grupp och nätverks säkerhets grupper](../../../virtual-network/security-overview.md) för att definiera vägar mellan SAP-program skiktet och SAP-DBMS-skiktet.
        - Kontrol lera att [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-programnivå och SAP DBMS-skiktet. Tänk på att olika OS-nivåer behövs för att stödja accelererat nätverk i Azure:
            - Windows Server 2012 R2 eller senare.
            - SUSE Linux 12 SP3 eller senare.
            - RHEL 7,4 eller senare.
            - Oracle Linux 7,5. Om du använder RHCKL-kärnan krävs version 3.10.0-862.13.1. el7. Om du använder Oracle UEK-kärnan krävs version 5.
        - Testa och utvärdera nätverks fördröjningen mellan SAP-programskiktets virtuella datorer och DBMS-VM: ar enligt SAP support Notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot rikt linjerna för nätverks fördröjning i [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga eller bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter, enligt beskrivningen i [den här artikeln](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
        - Se till att ILB-distributioner har kon figurer ATS för att använda direkt Server RETUR. Den här inställningen minskar svars tiden när Azure-ILB används för konfigurationer med hög tillgänglighet i DBMS-skiktet.
        - Om du använder Azure Load Balancer tillsammans med Linux-gäst operativ system, kontrollerar du att parametern för Linux-nätverket **net. IPv4. tcp_timestamps** har angetts till **0**. Den här rekommendationen står i konflikt med rekommendationer i äldre versioner av [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421). SAP-anteckningen har nu uppdaterats för att ange att den här parametern måste ställas in på **0** för att fungera med Azure Load Balancer.
        - Överväg att använda [Azure närhets placerings grupper](../../linux/co-location.md) för att få optimal nätverks fördröjning. Mer information finns i [placerings grupper för Azure närhet för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
   4. Distributioner av hög tillgänglighet och haveri beredskap.
        - Om du distribuerar SAP-programlagret utan att definiera en specifik Azure-tillgänglighets zon, se till att alla virtuella datorer som kör SAP-dialogrutor eller mellan-instanser av ett enda SAP-system distribueras i en [tillgänglighets uppsättning](../../windows/manage-availability.md).
        - Om du inte behöver hög tillgänglighet för SAP Central Services och DBMS kan du distribuera de virtuella datorerna till samma tillgänglighets uppsättning som SAP-program skiktet.
        - Om du skyddar SAP Central Services och DBMS-skiktet för hög tillgänglighet genom att använda passiv replikering, placerar du de två noderna för SAP Central Services i en separat tillgänglighets uppsättning och de två DBMS-noderna i en annan tillgänglighets uppsättning.
        - Om du distribuerar till Azure-tillgänglighetszoner kan du inte använda tillgänglighets uppsättningar. Men du måste kontrol lera att du distribuerar aktiva och passiva centrala tjänster-noder i två olika Tillgänglighetszoner. Använd Tillgänglighetszoner som har den lägsta svars tiden mellan dem.
          Tänk på att du måste använda [Azure standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) för att skapa Windows-eller pacemaker-redundanskluster för skiktet DBMS och SAP Central Services i Tillgänglighetszoner. Du kan inte använda [Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) för zonindelade-distributioner.
   5. Timeout-inställningar.
        - Kontrol lera SAP NetWeaver Developer-spår för SAP-instanserna för att se till att det inte finns några anslutnings avbrott mellan Server kön och SAP-arbetsprocesserna. Du kan undvika dessa anslutnings avbrott genom att ange dessa två register parametrar:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Mer information finns i [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Mer information finns i [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - För att undvika GUI-tidsgräns mellan lokala SAP GUI-gränssnitt och SAP-program som distribueras i Azure, kontrollerar du om dessa parametrar har angetts i standardvärdet. PFL eller instans profilen:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - För att förhindra störningar av etablerade anslutningar mellan SAP-kön och SAP-arbetsprocesserna, måste du ange parametern **Placera/encni/set_so_keepalive** till **True**. Se även [SAP obs #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Om du använder en konfiguration av Windows-redundanskluster kontrollerar du att tiden för att reagera på noder som inte svarar är korrekt inställd för Azure. I artikeln [Justera nätverk tröskelvärdena för redundanskluster](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) visas en lista över parametrar och hur de påverkar redundansväxlingen sensitivities. Förutsatt att klusternoderna finns i samma undernät bör du ändra dessa parametrar:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS-inställningar eller uppdateringar
        - För att köra HANA på SAP, Läs följande anteckningar och dokument:
            -   [Fel meddelande om SAP-support #2814271-SAP HANA säkerhets kopiering Miss lyckas på Azure med fel kontroll Summa](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Stöd för SAP-support #2753418-potentiell prestanda försämring på grund av timer-återställning](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Stöd för SAP-support #2791572 – prestanda försämring på grund av saknat stöd för VDSO för Hyper-V i Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Stöd för SAP-support #2382421 – optimera nätverks konfigurationen på HANA-och OS-nivå](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP support NOTE #2694118-Red Hat Enterprise Linux HA-tillägg på Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP support NOTE #1984787-SUSE LINUX Enterprise Server 12: installations information](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP support NOTE #2002167-Red Hat Enterprise Linux 7. x: installation och uppgradering](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support NOTE #2292690-SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP support NOTE #2772999-Red Hat Enterprise Linux 8. x: installation och konfiguration](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP support NOTE #2777782-SAP HANA DB: rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Support anteckning för SAP #2578899-SUSE Linux Enterprise Server 15: installations kommentar](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP support NOTE #2455582 – Linux: köra SAP-program som kompileras med GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [#2729475 för SAP-support NOTE-HWCCT misslyckades med felet "hypervisor stöds inte" på Azure VM: ar som certifierats för SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Testa din hög tillgänglighet och katastrof återställnings procedur.
   1. Simulera fel situationer genom att stänga av virtuella datorer (Windows gäst operativ system) eller placera operativ system i panik-läge (Linux gäst operativ system). Det här steget hjälper dig att avgöra om dina redundanskonfiguration fungerar som utformade.
   1. Mät hur lång tid det tar att köra en redundansväxling. Om tiderna är för långa bör du tänka på följande:
        - För SUSE Linux använder du SBD-enheter i stället för Azure-stängsel-agenten för att påskynda redundansväxlingen.
        - För SAP HANA, om omladdningen av data tar för lång tid, bör du överväga att tillhandahålla mer lagrings bandbredd.
   3. Testa din säkerhets kopierings-/återställnings ordning och tids inställning och gör eventuella ändringar om du behöver. Kontrol lera att säkerhets kopierings tiderna räcker. Du måste också testa återställnings-och tids återställnings aktiviteterna. Se till att återställnings tiderna är i din RTO-service avtal där din RTO förlitar sig på en databas eller återställnings process för virtuella datorer.
   4. Testa DR-funktioner och arkitektur för flera regioner.
1. Säkerhets kontroller.
   1. Testa giltigheten för Azure-rollbaserad åtkomst kontrolls arkitektur (Azure RBAC). Målet är att separera och begränsa åtkomsten och behörigheterna för olika team. Till exempel bör SAP basen-team medlemmar kunna distribuera virtuella datorer och tilldela diskar från Azure Storage till ett specifikt virtuellt Azure-nätverk. Men SAP-basen bör inte kunna skapa egna virtuella nätverk eller ändra inställningarna för befintliga virtuella nätverk. Medlemmar i nätverks teamet bör inte kunna distribuera virtuella datorer till virtuella nätverk där SAP-program och virtuella DBMS-datorer körs. Eller om medlemmar i det här teamet ska kunna ändra attribut för virtuella datorer eller till och med ta bort virtuella datorer eller diskar.  
   1.  Kontrol lera att [nätverks säkerhets gruppen och ASC](../../../virtual-network/security-overview.md) -reglerna fungerar som förväntat och kontrol lera de skyddade resurserna.
   1.  Kontrol lera att alla resurser som behöver krypteras är krypterade. Definiera och implementera processer för att säkerhetskopiera certifikat, lagra och komma åt dessa certifikat och återställa de krypterade entiteterna.
   1.  Använd [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) för OS-diskar där det är möjligt från en OS-support punkt i vyn.
   1.  Se till att du inte använder för många lager kryptering. I vissa fall kan det vara bra att använda Azure Disk Encryption tillsammans med en av DBMS-transparent datakryptering metoder för att skydda olika diskar eller komponenter på samma server.  Till exempel, på en SAP DBMS-Server, kan Azure Disk Encryption (ADE) aktive ras på Start disken för operativ systemet (om operativ systemet har stöd för ADE) och de data diskar som inte används av DBMS-datapersistens filer.  Ett exempel är att använda ADE på disken som innehåller krypterings nycklarna för DBMS-TDE.
1. Prestandatest. I SAP, baserat på SAP-spårning och mått, gör du följande jämförelser:
   - Jämför i tillämpliga fall de 10 främsta rapporterna med den aktuella implementeringen.
   - Jämför i tillämpliga fall de 10 främsta batch-jobben med den aktuella implementeringen.
   - Jämför data överföringar via gränssnitt i SAP-systemet. Fokusera på gränssnitt där du vet att överföringen kommer mellan olika platser, till exempel från lokala platser till Azure.


## <a name="non-production-phase"></a>Ej produktions fas 
I den här fasen förutsätter vi att efter en lyckad pilot eller POC (proof of Concept), börjar du distribuera SAP-system som inte är produktion till Azure. Ta med allt du lärt dig och erfarenhet av under POC-distributionen. Alla kriterier och steg som anges för POC gäller även för den här distributionen.

Under den här fasen distribuerar du vanligt vis utvecklings system, enhets test system och affärs Regressions test system till Azure. Vi rekommenderar att minst ett icke-produktionssystem på en SAP-programlinje har fullständig konfiguration för hög tillgänglighet som det framtida produktions systemet kommer att ha. Här följer några ytterligare steg som du måste utföra i den här fasen:  

1.  Innan du flyttar system från den gamla plattformen till Azure samlar du in resurs förbruknings data, till exempel processor användning, lagrings data flöde och IOPS-data. Samla in data från DBMS-skiktet, men samla in dem från program lager enheter. Mät även nätverks-och lagrings svars tider.
2.  Registrera systemets tillgänglighets användnings tids mönster. Målet är att ta reda på om icke-produktionssystem måste vara tillgängliga hela dagen, varje dag eller om det finns andra system än produktions system som kan stängas av under vissa faser i veckan eller i månaden.
3.  Testa och bestäm om du vill skapa egna OS-avbildningar för dina virtuella datorer i Azure eller om du vill använda en avbildning från Azure-galleriet för delad avbildning. Om du använder en avbildning från galleriet för delad avbildning ser du till att använda en avbildning som motsvarar support kontraktet med din OS-leverantör. För vissa OS-leverantörer kan du med hjälp av delat avbildnings Galleri ta med dina egna licens avbildningar. För andra OS-avbildningar ingår support i det pris som anges av Azure. Om du väljer att skapa egna OS-avbildningar kan du hitta dokumentation i de här artiklarna:
    -   [Bygg en generaliserad avbildning av en virtuell Windows-dator som distribuerats i Azure](../../windows/capture-image-resource.md)
    -   [Bygg en generaliserad avbildning av en virtuell Linux-dator som distribuerats i Azure](../../linux/capture-image.md)
3.  Om du använder SUSE-och Red Hat Linux-avbildningar från det delade bild galleriet, måste du använda avbildningarna för SAP som tillhandahålls av Linux-leverantörerna i det delade avbildnings galleriet.
4.  Se till att uppfylla support kraven för SAP för Microsofts support avtal. Se [#2015553 för SAP support NOTE](https://launchpad.support.sap.com/#/notes/2015553). För HANA-stora instanser, se [onboarding-krav](./hana-onboarding-requirements.md).
4.  Kontrol lera att rätt personer får [planerade underhålls aviseringar](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) så att du kan välja de bästa stillestånds tiden.
5.  Sök ofta efter Azure-presentationer på kanaler som [Channel 9](https://channel9.msdn.com/) för nya funktioner som kan gälla för dina distributioner.
6.  Se SAP-anteckningar som är relaterade till Azure, till exempel [support note #1928533](https://launchpad.support.sap.com/#/notes/1928533), för nya VM SKU: er och nya OS-och DBMS-versioner som stöds. Jämför priserna för nya VM-typer mot det för äldre VM-typer, så att du kan distribuera virtuella datorer med bästa pris/prestanda-förhållande.
7.  Kontrol lera om SAP-supportfrågor, SAP HANA maskin varu katalogen och SAP PAM. Se till att det inte fanns några ändringar i virtuella datorer som stöds för Azure, vilka OS-versioner som stöds på dessa virtuella datorer och vilka SAP-och DBMS-versioner som stöds
8.  Titta [på SAP-webbplatsen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för nya Hana-certifierade SKU: er i Azure. Jämför priserna för nya SKU: er med de som du har planerat att använda. Till sist kan du göra nödvändiga ändringar för att använda de som har bästa pris/prestanda-förhållande.
9.  Anpassa dina distributions skript för att använda nya VM-typer och inkludera nya Azure-funktioner som du vill använda.
10. Efter distributionen av infrastrukturen kan du testa och utvärdera nätverks fördröjningen mellan SAP-program på virtuella datorer och DBMS-VM: ar, enligt SAP support Notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot rikt linjerna för nätverks fördröjning i [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga eller bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter, enligt beskrivningen i [den här artikeln](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Se till att ingen av de begränsningar som anges i [överväganden för azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md#azure-network-considerations) och [SAP HANA infrastruktur konfiguration och åtgärder på Azure](./hana-vm-operations.md) gäller för din distribution.
11. Se till att dina virtuella datorer distribueras till rätt [placerings grupp för Azure närhet](../../linux/co-location.md)enligt beskrivningen i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
11. Utför alla andra kontroller som anges för koncept bevis fasen innan du tillämpar arbets belastningen.
12. När arbets belastningen används registrerar du resurs förbrukningen för systemen i Azure. Jämför den här förbrukningen med poster från din gamla plattform. Justera storleken på den virtuella datorn för framtida distributioner om du ser att du har stora skillnader. Tänk på att när du downsize, lagring och nätverks bandbredder för virtuella datorer kommer också att minskas.
    - [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Sizes for Linux virtual machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimentera med system kopierings funktioner och-processer. Målet är att göra det enkelt för dig att kopiera ett utvecklings system eller ett test system, så projekt teamen kan snabbt få nya system. Överväg att använda [SAP-Lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) för dessa uppgifter.
14. Optimera och finslipa din grupps Azure-rollbaserade åtkomst, behörigheter och processer för att se till att du har separering av uppgifter. Se till att alla team kan utföra sina uppgifter i Azure-infrastrukturen.
15. Öva, testa och dokumentera metoder för hög tillgänglighet och haveri beredskap för att göra det möjligt för personalen att utföra dessa uppgifter. Identifiera brister och anpassa nya Azure-funktioner som du integrerar i dina distributioner.


## <a name="production-preparation-phase"></a>Förberedelse fas för produktion 
I den här fasen samlar du in vad du har lärt dig och lärt dig under dina distributioner utan produktion och tillämpar dem på framtida produktions distributioner. Du måste också förbereda arbetet med data överföringen mellan din aktuella värd plats och Azure.

1.  Slutför nödvändiga SAP-release-uppgraderingar av dina produktions system innan du flyttar till Azure.
1.  Samtycker till företags ägarna vid funktionella och affärsrelaterade tester som måste utföras efter migreringen av produktions systemet.
1.  Se till att dessa tester har slutförts med käll systemen på den aktuella värd platsen. Undvik att utföra tester för första gången efter att systemet har flyttats till Azure.
1.  Testa processen för att migrera produktions system till Azure. Om du inte flyttar alla produktions system till Azure under samma tidsram skapar du grupper av produktions system som måste finnas på samma värd plats. Testa datamigreringen. Här följer några vanliga metoder:
    - Använd DBMS-metoder som säkerhets kopiering/återställning i kombination med SQL Server Always on, HANA System Replication eller logg överföring till seeding och synkronisera databas innehåll i Azure.
    - Använd säkerhets kopiering/återställning för mindre databaser.
    - Använd SAP-Migreringsverktyg, som är integrerad i SAP SWPM, för att utföra heterogena migreringar.
    - Använd processen [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) om du behöver kombinera migreringen med en uppgradering av SAP-release. Tänk på att det inte finns stöd för alla kombinationer av käll-DBMS och mål-DBMS. Du hittar mer information i de olika versionerna av DMO för SAP-supporten. Till exempel, [DMO (Database migration option) av SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Testa om data överförings data flödet är bättre via Internet eller via ExpressRoute, om du behöver flytta säkerhets kopior eller SAP-exportfiler. Om du flyttar data via Internet kan du behöva ändra några av reglerna för nätverks säkerhets gruppen/program säkerhets gruppen som du måste ha i stället för framtida produktions system.
1.  Innan du flyttar system från din gamla plattform till Azure samlar du in information om resursförbrukning. Användbara data inkluderar CPU-användning, lagrings data flöde och IOPS-data. Samla in data från DBMS-skiktet, men samla in dem från program lager enheter. Mät även nätverks-och lagrings svars tider.
1.  Kontrol lera om stöd anteckningar för SAP och de nödvändiga OS-inställningarna, SAP HANA maskin varu katalogen och SAP PAM. Se till att det inte fanns några ändringar i virtuella datorer som stöds för Azure, vilka OS-versioner som stöds på dessa virtuella datorer och vilka SAP-och DBMS-versioner som stöds
1.  Uppdatera distributions skript för att ta hänsyn till de senaste beslut som du har gjort på VM-typer och Azure-funktioner.
1.  När du har distribuerat infrastruktur och program kontrollerar du att:
    - Rätt VM-typer har distribuerats med rätt attribut och lagrings storlekar.
    - De virtuella datorerna är på rätt och önskade OS-versioner och-korrigeringsfiler och är enhetliga.
    - Virtuella datorer skärps vid behov och på ett enhetligt sätt.
    - Rätt program versioner och uppdateringar installerades och distribuerades.
    - De virtuella datorerna distribuerades till Azures tillgänglighets uppsättningar som planerat.
    - Azure Premium Storage används för latens känsliga diskar eller där [service avtalet för en enskild virtuell dator på 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) krävs.
    - Azure-Skrivningsaccelerator distribueras korrekt.
        - Se till att, inom de virtuella datorerna, lagrings utrymmena eller stripe-uppsättningarna har skapats korrekt på diskar som behöver Skrivningsaccelerator.
        - Kontrol lera [konfigurationen av programvaru-RAID på Linux](../../linux/configure-raid.md).
        - Kontrol lera [konfigurationen av LVM på virtuella Linux-datorer i Azure](../../linux/configure-lvm.md).
    - [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) används exklusivt.
    - De virtuella datorerna distribuerades till rätt tillgänglighets uppsättningar och Tillgänglighetszoner.
    - [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-program skiktet och SAP-DBMS-skiktet.
    - Inga [virtuella Azure-nätverk](https://azure.microsoft.com/solutions/network-appliances/) är i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i SAP-system baserat på SAP NetWeaver, hybris eller S/4HANA.
    - Reglerna för program säkerhets grupp och nätverks säkerhets grupper tillåter kommunikation som önskad och planerad och blockerar kommunikation där det behövs.
    - Tids gräns inställningarna är rätt inställda, enligt beskrivningen ovan.
    - Virtuella datorer distribueras till rätt [placerings grupp för Azure närhet](../../linux/co-location.md), enligt beskrivningen i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
    - Nätverks fördröjning mellan virtuella SAP-program på virtuella datorer och DBMS-VM: ar testas och verifieras enligt beskrivningen i SAP support Notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot rikt linjerna för nätverks fördröjning i [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga eller bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter, enligt beskrivningen i [den här artikeln](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
    - Kryptering implementerades vid behov och med lämplig krypterings metod.
    - Gränssnitt och andra program kan ansluta den nyligen distribuerade infrastrukturen.
1.  Skapa en Spelbok för att reagera på planerat Azure-underhåll. Fastställ i vilken ordning system och virtuella datorer ska startas om för planerat underhåll.
    

## <a name="go-live-phase"></a>Go-Live-fas
Under fasen Go-Live måste du följa spel böcker som du utvecklade under tidigare faser. Utför de steg som du har testat och tränat. Acceptera inte de senaste minuters ändringarna i konfigurationer och processer. Utför även följande steg:

1. Kontrol lera att Azure Portal övervakning och andra övervaknings verktyg fungerar. Vi rekommenderar Windows Performance Monitor (PerfMon) för Windows och SAR för Linux.
    - CPU-räknare.
        - Genomsnittlig CPU-tid, totalt (alla processorer)
        - Genomsnittlig CPU-tid, varje enskild processor (128 processorer på virtuella M128-datorer)
        - CPU kernel-tid, varje enskild processor
        - PROCESSOR användarens tid, varje enskild processor
    - Memory.
        - Ledigt minne
        - Minnes sidan i/sekund
        - Minnes sidans slut per sekund
    - Diskdefragmenter.
        - Disk läsning i kbit/s, per enskild disk
        - Disk läsningar per sekund per enskild disk
        - Disk läsning i mikrosekunder/läsning, per enskild disk
        - Disk skrivning i kbit/s per enskild disk
        - Disk skrivning/sekund, per enskild disk
        - Disk skrivning i mikrosekunder/läsning, per enskild disk
    - Nätverks.
        - Nätverks paket i/sekund
        - Nätverks paket ut/sekund
        - Nätverks-KB i/sekund
        - Nätverks-KB ut/sekund
1.  Efter datamigreringen utför du alla verifieringstester som du har samtyckt till företagets ägare. Godkänn endast verifierings test resultat när du har resultat för de ursprungliga käll systemen.
1.  Kontrol lera om gränssnitt fungerar och om andra program kan kommunicera med de nyligen distribuerade produktions systemen.
1.  Kontrol lera transport-och korrigerings systemet via SAP Transaction STMS.
1.  Säkerhetskopiera databasen när systemet har släppts för produktion.
1.  Säkerhetskopiera virtuella datorer i SAP-Programskiktet när systemet har släppts för produktion.
1.  För SAP-system som inte ingår i den aktuella Go-Live-fasen men som kommunicerar med SAP-systemen som du flyttade till Azure under den här Go-Live-fasen, måste du återställa värd namnets buffert i SM51. Om du gör det tas de gamla cachelagrade IP-adresserna som är kopplade till namnen på de program instanser som du flyttade till Azure bort.  


## <a name="post-production"></a>Publicera produktion
Den här fasen är till för att övervaka, driva och administrera systemet. Från en SAP-punkt i vyn gäller vanliga uppgifter som du behövde slutföra på din gamla värd plats. Utför följande Azure-speciella aktiviteter också:

1. Granska Azure-fakturor för system med hög debitering.
2. Optimera pris/prestanda-effektiviteten på VM-sidan och lagrings sidan.
3. Optimera tiderna när du kan stänga av system.  


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [Azure Virtual Machines planera och implementera SAP-NetWeaver](./planning-guide.md)
- [Azure Virtual Machines-distribution för SAP NetWeaver](./deployment-guide.md)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md)