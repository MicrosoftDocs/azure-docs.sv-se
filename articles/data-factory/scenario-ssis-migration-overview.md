---
title: Migrera lokala SQL Server Integration Services-arbetsbelastningar (SSIS) till SSIS i Azure Data Factory (ADF)
description: Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373614"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

När du migrerar dina databas arbets belastningar från SQL Server lokalt till Azure Database Services, nämligen Azure SQL Database eller Azure SQL-hanterad instans, kommer ETL-arbetsbelastningar på SQL Server Integration Services (SSIS) som en av de primära mervärdes tjänsterna också att behöva migreras.

Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) stöder körning av SSIS-paket. När Azure-SSIS IR har skapats kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) och kommando rads verktyg, till exempel dtinstall/dtutil/Dtexec, för att distribuera och köra dina paket i Azure. Mer information finns i [Översikt över Azure SSIS-och-Shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

I den här artikeln beskrivs migreringsprocessen för dina ETL-arbetsbelastningar från lokala SSIS till SSIS i ADF. Migreringsprocessen består av två faser: **utvärdering** och **migrering**.

## <a name="assessment"></a>Utvärdering

För att upprätta en komplett migrations plan kan en grundlig utvärdering hjälpa till att identifiera problem med SSIS-paket som förhindrar en lyckad migrering.

Data Migration Assistant (DMA) är ett fritt nedladdnings Bart verktyg för det här syftet som kan installeras och köras lokalt. DMA-utvärderings projekt av typen **integrerings tjänster** kan skapas för att utvärdera SSIS-paket i batchar och identifiera kompatibilitetsproblem som presenteras i följande kategorier:

- Migration Blocker: kompatibilitetsproblem som blockerar migreringens käll paket för att köras på Azure-SSIS IR. DMA ger vägledning som hjälper dig att åtgärda problemen.

- Informativa problem: delvis stödd eller inaktuell funktion som används i käll paket. DMA tillhandahåller en omfattande uppsättning rekommendationer, alternativa metoder som är tillgängliga i Azure och åtgärder för att lösa problemet.

### <a name="four-storage-types-for-ssis-packages"></a>Fyra lagrings typer för SSIS-paket

- SSIS-katalog (SSISDB). Introducerades med SQL Server 2012 och innehåller en uppsättning lagrade procedurer, vyer och tabell värdes funktioner som används för att arbeta med SSIS-projekt/-paket.
- Fil system.
- SQL Server System databas (MSDB).
- SSIS-paket arkiv. Ett paket hanterings lager ovanpå två under typer:
  - MSDB, som är en system databas i SQL Server som används för att lagra SSIS-paket.
  - Hanterat fil system, som är en bestämd mapp i SQL Server installations Sök väg som används för att lagra SSIS-paket.

DMA stöder för närvarande batch-utvärderingen av paket som lagras i **fil systemet**, i **paket lagret** och **SSIS-katalogen** sedan **DMA version v 5.0**.

Hämta [DMA](/sql/dma/dma-overview)och [genomför din paket utvärdering med den](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrering

De steg som krävs för att migrera **SSIS-paket** och **SQL Server Agent jobb** som schemalägger SSIS-programpaket kan variera beroende på [lagrings typerna](#four-storage-types-for-ssis-packages) för käll SSIS paket och migreringens mål för databas arbets belastningar. Det finns två scenarier:

- [**Azure SQL-hanterad instans** som databas arbets belastnings mål](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** som databasens arbets belastnings mål](#azure-sql-database-as-database-workload-destination)

Det är också ett praktiskt sätt att använda [SSIS DevOps-verktyg](/sql/integration-services/devops/ssis-devops-overview)för att distribuera om batch-paket till migrerings målet.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL-hanterad instans** som databas arbets belastnings mål

| **Paket lagrings typ** |Så här gruppmigrerar du SSIS-paket|Så här batch-migrerar du SSIS-jobb|
|-|-|-|
|SSISDB|[Migrera **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrera SSIS-jobb till Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Filsystem|Distribuera om dem till fil resurser/Azure Files via dtinstall/dtutil/manuell kopia eller för att hålla i fil system för åtkomst via VNet/egen värd-IR. Mer information finns i [dtutil-verktyget](/sql/integration-services/dtutil-utility).|<li>[Migrera SSIS-jobb till Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrera med [SSIS för migrering av jobb i SSMS](how-to-migrate-ssis-job-ssms.md) <li>Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportera dem till fil system/fil resurser/Azure Files via SSMS/dtutil. Mer information finns i [Exportera SSIS-paket](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket arkiv|Exportera dem till paket lagring via SSMS/dtutil eller distribuera om dem till paket lager via dtinstall/dtutil/manuell kopiering. Mer information finns i [Hantera paket med Azure-SSIS integration runtime Package Store](azure-ssis-integration-runtime-package-store.md).|<li>[Migrera SSIS-jobb till Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** som databasens arbets belastnings mål

| **Paket lagrings typ** |Så här gruppmigrerar du SSIS-paket|Så här kör du migrering av jobb|
|-|-|-|
|SSISDB|Distribuera om till Azure-SSISDB via SSDT/SSMS. Mer information finns i [distribuera SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Filsystem|Distribuera om dem till fil resurser/Azure Files via dtinstall/dtutil/manuell kopia eller för att hålla i fil system för åtkomst via VNet/egen värd-IR. Mer information finns i [dtutil-verktyget](/sql/integration-services/dtutil-utility).|<li> Migrera med [SSIS för migrering av jobb i SSMS](how-to-migrate-ssis-job-ssms.md) <li> Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportera dem till fil system/fil resurser/Azure Files via SSMS/dtutil. Mer information finns i [Exportera SSIS-paket](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket arkiv|Exportera dem till fil system/fil resurser/Azure Files via SSMS/dtutil eller distribuera om dem till fil resurser/Azure Files via dtinstall/dtutil/manuell kopia eller behåll dem i fil system för att få åtkomst via VNet/egen värd-IR. Mer information finns i dtutil-verktyget. Mer information finns i [dtutil-verktyget](/sql/integration-services/dtutil-utility).|Omvandla dem till ADF-pipeline/-aktiviteter/-utlösare via scripts/SSMS/ADF Portal. Mer information finns i [schemaläggnings funktionen för SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](./introduction.md)
- [Database Migration Assistant](/sql/dma/dma-overview)
- [Lyft och skifta SSIS-arbetsbelastningar till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps-verktyg](/sql/integration-services/devops/ssis-devops-overview)
- [Migrera SSIS-paket till en hanterad Azure SQL-instans](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Distribuera om paket till Azure SQL Database](../dms/how-to-migrate-ssis-packages.md)

- [Lokal data åtkomst från Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Anpassa installations programmet för en Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Komma åt datalager och filresurser med Windows-autentisering från SSIS-paket i Azure](ssis-azure-connect-with-windows-auth.md)
- [Använd hanterad identitets autentisering](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Använda Azure Key Vault](store-credentials-in-key-vault.md)
- [Konfigurera Azure-SSIS Integration Runtime för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)
- [Så startar och stoppar du Azure-SSIS Integration Runtime enligt ett schema](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Nästa steg

- [Verifiera SSIS-paket som har distribuerats till Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Köra SSIS-paket som distribuerats i Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Övervaka Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Schemalägg SSIS-paket körningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)