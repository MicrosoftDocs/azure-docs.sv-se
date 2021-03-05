---
title: Status för databasmigreringsscenario
titleSuffix: Azure Database Migration Service
description: Läs om status för de migrerings scenarier som stöds av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: d51de526205e8ef663dcfd682ec5858e4bc2f645
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183989"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status för migrerings scenarier som stöds av Azure Database Migration Service

Azure Database Migration Service har utformats för att stödja olika migreringar (käll-/mål par) för migrering av både offline-(eng-Time) och online (kontinuerlig synkronisering). Scenario täckningen som tillhandahålls av Azure Database Migration Service utökas med tiden. Nya scenarier läggs till regelbundet. I den här artikeln identifieras scenarier för migrering som för närvarande stöds av Azure Database Migration Service och status (privat för hands version, offentlig för hands version eller allmän tillgänglighet) för varje scenario.

## <a name="offline-versus-online-migrations"></a>Offline kontra online-migreringar

Med Azure Database Migration Service kan du göra en offline-eller en online-migrering. Vid migrering *offline* påbörjas avbrotts tiden för programmet samtidigt som migreringen startar. Använd en *online* -migrering om du vill begränsa stillestånds tiden till den tid som krävs för att skära över till den nya miljön när migreringen är klar. Vi rekommenderar att du testar en offline-migrering för att avgöra om avbrotts tiden är acceptabel. om inte, gör du en online-migrering.

## <a name="migration-scenario-status"></a>Status för migrerings scenario

Status för migrerings scenarier som stöds av Azure Database Migration Service varierar med tiden. I allmänhet släpps scenarierna först i **privat förhands granskning**. Delta i privat förhands granskning kräver att kunder skickar in en utnämning via [DMS Preview-webbplatsen](https://aka.ms/dms-preview). Efter privat förhands granskning ändras scenario statusen till **offentlig för hands version**. Azure Database Migration Service användare kan testa scenarier för migrering i offentlig för hands version direkt från användar gränssnittet. Ingen registrering krävs.  Migrerings scenarier i offentlig för hands version kanske däremot inte är tillgängliga i alla regioner och kan genomgå ytterligare ändringar innan den slutliga versionen. Efter den offentliga för hands versionen ändras scenario statusen till **allmänt tillgänglig**. Allmän tillgänglighet (GA) är den slutliga versions statusen och funktionen är fullständig och tillgänglig för alla användare.

## <a name="migration-scenario-support"></a>Stöd för migrerings scenario

Följande tabeller visar vilka migrerings scenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som anges som stöds nedan inte visas i användar gränssnittet kan du kontakta aliaset i [fråga Azure Database-migreringen](mailto:AskAzureDatabaseMigrations@service.microsoft.com) om du vill ha mer information.

> [!IMPORTANT]
> Om du vill visa alla scenarier som för närvarande stöds av Azure Database Migration Service i privat för hands version, se [DMS Preview-webbplatsen](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Stöd för migrering offline (eng ång slö läge)

I följande tabell visas Azure Database Migration Service stöd för offline-migrering.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB-MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL | X |  |
|   | Oracle | X |   |
| **Virtuell Azure SQL-dator** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL | X |   |
|   | RDS MySQL | X |   |
| **Azure DB för PostgreSQL – enskild server** | PostgreSQL | X |
|  | RDS-PostgreSQL | X |   |
| **Azure DB för PostgreSQL – storskalig (citus)** | PostgreSQL | X |
|  | RDS-PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Stöd för migrering online (kontinuerlig synkronisering)

I följande tabell visas Azure Database Migration Service stöd för online-migreringar.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | X | Allmän tillgänglighet (GA) |
|   | RDS-SQL | X | Allmän tillgänglighet (GA) |
|   | Oracle | X |  |
| **Azure SQL DB-MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL | X | Allmän tillgänglighet (GA) |
|   | Oracle | X |  |
| **Virtuell Azure SQL-dator** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS MySQL | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för PostgreSQL – enskild server** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Azure DB för PostgreSQL – enskild server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Offentlig för hands version |
| **Azure DB för PostgreSQL – storskalig (citus)** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-PostgreSQL | ✔ | Allmän tillgänglighet (GA) |


## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Azure Database migration service](dms-overview.md).
