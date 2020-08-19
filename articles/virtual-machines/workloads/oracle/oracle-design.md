---
title: Utforma och implementera en Oracle-databas på Azure | Microsoft Docs
description: Utforma och implementera en Oracle-databas i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogardle
ms.openlocfilehash: 0dd787916159637ce92a29a5d4baa1ffe7a09ba4
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510019"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Utforma och implementera en Oracle-databas i Azure

## <a name="assumptions"></a>Antaganden

- Du planerar att migrera en Oracle-databas från en lokal plats till Azure.
- Du har ett [diagnostiskt paket](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) för Oracle Database du vill migrera
- Du har en förståelse för de olika måtten i Oracle AWR-rapporter.
- Du har en grundläggande förståelse för program prestanda och plattforms användning.

## <a name="goals"></a>Mål

- Lär dig hur du optimerar din Oracle-distribution i Azure.
- Utforska alternativ för prestanda justering för en Oracle-databas i en Azure-miljö.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Skillnaderna mellan en lokal och Azure-implementering 

Nedan följer några viktiga saker att tänka på när du migrerar lokala program till Azure. 

En viktig skillnad är att i en Azure-implementering delas resurser som virtuella datorer, diskar och virtuella nätverk mellan andra klienter. Dessutom kan resurser begränsas baserat på kraven. I stället för att fokusera på att undvika att Miss lyckas (MTBF) är Azure mer fokuserat på att sluta fungera (MTTR).

I följande tabell visas några av skillnaderna mellan en lokal implementering och en Azure-implementering av en Oracle-databas.


