---
title: Funktioner i Azure Arc-aktiverad SQL-hanterad instans
description: Funktioner i Azure Arc-aktiverad SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589227"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Funktioner i Azure Arc-aktiverad SQL-hanterad instans

Azure Arc-aktiverad SQL-hanterad instans delar en gemensam kodbas med den senaste stabila versionen av SQL Server. De flesta standard-SQL-språk, fråge behandling och funktioner för databas hantering är identiska. De funktioner som är gemensamma för SQL Server och SQL Database eller SQL-hanterad instans är:

- Språk funktioner – [kontroll av nyckelord för Flow-språk](/sql/t-sql/language-elements/control-of-flow), [markörer](/sql/t-sql/language-elements/cursors-transact-sql), [data typer](/sql/t-sql/data-types/data-types-transact-sql), [DML-instruktioner](/sql/t-sql/queries/queries), [predikat](/sql/t-sql/queries/predicates), [sekvensnummer](/sql/relational-databases/sequence-numbers/sequence-numbers), [lagrade procedurer](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)och [variabler](/sql/t-sql/language-elements/variables-transact-sql).
- Databas funktioner – [Automatisk justering (schema framtvingande)](/sql/relational-databases/automatic-tuning/automatic-tuning), [ändrings spårning](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [databas sortering](/sql/relational-databases/collations/set-or-change-the-database-collation), [inneslutna databaser](/sql/relational-databases/databases/contained-databases), [inneslutna användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [data komprimering](/sql/relational-databases/data-compression/data-compression), [databas konfigurations inställningar](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [online index åtgärder](/sql/relational-databases/indexes/perform-index-operations-online), [partitionering](/sql/relational-databases/partitions/partitioned-tables-and-indexes)och [temporala tabeller](/sql/relational-databases/tables/temporal-tables) ([se komma igång-guide](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Säkerhetsfunktioner – [program roller](/sql/relational-databases/security/authentication-access/application-roles), [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking) ([Kom igång med SQL Database dynamisk datamaskering med Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)
- Funktioner för flera modeller – [diagram bearbetning](/sql/relational-databases/graphs/sql-graph-overview), [JSON-data](/sql/relational-databases/json/json-data-sql-server), [OpenXML](/sql/t-sql/functions/openxml-transact-sql), [spatial](/sql/relational-databases/spatial/spatial-data-sql-server), [openjson](/sql/t-sql/functions/openjson-transact-sql)och [XML-index](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Funktioner i Azure Arc-aktiverad SQL-hanterad instans

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS hög tillgänglighet  
  
|Funktion|Azure Arc-aktiverad SQL-hanterad instans|
|-------------|----------------|
|Loggöverföring|Ja| 
|Säkerhets kopierings komprimering|Ja|
|Databas ögonblicks bild|Ja|
|Always on-kluster instans<sup>1</sup>| Inte tillämpligt. Liknande funktioner är tillgängliga |
|Always on-tillgänglighetsgrupper<sup>2</sup>|HA-funktioner planeras.|
|Grundläggande tillgänglighets grupper <sup>2</sup>|HA-funktioner planeras.|
|Lägsta tillgänglighets grupp för replik incheckning <sup>2</sup>|HA-funktioner planeras.|
|Klusterfri tillgänglighetsgrupp|Ja|
|Online-sida och fil återställning|Ja|
|Online-indexering|Ja|
|Återuppbyggnad av återställnings Bart online-index|Ja|
|Schema ändring online|Ja|
|Snabb återställning|Ja|
|Speglade säkerhets kopior|Ja|
|Frekvent tillägg av minne och CPU|Ja|
|Krypterad säkerhetskopiering|Ja|
|Hybrid säkerhets kopiering till Azure (säkerhets kopiering till URL)|Ja|

<sup>1</sup> i scenariot där det finns Pod-problem startar en ny SQL-hanterad instans och återansluter till den permanenta volymen som innehåller dina data. [Lär dig mer om Kubernetes-beständiga volymer här](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> framtida versioner kommer att tillhandahålla AG-funktioner 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS-skalbarhet och prestanda  

| Funktion | Azure Arc-aktiverad SQL-hanterad instans |
|--|--|
| - | Ja |
| Binärfiler för stora objekt i klustrade columnstore-index | Ja |
| Återuppbyggnad av ogrupperade columnstore-index för online | Ja |
| Minnesintern OLTP | Ja |
| Beständigt huvud minne | Ja |
| Tabell-och index partitionering | Ja |
| Datakomprimering | Ja |
| Resource Governor | Ja |
| Parallellitet med partitionerad tabell | Ja |
| NUMA-medveten och stor sid minne och buffert mat ris tilldelning | Ja |
| Styrning av IO-resurser | Ja |
| Fördröjd hållbarhet | Ja |
| Automatisk justering | Ja |
| Anpassningsbara kopplingar i batch-läge | Ja |
| Feedback om minnes beviljande i batch-läge | Ja |
| Överlagrad körning för tabell värdes funktioner med flera instruktioner | Ja |
| Förbättringar av Mass infogning | Ja |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS-säkerhet

| Funktion | Azure Arc-aktiverad SQL-hanterad instans |
|--|--|
| Säkerhet på radnivå | Ja |
| Always Encrypted | Ja |
| Always Encrypted med säkra enklaver | Inga |
| Dynamisk datamaskning | Ja |
| Grundläggande granskning | Ja |
| Detaljerad granskning | Ja |
| Transparent databas kryptering | Ja |
| Användardefinierade roller | Ja |
| Inneslutna databaser | Ja |
| Kryptering för säkerhets kopior | Ja |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS-hanterbarhet  

| Funktion | Azure Arc-aktiverad SQL-hanterad instans |
|--|--|
| Dedikerad administratörs anslutning | Ja |
| Stöd för PowerShell-skript | Ja |
| Stöd för åtgärder på data skikts program komponent – extrahera, distribuera, uppgradera och ta bort | Ja |
| Princip automatisering (kontrol lera schemat och ändra) | Ja |
| Insamling av prestanda data | Ja |
| Standard prestanda rapporter | Ja |
| Planera guider och planera frysning för plan guider | Ja |
| Direkt fråga för indexerade vyer (med NOEXPAND-tips) | Ja |
| Underhåll av automatiska indexerade vyer | Ja |
| Distribuerade partitionerade vyer | Ja |
| Parallellt indexerade åtgärder | Ja |
| Automatisk användning av indexerad vy efter fråga optimering | Ja |
| Parallell konsekvens kontroll | Ja |

### <a name="programmability"></a><a name="Programmability"></a> Programmering  

| Funktion | Azure Arc-aktiverad SQL-hanterad instans |
|--|--|
| JSON | Ja |
| Query Store | Ja |  |
| Temporal | Ja |  |
| Inbyggt XML-stöd | Ja |  |
| XML-indexering | Ja |  |
| Sammanfoga & UPSERT-funktioner | Ja |  |
| Datum-och tids data typer | Ja |  |
| Stöd för internationalisering | Ja |  |
| Full text och semantisk sökning | Inga |
| Specifikation av språk i fråga | Ja |  |
| Service Broker (meddelande hantering) | Ja |  |
| Transact-SQL-slutpunkter | Ja |  |
| Graph | Ja |  |
| Machine Learning Services | Inga |  |
| PolyBase | Inga |


### <a name="tools"></a>Verktyg

Azure Arc-aktiverad SQL-hanterad instans har stöd för olika data verktyg som kan hjälpa dig att hantera dina data.

| **Verktyg** | Azure Arc-aktiverad SQL-hanterad instans|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | Inga |
| Azure CLI | Inga |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Ja |
| Azure PowerShell | Ja |
| [BACPAC-fil (exportera)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja |
| [BACPAC-fil (importera)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Ja |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Ja |
| [SQL Server-profilerare](/sql/tools/sql-server-profiler/sql-server-profiler) | Ja |

<sup>1</sup> Azure Portal används bara för att Visa Azure Arc-aktiverade SQL-hanterade instanser i skrivskyddat läge under för hands versionen.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Funktioner som inte stöds & tjänster

Följande funktioner och tjänster är inte tillgängliga för Azure Arc-aktiverade SQL-hanterade instanser. Stöd för de här funktionerna kommer att bli allt mer aktivt över tid.

| Område | Funktion eller tjänst som inte stöds |
|-----|-----|
| **Databasmotor** | Sammanfoga replikering |
| &nbsp; | Sträck ut databas |
| &nbsp; | Distribuerad fråga med anslutningar från tredje part |
| &nbsp; | Länkade servrar till andra data källor än SQL Server och Azure SQL-produkter |
| &nbsp; | Systemets utökade lagrade procedurer (XP_CMDSHELL osv.) |
| &nbsp; | FileTable, FILESTREAM |
| &nbsp; | CLR-sammansättningar med EXTERNAL_ACCESS eller osäker behörighets uppsättning |
| &nbsp; | Buffertpooltillägget |
| **SQL Server Agent** |  Under system: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Aviseringar |
| &nbsp; | Hanterad säkerhetskopiering |
| **Hög tillgänglighet** | Databasspegling  |
| **Säkerhet** | Utöknings bar nyckel hantering |
| &nbsp; | AD-autentisering för länkade servrar | 
| &nbsp; | AD-autentisering för tillgänglighets grupper (AGs) | 