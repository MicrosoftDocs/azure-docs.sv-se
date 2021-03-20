---
title: Bästa metoder för prestanda och rikt linjer för konfiguration – Azure SQL Edge
description: Lär dig mer om prestanda metod tips och rikt linjer för konfiguration i Azure SQL Edge
keywords: SQL Edge, data kvarhållning
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392019"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Metod tips för prestanda och rikt linjer för konfigurering

Azure SQL Edge innehåller flera funktioner och funktioner som kan användas för att förbättra prestandan för din SQL Edge-distribution. Den här artikeln innehåller metod tips och rekommendationer för att maximera prestanda. 

## <a name="best-practices"></a>Bästa praxis 

### <a name="configure-multiple-tempdb-data-files"></a>Konfigurera flera tempdb-datafiler

Azure SQL Edge som standard skapar bara en tempdb-datafil som en del av container initieringen. Vi rekommenderar att du planerar att skapa flera tempdb-datafiler efter distribution. Mer information finns i rikt linjerna i artikeln [rekommendationer för att minska tilldelnings konkurrens i SQL Server tempdb-databasen](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Använd klustrade columnstore-index där det är möjligt

IoT-och Edge-enheter tenderar att generera stora mängder data som vanligt vis sammanställs under en tids period för analys. Enskilda data rader används sällan för analyser. Columnstore-index är idealiska för att lagra och ställa frågor till sådana stora data mängder. Det här indexet använder kolumnbaserade data lagrings-och fråge bearbetning för att uppnå upp till 10 gånger frågans prestanda över traditionell orienterad lagring. Du kan också få upp till 10 gånger data komprimeringen över den okomprimerade data storleken. Mer information finns i [columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview)

Dessutom är andra Azure SQL Edge-funktioner som data strömning och datakvarhållning att dra nytta av columnstore-optimeringar kring data infogning och data borttagning. 

### <a name="simple-recovery-model"></a>Enkel återställnings modell

Eftersom lagringen kan begränsas till gräns enheter använder alla användar databaser i Azure SQL Edge den enkla återställnings modellen som standard. Enkel återställnings modell frigörs automatiskt logg utrymme för att hålla utrymmes kraven små, vilket innebär att behovet av att hantera transaktions logg utrymmet elimineras. Detta kan vara användbart på gräns enheter med begränsad lagring tillgänglig. Mer information om den enkla återställnings modellen och andra återställnings modeller finns i [återställnings modeller](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Åtgärder som logg överföring och omlagring av tidpunkter, som kräver säkerhets kopiering av transaktions loggar, stöds inte av den enkla återställnings modellen.  

## <a name="advanced-configuration"></a>Avancerad konfiguration 

### <a name="setting-memory-limits"></a>Ange minnes gränser

Azure SQL Edge stöder upp till 64 GB minne för bufferten, medan ytterligare minne kan krävas av satellit processer som körs i SQL Edge-behållaren. På mindre gräns enheter med begränsat minne rekommenderar vi att du begränsar det tillgängliga minnet för SQL Edge-behållare, så att Docker-värden och andra gräns processer eller moduler kan fungera korrekt. Det totala tillgängliga minnet för SQL Edge kan kontrol leras med hjälp av följande mekanismer. 

- Begränsa tillgängligt minne för SQL Edge-behållare: det totala minne som är tillgängligt för SQL Edge-behållaren kan begränsas med hjälp av konfigurations alternativet container runtime `--memory` . Mer information om hur du begränsar det minne som är tillgängligt för SQL Edge-behållaren finns i [körnings alternativ med minne, processorer och GPU: er](https://docs.docker.com/config/containers/resource_constraints/).

- Begränsa tillgängligt minne för SQL-processen i behållaren: SQL-processen i behållaren använder som standard bara 80% av det fysiska RAM-minne som är tillgängligt. För merparten av distributionerna är standard konfigurationen fin. Det kan dock finnas scenarier där ytterligare minne kan krävas för data strömningen och ONNX-processerna som körs inuti SQL Edge-behållare. I sådana fall kan det minne som är tillgängligt för SQL-processen kontrol leras med hjälp av `memory.memorylimitmb` inställningen i filen MSSQL-conf. Mer information finns i [Konfigurera med hjälp av MSSQL. conf-filen](configure.md#configure-by-using-an-mssqlconf-file).

Var noga med att inte ange värdet för lågt när du anger minnes gränserna. Om du inte anger tillräckligt med minne för SQL-processen kan det påverka SQL-prestanda allvarligt.

### <a name="delayed-durability"></a>Fördröjd hållbarhet

Transaktioner i Azure SQL Edge kan vara antingen helt varaktiga, SQL Server standard eller fördröjd tålighet (kallas även för Lazy commit).

Fullständigt varaktiga transaktions skrivningar är synkrona och rapporterar en genomför som lyckad och returnerar kontroll till klienten först efter att logg posterna för transaktionen skrivits till disk. Fördröjda varaktiga transaktions skrivningar är asynkrona och rapporterar ett genomförande som lyckat innan logg posterna för transaktionen skrivs till disk. Att skriva transaktions logg posterna till disk krävs för att en transaktion ska vara beständig. Fördröjda varaktiga transaktioner blir varaktiga när transaktions logg posterna töms på disk. 

I distributioner där **viss data förlust** kan tolereras eller på gräns enheter med långsam lagring, kan fördröjd hållbarhet användas för att optimera data inmatning och data kvarhållning baserad rensning. Mer information finns i [reglera hållbarhet för transaktioner](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Linux OS-konfigurationer 

Överväg att använda följande [konfigurations](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) inställningar för Linux-operativsystemet för att få bästa möjliga prestanda för en SQL-installation.