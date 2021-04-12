---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028409"
---
Standard tids zonen som används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska baseras på en annan tidszon skapar du en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE` . 

Värdet för den här inställningen beror på vilket operativ system och vilken funktion appen körs på.

|Operativsystem |Planera |Värde |
|-|-|-|
| **Windows** |Alla | Ange värdet till namnet på den önskade tids zonen som anges av den andra raden från varje par som anges av Windows-kommandot `tzutil.exe /L` |
| **Linux** |Premium<br/>Dedikerad |Ange värdet till namnet på den önskade tids zonen som visas i [TZ-databasen](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` stöds för närvarande inte i Linux-förbruknings planen.

Till exempel, Eastern Time i USA (representeras av `Eastern Standard Time` (Windows) eller `America/New_York` (Linux)) använder för närvarande UTC-05:00 under normal tid och UTC-04:00 under sommar tid. Om du vill ha en timer-utlösare vid 10:00 Eastern-tid varje dag, skapar du en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE` , anger värdet till `Eastern Standard Time` (Windows) eller `America/New_York` (Linux) och använder sedan följande NCRONTAB-uttryck: 

```
"0 0 10 * * *"
``` 

När du använder `WEBSITE_TIME_ZONE` tiden justeras tiden för tids ändringar i den angivna tids zonen, inklusive sommar tid och ändringar i normal tid.
