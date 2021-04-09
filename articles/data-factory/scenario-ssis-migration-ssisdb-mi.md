---
title: SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål
description: SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373597"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du migrerar databas arbets belastningar från en SQL Server instans till en Azure SQL-hanterad instans, bör du vara bekant med [Azure Data Migration service](../dms/dms-overview.md)(DMS) och [nätverkstopologier för migrering av SQL-hanterade instanser med DMS](../dms/resource-network-topologies.md).

Den här artikeln fokuserar på migreringen av SSIS-paket (SQL Server integration service) som lagras i SSIS Catalog (SSISDB) och SQL Server Agent jobb som schemalägger körning av SSIS-paket.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrera SSIS-katalogen (SSISDB)

SSISDB-migrering kan göras med hjälp av DMS, enligt beskrivningen i artikeln: [MIGRERA SSIS-paket till SQL-hanterad instans](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-jobb till SQL-hanterad instans agent

SQL-hanterad instans har en inbyggd schemaläggare i första klass, precis som SQL Server Agent lokalt.  Du kan [köra SSIS-paket via Azure SQL-hanterad instans agent](how-to-invoke-ssis-package-managed-instance-agent.md).

Eftersom ett Migreringsverktyg för SSIS-jobb inte är tillgängligt än, måste de migreras från SQL Server Agent lokalt till SQL-hanterad instans agent via skript/manuell kopiering.

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Nätverkstopologier för migrering av SQL-hanterade instanser med DMS](../dms/resource-network-topologies.md)
- [Migrera SSIS-paket till en SQL-hanterad instans](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Nästa steg

- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Köra SSIS-paket som distribuerats i Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)