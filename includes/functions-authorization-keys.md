---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91829284"
---
Med funktioner kan du använda nycklar för att göra det svårare att komma åt HTTP-funktionerna under utvecklingen. Om inte HTTP-filnivån på en HTTP-utlöst funktion har angetts till `anonymous` måste begär Anden innehålla en API-åtkomst nyckel i begäran. 

Även om nycklar ger en standardmekanism för säkerhetsmekanism, kanske du vill överväga ytterligare alternativ för att skydda en HTTP-slutpunkt i produktionen. Det är till exempel vanligt vis inte en bra idé att distribuera delad hemlighet i offentliga appar. Om din funktion anropas från en offentlig klient kanske du vill överväga att implementera en annan säkerhetsmekanism. Mer information finns i [skydda en HTTP-slutpunkt i produktion](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

När du förnyar funktions nyckel värden måste du manuellt distribuera de uppdaterade nyckelvärdena till alla klienter som anropar din funktion.  

#### <a name="authorization-scopes-function-level"></a>Authorization-omfattningar (funktions nivå)

Det finns två åtkomstscope för nycklar på funktions nivå:

* **Funktion**: dessa nycklar gäller endast för de funktioner som de är definierade under. När den används som en API-nyckel tillåter dessa endast åtkomst till den funktionen.

* **Värd**: nycklar med ett värd omfång kan användas för att få åtkomst till alla funktioner i Function-appen. När den används som en API-nyckel tillåter dessa åtkomst till alla funktioner i Function-appen. 

Varje nyckel namnges som referens och det finns en standard nyckel (med namnet "standard") på funktion-och värdnivå. Funktions tangenter prioriteras framför värd nycklar. När två nycklar definieras med samma namn används alltid funktions nyckeln.

#### <a name="master-key-admin-level"></a>Huvud nyckel (admin-nivå) 

Varje Function-app har också en värd nyckel på administratörs nivå med namnet `_master` . Förutom att tillhandahålla åtkomst på värdnivå till alla funktioner i appen, ger huvud nyckeln även administrativ åtkomst till REST-API: erna för körning. Den här nyckeln kan inte återkallas. När du anger en åtkomst nivå för `admin` måste begär Anden använda huvud nyckeln. eventuella andra nycklar leder till att åtkomsten Miss lyckas.

> [!CAUTION]  
> På grund av de utökade behörigheterna i din Function-app som beviljats av huvud nyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i interna klient program. Använd försiktighet när du väljer administratörs åtkomst nivå.
