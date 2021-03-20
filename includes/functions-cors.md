---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83648263"
---
Azure Functions stöder resurs delning mellan ursprung (CORS). CORS konfigureras [i portalen](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) och via [Azure CLI](/cli/azure/functionapp/cors). Listan över tillåtna ursprung för CORS-listan gäller på funktions nivån app. När CORS är aktiverat inkluderar Svaren `Access-Control-Allow-Origin` rubriken. Mer information finns i [Cross-origin resource sharing](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 