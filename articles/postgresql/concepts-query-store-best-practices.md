---
title: Metod tips för Query Store i Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver metod tips för Frågearkivet i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708840"
---
# <a name="best-practices-for-query-store"></a>Metod tips för Frågearkivet

**Gäller för:** Azure Database for PostgreSQL-enskild server version 9,6, 10, 11

Den här artikeln beskriver metod tips för att använda Query Store i Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ange det bästa läget för hämtning av frågor
Låt Query Store samla in de data som är viktiga för dig. 

|**pg_qs pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alla_  |Analysera din arbets belastning noggrant med avseende på alla frågor och deras körnings frekvenser och annan statistik. Identifiera nya frågor i din arbets belastning. Identifiera om ad hoc-frågor används för att identifiera möjligheter för användare eller automatiska Parameterisering. _Allt_ levereras med en ökad kostnad för resursförbrukning. |
|_Översta_  |Fokusera din uppmärksamhet på de vanligaste frågorna – de som utfärdats av klienter.
|_Ingen_ |Du har redan skapat ett fönster för frågeinställningar och tid som du vill undersöka och du vill undvika de störande som andra frågor kan introducera. _Ingen_ är lämplig för testning och bänk märknings miljöer. _Ingen_ bör användas med försiktighet eftersom du kan förlora möjligheten att spåra och optimera viktiga nya frågor. Det går inte att återställa data på de senaste tidnas fönster. |

Query Store innehåller även en butik för väntande statistik. Det finns en extra infångnings läges fråga som styr väntande statistik: **pgms_wait_sampling. query_capture_mode** kan anges till _none_ eller _all_. 

> [!NOTE] 
> **pg_qs. query_capture_mode** ersätter **pgms_wait_sampling. query_capture_mode**. Om pg_qs. query_capture_mode är _ingen_, har inställningen pgms_wait_sampling. query_capture_mode ingen påverkan. 


## <a name="keep-the-data-you-need"></a>Behåll de data du behöver
Parametern **pg_qs. retention_period_in_days** anger i dagar data lagrings perioden för frågearkivet. Äldre fråge-och statistik data tas bort. Som standard är Frågearkivet konfigurerat för att bevara data i 7 dagar. Undvik att behålla historiska data som du inte planerar att använda. Öka värdet om du behöver behålla data längre.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ange frekvensen för sampling av statistik 
Parametern **pgms_wait_sampling. history_period** anger hur ofta väntande händelser (i millisekunder) ska insamplas. Ju kortare tid desto mer frekventa samplingen. Mer information hämtas, men det levereras med kostnaden för större resursförbrukning. Öka den här perioden om servern är under belastning eller om du inte behöver kornig het


## <a name="get-quick-insights-into-query-store"></a>Få snabba insikter i Query Store
Du kan använda [query Performance Insight](concepts-query-performance-insight.md) i Azure Portal för att få snabba insikter om data i frågearkivet. Visualiseringarna visar den längsta körningen av frågorna och den längsta väntan händelserna över tid.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du hämtar eller anger parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).