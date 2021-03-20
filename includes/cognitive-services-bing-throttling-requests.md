---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187627"
---
Antalet frågor du kan ställa per sekund (QPS) beror på tjänsten och typen av prenumeration. Se till att programmet har den logik som behövs för att hålla dig inom kvoten. Om QPS-gränsen uppnås eller överskrids leder det till att begäran misslyckas och då returneras HTTP-statuskoden 429. Svaret innehåller `Retry-After`-huvudet, som anger hur länge du bör vänta innan du skickar en ny begäran.

## <a name="denial-of-service-versus-throttling"></a>Överbelastning eller begränsning?

Tjänsten skiljer på en överbelastningsattack (DoS) och en QPS-överträdelse. Om tjänsten misstänker en DoS-attack slutförs begäran (HTTP-statuskoden är 200 OK). Brödtexten i svaret är dock tom.