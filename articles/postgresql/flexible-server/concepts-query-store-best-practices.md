---
title: Metodtips för Query Store i Azure Database for PostgreSQL – Flex Server
description: Den här artikeln beskriver metodtips för Query Store i Azure Database for PostgreSQL – Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559236"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Metodtips för Query Store – flexibel server

**Gäller för:** Azure Database for PostgreSQL – Flex Server version 11, 12

Den här artikeln beskriver metodtips för att använda Query Store i Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ange det optimala läget för frågeinfångning
Låt Query Store samla in de data som är viktiga för dig. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alla_  |Analysera din arbetsbelastning noggrant med avseende på alla frågor och deras körningsfrekvenser och annan statistik. Identifiera nya frågor i din arbetsbelastning. Identifiera om ad hoc-frågor används för att identifiera möjligheter för användare eller automatisk parameterisering. _Alla_ levereras med en ökad kostnad för resursförbrukning. |
|_Topp_  |Fokusera din uppmärksamhet på de viktigaste frågorna – de som utfärdas av klienter.
|_Ingen_ |Om det är inställt på Ingen samlar Query Store inte in några nya frågor. Du har redan avbildat en frågeuppsättning och ett tidsfönster som du vill undersöka och du vill eliminera de störningar som andra frågor kan leda till. _Ingen_ är lämplig för testnings- och märkningsmiljöer. _Ingen_ bör användas med försiktighet eftersom du kanske missar möjligheten att spåra och optimera viktiga nya frågor. |


> [!NOTE] 
> **pg_qs.query_capture_mode** ersätter **pgms_wait_sampling.query_capture_mode**. Om pg_qs.query_capture_mode inte är _har_ inställningen pgms_wait_sampling.query_capture_mode ingen effekt. 


## <a name="keep-the-data-you-need"></a>Behåll de data du behöver
Parametern **pg_qs.retention_period_in_days** anger datalagringsperioden för Query Store i dagar. Äldre fråge- och statistikdata tas bort. Som standard är Query Store konfigurerat för att behålla data i 7 dagar. Undvik att behålla historiska data som du inte planerar att använda. Öka värdet om du behöver behålla data längre.


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du hämtar eller anger parametrar [med Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI.](howto-configure-server-parameters-using-cli.md)
