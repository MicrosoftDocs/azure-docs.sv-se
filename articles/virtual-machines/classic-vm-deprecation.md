---
title: Vi drar tillbaka de klassiska virtuella Azure-datorerna den 1 mars 2023
description: Artikeln innehåller en översikt över den klassiska VM-indragningen
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: c1807da76fe25d31581a07706489967d6e83f748
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505316"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrera dina IaaS-resurser till Azure Resource Manager den 1 mars 2023 

I 2014 lanserade vi IaaS på Azure Resource Manager och har ökat funktionerna någonsin sedan. Eftersom [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu har fullständiga IaaS-funktioner och andra framsteg förbrukade vi hanteringen av IaaS-datorer via [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) den 28 februari 2020 och den här funktionen kommer att dras tillbaka den 1 mars 2023. 

Idag, cirka 90% av de virtuella IaaS-datorerna använder Azure Resource Manager. Om du använder IaaS-resurser via Azure Service Manager (ASM) börjar du planera migreringen nu och slutför den 1 mars 2023 för att dra nytta av [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klassiska virtuella datorer kommer att följa den [moderna livs cykel policyn](https://support.microsoft.com/help/30881/modern-lifecycle-policy) för pensionering.

## <a name="how-does-this-affect-me"></a>Hur påverkar det här mig? 

- Från och med den 28 februari 2020 kommer kunder som inte utnyttjade IaaS-datorer via Azure Service Manager (ASM) under månaden februari 2020 inte längre att kunna skapa klassiska virtuella datorer. 
- Den 1 mars 2023 kommer kunder inte längre att kunna starta IaaS virtuella datorer med Azure Service Manager och alla som fortfarande körs eller allokeras stoppas och frigörs. 
- Den 1 mars 2023 kommer prenumerationer som inte har migrerats till Azure Resource Manager att informeras om tids linjer för borttagning av eventuella återstående klassiska virtuella datorer.  

Följande Azure-tjänster och-funktioner påverkas **inte** av den här indragningen: 
- Cloud Services 
- Lagrings konton som **inte** används av klassiska virtuella datorer 
- Virtuella nätverk (virtuella nätverk) som **inte** används av klassiska virtuella datorer. 
- Andra klassiska resurser

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag göra? 

- Börja planera migreringen till Azure Resource Manager, idag. 

- [Läs mer](./windows/migration-classic-resource-manager-overview.md) om hur du migrerar dina klassiska [Linux](./linux/migration-classic-resource-manager-plan.md) -och [Windows](./windows/migration-classic-resource-manager-plan.md) -datorer till Azure Resource Manager.

- Mer information finns i [vanliga frågor och svar om klassisk Azure Resource Manager migrering](./migration-classic-resource-manager-faq.md)

- [Kontakta supporten](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})för tekniska frågor, problem och lägga till prenumerationer i listan över tillåtna.

- För andra frågor som inte ingår i vanliga frågor och svar och kommentarer nedan.

- Slutför migreringen så snart som möjligt för att förhindra företags påverkan och förbättra prestandan, säkerhets & nya funktioner som tillhandahålls av Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Vilka resurser tillhandahålls för den här migreringen?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): Microsoft & community-support för migrering

- [Stöd för Azure migration](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): dedikerat support team för teknisk hjälp under migrering

- [Microsoft snabb spår](https://www.microsoft.com/fasttrack): Microsoft fast track-teamet kan ge teknisk hjälp vid migrering till berättigade kunder. 

- Om företaget/organisationen har samarbetat med Microsoft och/eller arbetar med Microsoft-representant som (Cloud Solution Architect (CSA), teknik konto hanterare (TAMs)), kan du arbeta med dem för ytterligare resurser för migrering. 

