---
title: Skillnader mellan T-SQL-funktioner i Synapse SQL
description: Lista över Transact-SQL-funktioner som är tillgängliga i Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 55639a8d36000af00e23391f39e9e1c7364c8b71
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076436"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-funktioner som stöds i Azure Synapse SQL

Azure Synapse SQL är en stor data analys tjänst som gör det möjligt att fråga och analysera dina data med hjälp av T-SQL-språket. Du kan använda ANSI-kompatibel dialekt av SQL-språk som används på SQL Server och Azure SQL Database för data analys. 

Transact-SQL-språket används i SQL-poolen utan server och en dedikerad modell kan referera till olika objekt och har vissa skillnader i uppsättningen med funktioner som stöds. På den här sidan kan du hitta skillnader i Transact-SQL på hög nivå mellan förbruknings modeller av Synapse SQL.

## <a name="database-objects"></a>Databas objekt

Med förbruknings modeller i Synapse SQL kan du använda olika databas objekt. Jämförelsen av objekt typer som stöds visas i följande tabell:

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **Tabeller** | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Nej, Server lös modell kan bara fråga på externa data som placeras på [Azure Storage](#storage-options) |
| **Vyer** | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Vyer kan använda [frågespråket för frågespråk](#query-language) som är tillgängliga i dedikerad modell. | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Vyer kan använda [frågespråket](#query-language) som är tillgängliga i en server lös modell. |
| **Scheman** | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Temporära tabeller** | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Inga |
| **Procedurer** | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja |
| **Funktioner** | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Ja, endast infogade tabell värdes funktioner. |
| **Utlösare** | Inga | Inga |
| **Externa tabeller** | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Se [data format](#data-formats)som stöds. | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Se [data format](#data-formats)som stöds. |
| **Cachelagra frågor** | Ja, flera formulär (SSD-baserad cachelagring, minnes intern cachelagring, ResultSet). Dessutom stöds materialiserad vy | Inga |
| **Tabellvariabler** | [Nej](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), Använd temporära tabeller | Inga |
| **[Tabell distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Ja | Inga |
| **[Tabell index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Ja | Inga |
| **[Table-partitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Ja | Inga |
| **[Statistik](develop-tables-statistics.md)**            | Ja | Ja |
| **[Hantering av arbets belastning, resurs klasser och concurrency-kontroll](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ja    | Inga |
| **Kostnads kontroll** | Ja, med åtgärder för att skala upp och skala ned. | Ja, med hjälp [av Azure Portal eller T-SQL-proceduren](./data-processed.md#cost-control). |

## <a name="query-language"></a>Frågespråk

Frågespråket som används i Synapse SQL kan ha olika funktioner som stöds beroende på förbruknings modellen. Följande tabell beskriver de viktigaste skillnaderna i frågespråket i Transact-SQL-dialekter:

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **SELECT-uttryck** | Ja. Transact-SQL [-frågeuttryck för XML/för JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [match](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), offset/Fetch stöds inte. | Ja. Transact-SQL-frågeuttryck [för XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)-, [match](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true)-, [predict](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)-, GROUPNG-och frågetipset-satser stöds inte. |
| **Infoga instruktion** | Ja | Inga |
| **UPDATE-instruktion** | Ja | Inga |
| **TA bort instruktion** | Ja | Inga |
| **MERGE-instruktion** | Ja (för[hands version](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | Inga |
| **[Transaktioner](develop-transactions.md)** | Ja | Ja, tillämpligt på meta-data-objekt. |
| **[Etiketter](develop-label.md)** | Ja | Inga |
| **Data inläsning** | Ja. Det rekommenderade verktyget är [copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) -instruktion, men systemet stöder både BCP (Mass inläsning) och [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) för data inläsning. | Inga |
| **Dataexport** | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Typer** | Ja, alla Transact-SQL-typer förutom [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text och image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [spatial typer](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [SQL- \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alla Transact-SQL-typer förutom [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text och image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [spatial typer](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true)och tabell typ |
| **Frågor över flera databaser** | Inga | Ja, inklusive [use](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) -instruktionen. |
| **Inbyggda funktioner (analys)** | Ja, alla [analytiska](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), konverteringar, [datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logiska, [matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) funktioner i Transact-SQL, förutom [val](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) och [parsning](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alla [analytiska](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)konverteringar, konvertering, [datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logiska, [matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) funktioner i Transact-SQL. |
| **Inbyggda funktioner ([sträng](https://docs.microsoft.com/sql/t-sql/functions/string-functions-transact-sql))** | Ja. Alla funktioner för Transact-SQL- [sträng](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)och-sortering, förutom [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) och [Översätt](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja. Alla funktioner för Transact-SQL- [sträng](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)och-sortering. |
| **Inbyggda funktioner ([kryptografiskt](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql))** | Vissa | Inga |
| **Inbyggda tabell värdes funktioner** | Ja, [Transact-SQL rowset-funktioner](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), förutom [OpenXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, [Transact-SQL rowset-funktioner](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), förutom [OpenXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Aggregeringar** |  Inbyggda Transact-SQL-mängder, förutom [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) och [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Inbyggda Transact-SQL-mängder. |
| **Operatorer** | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) utom [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) och [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Kontroll av flöde** | Ja. Alla [Transact-SQL Control-of-Flow-uttryck](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) utom [Fortsätt](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [gå](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true)till, [returnera](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [använda](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true)och [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja. Alla [Transact-SQL Control-of-Flow-instruktioner](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) Välj fråga i `WHILE (...)` villkor |
| **DDL-instruktioner (skapa, ändra, släpp)** | Ja. Alla Transact-SQL DDL-uttryck som gäller för de objekt typer som stöds | Ja. Alla Transact-SQL DDL-uttryck som gäller för de objekt typer som stöds |

## <a name="security"></a>Säkerhet

Synapse SQL gör att du kan använda inbyggda säkerhetsfunktioner för att skydda dina data och kontrol lera åtkomst. I följande tabell jämförs skillnader i hög nivå mellan Synapse-förbruknings modeller i SQL.

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **Inloggningar** | Ej tillämpligt (endast inneslutna användare stöds i databaser) | Ja |
| **Användare** |  Ej tillämpligt (endast inneslutna användare stöds i databaser) | Ja |
| **[Inneslutna användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Ja. **Obs:** endast en Azure AD-användare kan vara obegränsad administratör | Inga |
| **SQL username/Password Authentication**| Ja | Ja |
| **Azure Active Directory (Azure AD)-autentisering**| Ja, Azure AD-användare | Ja, Azure AD-inloggningar och användare |
| **Lagrings Azure Active Directory (Azure AD) genom strömnings autentisering** | Ja | Ja |
| **Autentisering av SAS-token för lagring** | Inga | Ja, Använd [databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) i den [externa data källan](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) eller på instans nivåns [autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Autentisering av lagrings åtkomst nyckel** | Ja, Använd [databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) i den [externa data källan](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Inga |
| **Autentisering med [hanterad identitet](../security/synapse-workspace-managed-identity.md) för lagring** | Ja, med [hanterad tjänstidentitet autentiseringsuppgift](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | Ja, med hjälp av `Managed Identity` autentiseringsuppgifter. |
| **Autentisering av lagrings program identitet** | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Inga |
| **Behörigheter-objekt nivå** | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar på de system objekt som stöds |
| **Behörigheter – schema nivå** | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar i schemat | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar i schemat |
| **Behörigheter – [databas nivå](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Ja | Ja |
| **Behörigheter- [Server nivå](/sql/relational-databases/security/authentication-access/server-level-roles)** | Inga | Ja, sysadmin och andra Server roller stöds |
| **Behörigheter- [säkerhet på kolumn nivå](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Ja | Ja |
| **Roller/grupper** | Ja (databas omfattning) | Ja (både server-och databas omfattning) |
| **Funktioner för säkerhets &amp; identitet** | Vissa säkerhets funktioner och operatorer för Transact-SQL:,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` , `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Vissa Transact-SQL-säkerhetsfunktioner och-operatorer:,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` ,,,, `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SESSION_CONTEXT` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` och `REVERT` . Säkerhets funktioner kan inte användas för att fråga externa data (lagra resultatet i variabeln som kan användas i frågan).  |
| **DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER** | Ja | Ja |
| **SERVERNS BEGRÄNSADE AUTENTISERINGSUPPGIFTER** | Inga | Ja |
| **Säkerhet på radnivå** | [Ja](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Inga |
| **Transparent datakryptering (TDE)** | [Ja](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | Inga | 
| **Dataidentifiering och -klassificering** | [Ja](../../azure-sql/database/data-discovery-and-classification-overview.md) | Inga |
| **Sårbarhetsbedömning** | [Ja](../../azure-sql/database/sql-vulnerability-assessment.md) | Inga |
| **Advanced Threat Protection** | [Ja](../../azure-sql/database/threat-detection-overview.md)
| **Granskning** | [Ja](../../azure-sql/database/auditing-overview.md) | Inga |
| **[Brandväggsregler](../security/synapse-workspace-ip-firewall.md)**| Ja | Ja |
| **[Privat slut punkt](../security/synapse-workspace-managed-private-endpoints.md)**| Ja | Ja |

Dedikerad SQL-pool och SQL-pool utan server använder standard språket Transact-SQL för att fråga efter data. För detaljerade skillnader, se [språk referens för Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Verktyg

Du kan använda olika verktyg för att ansluta till Synapse SQL för att fråga efter data.

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **Synapse Studio** | Ja, SQL-skript | Ja, SQL-skript |
| **Power BI** | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Ja | Ja |
| **Azure Data Studio** | Ja | Ja, version 1.18.0 eller högre. SQL-skript och SQL-anteckningsböcker stöds. |
| **SQL Server Management Studio** | Ja | Ja, version 18,5 eller senare |

> [!NOTE]
> Du kan använda SSMS för att ansluta till en server lös SQL-pool och fråga. Den stöds delvis från och med version 18,5. du kan bara använda den för att ansluta och fråga.

De flesta av programmen använder standard Transact-SQL-språk och kan fråga både dedikerade och serverbaserade förbruknings modeller i Synapse SQL.

## <a name="storage-options"></a>Lagringsalternativ

Data som analyseras kan lagras på olika lagrings typer. I följande tabell visas alla tillgängliga lagrings alternativ:

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **Intern lagring** | Ja | Inga |
| **Azure Data Lake v2** | Ja | Ja |
| **Azure Blob Storage** | Ja | Ja |
| **Azure SQL (fjärran sluten)** | Inga | Inga |
| **Azure CosmosDB transaktions lagring** | Inga | Inga |
| **Azure CosmosDB Analytical Storage** | Inga | Ja, använda [Synapse-länk (för hands version)](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) ([offentlig för hands version](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json#limitations)) |
| **Apache Spark tabeller (i arbets ytan)** | Inga | PARQUET-tabeller med endast [metadata-synkronisering](develop-storage-files-spark-tables.md) |
| **Apache Spark tabeller (fjär)** | Inga | Inga |
| **Databricks-tabeller (fjär)** | Inga | Inga |

## <a name="data-formats"></a>Data format

Data som analyseras kan lagras i olika lagrings format. I följande tabell visas alla tillgängliga data format som kan analyseras:

|   | Dedikerad | Utan server |
| --- | --- | --- |
| **Avgränsade** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-single-csv-file.md) |
| **SKV** | Ja (avgränsare för flera tecken stöds inte) | [Ja](query-single-csv-file.md) |
| **Parquet** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-parquet-files.md), inklusive filer med [kapslade typer](query-parquet-nested-types.md) |
| **Hive-ORC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Inga |
| **Hive RC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Inga |
| **JSON** | Ja | [Ja](query-json-files.md) |
| **Avro** | Inga | Inga |
| **[Delta – sjö](https://delta.io/)** | Inga | Inga |
| **[COMMON data service](/common-data-model/)** | Inga | Nej |

## <a name="next-steps"></a>Nästa steg
Mer information om metod tips för dedikerad SQL-pool och Server lös SQL-pool finns i följande artiklar:

- [Metod tips för dedikerad SQL-pool](best-practices-dedicated-sql-pool.md)
- [Metod tips för Server lös SQL-pool](best-practices-serverless-sql-pool.md)
