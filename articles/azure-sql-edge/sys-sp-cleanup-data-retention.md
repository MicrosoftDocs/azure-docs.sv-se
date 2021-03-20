---
title: sys.sp_cleanup_data_retention (Transact-SQL) – Azure SQL Edge
description: Lär dig mer om att använda sys.sp_cleanup_data_retention (Transact-SQL) i Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90941368"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Gäller för:** Azure SQL Edge

Utför rensning av föråldrade poster från tabeller som har aktiverade principer för data lagring. Mer information finns i [data kvarhållning](data-retention-overview.md). 

## <a name="syntax"></a>Syntax 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argument  
`[ @schema_name = ] schema_name`    
 Är namnet på det ägande schema för tabellen där rensning måste utföras. *schema_name* är en obligatorisk parameter av typen **sysname**.
  
`[ @table_name = ] 'table_name'`    
 Är namnet på tabellen där rensnings åtgärden måste utföras. *table_name* är en obligatorisk parameter av typen **sysname**.

## <a name="output-parameter"></a>Utdataparameter  

`[ @rowcount = ] rowcount OUTPUT`   
 rowCount är en valfri UTDATAPARAMETER som representerar antalet rensningar av poster i tabellen. *rowCount* är int.
  
## <a name="permissions"></a>Behörigheter    
 Kräver db_owner behörigheter.

## <a name="next-steps"></a>Nästa steg
- [Data lagring och automatisk data rensning](data-retention-overview.md)
- [Hantera historiska data med bevarande princip](data-retention-cleanup.md) 
- [Aktivera och inaktivera datakvarhållning](data-retention-enable-disable.md)
