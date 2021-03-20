---
title: sys.external_streaming_jobs (Transact-SQL) – Azure SQL Edge
description: Lär dig mer om att använda sys.external_streaming_jobs i Azure SQL Edge
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900340"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Returnerar en rad för varje externt strömnings jobb som skapats inom databasens omfattning.

|Kolumnnamn|Datatyp|Beskrivning|  
|-----------------|---------------|-----------------|
|**Namn**|**sysname**|Namn på data strömmen. Är unikt i databasen.|
|**object_id**|**int**|objekt identifierings nummer för Stream-objektet. Är unikt i databasen.|
|**principal_id**|**int**|ID för huvud kontot som äger den här sammansättningen|
|**schema_id**|**int**| ID för det schema som innehåller objektet.|
|**parent_object_id**|**id**| objekt identifierings nummer för det överordnade objektet för den här data strömmen. I den aktuella implementeringen är det här värdet alltid null|
|**bastyp**|**char (2)**|Objekt typ. För Stream-objekt är typen alltid "EJ"|
|**type_desc**|**nvarchar (60)**| Beskrivning av objekt typen. För Stream-objekt är typen alltid EXTERNAL_STREAMING_JOB|
|**create_date**|**datetime**| Datum då objektet skapades.|
|**modify_date**|**datetime**| I den aktuella implementeringen är detta värde detsamma som create_date för Stream-objektet |
|**is_ms_shipped**|**bit**| Objekt som skapats av en intern komponent.|  
|**is_published**|**bit**| Objektet har publicerats.|  
|**is_schema_published**|**bit**|Endast objektets schema har publicerats.|
|**uses_ansi_nulls**|**bit**| Stream-objektet skapades med alternativet Ange ANSI_NULLS databas på|
|**Sekretesspolicy**|**varchar(max)**| Stream Analytics-frågetexten för strömnings jobbet. Mer information finns i [sp_create_streaming_job](overview.md) |
|**statusfältet**|**int**| Den aktuella statusen för strömnings jobbet. Möjliga värden är <br /><br /> **Skapat** = 0. Direkt uppspelnings jobbet har skapats, men har ännu inte startats. <br /><br /> **Startar** = 1. Streaming-jobbet är i start fasen. <br /><br /> **Misslyckades** = 6. Streaming-jobbet misslyckades. Detta är vanligt vis ett tecken på ett allvarligt fel under bearbetningen. <br /><br /> **Stoppad** = 4. Direkt uppspelnings jobbet har stoppats. <br /><br /> **Inaktiv** = 7. Direkt uppspelnings jobbet körs, men det finns inga indata att bearbeta. <br /><br /> **Bearbetar** = 8. Direkt uppspelnings jobbet körs och bearbetar indata. Det här tillståndet anger ett felfritt tillstånd för strömnings jobbet. <br /><br /> **Degraderat** = 9. Direkt uppspelnings jobbet körs, men det fanns några icke allvarliga fel vid indata/utdata-serialisering/-serialisering under bearbetning av indata. Inmatnings jobbet kommer att fortsätta köras, men kommer att släppa indata som stöter på fel.|

## <a name="permissions"></a>Behörigheter

Synligheten för metadata i katalogvyer är begränsad till skydd bara objekt som en användare antingen äger eller som användaren har beviljats behörighet till. Mer information finns i [konfiguration av metadata-synlighet](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Se även

- [Vyer för T-SQL strömmad katalog](overview.md)
- [Katalogvyer (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systemvyer (Transact-SQL)](/sql/t-sql/language-reference/)

