---
title: ta med fil
description: ta med fil
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "77198334"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Varaktighet för Function-appen 

Tids gränsen för en Function-app definieras av `functionTimeout` egenskapen i [host.js](../articles/azure-functions/functions-host-json.md#functiontimeout) i projekt filen. I följande tabell visas standard-och max värden i minuter för båda planerna och de olika körnings versionerna:

| Planera | Körnings version | Standardvärde | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2x | 5 | 10 |
| Förbrukning | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Obegränsat |
| Premium | 2x | 30 | Obegränsat |
| Premium | 3.x | 30 | Obegränsat |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2x | 30 | Obegränsat |
| App Service | 3.x | 30 | Obegränsat |

> [!NOTE] 
> Oavsett inställningen för funktionen app-timeout är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan utföra för att svara på en begäran. Detta beror på [förvalda tids gräns för inaktivitet för Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). För längre bearbetnings tider kan du överväga att använda [Durable Functions asynkrona mönstret](../articles/azure-functions/durable/durable-functions-overview.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett omedelbart svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
