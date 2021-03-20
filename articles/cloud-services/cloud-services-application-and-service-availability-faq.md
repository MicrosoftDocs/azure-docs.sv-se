---
title: Vanliga frågor och svar om program-och tjänst tillgänglighet
description: Den här artikeln innehåller vanliga frågor om program-och tjänst tillgänglighet för Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742904"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problem med program-och tjänst tillgänglighet för Azure Cloud Services (klassisk): vanliga frågor och svar

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln innehåller vanliga frågor om problem med program-och tjänst tillgänglighet för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också se storleks information på [sidan Cloud Services virtuell dator storlek](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Min roll har återvunnits. Fanns några uppdateringar för min moln tjänst?
Ungefär en gång i månaden släpper Microsoft en ny gäst operativ system version för virtuella Windows Azure PaaS-datorer.Gäst operativ systemet är bara en sådan uppdatering i pipelinen. En version kan påverkas av många andra faktorer. Dessutom körs Azure på hundratals tusentals datorer. Därför går det inte att förutsäga exakt det datum och den tid som dina roller kommer att starta om. Vi uppdaterar RSS-flödet för gäst operativ systemet med den senaste informationen som vi har, men du bör överväga att rapportera tid till ett ungefärligt värde. Vi är medvetna om att detta är problematiskt för kunder och arbetar på en plan för att begränsa eller precis tid för omstarter.

Fullständig information om de senaste uppdateringarna för gäst operativ systemet finns i [Azure gäst operativ system versioner och SDK-kompatibilitet](cloud-services-guestos-update-matrix.md).

Information om omstarter och pekare till teknisk information om gäst-och värd operativ system uppdateringar finns i MSDN blogg inlägg [roll instans startar om på grund av OS-uppgraderingar](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Varför tar det den första begäran till min moln tjänst när tjänsten har varit inaktiv under en viss tid längre tid än vanligt?
När webb servern tar emot den första begäran kompilerar den först om koden och bearbetar sedan begäran. Det är därför den första begäran tar längre tid än de andra. Som standard stängs app-poolen i händelse av inaktivitet. Programpoolen återanvänds också som standard var 1 740: e minut (29 timmar).

Internet Information Services (IIS) programpooler kan regelbundet återvinnas för att undvika instabila tillstånd som kan leda till att program kraschar, låser sig eller minnes läckor.

Följande dokument hjälper dig att förstå och minimera det här problemet:
* [Korrigera långsam inledande belastning för IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7,5-webbprogramets första begäran efter att app-poolens återvinning är mycket långsamt](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Om du vill ändra standard beteendet för IIS måste du använda Start åtgärder, eftersom om du manuellt tillämpar ändringarna på webb roll instanserna går ändringarna förlorade.

Mer information finns i [så här konfigurerar och kör du Start åtgärder för en moln tjänst](cloud-services-startup-tasks.md).