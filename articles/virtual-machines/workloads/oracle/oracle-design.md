---
title: Utforma och implementera en Oracle-databas på Azure | Microsoft Docs
description: Utforma och implementera en Oracle-databas i din Azure-miljö.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669985"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Utforma och implementera en Oracle-databas i Azure

## <a name="assumptions"></a>Antaganden

- Du planerar att migrera en Oracle-databas från en lokal plats till Azure.
- Du har [diagnostikverktyget](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) eller det [automatiska arbets belastnings lagret](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) för Oracle Database som du vill migrera
- Du har en förståelse för de olika måtten i Oracle.
- Du har en grundläggande förståelse för program prestanda och plattforms användning.

## <a name="goals"></a>Mål

- Lär dig hur du optimerar din Oracle-distribution i Azure.
- Utforska alternativ för prestanda justering för en Oracle-databas i en Azure-miljö.
- Ha tydliga förväntningar mellan gränserna för fysisk justering genom arkitektur och fördelar eller logisk justering av databas kod, (SQL) och den övergripande databas designen.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Skillnaderna mellan en lokal och Azure-implementering 

Nedan följer några viktiga saker att tänka på när du migrerar lokala program till Azure. 

En viktig skillnad är att i en Azure-implementering delas resurser som virtuella datorer, diskar och virtuella nätverk mellan andra klienter. Dessutom kan resurser begränsas baserat på kraven. I stället för att fokusera på att undvika att Miss lyckas (MTBF) är Azure mer fokuserat på att sluta fungera (MTTR).

I följande tabell visas några av skillnaderna mellan en lokal implementering och en Azure-implementering av en Oracle-databas.


