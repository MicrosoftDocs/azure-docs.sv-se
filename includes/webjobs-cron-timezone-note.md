---
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 10/13/2019
ms.author: glenga
ms.openlocfilehash: 85c107451c8199f981ca742b998c6a4bdf15c366
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "72303544"
---
> [!NOTE]  
> Standard tids zonen som används för att köra CRON uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska köras baserat på en annan tidszon skapar du en app-inställning för din Function-app med namnet WEBSITE_TIME_ZONE. Läs mer i [NCRONTAB tids zoner](../articles/azure-functions/functions-bindings-timer.md#ncrontab-time-zones).
