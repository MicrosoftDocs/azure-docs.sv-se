---
title: sys.external_streams (Transact-SQL) – Azure SQL Edge
description: Lär dig mer om att använda sys.external_streams i Azure SQL Edge
keywords: sys.external_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900302"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Returnerar en rad för varje externt Stream-objekt som skapats inom databasens omfång.

|Kolumnnamn|Datatyp|Beskrivning|  
|-----------------|---------------|-----------------|
|**Namn**|**sysname**|Namn på data strömmen. Är unikt i databasen.|
|**object_id**|**int**|objekt identifierings nummer för Stream-objektet. Är unikt i databasen.|
|**principal_id**|**int**|ID för huvud kontot som äger den här sammansättningen|
|**schema_id**|**int**| ID för det schema som innehåller objektet.|
|**parent_object_id**|**id**| objekt identifierings nummer för det överordnade objektet för den här data strömmen. I den aktuella implementeringen är det här värdet alltid null|
|**bastyp**|**char (2)**|Objekt typ. För Stream-objekt är typen alltid ' ES '|
|**type_desc**|**nvarchar (60)**| Beskrivning av objekt typen. För Stream-objekt är typen alltid EXTERNAL_STREAM|
|**create_date**|**datetime**| Datum då objektet skapades.|
|**modify_date**|**datetime**| Datumet då objektet senast ändrades med hjälp av en ALTER-instruktion.|
|**is_ms_shipped**|**bit**| Objekt som skapats av en intern komponent.|  
|**is_published**|**bit**|Objektet har publicerats.|  
|**is_schema_published**|**bit**|Endast objektets schema har publicerats.|
|**max_column_id_used**|**bit**| Den här kolumnen används för internt bruk och tas bort i framtiden|  
|**uses_ansi_nulls**|**bit**| Stream-objektet skapades med alternativet Ange ANSI_NULLS databas på|
|**data_source_id**|**int**| Objekt-ID för den externa data källa som representeras av Stream-objektet |  
|**file_format_id**|**int**| Objekt-ID för det externa fil format som används av Stream-objektet. Det externa fil formatet krävs för att ange den faktiska layouten för data som refereras till av en extern data ström| 
|**sökvägen**|**varchar(max)**| Målet för det externa Stream-objektet. Mer information finns i [skapa extern ström](overview.md) |
|**input_option**|**varchar(max)**| De indata-alternativ som används när den externa data strömmen skapas. Mer information finns i [skapa extern ström](overview.md) |
|**output_option**|**varchar(max)**| De utmatnings alternativ som används när den externa data strömmen skapas. Mer information finns i [skapa extern ström](overview.md) | 

## <a name="permissions"></a>Behörigheter

Synligheten för metadata i katalogvyer är begränsad till skydd bara objekt som en användare antingen äger eller som användaren har beviljats behörighet till. Mer information finns i [konfiguration av metadata-synlighet](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Se även

- [Katalogvyer (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systemvyer (Transact-SQL)](/sql/t-sql/language-reference/)
