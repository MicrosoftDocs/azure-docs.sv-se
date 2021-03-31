---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673496"
---
I ett Java-projekt definieras bindningarna som bindnings anteckningar i funktions metoden. *function.js* filen skapas sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för funktions koden under _src/main/Java_, öppna filen *Function. java* -projekt och Lägg till följande parameter i `run` metod definitionen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametern är en [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ som representerar en samling strängar som skrivs som meddelanden till en utgående bindning när funktionen slutförs. I det här fallet är utdata en lagrings kö med namnet `outqueue` . Anslutnings strängen för lagrings kontot anges av- `connection` metoden. I stället för själva anslutnings strängen skickar du den program inställning som innehåller anslutnings strängen för lagrings kontot.

`run`Metod definitionen bör nu se ut som i följande exempel:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::