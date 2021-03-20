---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78190931"
---
I ett C#-klass biblioteks projekt definieras bindningarna som binding-attribut i funktions metoden. Den *function.js* filen som krävs av Functions genereras sedan automatiskt baserat på dessa attribut.

Öppna projekt filen *HttpExample. cs* och Lägg till följande parameter i `Run` metod definitionen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametern är en `ICollector<T>` typ som representerar en samling meddelanden som skrivs till en utgående bindning när funktionen slutförs. I det här fallet är utdata en lagrings kö med namnet `outqueue` . Anslutnings strängen för lagrings kontot anges av `StorageAccountAttribute` . Det här attributet anger den inställning som innehåller lagrings kontots anslutnings sträng och kan tillämpas på klass-, metod-eller parameter nivå. I det här fallet kan du utelämna `StorageAccountAttribute` eftersom du redan använder standard lagrings kontot.

Definitionen av Run-metoden bör nu se ut så här:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
