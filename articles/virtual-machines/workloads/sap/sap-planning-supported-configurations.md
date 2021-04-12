---
title: 'SAP på Azure: scenarier som stöds med virtuella Azure-datorer'
description: Azure Virtual Machines-scenarier som stöds med SAP-arbetsbelastning
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670935"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-arbetsbelastning på en virtuell Azure-dator – scenarier som stöds
Genom att utforma SAP NetWeaver, Business en `Hybris` eller S/4HANA Systems arkitektur i Azure öppnas många olika möjligheter för olika arkitekturer och verktyg som du kan använda för att komma till en skalbar, effektiv och hög tillgänglig distribution. Även om det är beroende av det operativ system eller DBMS som används, finns det några begränsningar. Dessutom stöds inte alla scenarier som stöds lokalt på samma sätt i Azure. Det här dokumentet leder till konfigurationer med stöd för icke-hög tillgänglighet och konfigurationer med hög tillgänglighet och arkitekturer med enbart virtuella Azure-datorer. För scenarier som stöds med [Hana-stora instanser](./hana-overview-architecture.md)kan du läsa artikeln [om vilka scenarier som stöds för Hana-stora instanser](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>konfiguration på två nivåer
En SAP 2-Tier-konfiguration anses vara byggd från ett kombinerat lager av SAP-DBMS och program skiktet som körs på samma server eller VM-enhet. Den andra nivån anses vara användar gränssnitts skiktet. Om det finns en konfiguration på två nivåer delar DBMS-och SAP-programlagret resurserna på den virtuella Azure-datorn. Därför måste du konfigurera de olika komponenterna på ett sätt som inte konkurrerar om resurser. Du måste också vara noga med att inte överprenumerera resurserna i den virtuella datorn. En sådan konfiguration ger ingen hög tillgänglighet, utöver [Azures service nivå avtal](https://azure.microsoft.com/support/legal/sla/) för de olika Azure-komponenter som ingår.

En grafisk representation av en sådan konfiguration kan se ut så här:

![Enkel konfiguration med två nivåer](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Sådana konfigurationer stöds med Windows, Red Hat, SUSE och Oracle Linux för DBMS-systemen i SQL Server, Oracle, DB2, maxDB och SAP ASE för produktions-och icke-produktions ärenden. För SAP HANA som DBMS stöds sådana typer av konfigurationer endast för icke-produktionsenheter. Detta inkluderar även distributions fallet av [stora instanser av Azure Hana](./hana-overview-architecture.md) .
Den här typen av konfiguration stöds för alla OS/DBMS-kombinationer som stöds i Azure. Men det är obligatoriskt att du ställer in konfigurationen av DBMS och SAP-komponenter på ett sätt som DBMS och SAP-komponenter inte konkurrerar om minnes-och processor resurser och därmed överskrider de fysiska tillgängliga resurserna. Detta måste göras genom att begränsa det minne som DBMS-DBMS får allokera. Du måste också begränsa det utökade SAP-minnet för program instanser. Du måste också övervaka processor förbrukningen för den virtuella datorn för att se till att komponenterna inte maximerar processor resurserna. 

> [!NOTE]
> För produktion av SAP-system rekommenderar vi ytterligare hög tillgänglighet och eventuell haveri beredskap som beskrivs senare i det här dokumentet


## <a name="3-tier-configuration"></a>konfiguration på tre nivåer
I dessa konfigurationer separerar du SAP-programlagret och DBMS-skiktet till olika virtuella datorer. Vanligt vis gör du det för större system och på grund av att det är mer flexibelt för resurserna i SAP-program skiktet. I den mest enkla installationen finns det ingen hög tillgänglighet utöver [Azures service nivå avtal](https://azure.microsoft.com/support/legal/sla/) för de olika Azure-komponenter som ingår. 

Den grafiska presentationen ser ut så här:

![Diagram som visar en enkel konfiguration med tre nivåer.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Den här typen av konfiguration stöds i Windows, Red Hat, SUSE och Oracle Linux för DBMS-systemen i SQL Server, Oracle, DB2, SAP HANA, maxDB och SAP ASE för produktion och icke-produktions ärenden. Det här är standard distributions konfigurationen för [Azure Hana-stora instanser](./hana-overview-architecture.md). För enkelhetens användning skiljer vi inte mellan SAP-centrala tjänster och SAP-dialogrutor i SAP-Programskiktet. I den här enkla konfigurationen på tre nivåer skulle det inte finnas något skydd för hög tillgänglighet för SAP Central Services.

> [!NOTE]
> För produktion av SAP-system rekommenderar vi ytterligare hög tillgänglighet och eventuell haveri beredskap som beskrivs senare i det här dokumentet


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Flera DBMS-instanser per virtuell dator eller HANA stor instans enhet
I den här konfigurations typen är du värd för flera DBMS-instanser per virtuell Azure-dator eller HANA-stor instans enhet. Motivationen kan vara att ha mindre operativ system för att underhålla och med de minskade kostnaderna. Andra motivation kan vara att öka flexibiliteten och öka effektiviteten genom att dela resurser i en större virtuell dator eller HANA-stor instans enhet mellan flera DBMS-instanser. Hittills visade de här konfigurationerna främst för icke-produktionssystem.

En konfiguration som kan se ut så här:

![Flera DBMS-instanser i en enhet](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Den här typen av DBMS-distribution stöds för:

- SQL Server på Windows
- IBM DB2. Hitta information i artikeln [flera instanser (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- För Oracle. Mer information finns i [SAP support note #1778431](https://launchpad.support.sap.com/#/notes/1778431) och relaterade SAP-anteckningar
- För SAP HANA, flera instanser på en virtuell dator, kan SAP anropa den här distributions metoden MCOS. Mer information finns i SAP-artikeln [flera SAP HANA system på en värd (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Genom att köra flera databas instanser på en värd måste du kontrol lera att de olika instanserna inte konkurrerar om resurser och därmed överskrida de fysiska resurs gränserna för den virtuella datorn. Detta gäller särskilt för minne där du måste använda minnet för alla instanser som delar den virtuella datorn kan allokera. Det kan också vara sant för de processor resurser som de olika databas instanserna kan utnyttja. Alla DBMS som nämns har konfigurationer som tillåter begränsad minnesallokering och processor resurser på en instans nivå.
För att ha stöd för en sådan konfiguration för virtuella Azure-datorer förväntas det att de diskar eller volymer som används för loggfilerna data och logg/gör om för de databaser som hanteras av de olika instanserna är separata. Eller med andra ord data eller logg/gör om loggfiler för databaser som hanteras av en annan DBMS-instans ska inte dela samma diskar eller volymer. 

Disk konfigurationen för HANA-stora instanser levereras konfigurerad och beskrivs i scenarier som [stöds för Hana-stora instanser](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> För produktion av SAP-system rekommenderar vi ytterligare hög tillgänglighet och eventuella haveri beredskap som beskrivs senare i det här dokumentet. Virtuella datorer med flera DBMS-instanser stöds inte med konfigurationer för hög tillgänglighet som beskrivs senare i det här dokumentet.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Flera SAP-dialogrutor i en virtuell dator
I många fall distribueras flera dialog instanser på Bare Metal-servrar eller även i virtuella datorer som körs i privata moln. Anledningen till att dessa konfigurationer var att skräddarsy vissa SAP-dialogrutor till vissa arbets belastningar, affärs funktioner eller arbets belastnings typer. Anledningen till att inte isolera dessa instanser i separata virtuella datorer var operativ systemets underhåll och åtgärder. Eller i flera fall kan kostnaderna om den virtuella datorns värd eller operatör begär en månatlig avgift per virtuell dator som hanteras och administreras. I Azure är ett scenario som är värd för flera SAP-dialogrutor inom en enda virtuell dator som har stöd för produktion och icke-produktion på operativ systemen för Windows, Red Hat, SUSE och Oracle Linux. Parametern SAP kernel PHYS_MEMSIZE, som är tillgänglig i Windows och moderna Linux-kernel, ska anges om flera SAP Application Server-instanser körs på en enda virtuell dator. Vi rekommenderar också att du begränsar expansionen av utökat SAP-minne på operativ system, till exempel Windows där automatisk tillväxt av det utökade SAP-minnet implementeras. Detta kan göras med parametern SAP-profil `em/max_size_MB` .

Vid konfiguration på tre nivåer där flera SAP-dialogrutor körs i virtuella Azure-datorer kan det se ut så här:

![Diagram som visar en konfiguration på tre nivåer där flera instansen av SAP-dialog rutor körs i virtuella Azure-datorer.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

För enkelhetens användning skiljer vi inte mellan SAP-centrala tjänster och SAP-dialogrutor i SAP-Programskiktet. I den här enkla konfigurationen på tre nivåer skulle det inte finnas något skydd för hög tillgänglighet för SAP Central Services. För produktions system rekommenderar vi inte att du lämnar SAP Central Services oskyddade. Mer information om så kallade multi-SID-konfigurationer runt SAP Central-instanser och hög tillgänglighet för sådana konfigurationer med flera SID finns i senare avsnitt i det här dokumentet.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Skydd med hög tillgänglighet för SAP DBMS-skiktet
När du tittar på att distribuera SAP-produktionssystem måste du överväga att använda snabb växlings typ för konfigurationer med hög tillgänglighet. I synnerhet med SAP HANA, där data måste läsas in i minnet innan de kan få fullständig prestanda och skalbarhet, är Azure Service reserver inte ett idealiskt mått för hög tillgänglighet. 

I allmänhet stöder Microsoft endast konfigurationer med hög tillgänglighet och program varu paket som beskrivs under avsnittet SAP-arbetsbelastning i docs.microsoft.com. Du kan läsa samma instruktion i SAP Obs [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Microsoft tillhandahåller inte support för andra tredjepartsprogram som inte är dokumenterade av Microsoft tillsammans med SAP-arbetsbelastningar. I sådana fall är leverantören av tredje part i ramverket för hög tillgänglighet den stödande parten för den hög tillgänglighets konfiguration som du måste använda som kund i support processen. Undantag kommer att anges i den här artikeln. 

I allmänhet har Microsoft stöd för en begränsad uppsättning konfigurationer med hög tillgänglighet på virtuella Azure-datorer eller HANA-enheter med stora instanser. För scenarier som stöds av HANA-stora instanser kan du läsa dokumentet [som stöds för Hana-stora instanser](./hana-supported-scenario.md).

För virtuella Azure-datorer stöds följande konfigurationer för hög tillgänglighet på DBMS-nivå:

- SAP HANA systemreplikering baserat på Linux-pacemaker på SUSE och Red Hat. Se de detaljerade artiklarna:
    - [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA skalbara n + m-konfigurationer med [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) på SUSE och Red Hat. Information finns i följande artiklar:
    - [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server redundanskluster baserat på Windows Scale-Out fil tjänster. Även om rekommendationen för produktions system är att använda SQL Server Always on i stället för klustring. SQL Server Always on ger bättre tillgänglighet med separat lagring. Information beskrivs i den här artikeln: 
    - [Konfigurera en SQL Server-redundanskluster på virtuella Azure-datorer](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always on stöds med operativ systemet Windows för SQL Server på Azure. Detta är standard rekommendationen för produktion SQL Server instanser på Azure. Information beskrivs i de här artiklarna:
    - [Vi presenterar SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Konfigurera en tillgänglighets grupp som alltid är tillgänglig på virtuella Azure-datorer i olika regioner](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Konfigurera en belastningsutjämnare för en Always on-tillgänglighets grupp i Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle data Guard för Windows och Oracle Linux. Information om Oracle Linux hittar du i den här artikeln:
    - [Implementera Oracle data Guard på en virtuell Azure Linux-dator](../oracle/configure-oracle-dataguard.md)
- IBM DB2-HADR på SUSE och RHEL detaljerad dokumentation för SUSE och RHEL med pacemaker finns här:
    - [Hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](./dbms-guide-ha-ibm.md)
    - [Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP ASE och SAP maxDB-konfiguration enligt beskrivningen i följande dokument:
    - [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](./dbms_guide_sapase.md)
    - [SAP MaxDB, liveCache och Content Server-distribution på virtuella Azure-datorer](./dbms_guide_maxdb.md)
- Scenarier med stora tillgänglighets scenarier i HANA beskrivs i:
    - [Scenarier som stöds för HANA stora instanser – HSR med STONITH för hög tillgänglighet](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Scenarier som stöds för HANA Large instances-Host Auto redundans (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> För inget av scenarierna som beskrivs ovan, stöder vi konfigurationer av flera DBMS-instanser på en virtuell dator. Innebär i varje fall att endast en databas instans kan distribueras per virtuell dator och skyddas med de metoder för hög tillgänglighet som beskrivs. Det finns **inte** stöd för att skydda flera DBMS-instanser under samma Windows-eller pacemaker-redundanskluster vid den här tidpunkten. Oracle data Guard stöds även för enskilda instanser per distributions fall för virtuella datorer. 

Olika databas system kan vara värdar för flera databaser under en DBMS-instans. I händelse av SAP HANA kan flera databaser finnas i flera databas behållare (MDC). De här konfigurationerna stöds för de fall där dessa konfigurationer för flera databaser arbetar inom en kluster resurs för växling vid fel. Konfigurationer som inte stöds är fall där flera kluster resurser krävs. För konfigurationer där du vill definiera flera SQL Server tillgänglighets grupper under en SQL Server instans.


![DBMS HA-konfiguration](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Som är beroende av DBMS ett/eller operativ system, kan komponenter som Azure Load Balancer kanske inte krävs som en del av lösnings arkitekturen. 

För maxDB måste lagrings konfigurationen vara annorlunda. I maxDB måste data-och loggfilerna finnas på delad lagring för konfigurationer med hög tillgänglighet. Endast i händelse av maxDB stöds delad lagring för hög tillgänglighet. För alla andra DBMS-separata lagrings stackar per nod är de enda disk konfigurationerna som stöds.

Det finns andra ramverk för hög tillgänglighet som är kända för att köras på Microsoft Azure också. Microsoft har dock inte testat dessa ramverk. Om du vill bygga en hög tillgänglighets konfiguration med dessa ramverk måste du arbeta med leverantören av program varan för att:

- Utveckla en distributions arkitektur
- Distribution av arkitekturen
- Stöd för arkitekturen

> [!IMPORTANT]
> Microsoft Azure Marketplace erbjuder en mängd mjuka apparater som tillhandahåller lagrings lösningar ovanpå Azure Native Storage. Dessa mjuka apparater kan användas för att skapa NFS-resurser och teoretiskt kan användas i SAP HANA skalbara distributioner där en standby-nod krävs. På grund av olika orsaker stöds ingen av dessa mjuka lagrings enheter för någon av DBMS-distributionerna av Microsoft och SAP på Azure. Distributioner av DBMS på SMB-resurser stöds inte för närvarande i det här läget. Distributioner av DBMS på NFS-resurser är begränsad till NFS 4,1-resurser på [Azure NetApp Files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Hög tillgänglighet för SAP central service
SAP-centrala tjänster är en andra felpunkt vid din SAP-konfiguration. Det innebär att du måste skydda dessa processer för centrala tjänster också. Erbjudandet som stöds och är dokumenterat för SAP-belastnings läsningar som:

- Windows-redundanskluster med Windows-skalbara fil tjänster för sapmnt och global transport katalog. Information beskrivs i artikeln:
    - [Klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en fil resurs i Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Förbered Azure-infrastrukturen för SAP med hög tillgänglighet genom att använda ett Windows-redundanskluster och en fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows-redundanskluster med hjälp av SMB-resurs baserat på [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) för sapmnt och global transport katalog. Mer information finns i artikeln:
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program](./high-availability-guide-windows-netapp-files-smb.md)
- Windows växlings kluster Server baserat på SIOS `Datakeeper` . Även om du har dokumenterat av Microsoft behöver du en support relation med SIOS, så att du kan använda SIOS-support när du använder den här lösningen. Information beskrivs i artikeln:
    - [Klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker på SUSE-operativsystem med att skapa en NFS-resurs med hög tillgänglighet med två SUSE virtuella datorer och `drdb` för filreplikering. Information finns dokumenterad i artikeln
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](./high-availability-guide-suse.md)
    - [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Pacemaker SUSE-operativsystem med utnyttjar NFS-resurser som tillhandahålls av [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Information finns dokumenterad i
    - [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md)
- Pacemaker i Red Hat-operativsystemet med NFS-resurs som finns i ett `glusterfs` kluster. Information finns i artiklarna
    - [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` på virtuella Azure-datorer med Red Hat Enterprise Linux för SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker i Red Hat-operativsystemet med NFS-resurs som finns på [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Information beskrivs i artikeln
    - [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](./high-availability-guide-rhel-netapp-files.md)

I de listade lösningarna behöver du en support relation med SIOS för att stödja `Datakeeper` produkten och för att engagera med SIOS direkt i händelse av problem. Beroende på hur du har licensierat Windows, Red Hat och/eller SUSE OS kan du också behöva ett support avtal med din OS-Provider för att få fullständig support för de listade konfigurationerna med hög tillgänglighet.

Konfigurationen kan också visas som:

![DBMS-och ASCS HA-konfiguration](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

På den högra sidan av grafiken visas de hög tillgängliga SAP-tjänsterna. Förutom att använda SAP Central Services som skyddas med ett redundanskluster som kan redundansväxla vid problem, finns det ett villkor för en NFS-eller SMB-resurs med hög tillgänglighet, eller en Windows-delad disk för att säkerställa att sapmnt och global transport katalog är tillgängliga oberoende av förekomsten av en enda virtuell dator. Ytterligare några av lösningarna, t. ex. Windows-redundanskluster och pacemaker, kommer att kräva en Azure Load Balancer för att dirigera eller omdirigera trafik till en felfri nod.

I listan som visas finns det ingen omnämnande av Oracle Linux operativ systemet. Oracle Linux stöder inte pacemaker som ett kluster ramverk. Om du vill distribuera SAP-systemet på Oracle Linux och du behöver ett ramverk med hög tillgänglighet för Oracle Linux, måste du arbeta med leverantörer från tredje part. En av leverantörerna är SIOS med sin skydds Svit för Linux som stöds av SAP på Azure. Mer information finns i SAP NOTE [#1662610-support information för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610) .



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Lagring som stöds med scenarierna i SAP Central Services ovan
Eftersom endast en delmängd av Azures lagrings typer tillhandahåller NFS-eller SMB-resurser med hög tillgänglighet, är kvaliteten för användningen i våra SAP Central Services-kluster scenarier en lista över lagrings typer som stöds

- Windows-redundanskluster med skalbar fil server för Windows kan distribueras på alla interna typer av Azure-lagring, förutom Azure NetApp Files. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna service avtal i genomflödet och IOPS.
- Windows-redundanskluster med SMB på Azure NetApp Files stöds på Azure NetApp Files. SMB-resurser i Azure File Services stöds **inte** vid den här tidpunkten.
- Windows-redundanskluster med Windows-delad disk baserad på SIOS `Datakeeper` kan distribueras på alla interna Azure Storage-typer, förutom Azure NetApp Files. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna service avtal i genomflödet och IOPS.
- SUSE eller Red Hat pacemaker med NFS-resurser på Azure NetApp Files stöds på Azure NetApp Files. 
- SUSE pacemaker med en `drdb` konfiguration mellan två virtuella datorer stöds med interna Azure Storage-typer, förutom Azure NetApp Files. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna service avtal i genomflödet och IOPS.
- Red Hat-pacemaker `glusterfs` som använder för att tillhandahålla NFS-resurs stöds med interna Azure Storage-typer, förutom Azure NetApp Files. Rekommendationen är dock att utnyttja Premium Storage på grund av överlägsna service avtal i genomflödet och IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace erbjuder en mängd mjuka apparater som tillhandahåller lagrings lösningar ovanpå Azure Native Storage. Dessa Soft-enheter kan användas för att skapa NFS-eller SMB-resurser och teoretiskt kan även användas i de klustrade SAP-tjänsterna för redundanskluster. Dessa lösningar stöds inte direkt för SAP-arbetsbelastningen av Microsoft. Om du bestämmer dig för att använda en sådan lösning för att skapa en NFS-eller SMB-resurs måste du tillhandahålla stöd för den centrala SAP-tjänstekonfigurationen av den tredje part som äger program varan i den mjuka lagrings enheten.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Redundanskluster för SAP Central Services med flera SID
För att minska antalet virtuella datorer som behövs i stor SAP-landskap, kan SAP köra SAP Central Services-instanser av flera olika SAP-system i konfigurationen för redundanskluster. Tänk dig att du har 30 eller fler NetWeaver-eller S/4HANA produktions system. Utan flera-SID-klustring kräver de här konfigurationerna 60 eller fler virtuella datorer i 30 eller fler Windows-eller pacemaker-konfigurationer för redundanskluster. Förutom de DBMS-redundanskluster som krävs. Att distribuera flera SAP Central-tjänster över två noder i en kluster konfiguration för växling vid fel kan minska antalet virtuella datorer avsevärt. Att distribuera flera SAP-instanser för central tjänster på en enda kluster konfiguration med två noder har dock också vissa nack delar. Problem kring en enskild virtuell dator i kluster konfigurationen gäller för flera SAP-system. Underhåll av gäst operativ systemet som körs i kluster konfigurationen kräver mer samordning eftersom flera produktions-SAP-system påverkas. Verktyg som SAP LaMa stöder inte Multi-SID-kluster i sin system klonings process.

På Azure stöds en kluster konfiguration med flera säkerhets typer för Windows operativ system med ENSA1 och ENSA2. Rekommendationen är inte att kombinera den äldre ENSA1 (Queue Replication Service Architecture) med den nya arkitekturen (ENSA2) på ett kluster med flera säkerhets identifierare. Information om en sådan arkitektur finns dokumenterad i artiklarna

- [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med Windows Server-redundanskluster och delad disk i Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med kluster för växling vid fel i Windows Server och fil resurs på Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

För SUSE stöds även ett multi-SID-kluster baserat på pacemaker. Så mycket stöds konfigurationen för:

- Högst fem SAP ASCS/SCS-instanser
- Den gamla ENSA1 (Queue Replication Server Ice Architecture)
- Pacemaker-klusterkonfigurationer för två noder

Konfigurationen dokumenteras i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program med flera sid-guide](./high-availability-guide-suse-multi-sid.md)

Ett multi-SID-kluster med en transaktionskö för replikering ser ut som

![Diagram som visar ett kluster med flera säkerhets-ID: et.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA skalnings scenarier
SAP HANA skalnings scenarier stöds för en delmängd av de HANA-certifierade virtuella Azure-datorerna som anges i den [SAP HANA maskin varu katalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Alla virtuella datorer som marker ATS med ja i kolumnen klustring kan användas för antingen OLAP-eller S/4HANA-utskalning. Konfigurationer utan vänte läge stöds med Azure Storage typer av: 

- Azure Premium Storage, inklusive Azure Write Accelerator för/Hana/log-volymen
- [Ultradisk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA skalbara konfigurationer för OLAP-eller S/4HANA med vänte läges nod (er) stöds exklusivt med NFS-delad värd på Azure NetApp Files.

Om du vill ha mer information om exakta maskinvarukonfigurationer med eller utan standby-nod, kontrollerar du artiklarna:

- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](./hana-vm-operations-storage.md) 
- [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [#2080991 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2080991)

För mer information om HANA-stora instanser som stöds HANA-konfigurationer, gäller följande dokumentation:

- [Scenarier som stöds för HANA stora instanser skalas ut med vänte läge](./hana-supported-scenario.md#scale-out-with-standby)
- [Scenarier som stöds för HANA stora instanser skalas upp utan vänte läge](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Katastrof återställnings scenario
Det finns flera olika katastrof återställnings scenarier som stöds. Vi definierar haveri arkitekturer som arkitekturer som ska kompensera för en fullständig Azure-region som går ur rutnätet. Detta innebär att haveri beredskap måste vara en annan Azure-region som mål för att kunna köra ditt SAP-landskap. Vi delar upp metoder och konfigurationer i DBMS-skiktet och på andra lager än DBMS. 

### <a name="dbms-layer"></a>DBMS-skikt
För DBMS-skiktet stöds konfigurationer som använder sig av mekanismerna för inbyggd DBMS-replikering, som Always on, Oracle data Guard, DB2 HADR, SAP ASE Always on eller HANA System Replication. Det är obligatoriskt att replikeringsuppsättningen i sådana fall är asynkron, i stället för synkrona som i vanliga scenarier med hög tillgänglighet som distribueras i en enda Azure-region. Ett typiskt exempel på en katastrof återställnings konfiguration för ett DBMS som stöds beskrivs i artikeln [SAP HANA tillgänglighet i Azure-regioner](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). Den andra bilden i avsnittet beskriver ett scenario med HANA som exempel. Huvud databaserna som stöds för SAP-program kan bara distribueras i ett sådant scenario.

Det finns stöd för att använda en mindre virtuell dator som mål instans i Disaster Recovery-regionen eftersom den virtuella datorn inte har den totala arbets belastnings trafiken. Då måste du tänka på följande:

- Mindre virtuella dator typer tillåter inte att många diskar är anslutna till mindre virtuella datorer
- Mindre virtuella datorer har färre nätverks-och lagrings data flöde
- Det kan vara problem med att ändra storlek på alla VM-familjer när de olika virtuella datorerna samlas in i en Azure-tillgänglighets uppsättning eller när storleks ändringen sker mellan M-seriens familj och Mv2-serien av virtuella datorer
- PROCESSOR-och minnes förbrukning för databas instansen kan ta emot data strömmen med minimal fördröjning och tillräckligt med processor-och minnes resurser för att tillämpa dessa ändringar med minimal fördröjning på data  

Mer information om begränsningar för olika VM-storlekar finns [här](../../sizes.md) 

En annan metod som stöds för att distribuera ett DR-mål är att ha en andra DBMS-instans installerad på en virtuell dator som kör en icke-produktions-DBMS-instans av en icke-produktions-SAP-instans. Detta kan vara lite mer utmanande eftersom du behöver ta reda på vad minne, processor resurser, nätverks bandbredd och lagrings bandbredd som krävs för de specifika mål instanser som ska fungera som huvud instans i DR-scenariot. Särskilt i HANA rekommenderar vi starkt att du konfigurerar instansen som fungerar som ett DR-mål på en delad värd så att data inte försätts i inläsnings mål instansen för DR.

För scenarier med stora instanser av HANA kontrollerar du följande dokument:

- [Enkel nod med DR med Storage Replication](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Enkel nod med DR (flera syften) med Storage Replication](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Enkel nod med DR (flera syften) med Storage Replication](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Hög tillgänglighet med HSR och DR med Storage Replication](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Skala ut med DR med hjälp av Storage Replication](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Enkel nod med DR med HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [Enkel nod HSR till DR (kostnads optimerad)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Hög tillgänglighet och haveri beredskap med HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Hög tillgänglighet och haveri beredskap med HSR (kostnads optimerad)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Skala ut med DR med hjälp av HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> Användningen av [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) har inte testats för DBMS-distributioner under SAP-arbetsbelastningar. Därför stöds det inte för DBMS-skiktet i SAP-system vid den här tidpunkten. Andra metoder för replikering av Microsoft och SAP som inte finns med i listan stöds inte. Att använda program vara från tredje part för att replikera DBMS-skiktet i SAP-system mellan olika Azure-regioner, behöver stödjas av leverantören av program varan och kommer inte att stödjas via Microsoft och SAP-support kanaler. 
 
## <a name="non-dbms-layer"></a>Icke-DBMS-lager
För SAP-Programskiktet och eventuella resurser eller lagrings platser som behövs utnyttjas de två huvud scenarierna av kunderna:

- Haveri beredskaps målen i den andra Azure-regionen används inte för produktions-eller icke-produktions syfte. I det här scenariot, är de virtuella datorer som fungerar som haveri beredskaps mål inte att distribueras och avbildningen och ändringar av avbildningarna av produktions-SAP-program skiktet replikeras till Disaster Recovery-regionen. En funktion som kan utföra en sådan uppgift är [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery stöd för Azure-till-Azure-replikering som detta. 
- Haveri beredskaps målen är virtuella datorer som faktiskt används av andra system än produktions system. Hela SAP-landskapet sprids över två olika Azure-regioner med produktions system vanligt vis i en region och andra datorer än produktions system i en annan region. I många kund distributioner har kunden ett icke-produktionssystem som motsvarar ett produktions system. Kunden har för hands versioner av produktions program instanser i program skiktet icke-produktions system. I händelse av en redundansväxling stängs icke-produktions instanserna, de virtuella datorernas virtuella datorer har flyttats till de virtuella datorerna som inte är produktion (när nya IP-adresser har tilldelats i DNS) och de förinstallerade produktions instanserna kommer att komma igång

### <a name="sap-central-services-clusters"></a>SAP Central Services-kluster
SAP Central Services-kluster som använder delade diskar (Windows), SMB-resurser (Windows) eller NFS-resurser är lite svårare att replikera. På Windows-sidan är Windows Storage-replikering en möjlig lösning. I Linux rsync är en praktisk lösning.



## <a name="non-supported-scenario"></a>Scenario som inte stöds
Det finns en lista över scenarion som inte stöds för SAP-arbetsbelastningar i Azure-arkitekturer. Det finns **inte stöd** för att SAP och Microsoft inte kommer att kunna stödja dessa konfigurationer och behöver överskjuta till en eventuell berörd tredje part som tillhandahållit program vara för att upprätta sådana arkitekturer. Två av kategorierna är:

- Mjuk utrustning för lagring: det finns ett antal mjuka lagrings enheter som erbjuds i Azure Marketplace. Några av leverantörerna erbjuder egen dokumentation om hur du använder dessa lagrings enheter på Azure relaterade till SAP-programvara. Stöd för konfigurationer eller distributioner som inbegriper sådana lagrings mjuka anordningar måste tillhandahållas av leverantören av dessa lagrings enheter. Detta faktum visas också i [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Ramverk med hög tillgänglighet: endast pacemaker och Windows Server-redundanskluster stöds med hög tillgänglighets ramverk för SAP-arbetsbelastningar på Azure. Som nämnts tidigare beskrivs lösningen i SIOS `Datakeeper` och dokumenteras av Microsoft. Komponenterna i SIOS `Datakeeper` måste dock stödjas genom SIOS när leverantören tillhandahåller dessa komponenter. SAP listade även andra certifierade ramverk för hög tillgänglighet i olika SAP-anteckningar. En del av dem har certifierats av leverantören av tredje part för Azure även. Dock måste stöd för konfigurationer som använder dessa produkter tillhandahållas av produkt leverantören. Olika leverantörer har olika integrering i stöd processerna för SAP. Du bör klargöra vilken support process som fungerar bäst för den specifika leverantören innan du bestämmer dig för att använda produkten i SAP-konfigurationer som distribueras i Azure.
- Delade disk kluster där databasfilerna är bosatta på de delade diskarna stöds inte med undantag för maxDB. För alla andra databaser är den lösning som stöds att ha separata lagrings platser i stället för en SMB-eller NFS-resurs eller delad disk för att konfigurera scenarier med hög tillgänglighet

Andra scenarier, som inte stöds är scenarier som:

- Distributions scenarier som introducerar en större nätverks fördröjning mellan SAP-programnivån och SAP DBMS-nivån i SAP: S gemensamma arkitektur, som visas i NetWeaver, S/4HANA och t. ex. `Hybris` . Du måste bland annat:
    - Att distribuera en av de olika nivåerna på plats medan den andra nivån distribueras i Azure
    - Distribuera SAP-programnivån för ett system i en annan Azure-region än DBMS-nivån
    - Distribuera en nivå i Data Center som är samplacerade i Azure och den andra nivån i Azure, förutom var sådana arkitektur mönster tillhandahålls av en inbyggd Azure-tjänst
    - Distribuera virtuella nätverks enheter mellan SAP-programnivån och DBMS-skiktet
    - Att använda lagrings utrymmen som finns i Data Center som är samplacerade i Azure Data Center för SAP-DBMS-nivån eller SAP global transport katalog
    - Distribuera de två lagren med två olika moln leverantörer. Till exempel distribuera DBMS-nivån i Oracle Cloud Infrastructure och program nivån i Azure
- HANA pacemaker-klusterkonfigurationer för flera instanser
- Windows-klusterkonfigurationer med delade diskar via SOFS eller SMB på ANF för SAP-databaser som stöds i Windows. I stället rekommenderar vi att du använder intern replikering med hög tillgänglighet för de specifika databaserna och använder separata lagrings stackar
- Distribution av SAP-databaser som stöds i Linux med databasfiler som finns i NFS-resurser ovanpå ANF, med undantag för SAP HANA
- Distribution av Oracle-DBMS på andra gäst operativ system än Windows och Oracle Linux. Se även [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenarion som vi inte har testat och har därför ingen erfarenhet av listan som:

- Azure Site Recovery att replikera virtuella datorer i DBMS-skiktet. Därför rekommenderar vi att du använder databasens interna asynkrona replikering för eventuell haveri beredskaps konfiguration
 

## <a name="next-steps"></a>Nästa steg
Läs nästa steg i [Azure Virtual Machines planera och implementera för SAP NetWeaver](./planning-guide.md)




  