---
title: Funktioner som stöds i Azure SQL Edge
description: Lär dig mer om de funktioner som stöds av Azure SQL Edge.
keywords: Introduktion till SQL Edge, vad är SQL Edge, SQL Edge-översikt
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392035"
---
# <a name="supported-features-of-azure-sql-edge"></a>Funktioner som stöds i Azure SQL Edge 

Azure SQL Edge bygger på den senaste versionen av SQL Database-motorn. Den har stöd för en delmängd av funktionerna som stöds i SQL Server 2019 på Linux, förutom vissa funktioner som för närvarande inte stöds eller är tillgängliga i SQL Server 2019 på Linux (eller i SQL Server i Windows).

En fullständig lista över de funktioner som stöds i SQL Server på Linux finns i [utgåvor och stödda funktioner i SQL Server 2019 i Linux](/sql/linux/sql-server-linux-editions-and-components-2019). För utgåvor och stödda funktioner i SQL Server i Windows, se [utgåvor och stödda funktioner i SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-utgåvor

Azure SQL Edge är tillgängligt med två olika utgåvor eller program varu planer. Dessa versioner har identiska funktions uppsättningar och skiljer sig bara åt när det gäller användnings rättigheter och mängden minne och kärnor som de har åtkomst till i värd systemet.

   |**Planera**  |**Beskrivning**  |
   |---------|---------|
   |Azure SQL Edge-utvecklare  |  Endast för utveckling. Varje Azure SQL Edge Developer-behållare är begränsad till upp till 4 kärnor och 32 GB minne.  |
   |Azure SQL Edge    |  För produktion. Varje Azure SQL Edge-behållare är begränsad till upp till 8 kärnor och 64 GB minne.  |

## <a name="operating-system"></a>Operativsystem

Azure SQL Edge-behållare baseras på Ubuntu 18,04, och det är därför bara stöd för att köra på Docker-värdar som kör antingen Ubuntu 18,04 LTS (rekommenderas) eller Ubuntu 20,04 LTS. Det är möjligt att köra Azure SQL Edge-behållare på andra operativ system värdar, till exempel kan köras på andra distributioner av Linux eller Windows (med Docker CE eller Docker EE), men Microsoft rekommenderar inte att du gör detta, eftersom den här konfigurationen inte kan testas grundligt.

Den rekommenderade konfigurationen för att köra Azure SQL Edge i Windows är att konfigurera en virtuell Ubuntu-dator på Windows-värden och sedan köra Azure SQL Edge i den virtuella Linux-datorn.

Det rekommenderade och fil system som stöds för Azure SQL Edge är EXT4 och XFS. Om beständiga volymer används för att återställa lagringen i Azure SQL Edge-databasen måste det underliggande värd fil systemet vara EXT4 och XFS.

## <a name="hardware-support"></a>Maskinvarustöd

Azure SQL Edge kräver en 64-bitars processor (antingen x64 eller ARM64), med minst en processor och ett GB RAM-minne på värden. Även om start minnes storleken för Azure SQL Edge är nära 450MB, krävs ytterligare minne för andra IoT Edge moduler eller processer som körs på gräns enheten. De faktiska minnes-och processor kraven för Azure SQL Edge varierar beroende på komplexiteten för arbets belastningen och mängden data som bearbetas. När du väljer maskin vara för din lösning rekommenderar Microsoft att du kör omfattande prestandatester för att säkerställa att de nödvändiga prestanda egenskaperna för din lösning är uppfyllda.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-komponenter

Azure SQL Edge stöder bara databas motorn. Det omfattar inte stöd för andra komponenter som är tillgängliga med SQL Server 2019 på Windows eller med SQL Server 2019 i Linux. Mer specifikt stöder inte Azure SQL Edge SQL Server-komponenter som Analysis Services, repor ting Services, Integration Services, Master Data Services, Machine Learning Services (in-Database) och Machine Learning Server (fristående).

## <a name="supported-features"></a>Funktioner som stöds

Förutom stöd för en delmängd av funktionerna i SQL Server på Linux innehåller Azure SQL Edge stöd för följande nya funktioner: 

- SQL streaming, som baseras på samma motor som har Azure Stream Analytics, tillhandahåller funktioner för strömning i real tid i Azure SQL Edge. 
- T-SQL-funktionen anropar `Date_Bucket` för Time-Series data analys.
- Machine Learning-funktioner via ONNX-körningsmiljön som ingår i SQL-motorn.

## <a name="unsupported-features"></a>Funktioner som inte stöds

I följande lista finns SQL Server 2019 på Linux-funktioner som inte stöds för närvarande i Azure SQL Edge.

| Område | Funktion eller tjänst som inte stöds |
|-----|-----|
| **Databas design** | InMemory OLTP och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering. |
| &nbsp; | `HierarchyID` datatyp och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering. |
| &nbsp; | `Spatial` datatyp och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering. |
| &nbsp; | Sträck ut DB och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering. |
| &nbsp; | Full text index och Sök, och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering.|
| &nbsp; | `FileTable`, `FILESTREAM` , och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering.|
| **Databas motor** | Replikering. Observera att du kan konfigurera Azure SQL Edge som en push-prenumerant av en replikeringstopologi. |
| &nbsp; | PolyBase. Observera att du kan konfigurera Azure SQL Edge som mål för externa tabeller i PolyBase. |
| &nbsp; | Språk utöknings Bart med Java och Spark. |
| &nbsp; | Active Directory-integrering. |
| &nbsp; | Automatisk krympning av databas. Egenskapen automatisk krympning för en databas kan anges med hjälp av `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` kommandot, men ändringen har ingen påverkan. Den automatiska krympningen kommer inte att köras mot databasen. Användare kan fortfarande krympa databasfilerna med hjälp av DBCC-kommandon. |
| &nbsp; | Databas ögonblicks bilder. |
| &nbsp; | Stöd för beständigt minne. |
| &nbsp; | Microsoft Distributed Transaction Coordinator. |
| &nbsp; | Styrning av resurs styrning och IO-resurser. |
| &nbsp; | Buffertpooltillägget. |
| &nbsp; | Distribuerad fråga med anslutningar från tredje part. |
| &nbsp; | Länkade servrar. |
| &nbsp; | Systemets utökade lagrade procedurer (till exempel `XP_CMDSHELL` ). |
| &nbsp; | CLR-sammansättningar och relaterade DDL-kommandon och Transact-SQL-funktioner, katalogfiler och vyer för dynamisk hantering. |
| &nbsp; | CLR-relaterade T-SQL-funktioner, till exempel,, `ASSEMBLYPROPERTY` `FORMAT` `PARSE` och `TRY_PARSE` . |
| &nbsp; | CLR-beroende datum-och tids katalogs visningar, Functions och frågeuttryck. |
| &nbsp; | Buffertpooltillägget. |
| &nbsp; | Database mail. |
| &nbsp; | Service Broker |
| &nbsp; | Principbaserad hantering |
| &nbsp; | Hanterings informations lager |
| &nbsp; | Inneslutna databaser |
| **SQL Server Agent** |  Under system: CmdExec, PowerShell, Queue Reader, SSIS, SSAS och SSRS. |
| &nbsp; | Aviseringar. |
| &nbsp; | Hanterad säkerhets kopiering. |
| **Hög tillgänglighet** | Always on-tillgänglighetsgrupper.  |
| &nbsp; | Grundläggande tillgänglighets grupper. |
| &nbsp; | Alltid på kluster instansen för redundans. |
| &nbsp; | Databas spegling. |
| &nbsp; | Lägg till minne och processor frekvent. |
| **Säkerhet** | Utöknings bar nyckel hantering. |
| &nbsp; | Active Directory-integrering.|
| &nbsp; | Stöd för säker enclaves.|
| **Tjänster** | SQL Server Browser. |
| &nbsp; | Machine Learning via R och python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Repor ting Services. |
| &nbsp; | Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Hanterbarhet** | SQL Server-verktyg kontroll punkt. |

## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure SQL Edge](deploy-portal.md)
- [Konfigurera Azure SQL Edge](configure.md)
- [Ansluta till en instans av Azure SQL Edge med SQL Server klient verktyg](connect.md)
- [Säkerhetskopiera och återställa databaser i Azure SQL Edge](backup-restore.md)