---
title: Status för databasmigreringsscenario
titleSuffix: Azure Database Migration Service
description: Lär dig mer om status för de migreringsscenarier som stöds av Azure Database Migration Service.
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
ms.openlocfilehash: f06c1a055b2f08259bf5318afe3c969d763bbbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818628"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status för migreringsscenarier som stöds av Azure Database Migration Service

Azure Database Migration Service har utformats för att stödja olika migreringsscenarier (käll-/målpar) för både offlinemigrering (en-gång) och onlinemigrering (kontinuerlig synkronisering). Scenariotäckningen som tillhandahålls av Azure Database Migration Service utökas med tiden. Nya scenarier läggs till regelbundet. Den här artikeln identifierar migreringsscenarier som för närvarande stöds av Azure Database Migration Service och status (privat förhandsversion, offentlig förhandsversion eller allmän tillgänglighet) för varje scenario.

## <a name="offline-versus-online-migrations"></a>Offlinemigrering jämfört med onlinemigrering

Med Azure Database Migration Service kan du göra en offline- eller onlinemigrering. Med  offlinemigrering börjar programmets stilleståndstid samtidigt som migreringen startar. Om du vill begränsa stilleståndstiden till den tid som krävs för att gå över till den nya miljön när migreringen är klar använder du en *onlinemigrering.* Vi rekommenderar att du testar en offlinemigrering för att avgöra om driftstoppet är acceptabelt. Om inte, gör du en onlinemigrering.

## <a name="migration-scenario-status"></a>Status för migreringsscenario

Statusen för migreringsscenarier som stöds Azure Database Migration Service varierar beroende på tid. I allmänhet släpps scenarier först i den **privata förhandsversionen**. Efter den privata förhandsversionen ändras scenariostatusen till **offentlig förhandsversion**. Azure Database Migration Service kan prova migreringsscenarier i offentlig förhandsversion direkt från användargränssnittet. Ingen registrering krävs.  Migreringsscenarier i den offentliga förhandsversionen kanske inte är tillgängliga i alla regioner och kan genomgå ytterligare ändringar innan den slutliga versionen släpps. Efter den offentliga förhandsversionen ändras scenariostatusen till **allmänt tillgänglig**. Allmän tillgänglighet (GA) är den slutliga lanseringsstatusen och funktionen är fullständig och tillgänglig för alla användare.

## <a name="migration-scenario-support"></a>Stöd för migreringsscenario

Följande tabeller visar vilka migreringsscenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som anges som stöds nedan inte visas i användargränssnittet kontaktar du aliaset [Fråga Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) om du vill ha mer information.

> [!IMPORTANT]
> Om du vill visa alla scenarier som stöds av Azure Database Migration Service i privat förhandsversion, se [DMS-förhandsgranskningswebbplatsen](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Migreringsstöd offline (en gång)

I följande tabell visas Azure Database Migration Service stöd för offlinemigrering.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Virtuell Azure SQL-dator** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL | ✔ |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL – enskild server** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL – Hyperskala (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Migreringsstöd online (kontinuerlig synkronisering)

I följande tabell visas Azure Database Migration Service stöd för onlinemigrering.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Virtuell Azure SQL-dator** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS MySQL | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB for PostgreSQL – enskild server** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Azure DB for PostgreSQL – enskild server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Offentlig förhandsversion (kommer att bli inaktuell efter den 1 maj 2021) |
| **Azure DB for PostgreSQL – Hyperskala (Citus)** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS PostgreSQL | ✔ | Allmän tillgänglighet (GA) |

> [!IMPORTANT]
> Migreringsscenariot "Oracle Azure Database for PostgreSQL" (för närvarande i förhandsversion) kommer inte längre att vara tillgängligt efter den 1 maj 2021. Vi fortsätter att ge stöd via alternativa verktyg (till exempel Ora2pg) och ger den bästa migreringsupplevelsen för migrering från Oracle till PostgreSQL. Metodtips för migrering finns i [Migreringsguiden för Oracle till Azure Database for PostgreSQL](https://aka.ms/OracletoPGguide).


## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Azure Database Migration Service](dms-overview.md).
