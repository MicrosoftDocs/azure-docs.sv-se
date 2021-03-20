---
title: Populära scenarier för att använda Azure DevTest Labs
description: Den här artikeln innehåller de vanligaste scenarierna för att använda Azure DevTest Labs och två allmänna sökvägar för att börja använda tjänsten i din organisation.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481603"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populära scenarier för att använda Azure DevTest Labs
DevTest Labs kan konfigureras för att uppfylla olika krav, beroende på företagets behov.  I den här artikeln beskrivs de populära scenarierna. Varje scenario täcker fördelarna med DevTest Labs och resurser som används för att implementera dessa scenarier.  

- Developer-datorer
- Test miljöer
- Utbilda sessioner, praktiska labb övningar och Hackathon kickar
- Begränsade undersökningar
- Klassrumslabb

## <a name="developer-desktops"></a>Developer-datorer
Utvecklare har ofta olika krav på utvecklings datorer för olika projekt. Med DevTest Labs kan utvecklare ha till gång till virtuella datorer på begäran som är konfigurerade för att passa de vanligaste scenarierna. DevTest Labs ger följande fördelar:

- Organisationer kan tillhandahålla vanliga utvecklings datorer för att säkerställa en enhetlighet i team.
- Utvecklare kan snabbt etablera sina utvecklings maskiner på begäran eller göra [anspråk på en befintlig förkonfigurerad dator](devtest-lab-add-claimable-vm.md).
- Utvecklare kan etablera resurser på ett självbetjänings sätt utan att behöva behörigheter på prenumerations nivå.
- IT-administratören kan i [förväg definiera nätverk sto pol Ogin](devtest-lab-configure-vnet.md) och utvecklare kan direkt använda dem på ett enkelt och intuitivt sätt utan att behöva särskild åtkomst.
- Utvecklare kan enkelt [Anpassa](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sina utvecklings datorer efter behov.
- Administratörer kan kontrol lera kostnaderna genom att se till att:
    - Utvecklare [kan inte få fler virtuella datorer](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) än de behöver för utveckling
    - [Virtuella datorer stängs av när de](devtest-lab-set-lab-policy.md#set-auto-shutdown) inte används
    - [Tillåter endast en delmängd av storlekarna för virtuella dator instanser](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) för de speciella labben
    - [Hantera kostnads mål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Läs mer i [använda Azure DevTest Labs för utvecklare](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Test miljöer
Att skapa och hantera test miljöer i ett företag kan kräva stor ansträngning. Med DevTest Labs kan du enkelt skapa, uppdatera eller duplicera test miljöer. Det gör det möjligt för grupper att komma åt en helt konfigurerad miljö när den behövs. I det här scenariot ger DevTest Labs följande fördelar:

- Organisationer kan tillhandahålla vanliga testnings miljöer som garanterar konsekvens i olika team.
- Testare kan testa den senaste versionen av programmet genom att snabbt konfigurera Windows-och Linux-miljöer med hjälp av åter användnings bara mallar.
- Administratörer kan ansluta labbet till Azure-DevOps för att aktivera DevOps-scenarier
- Labb ägare kan kontrol lera kostnader genom att se till att:
    - [Virtuella datorer i miljöer stängs av när de](devtest-lab-set-lab-policy.md#set-auto-shutdown) inte används
    - [Tillåter endast en delmängd av storlekarna för virtuella dator instanser för](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) de speciella labben
    - [Hantera kostnads mål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Mer information finns i [använda Azure DevTest Labs för VM-och PaaS test miljöer](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Begränsade undersökningar
Utvecklare undersöker ofta olika tekniker eller infrastruktur design. Som standard skapas alla miljöer som skapats med DevTest Labs i sin egen resurs grupp. DevTest Labs-användaren får bara Läs åtkomst till dessa resurser. För utvecklare som behöver mer kontroll kan dock en inställning för hela labbet uppdateras för att ge [deltagar rättigheter](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) till den ursprungliga DevTest Labs-användaren för varje miljö de skapar.  Med DevTest Labs kan utvecklare få deltagare behörighet automatiskt till miljöer som de skapar i labbet.  Med det här scenariot kan utvecklare lägga till och/eller ändra Azure-resurser när de behöver sina utvecklings-eller test miljöer. På sidan [kostnad per resurs](devtest-lab-configure-cost-management.md#view-cost-by-resource) kan labb ägare spåra kostnaden för varje miljö som används för undersökningar.

Mer information finns i [ange åtkomst behörighet till en miljö resurs grupp](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Utbildning, praktiska labb övningar och Hackathon kickar 
Ett labb i Azure DevTest Labs fungerar som en bra behållare för tillfälliga aktiviteter som workshops, praktiska labb övningar, utbildningar eller Hackathon kickar.  Med tjänsten kan du skapa ett labb där du kan ange anpassade mallar som varje fordonsbaserad kan använda för att skapa identiska och isolerade miljöer för utbildning. I det här scenariot ger DevTest Labs följande fördelar:

- [Principer](devtest-lab-set-lab-policy.md) säkerställer att eleverna bara får antalet resurser, till exempel virtuella datorer, som de behöver.
- Förkonfigurerade och skapade datorer [åberopas](devtest-lab-add-claimable-vm.md) med en åtgärd från tågen.
- Labben delas med eleverna genom att få åtkomst till [URL: en för labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Förfallo datum](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) på virtuella datorer se till att datorerna tas bort efter att de inte längre behövs.
- Det är enkelt att [ta bort ett labb](devtest-lab-delete-lab-vm.md#delete-a-lab) och alla [relaterade resurser](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) när utbildningen är över.

Mer information finns i [använda Azure DevTest Labs för utbildning](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Koncept bevis jämfört med skalad distribution
När du har bestämt dig för att utforska DevTest Labs finns det två allmänna sökvägar: bevis på koncept vs skalad distribution.  

En **skalad distribution** består av veckor/månader med att granska och planera med avsikt att distribuera DevTest Labs till hela företaget som har hundratals eller tusentals utvecklare.

En **proof of Concept** -distribution fokuserar på en koncentrerad ansträngning från ett enda team för att upprätta ett organisations värde. Även om det kan vara frestande att tänka på en skalad distribution, är det bra att köra metoden oftare än koncept bevis alternativet. Därför rekommenderar vi att du startar små, lär dig från det första teamet, upprepar samma tillvägagångs sätt med två till tre ytterligare team och planerar sedan för en skalad distribution utifrån den kunskap som du har fått. Vi rekommenderar att du väljer ett eller två team för att kunna identifiera sina scenarier (utvecklings miljö vs test miljöer), dokumentera deras aktuella användnings fall och distribuera DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [DevTest Labs-koncept](devtest-lab-concepts.md)
- [Vanliga frågor och svar om DevTest Labs](devtest-lab-faq.md)

