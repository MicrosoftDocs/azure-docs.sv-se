---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673291"
---
## <a name="update-the-tests"></a>Uppdatera testerna

Eftersom archetype också skapar en uppsättning tester måste du uppdatera de här testerna för att hantera den nya `msg` parametern i `run` Metodsignaturen.  

Bläddra till platsen för test koden under _src/test/java_, öppna filen *Function. java* -projekt och ersätt kodraden under `//Invoke` med följande kod.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::