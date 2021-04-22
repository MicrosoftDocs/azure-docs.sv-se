---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d10090954162bdf614234d2bce863a0d9a1635af
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867150"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Delade instrumentpaneler bör inte ha markdown-paneler med infogade innehåll](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Du får inte skapa en delad instrumentpanel som har infogade innehåll i markdown-paneler och framtvinga att innehållet ska lagras som en markdown-fil som finns online. Om du använder infogade innehåll i markdown-panelen kan du inte hantera kryptering av innehållet. Genom att konfigurera din egen lagring kan du kryptera, dubbelkryptera och även ta med dina egna nycklar. Om du aktiverar den här principen begränsas användarna till att använda förhandsversionen 2020-09-01 eller senare av delade instrumentpaneler REST API. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
