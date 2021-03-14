---
author: baanders
description: ta med fil för Azure Digitals dubbla modeller – modell instruktioner för kommando rads guiden
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463927"
---
1. **Uppdatera versions numret** för att indikera att du tillhandahåller en mer uppdaterad version av den här modellen. Gör detta genom att ändra *1* i slutet av `@id` värdet till *2*. Alla tal som är större än det aktuella versions numret fungerar också.
1. **Redigera en egenskap**. Ändra namnet på `Humidity` egenskapen till *HumidityLevel* (eller något annat om du vill. Om du använder något annat än *HumidityLevel*, kom ihåg vad du använde och fortsätta att använda det i stället för *HumidityLevel* i hela kursen.
1. **Lägg till en egenskap**. Under den `HumidityLevel` egenskap som slutar på rad 15 klistrar du in följande kod för att lägga till en `RoomName` egenskap till rummet:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Lägg till en relation**. Under den `RoomName` egenskap som du just har lagt till klistrar du in följande kod för att lägga till möjligheten för den här typen av dubbla till formulär som *innehåller* relationer med andra dubbla:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

När du är färdig ska den uppdaterade modellen matcha detta:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Se till att spara filen innan du fortsätter.