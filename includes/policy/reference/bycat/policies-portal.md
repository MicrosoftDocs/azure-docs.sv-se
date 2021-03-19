---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 961762559ba27deee519ad3a9516bc351ca724f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605712"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Delade instrument paneler får inte ha markdown paneler med infogat innehåll](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Tillåt inte att du skapar en delad instrument panel som har infogat innehåll i markdown paneler och tvingar fram att innehållet ska lagras som en MARKDOWN-fil som är online. Om du använder infogat innehåll på markdown-panelen kan du inte hantera kryptering av innehållet. Genom att konfigurera ditt eget lagrings utrymme kan du kryptera, kryptera och till och med ta med dina egna nycklar. Genom att aktivera den här principen begränsar du användare till att använda 2020-09-01-för hands version eller en högre version av delade instrument paneler REST API. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
