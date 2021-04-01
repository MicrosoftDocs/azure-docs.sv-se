---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425150"
---
Kör följande kommando för att Visa nästan real tids [strömnings loggar](../articles/azure-functions/functions-run-local.md#enable-streaming-logs):

```console
func azure functionapp logstream <APP_NAME> 
```

I ett separat terminalfönster eller i webbläsaren, anropar du fjärrfunktionen igen. En utförlig logg över funktions körningen i Azure visas i terminalen. 