---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5ad3af71227ae1f6d6701102bf29c042454a965c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091299"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Delade instrument paneler får inte ha markdown paneler med infogat innehåll](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Tillåt inte att du skapar en delad instrument panel som har infogat innehåll i markdown paneler och tvingar fram att innehållet ska lagras som en MARKDOWN-fil som är online. Om du använder infogat innehåll på markdown-panelen kan du inte hantera kryptering av innehållet. Genom att konfigurera ditt eget lagrings utrymme kan du kryptera, kryptera och till och med ta med dina egna nycklar. Genom att aktivera den här principen begränsar du användare till att använda 2020-09-01-för hands version eller en högre version av delade instrument paneler REST API. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