|  | Lokal implementering | Azure-implementering |
| --- | --- | --- |
| **Nätverk** |LAN/WAN  |SDN (program varu Defined Networking)|
| **Säkerhets grupp** |Verktyg för begränsning av IP/port |[Nätverks säkerhets grupp (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Återhämtning** |MTBF (genomsnittlig tid mellan haverier) |MTTR (genomsnittlig tid för återställning)|
| **Planerat underhåll** |Korrigeringar/uppgraderingar|[Tillgänglighets uppsättningar](../../windows/infrastructure-example.md) (uppdatering/uppgraderingar som hanteras av Azure) |
| **Resurs** |Dedikerad  |Delas med andra klienter|
| **Regioner** |Datacenter |[Regionpar](../../regions.md#region-pairs)|
| **Storage** |SAN/fysiska diskar |[Azure-hanterad lagring](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Skalning** |Lodrät skala |Horisontell skalning|


### <a name="requirements"></a>Krav

- Bestäm databasens storlek och tillväxt hastighet.
- Fastställ IOPS-kraven, som du kan beräkna utifrån Oracle AWR-rapporter eller andra verktyg för nätverks övervakning.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns fyra möjliga områden som du kan finjustera för att förbättra prestanda i en Azure-miljö:

- Storlek för virtuell dator
- Nätverks data flöde
- Disk typer och konfigurationer
- Inställningar för disk-cache

### <a name="generate-an-awr-report"></a>Generera en AWR-rapport

Om du har en befintlig Oracle-databas och planerar att migrera till Azure har du flera alternativ. Om du har ett [diagnostiskt paket](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) för dina Oracle-instanser kan du köra rapporten Oracle AWR för att hämta måtten (IOPS, Mbps, GiBs och så vidare). Välj sedan den virtuella datorn baserat på de mått som du har samlat in. Eller så kan du kontakta infrastruktur teamet för att få liknande information.

Du kan överväga att köra din AWR-rapport under både regelbundna och högsta arbets belastningar, så att du kan jämföra. Utifrån dessa rapporter kan du ändra storlek på de virtuella datorerna baserat på antingen den genomsnittliga arbets belastningen eller den maximala arbets belastningen.

Följande är ett exempel på hur du skapar en AWR-rapport (generera dina AWR-rapporter med hjälp av Oracle Enterprise Manager, om din aktuella installation har en):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Viktiga mått

Följande är de mått som du kan hämta från AWR-rapporten:

- Totalt antal kärnor
- PROCESSOR klock hastighet
- Totalt minne i GB
- CPU-användning
- Högsta data överförings hastighet
- Antal i/O-ändringar (Läs/skriv)
- Gör om logg frekvens (Mbit/s)
- Nätverks data flöde
- Hastighet för nätverks fördröjning (låg/hög)
- Databas storlek i GB
- Byte som tagits emot via SQL * net från/till klient

### <a name="virtual-machine-size"></a>Storlek för virtuell dator

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. beräkna VM-storlek baserat på CPU, minne och I/O-användning från AWR-rapporten

En sak som du kan titta på är de fem främsta förgrundade händelserna som visar var systemets Flask hals är.

I följande diagram är till exempel logg fils synkronisering överst. Det anger det antal väntande vänte tid som krävs innan LGWR skriver till logg filen för att göra om-logg filen. Dessa resultat visar att det krävs bättre lagring eller diskar. Dessutom visar diagrammet också antalet processor kärnor (kärnor) och mängden minne.

![Skärm bild av rapport sidan för AWR](./media/oracle-design/cpu_memory_info.png)

Följande diagram visar det totala I/O för Läs-och skriv åtgärder. Det fanns 59 GB läst och 247,3 GB som skrevs under rapportens tid.

![Skärm bild av rapport sidan för AWR](./media/oracle-design/io_info.png)

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
- För vissa Linux-distributioner kan du överväga att aktivera [stöd för trimning/avmappning](../../linux/configure-lvm.md#trimunmap-support).
- Installera [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) på en separat virtuell dator.
- Enorma sidor är inte aktiverade på Linux som standard. Överväg att aktivera enorma sidor och ange `use_large_pages = ONLY` Oracle dB. Detta kan hjälpa till att öka prestandan. Mer information hittar du [här](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Disk typer och konfigurationer

- *Standard operativ system diskar*: dessa disk typer erbjuder beständiga data och cachelagring. De är optimerade för åtkomst till operativ systemet vid start och har inte utformats för antingen transaktions-eller informations lager (analytiska) arbets belastningar.

- *Ohanterade diskar*: med dessa disk typer hanterar du de lagrings konton som lagrar den virtuella hård disk filen (VHD) som motsvarar dina virtuella dator diskar. VHD-filer lagras som Page blobbar i Azure Storage-konton.

- *Hanterade diskar*: Azure hanterar de lagrings konton som du använder för dina virtuella dator diskar. Du anger disk typen (Premium eller standard) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig.

- *Premium Storage-diskar*: dessa disk typer passar bäst för produktions arbets belastningar. Premium Storage stöder VM-diskar som kan kopplas till vissa virtuella datorer i storleks serien, till exempel DS, DSv2, GS och F-seriens virtuella datorer. Premium-disken har olika storlekar och du kan välja mellan diskar som sträcker sig från 32 GB till 4 096 GB. Varje disk storlek har sina egna prestanda krav. Beroende på dina program krav kan du koppla en eller flera diskar till den virtuella datorn.

När du skapar en ny hanterad disk från portalen kan du välja **konto typen** för den typ av disk som du vill använda. Tänk på att inte alla tillgängliga diskar visas på den nedrullningsbara menyn. När du har valt en viss VM-storlek visar menyn endast de tillgängliga Premium Storage-SKU: erna som baseras på den virtuella dator storleken.

![Skärm bild av sidan hanterad disk](./media/oracle-design/premium_disk01.png)

När du har konfigurerat lagringen på en virtuell dator kanske du vill läsa in testa diskarna innan du skapar en databas. Att känna till i/O-priset vad gäller både svars tid och data flöde kan hjälpa dig att avgöra om de virtuella datorerna stöder det förväntade data flödet med svars mål.

Det finns ett antal verktyg för belastnings testning av program, till exempel Oracle Orion, Sysbench och Fio.

Kör belastnings testet igen när du har distribuerat en Oracle-databas. Starta dina vanliga arbets belastningar och resultatet visar din miljös bas linje.

Det kan vara mer viktigt att ändra lagrings utrymme baserat på IOPS-priset snarare än lagrings storleken. Om till exempel den nödvändiga IOPS är 5 000, men du bara behöver 200 GB, kan du fortfarande hämta P30-klass Premium-disken även om den har mer än 200 GB lagrings utrymme.

IOPS-priset kan hämtas från AWR-rapporten. Den bestäms av processen för att göra om-loggen, fysiska läsningar och skrivnings takt.

![Skärm bild av rapport sidan för AWR](./media/oracle-design/awr_report.png)

Till exempel är Restore-storleken 12 200 000 byte per sekund, vilket är lika med 11,63 Mbit/s.
IOPS är 12 200 000/2 358 = 5 174.

När du har en tydlig bild av I/O-kraven kan du välja en kombination av enheter som passar bäst för att uppfylla dessa krav.

**Rekommendationer**

- För data tabell utrymme sprider du i/O-arbetsbelastningen över flera diskar med hjälp av hanterad lagring eller Oracle ASM.
- När I/O-blocket ökar för Läs intensiva och skriv intensiva åtgärder, lägger du till fler data diskar.
- Öka block storleken för stora sekventiella processer.
- Använd data komprimering för att minska I/O (för både data och index).
- Separera återupprepnings loggar, system, och temporärt, och ångra TS på separata data diskar.
- Lägg inte till några programfiler på standard OS-diskar (/dev/SDA). Diskarna är inte optimerade för start tider för snabb virtuell dator och de kanske inte ger dig bästa prestanda för ditt program.
- När du använder virtuella datorer i M-serien på Premium Storage aktiverar du [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) on REG logs disks.

### <a name="disk-cache-settings"></a>Inställningar för disk-cache

Det finns tre alternativ för cachelagring av värdar:

- *ReadOnly*: alla begär Anden cachelagras för framtida läsningar. Alla skrivningar sparas direkt i Azure Blob Storage.

- *Readwrite*: det här är en "Read-Ahead"-algoritm. Läsningarna och skrivningarna cachelagras för framtida läsningar. Skrivningar som inte skrivs över sparas i det lokala cacheminnet först. Den ger också den lägsta disk fördröjningen för lätta arbets belastningar. Att använda ReadWrite cache med ett program som inte hanterar beständiga data kan leda till data förlust, om den virtuella datorn kraschar.

- *Ingen* (inaktive rad): med det här alternativet kan du kringgå cacheminnet. Alla data överförs till disk och sparas i Azure Storage. Med den här metoden får du det högsta I/O-priset för I/O-intensiva arbets belastningar. Du måste också ta med "transaktions kostnader".

**Rekommendationer**

För att maximera data flödet rekommenderar vi att du börjar med **ingen** för cachelagring av värden. Tänk på att du måste inaktivera "barriärer" när du monterar fil systemet med alternativen **ReadOnly** eller **ingen** för Premium Storage. Uppdatera/etc/fstab-filen med UUID: n till diskarna.

![Skärm bild av sidan hanterad disk](./media/oracle-design/premium_disk02.png)

- Använd standardcachelagring för **läsning och skrivning** för OS-diskar.
- För SYSTEM, TEMP och ångra Använd **ingen** för cachelagring.
- För DATA använder du **ingen** för cachelagring. Om databasen är skrivskyddad eller Läs intensiv använder du **skrivskyddad** cachelagring.

När din datadisk-inställning har sparats kan du inte ändra inställningen för cachelagring av värden om du inte avmonterar enheten på operativ system nivå och sedan monterar den igen när du har gjort ändringen.

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
- [Säkerhets kopiering och återställning i Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)
