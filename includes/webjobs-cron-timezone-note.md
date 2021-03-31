---
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 10/13/2019
ms.author: glenga
ms.openlocfilehash: 85c107451c8199f981ca742b998c6a4bdf15c366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "72303544"
---
> [!NOTE]  
> Standard tids zonen som används för att köra CRON uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska köras baserat på en annan tidszon skapar du en app-inställning för din Function-app med namnet WEBSITE_TIME_ZONE. Läs mer i [NCRONTAB tids zoner](../articles/azure-functions/functions-bindings-timer.md#ncrontab-time-zones).
