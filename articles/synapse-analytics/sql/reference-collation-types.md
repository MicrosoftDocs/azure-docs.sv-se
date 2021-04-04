---
title: Sorterings stöd
description: Sorterings typer stöd för Synapse SQL i Azure Synapse Analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93311928"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Stöd för databas sortering för Synapse SQL i Azure Synapse Analytics 

Sorteringarna anger språk, tecken tabell, sorterings ordning och tecken känslighets regler för teckenbaserade data typer. När du har valt alla kolumner och uttryck som kräver sorterings information ärver du den valda sorteringen från databas inställningen. Standard arvet kan åsidosättas genom att uttryckligen ange en annan sortering för en tecken-baserad datatyp.

Du kan ändra standard databas sorteringen från Azure Portal när du skapar en ny dedikerad SQL-adresspool. Den här funktionen gör det ännu enklare att skapa en ny databas med en av de 3800 databas sorteringar som stöds.

Du kan ange den standardinställda SQL-poolens databas sortering vid skapande tillfället med instruktionen CREATE DATABASE.

## <a name="change-collation"></a>Ändrings sortering
Om du vill ändra standard sorteringen för dedikerad SQL-adresspool, uppdatera till sorterings fältet i etablerings upplevelsen. Om du till exempel vill ändra standard sorteringen till Skift läges känslig skulle du byta namn på sorteringen från SQL_Latin1_General_CP1_CI_AS till SQL_Latin1_General_CP1_CS_AS. 

Om du vill ändra standard sorteringen för en server lös SQL-adresspool kan du använda ALTER DATABASE-instruktionen.

## <a name="list-of-unsupported-collation-types"></a>Lista över sorterings typer som inte stöds
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Dessutom stöder dedikerad SQL-pool inte följande sorterings typer:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Kontrol lera den aktuella sorteringen
Om du vill kontrol lera den aktuella sorteringen för databasen kan du köra följande T-SQL-kodfragment:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
När den skickade sorteringen som egenskaps parameter returnerar funktionen DatabasePropertyEx den aktuella sorteringen för den angivna databasen. Du kan lära dig mer om DatabasePropertyEx-funktionen på MSDN.

## <a name="next-steps"></a>Nästa steg

Mer information om metod tips för dedikerad SQL-pool och Server lös SQL-pool finns i följande artiklar:

- [Metod tips för dedikerad SQL-pool](best-practices-sql-pool.md)
- [Metod tips för Server lös SQL-pool](best-practices-sql-on-demand.md)


