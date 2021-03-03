---
title: Virtuella Azure-datorer tas ur bruk (klassisk) den 1 mars 2023
description: Den här artikeln innehåller en översikt över pensionering av virtuella datorer som skapats med den klassiska distributions modellen.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668202"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrera dina IaaS-resurser till Azure Resource Manager den 1 mars 2023 

I 2014 lanserade vi infrastruktur som en tjänst (IaaS) på [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vi har ökat funktionerna någonsin sedan. Eftersom Azure Resource Manager nu har fullständiga IaaS-funktioner och andra framsteg förbrukade vi hanteringen av virtuella datorer med IaaS (VM) via [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) den 28 februari 2020. Den här funktionen kommer att dras tillbaka den 1 mars 2023. 

Idag är cirka 90 procent av de virtuella IaaS-datorerna som använder Azure Resource Manager. Om du använder IaaS-resurser via ASM börjar du planera migreringen nu. Slutför den 1 mars 2023 för att dra nytta av [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Virtuella datorer som skapats med den klassiska distributions modellen följer den [moderna livs cykel policyn](https://support.microsoft.com/help/30881/modern-lifecycle-policy) för att dra tillbaka.

## <a name="how-does-this-affect-me"></a>Hur påverkar det här mig? 

- Från och med den 28 februari 2020 kan kunder som inte använde IaaS VM: ar med ASM under den månaden februari 2020 inte längre skapa virtuella datorer (klassiska). 
- Den 1 mars 2023 kommer kunder inte längre att kunna starta IaaS virtuella datorer med hjälp av ASM. Alla som fortfarande körs eller allokeras kommer att stoppas och frigöras. 
- Den 1 mars 2023 kommer prenumerationer som inte har migrerats till Azure Resource Manager att informeras om tids linjer för borttagning av återstående virtuella datorer (klassisk).  

Den här indragningen påverkar *inte* följande Azure-tjänster och-funktioner: 
- [Azure Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md)
- Lagrings konton som *inte* används av virtuella datorer (klassisk) 
- Virtuella nätverk som *inte* används av virtuella datorer (klassisk) 
- Andra klassiska resurser

## <a name="what-resources-are-available-for-this-migration"></a>Vilka resurser är tillgängliga för den här migreringen?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): Microsoft och community-support för migrering.

- [Stöd för Azure migration](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): dedikerat support team för teknisk hjälp under migreringen. Kunder utan teknisk support kan använda [kostnads fri support](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) som är specifikt för migreringen. 

- [Microsoft fast track](https://www.microsoft.com/fasttrack): snabb spårning kan hjälpa kunder att planera & körning för migreringen. [Nominera dig själv](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) för programmet för DC-migrering.  

- Om företaget/organisationen har samarbetat med Microsoft eller fungerar med Microsoft-representanter (t. ex. Cloud Solution Architects (CSAs) eller tekniska konto hanterare (TAMs)), kan du arbeta med dem för ytterligare resurser för migrering.

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag göra? 

Börja planera migreringen till Azure Resource Manager, idag. 

1. Gör en lista över alla virtuella datorer som påverkas: 

   - De virtuella datorerna av typen **Virtual Machines (klassisk)** i [Azure portalens VM-fönster](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) är alla berörda virtuella datorer i prenumerationen. 
   - Du kan också fråga Azure Resource Graph genom att använda [portalen](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) eller [PowerShell](../governance/resource-graph/concepts/work-with-data.md) för att visa en lista över alla flaggade virtuella datorer (klassisk) och relaterad information för de valda prenumerationerna. 
   - Den 8 februari och den 2 september 2020 skickade vi ut e-postmeddelanden med ämnet "börja planera din IaaS VM-migrering till Azure Resource Manager" till prenumerations ägare. E-postmeddelandet innehåller en lista över alla virtuella datorer och prenumerationer och virtuella datorer (klassiska). Använd dem för att bygga den här listan. 

1. [Läs mer](./migration-classic-resource-manager-overview.md) om hur du migrerar dina virtuella [Linux](./migration-classic-resource-manager-plan.md) -och [Windows](./migration-classic-resource-manager-plan.md) -datorer (klassisk) till Azure Resource Manager. Mer information finns i [vanliga frågor och svar om klassisk att Azure Resource Manager migrering](./migration-classic-resource-manager-faq.md).

1. Vi rekommenderar att du startar planeringen genom att använda [verktyget Platform support migration](./migration-classic-resource-manager-overview.md) för att migrera dina befintliga virtuella datorer med tre enkla steg: validera, Förbered och genomför. Verktyget är utformat för att migrera dina virtuella datorer inom minimalt till ingen stillestånds tid. 

   - Det första steget, validera, har ingen inverkan på din befintliga distribution och innehåller en lista över alla scenarier som inte stöds för migrering. 
   - Gå igenom [listan med lösningar](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) för att åtgärda distributionen och gör den redo för migrering. 
   - Helst när alla verifierings fel har åtgärd ATS bör du inte stöta på några problem under förbereda och genomföra. När genomförandet har genomförts migreras distributionen till Azure Resource Manager och kan sedan hanteras via nya API: er som exponeras av Azure Resource Manager. 

   Om migreringen inte är lämplig för migreringen kan du utforska [andra beräknings erbjudanden](/azure/architecture/guide/technology-choices/compute-decision-tree) för migreringen. Eftersom det finns många Azure Compute-erbjudanden, och de skiljer sig från varandra, kan vi inte tillhandahålla en sökväg för migrering till plattformar som stöds.  

1. [Kontakta supporten](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})för tekniska frågor, problem och hjälp med att lägga till prenumerationer i listan över tillåtna.

1. Slutför migreringen så snart som möjligt för att förhindra företags påverkan och dra nytta av de förbättrade prestanda-, säkerhets-och nya funktionerna i Azure Resource Manager. 