|  | Lokal implementering | Azure-implementering |
| --- | --- | --- |
| **Nätverk** |LAN/WAN  |SDN (program varu Defined Networking)|
| **Säkerhets grupp** |Verktyg för begränsning av IP/port |[Nätverks säkerhets grupp (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Återhämtning** |MTBF (genomsnittlig tid mellan haverier) |MTTR (genomsnittlig tid för återställning)|
| **Planerat underhåll** |Korrigeringar/uppgraderingar|[Tillgänglighets uppsättningar](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (uppdatering/uppgraderingar som hanteras av Azure) |
| **Resurs** |Dedikerad  |Delas med andra klienter|
| **Regioner** |Datacenter |[Regionpar](../../regions.md#region-pairs)|
| **Storage** |SAN/fysiska diskar |[Azure-hanterad lagring](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Skalning** |Lodrät skala |Horisontell skalning|


### <a name="requirements"></a>Krav

- Ta reda på verklig CPU-användning, eftersom Oracle licensieras av Core, så kan storleken på vCPU-behoven vara en viktig övning för att spara pengar. 
- Bestäm databas storlek, lagring av säkerhets kopior och tillväxt takt.
- Fastställ i/o-kraven, som du kan beräkna baserat på Oracle-STATSPACK och AWR-rapporter eller från övervaknings verktyg för lagring på operativ system nivå.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns fyra möjliga områden som du kan finjustera för att förbättra prestanda i en Azure-miljö:

- Storlek för virtuell dator
- Nätverks data flöde
- Disk typer och konfigurationer
- Inställningar för disk-cache

### <a name="generate-an-awr-report"></a>Generera en AWR-rapport

Om du har en befintlig Oracle Enterprise Edition-databas och planerar att migrera till Azure har du flera alternativ. Om du har ett [diagnostiskt paket](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) för dina Oracle-instanser kan du köra rapporten Oracle AWR för att hämta måtten (IOPS, Mbps, GiBs och så vidare). För dessa databaser utan Diagnostics Pack-licensen eller för en standard-version kan samma viktiga mått samlas in med en STATSPACK-rapport när manuella ögonblicks bilder har samlats in.  Den största skillnaden mellan dessa två rapporterings metoder är att AWR samlas in automatiskt och ger mer information om databasen än den föregående rapporterings alternativet för STATSPACK.

Du kan överväga att köra din AWR-rapport under både regelbundna och högsta arbets belastningar, så att du kan jämföra. För att samla in den mer exakta arbets belastningen bör du överväga en utökad fönster rapport om en vecka, jämfört med en 24-timmarsformat-körning och inse att AWR ger medelvärden som en del av dess beräkningar i rapporten.  För en migrering av data Center rekommenderar vi att du samlar in rapporter för storleks ändring på produktions systemen och uppskattar återstående databas kopior som används för användar testning, testning, utveckling osv. i procent (UAT som är lika med produktion, testning och utveckling 50% av produktions storlek osv.)

Som standard behåller AWR-lagringsplatsen 8 dagars data och tar ögonblicks bilder i Tim intervall.  Om du vill köra en AWR-rapport från kommando raden kan följande utföras från en Terminal:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Viktiga mått

Rapporten kommer att uppmanas att ange följande information:
- Rapport typ: HTML eller TEXT, (HTML i 12,1 och ger ytterligare information än TEXT formatet.)
- Antalet dagar i ögonblicks bilder som ska visas (i ett Tim intervall skulle en veckas rapport vara en 168 annan i ögonblicks bilds-ID)
- Den första SnapshotID för rapport fönstret.
- Den avslutande SnapshotId för rapport fönstret.
- Namnet på rapporten som ska skapas av AWR-skriptet.

Om du kör AWR på ett verkligt program kluster (RAC) är kommando rads rapporten awrgrpt. SQL i stället för awrrpt. SQL.  Rapporten "g" skapar en rapport för alla noder i RAC-databasen i en enda rapport eller som måste köras en på varje RAC-nod.

Följande är de mått som du kan hämta från AWR-rapporten:

- Databas namn, instans namn och värdnamn
- Databas version, (Support för Oracle)
- CPU/kärnor
- SGA/PGA, (och rådgivare för att meddela om de är underändrade)
- Totalt minne i GB
- CPU% upptagen
- DB-processorer
- IOPs (Läs/skriv)
- Mbit/s (Läs/skriv)
- Nätverks data flöde
- Hastighet för nätverks fördröjning (låg/hög)
- Främsta wait-händelser 
- Parameter inställningar för databas
- Är databasens RAC, Exadata, med hjälp av avancerade funktioner eller konfigurationer

### <a name="virtual-machine-size"></a>Storlek för virtuell dator

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. beräkna VM-storlek baserat på CPU, minne och I/O-användning från AWR-rapporten

En sak som du kan titta på är de fem främsta förgrundade händelserna som visar var systemets Flask hals är.

I följande diagram är till exempel logg fils synkronisering överst. Det anger det antal väntande vänte tid som krävs innan LGWR skriver till logg filen för att göra om-logg filen. Dessa resultat visar att det krävs bättre lagring eller diskar. Dessutom visar diagrammet också antalet processor kärnor (kärnor) och mängden minne.

![Skärm bild som visar logg fils synkroniseringen överst i tabellen.](./media/oracle-design/cpu_memory_info.png)

Följande diagram visar det totala I/O för Läs-och skriv åtgärder. Det fanns 59 GB läst och 247,3 GB som skrevs under rapportens tid.

![Skärm bild som visar det totala I/O för läsning och skrivning.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Välj en virtuell dator

Nästa steg är att välja en virtuell dator av en liknande storlek som uppfyller dina krav baserat på den information som du har samlat in från AWR-rapporten. Du hittar en lista över tillgängliga virtuella datorer i artikel [minnet optimerat](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. finjustera storleken på den virtuella datorn med en liknande VM-serie som baseras på ACU

När du har valt den virtuella datorn ska du tänka på ACU för den virtuella datorn. Du kan välja en annan virtuell dator baserat på ACU-värdet som bättre passar dina behov. Mer information finns i [Azure Compute Unit](../../acu.md).

![Skärm bild av sidan ACU units](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Nätverks data flöde

I följande diagram visas relationen mellan genomflödet och IOPS:

![Skärm bild av data flöde](./media/oracle-design/throughput.png)

Det totala nätverks data flödet beräknas utifrån följande information:
- SQL * net-trafik
- Mbit/s x antal servrar (utgående data ström som Oracle data Guard)
- Andra faktorer, till exempel programreplikering

![Skärm bild av SQL * net-dataflöde](./media/oracle-design/sqlnet_info.png)

Utifrån dina krav på nätverks bandbredd finns det olika typer av gatewayer som du kan välja mellan. Dessa omfattar Basic, VpnGw och Azure ExpressRoute. Mer information finns på sidan med [priser för VPN gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Rekommendationer**

- Nätverks fördröjningen är högre jämfört med en lokal distribution. Att minska antalet nätverks fördröjningar kan förbättra prestanda avsevärt.
- För att minska antalet turer kan du konsolidera program som har höga transaktioner eller "chatty"-appar på samma virtuella dator.
- Använd Virtual Machines med [accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-cli.md) för bättre nätverks prestanda.
- För vissa Linux-distributioner kan du överväga att aktivera [stöd för trimning/avmappning](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installera [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) på en separat virtuell dator.
- Enorma sidor är inte aktiverade på Linux som standard. Överväg att aktivera enorma sidor och ange `use_large_pages = ONLY` Oracle dB. Detta kan hjälpa till att öka prestandan. Läs mer [här](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Disk typer och konfigurationer

- *Standard operativ system diskar*: dessa disk typer erbjuder beständiga data och cachelagring. De är optimerade för åtkomst till operativ systemet vid start och har inte utformats för antingen transaktions-eller informations lager (analytiska) arbets belastningar.

- *Hanterade diskar*: Azure hanterar de lagrings konton som du använder för dina virtuella dator diskar. Du anger disk typen (oftast Premium SSD för Oracle-arbetsbelastningar) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig.  Premium SSD Managed disk är bara tillgänglig för minnesoptimerade och särskilt utformade VM-serier. När du har valt en viss VM-storlek visar menyn endast de tillgängliga Premium Storage-SKU: erna som baseras på den virtuella dator storleken.

![Skärm bild av sidan hanterad disk](./media/oracle-design/premium_disk01.png)

När du har konfigurerat lagringen på en virtuell dator kanske du vill läsa in testa diskarna innan du skapar en databas. Att känna till i/O-priset vad gäller både svars tid och data flöde kan hjälpa dig att avgöra om de virtuella datorerna stöder det förväntade data flödet med svars mål.

Det finns ett antal verktyg för belastnings testning av program, till exempel Oracle Orion, Sysbench, SLOB och Fio.

Kör belastnings testet igen när du har distribuerat en Oracle-databas. Starta dina vanliga arbets belastningar och resultatet visar din miljös bas linje.  Var realistisk i arbets belastnings testet – det är inte klokt att köra en arbets belastning som inte är något som liknar vad du kommer att köra på den virtuella datorn i verkligheten.

Eftersom Oracle är en IO-intensiv databas för många är det ganska viktigt att storlek på lagringen baserat på IOPS-priset snarare än lagrings storleken. Om till exempel den nödvändiga IOPS är 5 000, men du bara behöver 200 GB, kan du fortfarande hämta P30-klass Premium-disken även om den har mer än 200 GB lagrings utrymme.

IOPS-priset kan hämtas från AWR-rapporten. Den bestäms av processen för att göra om-loggen, fysiska läsningar och skrivnings takt.  Kontrol lera alltid att den virtuella dator serien som valts har möjlighet att hantera IO-behovet för arbets belastningen.  Om den virtuella datorn har en lägre IO-gräns än lagringen anges Max gränsen för den virtuella datorn.

![Skärm bild av rapport sidan för AWR](./media/oracle-design/awr_report.png)

Till exempel är Restore-storleken 12 200 000 byte per sekund, vilket är lika med 11,63 Mbit/s.
IOPS är 12 200 000/2 358 = 5 174.

När du har en tydlig bild av I/O-kraven kan du välja en kombination av enheter som passar bäst för att uppfylla dessa krav.

**Rekommendationer**

- För data tabell utrymme sprider du i/O-arbetsbelastningen över flera diskar med hjälp av hanterad lagring eller Oracle ASM.
- Använd Oracle avancerad komprimering för att minska I/O (för både data och index).
- Separera återupprepnings loggar, temporära och ångra register utrymmen på separata data diskar.
- Lägg inte till några programfiler på standard OS-diskar (/dev/SDA). Diskarna är inte optimerade för start tider för snabb virtuell dator och de kanske inte ger dig bästa prestanda för ditt program.
- När du använder virtuella datorer i M-serien på Premium Storage aktiverar du [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) on REG logs disks.
- Överväg att flytta om återställnings loggar med hög latens till Ultra disk.

### <a name="disk-cache-settings"></a>Inställningar för disk-cache

Det finns tre alternativ för cachelagring av värden, men för en Oracle-databas rekommenderas skrivskyddad cachelagring för en databas arbets belastning.  ReadWrite kan leda till betydande sårbarheter i en datafile, där målet för en databas skrivning är att registrera den på data filen, inte cachelagra informationen.

Till skillnad från ett fil system eller program för en databas är rekommendationen för cachelagring av värden *skrivskyddad*: alla begär Anden cachelagras för framtida läsningar. Alla skrivningar fortsätter att skrivas till disk.

**Rekommendationer**

För att maximera data flödet rekommenderar vi att du börjar med **ReadOnly** för cachelagring av värdar när det är möjligt. Tänk på att du måste inaktivera "barriärer" när du monterar fil systemet med alternativen **ReadOnly** . Premium Storage Uppdatera/etc/fstab-filen med UUID: n till diskarna.

![Skärm bild av sidan hanterad disk som visar alternativen ReadOnly och ingen.](./media/oracle-design/premium_disk02.png)

- För OS-diskar använder du standard för **Read/Write-** cachelagring och använder Premium SSD för virtuella datorer med Oracle-arbetsbelastningar.  Se också till att den volym som används för växling också är på Premium SSD.
- Använd **ReadOnly** för cachelagring för alla datadatafiler. ReadOnly-cachelagring är bara tillgängligt för Premium Managed disk, P30 och senare.  Det finns en gräns på en 4095GiB-volym som kan användas med ReadOnly-cachelagring.  En allokering som är större inaktiverar cachelagring av värden som standard.

Om arbets belastningarna varierar kraftigt mellan dygnet och kvällen och i/o-arbetsbelastningen kan det stödja den, P1-P20 Premium SSD med bursting kan ge den prestanda som krävs under tids periodens batch-belastning eller begränsade IO-krav.  

## <a name="security"></a>Säkerhet

När du har konfigurerat och konfigurerat Azure-miljön är nästa steg att skydda nätverket. Här följer några rekommendationer:

- *NSG-princip*: NSG kan definieras av ett undernät eller ett nätverkskort. Det är enklare att kontrol lera åtkomst på under näts nivån, både för säkerhet och tvinga framning av saker som program brand väggar.

- *Hopp*: för säkrare åtkomst bör administratörer inte ansluta direkt till program tjänsten eller databasen. En hoppning används som ett medium mellan administratörs datorn och Azure-resurserna.
![Skärm bild av sidan för bygel-topologin](./media/oracle-design/jumpbox.png)

    Administratörs datorn ska endast erbjuda IP-begränsad åtkomst till hoppet. Hoppet ska ha åtkomst till programmet och databasen.

- *Privat nätverk* (undernät): Vi rekommenderar att du har program tjänsten och databasen på separata undernät, så att kontrollen kan ställas in av NSG-principen på ett bättre sätt.


## <a name="additional-reading"></a>Mer att läsa

- [Konfigurera Oracle ASM](configure-oracle-asm.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera den gyllene Oracle-grinden](configure-oracle-golden-gate.md)
- [Säkerhets kopiering och återställning i Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
