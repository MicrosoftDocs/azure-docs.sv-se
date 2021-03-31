---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648263"
---
Azure Functions stöder resurs delning mellan ursprung (CORS). CORS konfigureras [i portalen](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) och via [Azure CLI](/cli/azure/functionapp/cors). Listan över tillåtna ursprung för CORS-listan gäller på funktions nivån app. När CORS är aktiverat inkluderar Svaren `Access-Control-Allow-Origin` rubriken. Mer information finns i [Cross-origin resource sharing](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 