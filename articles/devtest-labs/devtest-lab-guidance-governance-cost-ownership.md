---
title: Hantera kostnader och ägarskap i Azure DevTest Labs
description: Den här artikeln innehåller information som hjälper dig att optimera för kostnader och anpassa ägandet i din miljö.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dfac69a055c9b0c75032622caf7fb8502fad3406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328274"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Styrning av Azure DevTest Labs infrastruktur – hantera kostnader och ägarskap
Kostnader och ägarskap är huvudsakliga problem när du ska skapa utvecklings-och test miljöer. I det här avsnittet hittar du information som hjälper dig att optimera för kostnad och anpassa ägarskapet i din miljö.

## <a name="optimize-for-cost"></a>Optimera för kostnad

### <a name="question"></a>Fråga
Hur kan jag optimera för kostnad i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Det finns ett antal inbyggda funktioner i DevTest Labs som hjälper dig att optimera kostnaderna. Se [Cost Management, Thresholds](devtest-lab-configure-cost-management.md) [och policys](devtest-lab-set-lab-policy.md) artiklar för att begränsa användarnas aktiviteter. 

När du använder DevTest Labs för att utveckla och testa arbets belastningar kan du överväga att använda [Enterprise dev/test prenumerations förmån](https://azure.microsoft.com/offers/ms-azr-0148p/), som en del av din Enterprise-avtal. Alternativt, om du betalar per användning, kanske du vill överväga [DevTest-erbjudandet betala per](https://azure.microsoft.com/offers/ms-azr-0023p/)användning.

Den här metoden ger flera fördelar:

- Särskilda lägre kostnader för utveckling och testning på virtuella Windows-datorer, Cloud Services, HDInsight, App Service och Logic Apps
- Fantastiska priser för Enterprise-avtal (EA) för andra Azure-tjänster
- Åtkomst till exklusiva dev/test-bilder i galleriet, inklusive Windows 8,1 och Windows 10
 
Endast aktiva Visual Studio-prenumeranter (standard prenumerationer, årliga moln prenumerationer och månatliga moln prenumerationer) kan använda Azure-resurser som körs i en Enterprise dev/test-prenumeration. Slutanvändare kan dock komma åt programmet för att ge feedback eller utföra acceptans test. Användningen av resurser i den här prenumerationen är begränsad till att utveckla och testa program och ingen garanti för drift tid erbjuds.

Om du väljer att använda DevTest-erbjudandet noterar du att den här förmånen är enbart avsedd för utveckling och testning av dina program. Användningen i prenumerationen har inget ekonomiskt service avtal, med undantag för användning av Azure-DevOps och HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Definiera rollbaserad åtkomst i hela organisationen
### <a name="question"></a>Fråga
Hur gör jag för att definierar du Azure rollbaserad åtkomst kontroll för mina DevTest Labs-miljöer för att säkerställa att den kan styra medan utvecklare/test kan utföra sitt arbete? 

### <a name="answer"></a>Svar
Det finns ett brett mönster, men detaljerna beror på din organisation.

Den centrala IT-avdelningen bör endast äga vad som är nödvändigt och göra det möjligt för projekt-och program team att ha den kontroll nivå som krävs. Det innebär vanligt vis att central IT äger prenumerationen och hanterar kärn IT-funktioner, till exempel nätverkskonfigurationer. Uppsättningen **ägare** för en prenumeration ska vara liten. Dessa ägare kan utse ytterligare ägare när det behövs eller tillämpa principer på prenumerations nivå, till exempel "ingen offentlig IP".

Det kan finnas en delmängd av användare som behöver åtkomst över en prenumeration, till exempel 1 eller support på nivå 2. I det här fallet rekommenderar vi att du ger användarna **deltagare** åtkomst så att de kan hantera resurserna, men inte ge användar åtkomst eller ändra principer.

DevTest Labs-resursen bör ägas av ägare som är nära projekt-/program gruppen. Det beror på att de förstår kraven på datorer och nödvändig program vara. I de flesta organisationer är ägaren av den här DevTest Labs-resursen ofta projekt-/utvecklings ledare. Den här ägaren kan hantera användare och principer i labb miljön och kan hantera alla virtuella datorer i DevTest Labs miljö.

Projekt-/program grupp medlemmar ska läggas till i rollen DevTest Labs-användare. Dessa användare kan skapa virtuella datorer (i linje med principerna för labb och prenumerations nivå). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure Enterprise-Autogenerera – dokumentation om preskripts styrning av prenumerationer](/azure/architecture/cloud-adoption/appendix/azure-scaffold) .


## <a name="next-steps"></a>Nästa steg
Se [företags policy och efterlevnad](devtest-lab-guidance-governance-policy-compliance.md).
