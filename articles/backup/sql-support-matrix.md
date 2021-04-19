---
title: Azure Backup för säkerhetskopiering SQL Server virtuella Azure-datorer
description: Innehåller en sammanfattning av supportinställningar och begränsningar vid SQL Server i virtuella Azure-datorer med Azure Backup tjänsten.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: 354f64eb86cd545860c47562fba7ff43babe72ca
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714154"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Stödmatris för SQL Server Backup på virtuella Azure-datorer

Du kan använda Azure Backup för att SQL Server databaser i virtuella Azure-datorer som finns på Microsoft Azure molnplattformen. Den här artikeln sammanfattar allmänna supportinställningar och begränsningar för scenarier och distributioner av SQL Server Backup på virtuella Azure-datorer.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Information**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Regioner som stöds** | Australien, sydöstra (ASE), Australien, östra (AE), Australien, centrala (AC), Australien, centrala 2 (AC) <br> Brasilien, södra (BRS)<br> Kanada, centrala (QUEBEC), Kanada, östra (CE)<br> Asien, sydöstra (SEA), Asien, östra (EA) <br> USA, östra (EUS), USA, östra 2 (EUS2), USA, västra centrala (WCUS), USA, västra (WUS); USA, västra 2 (WUS 2) USA, norra centrala (NCUS) USA, centrala (CUS) USA, södra centrala (SCUS) <br> Indien, centrala (INC), Indien, södra (INS), Indien, västra <br> Japan, östra (JPE), Japan, västra (JPW) <br> Sydkorea, centrala (KRC), Sydkorea, södra (KRS) <br> Europa, norra (NE), Europa, västra <br> Storbritannien, södra (UKS), Storbritannien, västra (UKW) <br> US Gov, Arizona, US Gov, Virginia, US Gov, Texas, USA DoD, centrala, USA DoD, östra <br> Tyskland, norra, Tyskland, västra centrala <br> Schweiz, norra, Schweiz, västra <br> Frankrike, centrala <br> Kina, östra, Kina, östra 2, Kina, norra, Kina, norra 2
**Operativsystem som stöds** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2019 SQL Server 2017 enligt beskrivningen på sidan Sök efter produktlivscykel [,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)SQL Server 2016 och SPS enligt beskrivningen på sidan [Sök](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)produktlivscykel , SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Express Local DB-versioner stöds inte.
**.NET-versioner som stöds** | .NET Framework 4.5.2 eller senare installerat på den virtuella datorn

## <a name="feature-considerations-and-limitations"></a>Överväganden och begränsningar för funktioner

|Inställning  |Övre gräns |
|---------|---------|
|Antal databaser som kan skyddas på en server (och i ett valv)    |   2000      |
|Databasstorlek som stöds (utöver detta kan prestandaproblem uppstå)   |   6 TB*      |
|Antal filer som stöds i en databas    |   1000      |

