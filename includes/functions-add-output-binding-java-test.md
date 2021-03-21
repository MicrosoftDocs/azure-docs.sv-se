---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739828"
---
Eftersom archetype också skapar en uppsättning tester måste du uppdatera de här testerna för att hantera den nya `msg` parametern i `run` Metodsignaturen.  

Bläddra till platsen för test koden under _src/test/java_, öppna filen *Function. java* -projekt och ersätt kodraden under `//Invoke` med följande kod.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
