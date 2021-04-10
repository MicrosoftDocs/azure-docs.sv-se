---
title: Matris för Azure Database Migration Service verktyg
description: Lär dig mer om tjänsterna och verktygen som är tillgängliga för att migrera databaser och stöd för olika faser av migreringsprocessen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: reference
ms.date: 03/03/2020
ms.openlocfilehash: 07cf2695f4d6256e717c7ce1a456d32b25c34ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643763"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Tillgängliga tjänster och verktyg för datamigreringsscenarier

Den här artikeln innehåller en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter.

Följande tabeller identifierar de tjänster och verktyg som du kan använda för att planera för datamigrering och för att slutföra de olika faserna.

> [!NOTE]
> I följande tabeller representerar objekt som är markerade med en asterisk (*) verktyg från tredje part.

## <a name="business-justification-phase"></a>Affärs justerings fas

| Källa | Mål | Undersöka<br/>Inventering | Mål och SKU<br/>rekommenderade | TCO/ROI och<br/>Affärs ärende |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL DB-MI | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Virtuell Azure SQL-dator | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure Synapse Analytics | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS-SQL | Azure SQL DB, MI, VM |  | [DMA](/sql/dma/dma-overview) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | Azure Synapse Analytics | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server | [Kart verktyg](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB för MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB för MySQL |  |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fas för migrering

| Källa | Mål | Åtkomst till AppData<br/>Skikt utvärdering | Databas<br/>Utvärdering | Prestanda<br/>Utvärdering |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB-MI | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtuell Azure SQL-dator | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS-SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB för MySQL |  |  |  |
| RDS MySQL | Azure DB för MySQL |  |  |  |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  |  |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | Azure SQL DB, MI, VM |  | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Migrerings fas

| Källa | Mål | Schema | Data<br/>Anslutningen | Data<br/>Onlinemallar |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [DMA](/sql/dma/dma-overview)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB-MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Virtuell Azure SQL-dator | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure Synapse Analytics |  |  |  |
| RDS-SQL | Azure SQL DB, MI, VM | [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure Synapse Analytics | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server | [Inspirer](https://www.ispirer.com/solutions) | [Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [Imanis-data *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) |
| MySQL | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB för MySQL | [MySQL-dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB för MySQL | [MySQL-dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Dumpning av PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Dumpning av PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM | [Inspirer](https://www.ispirer.com/solutions) | [Inspirer](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Fasen efter migreringen

| Källa | Mål | Optimera |
| --- | --- | --- |
| SQL Server | Azure SQL-databas | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB-MI | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtuell Azure SQL-dator | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics |  |
| RDS-SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | Azure Synapse Analytics |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | Azure DB för MySQL |  |
| RDS MySQL | Azure DB för MySQL |  |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Access | Azure SQL DB, MI, VM |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service](dms-overview.md).