_*Storleksgränsen för databasen beror på dataöverföringshastigheten som vi stöder och konfigurationen av tidsgränsen för säkerhetskopiering. Det är inte den hårda gränsen. [Läs mer om](#backup-throughput-performance) prestanda för säkerhetskopieringsflöde._

* SQL Server säkerhetskopiering kan konfigureras i Azure Portal eller **PowerShell.** CLI stöds inte.
* Lösningen stöds på båda typerna av [distributioner – Azure Resource Manager](../azure-resource-manager/management/deployment-models.md) virtuella datorer och klassiska virtuella datorer.
* Alla säkerhetskopieringstyper (fullständig/differentiell/logg) och återställningsmodeller (enkel/fullständig/massloggad) stöds.
* För **skrivskyddade databaser:** fullständiga och endast kopierade fullständiga säkerhetskopior är de enda säkerhetskopieringstyper som stöds.
* Intern SQL-komprimering stöds om den uttryckligen aktiveras av användaren i säkerhetskopieringspolicyn. Azure Backup åsidosätter standardvärden på instansnivå med satsen COMPRESSION/NO_COMPRESSION, beroende på värdet för den här kontrollen som angetts av användaren.
* TDE – aktiverad databassäkerhetskopiering stöds. Om du vill återställa en TDE-krypterad databas SQL Server en annan databas måste du först återställa [certifikatet till målservern](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). Komprimering av säkerhetskopior för TDE-aktiverade databaser för SQL Server 2016 och senare versioner är tillgänglig, men med lägre överföringsstorlek enligt förklaringen [här](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Säkerhetskopierings- och återställningsåtgärder för speglingsdatabaser och ögonblicksbilder av databaser stöds inte.
* SQL Server **redundansklusterinstans (FCI)** stöds inte.
* Om du använder fler än en säkerhetskopieringslösning för att säkerhetskopiera din fristående SQL Server-instans eller SQL Always on-tillgänglighetsgrupp kan det leda till säkerhetskopieringsfel. Undvik att göra det. Säkerhetskopiering av två noder i en tillgänglighetsgrupp individuellt med samma eller olika lösningar kan också leda till säkerhetskopieringsfel.
* När tillgänglighetsgrupper har konfigurerats tas säkerhetskopior från de olika noderna baserat på några faktorer. Säkerhetskopieringsbeteendet för en tillgänglighetsgrupp sammanfattas nedan.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Så fungerar säkerhetskopiering med AlwaysOn-tillgänglighetsgrupper

Vi rekommenderar att säkerhetskopieringen endast konfigureras på en nod i en tillgänglighetsgrupp (AG). Konfigurera alltid säkerhetskopiering i samma region som den primära noden. Med andra ord måste du alltid ha den primära noden i den region där du konfigurerar säkerhetskopieringen. Om alla noder i ag finns i samma region där säkerhetskopieringen har konfigurerats finns det inget problem.

#### <a name="for-cross-region-ag"></a>För AG mellan regioner

* Oavsett inställningarna för säkerhetskopiering körs säkerhetskopieringar endast från noder som finns i samma region där säkerhetskopieringen har konfigurerats. Det beror på att säkerhetskopieringar mellan regioner inte stöds. Om du bara har två noder och den sekundära noden finns i den andra regionen fortsätter säkerhetskopieringarna att köras från den primära noden (om inte inställningen för säkerhetskopiering är "endast sekundär").
* Om en nod redundans redundansar till en annan region än den där säkerhetskopieringen har konfigurerats, misslyckas säkerhetskopieringar på noderna i regionen för redundans.

Beroende på säkerhetskopieringsinställningar och säkerhetskopieringstyper (fullständig/differentiell/endast logg/endast kopia) tas säkerhetskopior från en viss nod (primär/sekundär).

#### <a name="backup-preference-primary"></a>Inställningar för säkerhetskopiering: Primär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Loggas |  Primär
Copy-Only Fullständig |  Primär

#### <a name="backup-preference-secondary-only"></a>Inställningar för säkerhetskopiering: Endast sekundär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Loggas |  Sekundär
Copy-Only Fullständig |  Sekundär

#### <a name="backup-preference-secondary"></a>Inställningar för säkerhetskopiering: Sekundär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Loggas |  Sekundär
Copy-Only Fullständig |  Sekundär

#### <a name="no-backup-preference"></a>Inga inställningar för säkerhetskopiering

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Loggas |  Sekundär
Copy-Only Fullständig |  Sekundär

## <a name="backup-throughput-performance"></a>Prestanda för säkerhetskopieringsflöde

Azure Backup stöder en konsekvent dataöverföringshastighet på 200 Mbit/s för fullständiga och differentiella säkerhetskopior av stora SQL-databaser (på 500 GB). Om du vill använda optimala prestanda ser du till att:

- Den underliggande virtuella datorn (som innehåller SQL Server instansen som är värd för databasen) konfigureras med det nödvändiga nätverkets dataflöde. Om det maximala dataflödet för den virtuella datorn är mindre än 200 Mbit/s kan Azure Backup inte överföra data med optimal hastighet.<br>Dessutom måste den disk som innehåller databasfilerna ha tillräckligt med etablerat dataflöde. [Läs mer](../virtual-machines/disks-performance.md) om diskgenomflöde och prestanda i virtuella Azure-datorer. 
- Processer som körs på den virtuella datorn förbrukar inte vm-bandbredden. 
- Säkerhetskopieringsschemana är utspridda över en delmängd av databaserna. Flera säkerhetskopieringar som körs samtidigt på en virtuell dator delar nätverkets förbrukningshastighet mellan säkerhetskopiorna. [Läs mer](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) om hur du styr antalet samtidiga säkerhetskopieringar.

>[!NOTE]
> [Ladda ned den detaljerade Resurshanteraren för](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) att beräkna det ungefärliga antalet skyddade databaser som rekommenderas per server baserat på VM-resurser, bandbredd och säkerhetskopieringspolicy.

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du SQL Server en databas](backup-azure-sql-database.md) som körs på en virtuell Azure-dator.