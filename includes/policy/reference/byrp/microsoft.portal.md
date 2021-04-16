---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e28dc20fe5e789bbaa5aad9402d37f039eabe6e5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504225"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Delade instrumentpaneler bör inte ha markdown-paneler med infogade innehåll](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Du får inte skapa en delad instrumentpanel som har infogade innehåll i markdown-paneler och framtvinga att innehållet ska lagras som en markdown-fil som finns online. Om du använder infogade innehåll i markdown-panelen kan du inte hantera kryptering av innehållet. Genom att konfigurera din egen lagring kan du kryptera, dubbelkryptera och även ta med dina egna nycklar. Om du aktiverar den här principen begränsas användarna till att använda förhandsversionen 2020-09-01 eller senare av delade instrumentpaneler REST API. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
