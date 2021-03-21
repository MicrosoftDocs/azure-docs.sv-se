---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996470"
---
## <a name="validate-that-a-container-is-running"></a>Verifiera att en behållare körs 

Det finns flera sätt att verifiera att behållaren körs. Leta reda på den *externa IP-* adressen och den exponerade porten för den aktuella behållaren och öppna din favorit webbläsare. Använd de olika URL: erna för begäran nedan för att verifiera att behållaren körs. URL: erna för exempel begär Anden som anges nedan är `http://localhost:5000` , men din speciella behållare kan variera. Tänk på att du är beroende av behållarens *externa IP* -adress och exponerad port.

| URL för begäran | Syfte |
|--|--|
| `http://localhost:5000/` | Containern tillhandahåller en startsida. |
| `http://localhost:5000/ready` | Begärd med GET, detta ger en verifiering av att behållaren är redo att acceptera en fråga mot modellen.  Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/status` | Begärd med GET, kontrollerar detta om den API-nyckel som används för att starta behållaren är giltig utan att orsaka en slut punkts fråga. Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | Containern tillhandahåller en fullständig uppsättning dokumentation för slutpunkterna samt en **Prova**-funktion. Med den här funktionen kan du ange dina inställningar i ett webbaserat HTML-formulär och göra frågan utan att behöva skriva någon kod. När frågan returnerar visas ett exempel på ett spiral kommando som visar de HTTP-rubriker och det text format som krävs. |

![Behållarens start sida](./media/cognitive-services-containers-api-documentation/container-webpage.png)
