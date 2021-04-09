---
title: Installera SAP HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Så här installerar du SAP HANA på en SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1c1397416943e3e25719204a5ccfd593559476e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557606"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Så här installerar och konfigurerar du SAP HANA (stora instanser) i Azure

Innan du läser den här artikeln kan du bekanta dig [med de stora och Hana-](hana-available-skus.md) [stora instanserna](hana-know-terms.md) .

Installationen av SAP HANA är ditt ansvar. Du kan börja installera en ny SAP HANA på Azure-servern (stora instanser) när du har upprättat anslutningen mellan dina virtuella Azure-nätverk och de HANA-stora instans enheterna. 

> [!Note]
> Per SAP-princip måste installationen av SAP HANA utföras av en person som har godkänt att den certifierade SAP-tekniken associerar examen, SAP HANA installationens certifierings examen eller som är en SAP-certifierad system integrerare (SI).

När du planerar att installera HANA 2,0, se [SAP support note #2235581-SAP HANA: operativ system som stöds](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att operativ systemet stöds med den SAP HANA version som du installerar. Operativ systemet som stöds för HANA 2,0 är mer restriktivt än det operativ system som stöds för HANA 1,0. Du måste också kontrol lera om den OS-version du är intresse rad av är listad som stöds för den specifika HLI-enheten i den här publicerade [listan](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klicka på enheten för att hämta all information med listan över operativ system som stöds för den enheten. 

Verifiera följande innan du påbörjar HANA-installationen:
- [HLI-enhet (er)](#validate-the-hana-large-instance-units)
- [Konfiguration av operativ system](#operating-system)
- [Konfiguration av nätverk](#networking)
- [Storage-konfiguration](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Verifiera de HANA-stora instans enheterna

När du har fått den stora instans enheten HANA från Microsoft kontrollerar du följande inställningar och justerar vid behov.

Det **första steget** efter att du har fått en stor instans av Hana och upprättar åtkomst och anslutning till instanserna är att checka in Azure Portal om instanserna visas med rätt SKU: er och OS. Läs [Azure Hana-stora instans kontroll genom Azure Portal](./hana-li-portal.md) för de steg som krävs för att utföra kontrollerna.

Det **andra steget** efter att du har tagit emot den stora Hana-instansen och upprättar åtkomst och anslutning till instanserna är att registrera operativ systemet för instansen med din OS-Provider. Det här steget omfattar att registrera din SUSE Linux-OS i en instans av SUSE SMT-SMT som distribueras i en virtuell dator i Azure. 

Den stora volymen HANA kan ansluta till den här SMT-instansen. (Mer information finns i [så här konfigurerar du SMT-Server för SUSE Linux](hana-setup-smt.md)). Alternativt måste ditt Red Hat-operativsystem vara registrerat med Red Hat-prenumerations hanteraren som du behöver ansluta till. Mer information finns i kommentarerna i [Vad är SAP HANA på Azure (stora instanser)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Det här steget krävs för att korrigera operativ systemet, vilket är kundens ansvar. För SUSE hittar du dokumentationen för att installera och konfigurera SMT på den här sidan om [SMT-installation](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Det **tredje steget** är att söka efter nya korrigeringar och korrigeringar av den aktuella OS-versionen/-versionen. Kontrol lera att korrigerings nivån för den stora HANA-instansen är i det senaste läget. Det kan finnas fall där de senaste korrigeringarna inte ingår. När du har tagit över en HANA-stor instans enhet är det obligatoriskt att kontrol lera om korrigeringarna måste tillämpas.

Det **fjärde steget** är att ta en titt på relevanta SAP-anteckningar för att installera och konfigurera SAP HANA på den aktuella OS-versionen/-versionen. På grund av ändrade rekommendationer eller ändringar i SAP-anteckningar eller konfigurationer som är beroende av enskilda installations scenarier, kommer Microsoft inte alltid att kunna konfigurera en HANA stor instans enhet perfekt. 

Därför är det obligatoriskt för dig som kund att läsa SAP-anteckningar som är relaterade till SAP HANA för din exakta Linux-version. Kontrol lera också konfigurationerna för OS-versionen/-versionen och tillämpa konfigurations inställningarna om du inte redan gjort det.

Mer specifikt, kontrol lera följande parametrar och ändra till sist till:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Från och med SLES12 SP1 och RHEL 7,2 måste dessa parametrar anges i en konfigurations fil i katalogen/etc/sysctl.d. Till exempel måste en konfigurations fil med namnet 91-NetApp-HANA. conf skapas. För äldre SLES-och RHEL-versioner måste dessa parametrar anges i/etc/sysctl. conf.

För alla RHEL-utgåvor som börjar med RHEL 6,3, Tänk på följande: 
- Parametern sunrpc.tcp_slot_table_entries = 128 måste anges i/etc/modprobe. d/sunrpc-Local. conf. Om filen inte finns måste du först skapa den genom att lägga till posten: 
    - alternativ sunrpc tcp_max_slot_table_entries = 128

Det **femte steget** är att kontrol lera system tiden för din Hana-stora instans enhet. Instanserna distribueras med en system tids zon. Den här tids zonen representerar platsen för den Azure-region där den stora instans stämpeln för HANA finns. Du kan ändra system tiden eller tids zonen för de instanser som du äger. 

Om du beställer fler instanser till klienten måste du anpassa tids zonen för de nya levererade instanserna. Microsoft har ingen inblick i system tids zonen som du ställer in med instanserna efter överlämnande. Därför kan nyligen distribuerade instanser inte anges i samma tidszon som den som du ändrade till. Det är ditt ansvar som kunden kan anpassa tids zonen för de instanser som överlämnades, om det behövs. 

Det **sjätte steget** är att kontrol lera etc/hosts. När bladet blir överutnyttjat har de olika IP-adresser som har tilldelats för olika syfte. Kontrol lera filen etc/hosts. När enheter läggs till i en befintlig klient, förväntar vi inte att ha etc/värdarna för de nyligen distribuerade systemen som bevaras korrekt med IP-adresserna för system som levererades tidigare. Det är ditt ansvar som kunden ser till att en nyligen distribuerad instans kan interagera och matcha namnen på de enheter som du har distribuerat tidigare i din klient organisation. 


## <a name="operating-system"></a>Operativsystem

Växlings området för den levererade OS-avbildningen är inställt på 2 GB enligt [SAP-support note #1999997 – vanliga frågor och svar: SAP HANA minne](https://launchpad.support.sap.com/#/notes/1999997/E). Som kund, om du vill ha en annan inställning, måste du ange den själv.

[SUSE Linux Enterprise Server 12 SP1 för SAP-program](https://www.suse.com/products/sles-for-sap/download/) är distributionen av Linux som är installerad för SAP HANA på Azure (stora instanser). Denna specifika distribution ger SAP-specifika funktioner "out of box" (inklusive fördefinierade parametrar för att köra SAP på SLES effektivt).

Se [resurs bibliotek/fakta blad](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) på webbplatsen SUSE och [SAP på SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) på SAP community Network (SCN) för flera användbara resurser som rör distribution av SAP HANA på SLES (inklusive konfiguration av hög tillgänglighet, säkerhets härdning som är speciell för SAP-åtgärder och mer).

Följande är ytterligare och användbart SAP på SUSE-relaterade länkar:

- [SAP HANA på SUSE Linux-webbplats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Metod tips för SAP: köa replikering – SAP NetWeaver på SUSE Linux Enterprise 12](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)
- [ClamSAP – SLES virus skydd för SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inklusive SLES 12 för SAP-program)

Följande är SAP-support anteckningar som är tillämpliga för att implementera SAP HANA på SLES 12:

- [Stöd för SAP-support #1944799 – SAP HANA rikt linjer för installation av operativ system för SLES](http://service.sap.com/sap/support/notes/1944799)
- [Stöd för SAP-support #2205917 – SAP HANA DB rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E)
- [#1984787 för SAP support NOTE – SUSE Linux Enterprise Server 12: installations information](https://launchpad.support.sap.com/#/notes/1984787)
- [Stöd för SAP-support #171356 – SAP-program på Linux: allmän information](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support NOTE #1391070 – Linux UUID-lösningar](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux för SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) är ett annat erbjudande om att köra SAP HANA på Hana-stora instanser. Versioner av RHEL 7,2 och 7,3 är tillgängliga och stöds. 

Följande är ytterligare användbara SAP för Red Hat-relaterade länkar:
- [SAP HANA på Red Hat Linux-webbplatsen](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Följande är SAP-support anteckningar som är tillämpliga för att implementera SAP HANA i Red Hat:

- [SAP support NOTE #2009879-SAP HANA rikt linjer för Red Hat Enterprise Linux (RHEL)-operativ system](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP support NOTE #2292690-SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP support NOTE #1391070 – Linux UUID-lösningar](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP support NOTE #2228351 – Linux: SAP HANA Database SPS 11 revision 110 (eller högre) på RHEL 6 eller SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Stöd för SAP-support #2397039 – vanliga frågor och svar: SAP på RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP support NOTE #2002167-Red Hat Enterprise Linux 7. x: installation och uppgradering](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver-arkitekturen är känsliga för tids skillnader för de olika komponenter som utgör SAP-systemet. ABAP för SAP-kort med fel rubriken för ZDATE \_ stor \_ \_ skillnad är förmodligen bekant. Det beror på att de här korta dumparna visas när system tiden för olika servrar eller virtuella datorer är för långt ifrån varandra.

För SAP HANA på Azure (stora instanser) gäller tidssynkroniseringen som är utförd i Azure inte beräknings enheterna i de stora instans stämplarna. Den här synkroniseringen gäller inte för att köra SAP-program i interna virtuella Azure-datorer eftersom Azure säkerställer att systemets tid är korrekt synkroniserad. 

Därför måste du konfigurera en separat tids server som kan användas av SAP-program servrar som körs på virtuella Azure-datorer och av SAP HANA databas instanser som körs på HANA-stora instanser. Lagrings infrastrukturen i stora instans-stämplar är tids synkroniserad med NTP-servrar.


## <a name="networking"></a>Nätverk
Vi antar att du har följt rekommendationerna för att utforma dina virtuella Azure-nätverk och att ansluta de virtuella nätverken till de stora HANA-instanserna, enligt beskrivningen i följande dokument:

- [Översikt över SAP HANA (stor instans) och arkitektur på Azure](./hana-overview-architecture.md)
- [SAP HANA (stora instanser) infrastruktur och anslutning i Azure](hana-overview-infrastructure-connectivity.md)

Det finns vissa detaljer som är värda att nämna om nätverk för de enskilda enheterna. Varje HANA-stor instans enhet levereras med två eller tre IP-adresser som tilldelas till två eller tre NIC-portar. Tre IP-adresser används i HANA-konfigurationer och i scenariot HANA-system replikering. En av de IP-adresser som är tilldelade till NÄTVERKSKORTet finns inte på den server-IP-pool som beskrivs i [SAP HANA (stora instanser) Översikt och arkitektur på Azure](./hana-overview-architecture.md).

Mer information om Ethernet-information för din arkitektur finns i [scenarier för HLI som stöds](hana-supported-scenario.md).

## <a name="storage"></a>Storage

Lagrings layouten för SAP HANA på Azure (stora instanser) konfigureras genom att SAP HANA på Azure `service management` med hjälp av SAP-rekommenderade rikt linjer. Dessa rikt linjer beskrivs i [SAP HANA lagrings krav](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) White Paper. 

De höga storlekarna för de olika volymerna med de olika volymerna för HANA-stora instanser dokumenteras i [SAP HANA (stora instanser) Översikt och arkitektur på Azure](hana-overview-architecture.md).

Namngivnings konventionerna för lagrings volymerna visas i följande tabell:

| Lagrings användning | Monterings namn | Volymnamn | 
| --- | --- | ---|
| HANA-data | /hana/data/SID/mnt0000\<m> | Lagrings-IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logg | /hana/log/SID/mnt0000\<m> | Lagrings-IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA-logg säkerhets kopiering | /hana/log/backups | Lagrings-IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA delad | /hana/shared/SID | Lagrings-IP:/hana_shared_SID_mnt00001_tenant_vol/Shared |
| usr/SAP | /usr/sap/SID | Lagrings-IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Sid* är Hana-instansens system-ID. 

*Klienten* är en intern uppräkning av åtgärder vid distribution av en klient.

HANA usr/SAP delar samma volym. Mountpoints-nomenklaturen innehåller system-ID: t för HANA-instanserna och monterings numret. I storskaliga distributioner finns det bara en montering, till exempel mnt00001. Vid skalbara distributioner visas å andra sidan så många monteringar som du har för Worker-och Master-noder. 

För skalbara miljöer delas och kopplas data, loggar och logg säkerhets kopierings volymer till varje nod i den skalbara konfigurationen. För konfigurationer som är flera SAP-instanser skapas en annan uppsättning volymer som är kopplade till den stora HANA-instansen het. Information om layoutinformation för ditt scenario finns i [HLI-scenarier som stöds](hana-supported-scenario.md).

När du tittar på en instans av HANA-stor instans inser du att enheterna levereras med den generösa disk volymen för HANA/data och att det finns en volym HANA/logg/säkerhets kopiering. Anledningen till att vi gjorde HANA/data så stora är att lagrings ögonblicks bilderna vi erbjuder dig som en kund använder samma disk volym. Med de mer lagrings ögonblicks bilder som du utför används mer utrymme av ögonblicks bilder i de tilldelade lagrings volymerna. 

Volymen HANA/log/backup ska inte vara volymen för databas säkerhets kopior. Det är en storlek som ska användas som säkerhets kopierings volym för säkerhets kopior av HANA-transaktionsloggar. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md). 

Utöver det lagrings utrymme som tillhandahålls kan du köpa ytterligare lagrings kapacitet i steg om 1 TB. Detta ytterligare lagrings utrymme kan läggas till som nya volymer i en stor HANA-instans.

Vid onboarding med SAP HANA på Azure `service management` anger kunden ett användar-ID (UID) och grupp-ID (GID) för användar-och sapsys-gruppen sidadm (till exempel: 1 000 500). Under installationen av SAP HANA systemet måste du använda samma värden. Eftersom du vill distribuera flera HANA-instanser på en enhet får du flera uppsättningar med volymer (en uppsättning för varje instans). Därför måste du vid distributions tiden definiera:

- SID för de olika HANA-instanserna (sidadm härleds från den).
- Minnes storlekarna för de olika HANA-instanserna. Minnes storleken per instans definierar storleken på volymerna i varje enskild volym uppsättning.

Enligt rekommendationerna för lagringsprovider konfigureras följande monterings alternativ för alla monterade volymer (exklusive start-LUN):

- NFS RW, vers = 4, hård, tidsbegränsad = 600, rsize = 1048576, wsize = 1048576, intr, noatime, lock 0 0

Dessa monterings punkter konfigureras i/etc/fstab som visas i följande grafik:

![fstab av monterade volymer i HANA stor instans enhet](./media/hana-installation/image1_fstab.PNG)

Utdata från kommandot DF-h på en S72m HANA stor instans enhet ser ut så här:

![Skärm bild där kommandots utdata för HANA-stor instans enhet visas.](./media/hana-installation/image2_df_output.PNG)


Lagrings styrenheten och noderna i de stora instans stämplarna synkroniseras med NTP-servrar. När du synkroniserar SAP HANA på Azure-enheter (stora instanser) och virtuella Azure-datorer mot en NTP-server, bör det inte finnas någon betydande tids fördröjning mellan infrastrukturen och beräknings enheterna i Azure eller stora instans stämplar.

Om du vill optimera SAP HANA till det lagrings utrymme som används under anger du följande SAP HANA konfigurations parametrar:

- max_parallel_io_requests 128
- async_read_submit på
- async_write_submit_active på
- async_write_submit_blocks alla
 
För SAP HANA 1,0-versioner upp till SPS12, kan dessa parametrar anges under installationen av SAP HANA-databasen, enligt beskrivningen i [SAP obs #2267798-konfiguration av SAP HANA-databasen](https://launchpad.support.sap.com/#/notes/2267798).

Du kan också konfigurera parametrarna efter installationen av SAP HANA-databasen med hjälp av hdbparam-ramverket. 

Lagringen som används i HANA stora instanser har en begränsning för fil storlek. [Storleks begränsningen är 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per fil. Till skillnad från fil storleks begränsningar i EXT3-filsystemen är HANA inte medveten om den lagrings begränsning som tillämpas av lagrings utrymmet för stora instanser av HANA. Det innebär att HANA inte automatiskt skapar en ny datafil när fil storleks gränsen på 16TB nås. När HANA försöker växa till filen bortom 16 TB rapporterar HANA fel och index servern kommer att krascha i slutet.

> [!IMPORTANT]
> För att förhindra att HANA försöker växa datafiler utöver 16 TB fil storleks gräns på HANA stor instans lagring, måste du ange följande parametrar i SAP HANA global.ini konfigurations filen
> 
> - datavolume_striping = sant
> - datavolume_striping_size_gb = 15000
> - Se även SAP Obs [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Tänk på SAP not [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Med SAP HANA 2,0 är hdbparam Framework inaktuellt. Därför måste parametrarna anges med hjälp av SQL-kommandon. Mer information finns i [SAP obs #2399079: Eli minering of hdbparam i Hana 2](https://launchpad.support.sap.com/#/notes/2399079).

Se [HLI-scenarier som stöds](hana-supported-scenario.md) för att lära dig mer om Storage-layouten för din arkitektur.


**Nästa steg**

- Se [Hana-installation på HLI](hana-example-installation.md)










































 







 
