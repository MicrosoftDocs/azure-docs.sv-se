---
title: Optimera prestanda för virtuella datorer i Azure Lsv2-serien
description: Lär dig hur du optimerar prestanda för din lösning på virtuella datorer i Lsv2-serien med hjälp av ett Windows-exempel.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 8d38e3f58de0fc4cc1e963c18b002dc1e16852ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556017"
---
# <a name="optimize-performance-on-the-lsv2-series-windows-virtual-machines"></a>Optimera prestanda för virtuella Windows-datorer med Lsv2-serien

Virtuella datorer i Lsv2-serien har stöd för en mängd arbets belastningar som kräver höga I/O och data flöden i lokal lagring i en mängd olika program och branscher.  Lsv2-serien är idealisk för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser, inklusive Cassandra, MongoDB, Cloudera och Redis.

Utformningen av Lsv2-serien Virtual Machines (VM) maximerar AMD EPYC™ 7551-processorn för att ge bästa möjliga prestanda mellan processor, minne, NVMe-enheter och de virtuella datorerna. Förutom att maximera maskin varu prestanda är virtuella datorer i Lsv2-serien utformade för att fungera med behoven hos Windows-och Linux-operativsystem för bättre prestanda med maskin vara och program vara.

Att justera program varan och maskin varan resulterade i den optimerade versionen av [Windows Server 2019 Data Center](https://www.microsoft.com/cloud-platform/windows-server-pricing), som lanserades i början av december 2018 till Azure Marketplace, som stöder högsta prestanda på NVMe-enheter i virtuella datorer i Lsv2-serien.

Den här artikeln innehåller tips och förslag på hur du kan se till att dina arbets belastningar och program uppnår högsta möjliga prestanda för de virtuella datorerna. Informationen på den här sidan uppdateras kontinuerligt eftersom mer Lsv2 optimerade avbildningar läggs till i Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arkitektur för AMD EYPC™ chipset

Virtuella datorer i Lsv2-serien använder AMD-EYPC™ Server processorer baserat på Zen mikroarkitektur. AMD utvecklade Oändlighets infrastruktur (om) för EYPC™ som skalbara Interconnect för den NUMA-modell som kan användas för on-die-, paket-och multi-Pack-kommunikation. Jämfört med QPI (Quick-Path Interconnect) och UPI (Ultra-Path Interconnect) som används på Intel moderna monolitisk-Die-processorer kan AMD: s många-NUMA-arkitektur ha både prestanda-och utmaningar. Den faktiska effekten av minnes bandbredd och latens begränsningar kan variera beroende på vilken typ av arbets belastningar som körs.

## <a name="tips-for-maximizing-performance"></a>Tips för att maximera prestanda

* Maskin varan som ger de virtuella datorerna i Lsv2-serien använder NVMe-enheter med åtta I/O-ködjup (QP) s. Varje NVMe-enhet I/O-kö är faktiskt ett par: en sändnings kö och en slut för ande kö. NVMe-drivrutinen har kon figurer ATS för att optimera användningen av dessa åtta I/O-frågor per sekund genom att distribuera i/O i ett schema med resursallokering (Round Robin). För att få högsta prestanda kan du köra åtta jobb per enhet för att matcha.

* Undvik att blanda NVMe-administratörs kommandon (t. ex. en SMART informations fråga för NVMe, osv.) med kommandona I/O för NVMe under aktiva arbets belastningar. Lsv2 NVMe-enheter backas upp av Hyper-V NVMe Direct-teknik, som växlar till "långsamt läge" när några NVMe-administratörs kommandon väntar. Lsv2-användare kan se en dramatisk prestanda minskning i i/O-prestanda i NVMe om det händer.

* Lsv2-användare bör inte förlita sig på enhetens NUMA-information (all 0) som rapporteras från den virtuella datorn för data enheter för att bestämma NUMA-tillhörighet för sina appar. Det rekommenderade sättet för bättre prestanda är att sprida arbets belastningar mellan CPU: er om det är möjligt. 

* Det maximala ködjup som stöds per I/O-ködjup för Lsv2 VM NVMe-enhet är 1024 (vs. Amazon i3 KÖDJUP 32 Limit). Lsv2-användare bör begränsa sina (syntetiska) arbets belastningar till ködjup 1024 eller lägre för att undvika att utlösa kön med fullständiga villkor, vilket kan minska prestandan.

## <a name="utilizing-local-nvme-storage"></a>Använd lokal NVMe-lagring

Den lokala lagringen på 1,92 TB NVMe-disken på alla virtuella Lsv2-datorer är tillfällig. Vid en lyckad standard omstart av den virtuella datorn kommer data på den lokala NVMe-disken att bevaras. Data bevaras inte i NVMe om den virtuella datorn distribueras om, avallokeras eller tas bort. Data bevaras inte om ett annat problem gör att den virtuella datorn eller den maskin vara som körs på inte är felfri. När detta händer raderas alla data på den gamla värden på ett säkert sätt.

Det kan också finnas fall då den virtuella datorn måste flyttas till en annan värddator, till exempel under en planerad underhålls åtgärd. Planerade underhålls åtgärder och vissa maskin varu problem kan förväntas med [schemalagda händelser](scheduled-events.md). Schemalagda händelser bör användas för att uppdatera för förväntade underhålls-och återställnings åtgärder.

Om en planerad underhålls händelse kräver att den virtuella datorn återskapas på en ny värd med tomma lokala diskar, måste data synkroniseras igen (återigen, med data på den gamla värden på ett säkert sätt raderas). Detta beror på att virtuella datorer i Lsv2-serien inte har stöd för Direktmigrering på den lokala NVMe-disken.

Det finns två lägen för planerat underhåll.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard kund styrt underhåll av virtuell dator

- Den virtuella datorn flyttas till en uppdaterad värd under en 30-dagars period.
- Lsv2 lokala lagrings data kan gå förlorade, så säkerhets kopiering av data före händelsen rekommenderas.

### <a name="automatic-maintenance"></a>Automatiskt underhåll

- Inträffar om kunden inte utför kundstyrt underhåll, eller i händelse av nöd procedurer som ett värdepapper som är noll dagar.
- Avsedd för att bevara kund information, men det finns en liten risk för en VM-frysning eller omstart.
- Lsv2 lokala lagrings data kan gå förlorade, så säkerhets kopiering av data före händelsen rekommenderas.

För alla kommande tjänste händelser använder du den kontrollerade underhålls processen för att välja en tid som är mest praktisk för uppdateringen. Före händelsen kan du säkerhetskopiera dina data i Premium Storage. När underhålls händelsen har slutförts kan du returnera dina data till den uppdaterade Lsv2 VMS lokalt NVMe-lagring.

Scenarier som upprätthåller data på lokala NVMe-diskar är:

- Den virtuella datorn körs och är felfri.
- Den virtuella datorn har startats om på plats (av dig eller Azure).
- Den virtuella datorn har pausats (Stoppad utan att ta bort tilldelningen).
- Merparten av de planerade underhålls åtgärderna för underhåll.

Scenarier som skyddar kunderna på ett säkert sätt är:

- Den virtuella datorn distribueras om, stoppas (avallokeras) eller tas bort (av dig).
- Den virtuella datorn blir ohälsosam och måste serva till en annan nod på grund av ett maskin varu problem.
- Ett litet antal planerade underhålls åtgärder som kräver att den virtuella datorn allokeras om till en annan värd för underhåll.

Mer information om alternativ för att säkerhetskopiera data i lokal lagring finns i [säkerhets kopiering och haveri beredskap för Azure IaaS-diskar](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Hur gör jag för att du börja distribuera virtuella datorer i Lsv2-serien?**  
   På samma sätt som andra virtuella datorer kan du använda [portalen](quick-create-portal.md), [Azure CLI](quick-create-cli.md)eller [PowerShell](quick-create-powershell.md) för att skapa en virtuell dator.

* **Kommer en enskild NVMe-disk orsaka att alla virtuella datorer på värden Miss lyckas?**  
   Om ett diskfel upptäcks på den fjärrnoden, är maskin varan i ett felaktigt tillstånd. När detta inträffar är alla virtuella datorer i noden automatiskt avallokerade och flyttas till en felfri nod. För virtuella datorer i Lsv2-serien innebär detta att kundens data på den misslyckade noden också raderas på ett säkert sätt och att de måste återskapas av kunden på den nya noden. Innan Direktmigrering blir tillgängligt på Lsv2, kommer data på den felaktiga noden att proaktivt flyttas med de virtuella datorerna när de överförs till en annan nod.

* **Behöver jag göra korrigeringar i Windows i Windows Server 2012 eller Windows Server 2016?**  
   NVMe-avsökning är bara tillgängligt på Windows Server 2019 på Azure.  

* **Kan jag växla tillbaka till en traditionell Interrupt Service Routine (ISR)-modell?**  
   Virtuella datorer i Lsv2-serien är optimerade för NVMe-avsökning. Uppdateringar tillhandahålls kontinuerligt för att förbättra avsöknings prestanda.

* **Kan jag justera avsöknings inställningarna i Windows Server 2019?**  
   Avsöknings inställningarna är inte inställbara.
   
## <a name="next-steps"></a>Nästa steg

* Se specifikationer för alla [virtuella datorer som är optimerade för lagrings prestanda](../sizes-storage.md) på Azure
