---
title: Användardefinierade scheman i Synapse SQL
description: I avsnitten nedan hittar du olika tips för att använda användardefinierade SQL-scheman i T-SQL för att utveckla lösningar med Synapse SQL-funktionen i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451697"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Användardefinierade scheman i Synapse SQL

I avsnitten nedan hittar du olika tips för att använda användardefinierade scheman för T-SQL för att utveckla lösningar i Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Scheman för program gränser

Traditionell analys arkitektur använder ofta separata databaser för att skapa program gränser utifrån arbets belastning, domän eller säkerhet. En traditionell SQL Server analys infrastruktur kan till exempel omfatta en mellanlagringsdatabas, en Analytics-databas och data mart databaser. I den här topologin fungerar varje databas som arbets belastning och säkerhets gränser i arkitekturen.

I stället kör Synapse SQL hela analys arbets belastningen i en databas. Kopplingar mellan databaser är inte tillåtna. Synapse SQL förväntar sig att alla tabeller som används av lagret lagras i en databas.

> [!NOTE]
> Dedikerade SQL-pooler stöder inte kors databas frågor av någon typ. Därför måste analys implementeringar som använder det här mönstret ändras. SQL-poolen utan Server stöder kors databas frågor.

## <a name="user-defined-schema-recommendations"></a>Användar definierade schema rekommendationer

Här följer rekommendationer för konsolidering av arbets belastningar, säkerhet, domän och funktionella gränser med hjälp av användardefinierade scheman:

- Använd en databas för att köra hela analys arbets belastningen.
- Konsolidera din befintliga analys miljö för att använda en databas.
- Använd **användardefinierade scheman** för att tillhandahålla den gräns som tidigare implementerats med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du en ren Skriv platta. Använd det gamla databas namnet som bas för dina användardefinierade scheman i Synapse SQL-databasen.

Om scheman redan har använts har du några alternativ:

- Ta bort de gamla schema namnen och börja om på nytt
- Behåll gamla schema namn genom att vänta det gamla schema namnet till tabell namnet
- Behåll gamla schema namn genom att implementera vyer över tabellen i ett extra schema, vilket skapar den gamla schema strukturen igen.

> [!NOTE]
> Vid första inspektion kan alternativ 3 verka som det mest tilltalande valet. Vyer är skrivskyddade i Synapse SQL. Data-eller tabell ändringar måste utföras mot bas tabellen. Alternativ 3 introducerar också ett lager med vyer i systemet. Du kanske vill ge ytterligare en tanke på att du redan använder vyer i din arkitektur.
> 
> 

### <a name="examples"></a>Exempel

Implementera användardefinierade scheman baserat på databas namn.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behåll gamla schema namn genom att vänta dem i tabell namnet. Använd scheman för arbets belastnings gränser.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behåll gamla schema namn med hjälp av vyer.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Alla ändringar i schema strategin kräver en granskning av databasens säkerhets modell. I många fall kanske du kan förenkla säkerhets modellen genom att tilldela behörigheter på schema nivå.

Om mer detaljerade behörigheter krävs kan du använda databas roller. Mer information om databas roller finns i artikeln [Hantera databas roller och användare](../../analysis-services/analysis-services-database-users.md) .

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [Översikt över SYNAPSE SQL-utveckling](develop-overview.md).
