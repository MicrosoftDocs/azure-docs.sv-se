---
title: Styrning av Azure DevTest Labs infrastruktur-resurs
description: Den här artikeln beskriver justering och hantering av resurser för Azure DevTest Labs i din organisation.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480923"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Styrning av Azure DevTest Labs infrastruktur – resurser
Den här artikeln beskriver justering och hantering av resurser för DevTest Labs i din organisation. 

## <a name="align-within-an-azure-subscription"></a>Justera i en Azure-prenumeration 

### <a name="question"></a>Fråga
Hur gör jag för att du justera DevTest Labs-resurser i en Azure-prenumeration?

### <a name="answer"></a>Svar
Innan en organisation börjar använda Azure för allmän program utveckling bör IT-planeraren först gå igenom hur du introducerar kapaciteten som en del av den övergripande portföljen av tjänster. Följande gäller för gransknings områden:

- Hur mäter du kostnaden som är kopplad till program utvecklings livs cykeln?
- Hur justerar organisationen det föreslagna tjänst erbjudandet med företagets säkerhets policy? 
- Krävs segmentering för att separera utvecklings-och produktions miljöerna? 
- Vilka kontroller introduceras för långsiktig enkel hantering, stabilitet och tillväxt?

Den **första rekommenderade metoden** är att granska organisationens Azure-taxonomi där indelningarna mellan produktions-och utvecklings prenumerationerna beskrivs. I följande diagram möjliggör den föreslagna taxonomin en logisk avgränsning av utvecklings-/testnings-och produktions miljöer. Med den här metoden kan en organisation införa fakturerings koder för att spåra kostnader som är associerade med varje miljö separat. Mer information finns i [preskripting-styrning av prenumerationer](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Dessutom kan du använda [Azure-Taggar](../azure-resource-manager/management/tag-resources.md) för att organisera resurser för spårnings-och fakturerings syfte.

Den **Andra rekommenderade metoden** är att aktivera DevTest-prenumerationen i Azure Enterprise Portal. Det gör det möjligt för en organisation att köra klient operativ system som inte normalt är tillgängliga i en Azure Enterprise-prenumeration. Använd sedan företags program varan där du bara betalar för beräkningen och oroa dig inte om licensiering. Det garanterar att faktureringen för avsedda tjänster, inklusive Galleri bilder i IaaS, till exempel Microsoft SQL Server, endast baseras på konsumtion. Information om Azure DevTest-prenumerationen finns [här](https://azure.microsoft.com/offers/ms-azr-0148p/) för kunder med Enterprise-avtal (EA) och [här](https://azure.microsoft.com/offers/ms-azr-0023p/) kan du betala per användning.

![Resurs justering med prenumerationer](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Den här modellen ger en organisation flexibiliteten att distribuera Azure DevTest Labs i stor skala. En organisation kan stödja hundratals labb för olika affär senheter med 100 till 1000 virtuella datorer som körs parallellt. Det främjar begreppet en centraliserad Enterprise Lab-lösning som kan dela samma principer för konfigurations hantering och säkerhets kontroller.

Den här modellen säkerställer också att organisationen inte utsätter sina resurs gränser som är kopplade till deras Azure-prenumeration. Mer information om prenumerations-och tjänst begränsningar finns i [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../azure-resource-manager/management/azure-subscription-service-limits.md). Etablerings processen för DevTest Labs kan förbruka ett stort antal resurs grupper. Du kan begära att gränser ökar genom en supportbegäran i Azure DevTest-prenumerationen. Resurserna i produktions prenumerationen påverkas inte när utvecklings prenumerationen växer. Mer information om skalning av DevTest Labs finns i [skala kvoter och begränsningar i DevTest Labs](devtest-lab-scale-lab.md).

En gemensam prenumerations nivå gräns som måste redovisas för är hur tilldelningen av nätverks-IP-intervall tilldelas för att stödja både produktions-och utvecklings prenumerationer. De här tilldelningarna bör redovisas över tid (förutsatt att den lokala anslutningen eller en annan nätverkstopologi som kräver att företaget hanterar sin nätverks stack i stället för att användas som standard för Azures implementering). Den rekommenderade metoden är att ha några virtuella nätverk som har tilldelats ett stort IP-adressprefix och delas med många stora undernät i stället för att ha flera virtuella nätverk med små undernät. Med 10 prenumerationer kan du till exempel definiera 10 virtuella nätverk (ett för varje prenumeration). Alla labb som inte kräver isolering kan dela samma undernät i prenumerationens VNet.

## <a name="maintain-naming-conventions"></a>Bevara namn konventioner

### <a name="question"></a>Fråga
Hur gör jag för att underhålla en namngivnings konvention i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Du kanske vill utöka de nuvarande konventionerna för företags namn till Azure-åtgärder och göra dem konsekventa i DevTest Labs-miljön.

När du distribuerar DevTest Labs rekommenderar vi att du har vissa start principer. Du distribuerar dessa principer av ett centralt skript och JSON-mallar för att genomdriva konsekvens. Namngivnings principer kan implementeras via Azure-principer som tillämpas på prenumerations nivån. JSON-exempel för Azure Policy finns i [Azure policy exempel](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Antal användare per labb och labb per organisation

### <a name="question"></a>Fråga 
Hur gör jag för att fastställa förhållandet mellan användarna per labb och det totala antalet Labb som krävs i en organisation?

### <a name="answer"></a>Svar
Vi rekommenderar att affär senheter och utvecklings grupper som är associerade med samma utvecklings projekt är associerade med samma labb. Den tillåter samma typer av principer, avbildningar och avslutnings principer som tillämpas på båda grupperna. 

Du kan också behöva överväga geografiska gränser. Till exempel kan utvecklare i norra östra USA (US) använda ett labb som har etablerad i östra 2; USA. Och utvecklare i Borås, Texas och Denver, kan i USA hänvisas till att använda en resurs i södra centrala USA. Om det finns en samarbets ansträngning med en extern tredje part kan de tilldelas ett labb som inte används av interna utvecklare. 

Du kan också använda ett labb för ett enskilt projekt i Azure DevOps-projekt. Sedan tillämpar du säkerheten via en angiven Azure Active Directory grupp, som ger åtkomst till båda uppsättningarna av resurser. Det virtuella nätverk som tilldelats labbet kan vara en annan bindning för att konsolidera användare.

## <a name="deletion-of-resources"></a>Borttagning av resurser

### <a name="question"></a>Fråga
Hur kan vi förhindra att resurser tas bort inom ett labb?

### <a name="answer"></a>Svar
Vi rekommenderar att du ställer in rätt behörigheter på labb nivån så att endast auktoriserade användare kan ta bort resurser eller ändra labb principer. Utvecklare bör placeras i gruppen **DevTest Labs-användare** . Lead-utvecklaren eller infrastrukturens lead bör vara **DevTest Labs-ägare**. Vi rekommenderar att du bara har två labb ägare. Den här principen sträcker sig över kod lagrings platsen för att undvika skador. Labb användning har behörighet att använda resurser, men kan inte uppdatera labb principer. Se följande artikel som visar de roller och rättigheter som varje inbyggd grupp har i ett labb: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Flytta labbet till en annan resurs grupp 

### <a name="question"></a>Fråga
Finns det stöd för att flytta ett labb till en annan resurs grupp?

### <a name="answer"></a>Svar
Ja. Gå till sidan resurs grupp från start sidan för ditt labb. Välj sedan **Flytta** i verktygsfältet och välj det labb som du vill flytta till en annan resurs grupp. När du skapar ett labb skapas en resurs grupp automatiskt åt dig. Men du kanske vill flytta labbet till en annan resurs grupp som följer organisationens namngivnings konventioner. 

## <a name="next-steps"></a>Nästa steg
Se [hantera kostnader och ägarskap](devtest-lab-guidance-governance-cost-ownership.md).
