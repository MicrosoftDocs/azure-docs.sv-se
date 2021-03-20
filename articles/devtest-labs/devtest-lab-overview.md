---
title: Om Azure DevTest Labs | Microsoft Docs
description: Lär dig hur DevTest Labs kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480209"
---
# <a name="about-azure-devtest-labs"></a>Om Azure DevTest Labs
Azure DevTest Labs gör det möjligt för utvecklare i team att effektivt hantera virtuella datorer och PaaS-resurser utan att vänta på godkännanden.

DevTest Labs skapar labb som består av förkonfigurerade baser eller Azure Resource Manager mallar. Dessa har alla verktyg och program som du kan använda för att skapa miljöer. Du kan skapa miljöer på några minuter, i stället för timmar eller dagar.

Genom att använda DevTest Labs kan du testa de senaste versionerna av dina program genom att utföra följande uppgifter:

- Etablera Windows-och Linux-miljöer snabbt med hjälp av åter användnings bara mallar och artefakter.
- Integrera enkelt din distributionspipeline med DevTest Labs och etablera miljöer på begäran.
- Skala upp belastnings testningen genom att tillhandahålla flera test agenter och skapa företablerade miljöer för utbildning och demonstrationer.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funktioner
DevTest Labs tillhandahåller följande funktioner för utvecklare som arbetar med virtuella datorer:

- Skapa virtuella datorer snabbt genom att följa färre än fem enkla steg.
- Välj bland en granskad lista med virtuella dator baser som har kon figurer ATS, godkänts och godkänts av teamets lead eller central IT.
- Skapa virtuella datorer från förskapade anpassade avbildningar som har alla program och verktyg som redan är installerade. 
- Skapa virtuella datorer från formler som är i princip anpassade avbildningar tillsammans med de senaste build-versionerna av program varan som installeras när de virtuella datorerna skapas. 
- Installera artefakter som är tillägg som distribueras på virtuella datorer när de har allokerats.
- Ange scheman för automatisk avstängning och automatisk start för virtuella datorer.
- Anspråk på en förskapad virtuell dator utan att gå igenom processen för att skapa.

DevTest Labs tillhandahåller följande funktioner för utvecklare som arbetar med PaaS-miljöer:

- Använd Resource Manager för att snabbt skapa PaaS-miljöer genom att följa färre än tre enkla steg.
- Välj bland en granskad lista med Resource Manager-mallar, som är konfigurerade och auktoriserade av teamets lead eller central IT.
- Skapa en tom resurs grupp (sandbox) genom att använda en Resource Manager-mall för att utforska Azure inom ramen för ett labb.

DevTest Labs ger också en central IT-kontroll för att kontrol lera avfall, optimera kostnader för resurser och hålla dig inom budgetarna genom att utföra följande uppgifter:  

- Ange automatiska avstängnings-och Autostart-scheman på virtuella datorer.
- Ställer in principer för antalet virtuella datorer som användare kan skapa.
- Ange principer för VM-storlekar och Galleri bilder som användarna väljer bland.
- Spåra kostnader och ställa in mål för labb.
- Få ett meddelande om höga kostnader för labbet så att du kan vidta nödvändiga åtgärder.

DevTest Labs ger följande fördelar med att skapa, konfigurera och hantera miljöer i molnet.

## <a name="cost-control-and-governance"></a>Kostnads kontroll och styrning
DevTest Labs gör det lättare att kontrol lera kostnaderna genom att låta dig utföra följande uppgifter:

- [Ange principer för dina labb](devtest-lab-set-lab-policy.md), till exempel antal virtuella datorer per användare eller per labb. 
- Skapa [principer för att automatiskt stänga av](devtest-lab-set-lab-policy.md) och starta virtuella datorer.
- Håll koll på kostnaderna för virtuella datorer och PaaS-resurser i labbet för att hålla dig inom [din budget](devtest-lab-configure-cost-management.md).
- Håll dig inom kontexten för dina labb så att du inte skapar resurser utanför dem.

## <a name="quickly-get-to-ready-to-test"></a>Kom snabbt till klar att testa
Med DevTest Labs kan du skapa företablerade miljöer som är utrustade med allt ditt team behöver för att utveckla och testa program. Gör bara [anspråk på de miljöer](devtest-lab-add-claimable-vm.md) där den senaste fungerande versionen av programmet är installerad och börja arbeta. Eller använda behållare för ännu snabbare, skapande av Lean-miljö.

## <a name="create-once-use-everywhere"></a>Skapa en gång, Använd överallt
Avbilda och dela PaaS- [miljömallar](devtest-lab-create-environment-from-arm.md) och [artefakter](add-artifact-repository.md) i ditt team eller din organisation – allt i käll kontroll – så att du enkelt kan skapa utvecklings-och test miljöer.

## <a name="worry-free-self-service"></a>Oroa dig kostnads fri självbetjäning
DevTest Labs gör det möjligt för utvecklare och testare att snabbt och enkelt [skapa IaaS-VM](devtest-lab-add-vm.md) -och [PaaS-resurser](devtest-lab-create-environment-from-arm.md) med hjälp av en uppsättning förkonfigurerade resurser.

## <a name="use-iaas-and-paas-resources"></a>Använda IaaS-och PaaS-resurser 
Utvecklare kan också skapa PaaS-resurser, till exempel Azure Service Fabric-kluster, Web Apps funktionen i Azure App Service och SharePoint-grupper med hjälp av Resource Manager-mallar. Om du vill komma igång med PaaS i labb använder du mallarna från den [offentliga miljöns lagrings plats](devtest-lab-configure-use-public-environments.md) eller [ansluter labbet till din egen git-lagringsplats](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Du kan också spåra kostnader på dessa resurser för att hålla dig inom din budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integrera med din befintliga verktygskedjan
Använd färdiga plugin-program eller API för att etablera utvecklings-och testnings miljöer direkt från ditt prioriterade [verktyg för kontinuerlig integrering (CI)](devtest-lab-integrate-ci-cd.md), Integrated Development Environment (IDE) eller automatisk versions pipeline. Du kan också använda det omfattande kommando rads verktyget.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Läs mer om DevTest Labs i [DevTest Labs-koncept](devtest-lab-concepts.md).
- En genom gång med stegvisa instruktioner finns i [Självstudier: Konfigurera ett labb med hjälp av Azure DevTest Labs](tutorial-create-custom-lab.md).