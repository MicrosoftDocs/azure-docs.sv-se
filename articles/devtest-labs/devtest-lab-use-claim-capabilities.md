---
title: Använda anspråks funktioner i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om olika scenarier för att använda anspråk/fordrings funktioner i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 73ed3c0b94a66f5d17b5c8e2561c65bb48579aa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476537"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Använd anspråks funktioner i Azure DevTest Labs
Tjänsten Azure DevTest Labs förbättrar effektiviteten och effektiviteten hos utvecklare och testare. Den här artikeln fokuserar på möjligheten att begära eller frigöra virtuella datorer i Azure DevTest Labs. Det visar även olika sätt som den här funktionen förbättrar användar upplevelsen. Innan du tittar på olika scenarier där den här funktionen kan användas kan vi titta **på vad det** är och hur det fungerar.

## <a name="claimable-machines"></a>Anspråks bara datorer
En anspråks bara dator är en virtuell dator som skapas i ett labb utan någon ägare. När datorn har ansökts har användaren ett fullständigt utbud av alternativ för den virtuella datorn. När en användare hävdar en dator görs några ändringar. Den virtuella datorn flyttas från listan över **virtuella datorer** som kan frigöras till listan **mina virtuella datorer** i Azure Portal. 

Användaren kan ansluta till den virtuella datorn, anpassa artefakter, starta om, stoppa eller frigöra datorn. Det finns ett par olika sätt att göra en VM-begäran:

- Skapa en dator och frigör den så att den flyttas till den fordrings bara poolen. 
- Skapa en virtuell dator och placera den i den delade poolen med [Avancerade inställningar](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Det finns två fall där anspråks-/anspråks funktionerna kan användas effektivt. Det första fallet kräver mer forethought och planering, för att kunna utformas och köras på rätt sätt. Och det andra är en mer situation. Här följer några exempel på olika fall.

## <a name="designed-use-of-claimable-machines"></a>Utformad användning av anspråks bara datorer

- **Program utveckling/testning:** Tillåt utvecklare eller testare att bli mer produktiva genom att låta konfigurerade datorer vara klara och i ett tillstånd som inte är anspråk. Med hjälp av en uppsättning virtuella datorer med olika konfigurationer, nödvändiga verktyg och med den senaste koden kan användare anställa en virtuell dator och börja arbeta utan att behöva ägna tid åt att konfigurera en dator. Innan de virtuella datorerna begärs, är datorerna etablerade, men avstängningar minimerar kostnaden för att ha datorer som används mindre ofta. När de virtuella datorerna behövs anlitar användaren bara den virtuella datorn som startar datorn. Alternativet för att frigöra är inte användbart i det här fallet eftersom det ofta är enklare och billigare att skapa en ny virtuell dator.
- **Klass rum/labb:** Ha förkonfigurerade virtuella datorer för en klass eller ett labb så att eleverna omedelbart kan ansluta till en dator med hjälp av Azure Portal.  När en student hävdar anspråk på en virtuell dator ser labbet till att ingen kan göra anspråk på samma dator. Genom att automatisera den här processen ser du till att det nödvändiga antalet datorer med den angivna miljön är tillgängliga. Om eleverna inte visar eller körs sent kan de datorer som inte omfattas vara tillgängliga tills sessionen är över med minimal kostnad. Alternativet för att frigöra är inte lika effektivt i det här scenariot eftersom den virtuella datorn är i ett okänt tillstånd när den tidigare användaren är färdig.
- **Demonstrationer:** Använd datorer för demonstrationer, där datorerna i labbet har kon figurer ATS med vissa miljöer. Den här funktionen är användbar när flera personer kan få en demonstration på samma gång eller vid slumpmässiga tidpunkter, till exempel på en konferens. Alternativet för att frigöra kan vara användbart i den här situationen eftersom demonstrationen inte bör ändra tillståndet för datorn, så att användarna kan återställa en virtuell dator till den fordrings bara poolen för nästa demonstration. Med den ej anspråks Gill dator som etableras och det uppstår en minimal kostnad, kan virtuella datorer ligga kvar i labbet under längre tids perioder.
- **Tillfälliga/kontrakterande arbetare:** Tillåt användare att använda en dator. När de lämnar, returnerar de den virtuella datorn till den anspråk bara poolen utan data förlust. När den virtuella datorn har frigjorts kan en annan användare göra anspråk på den virtuella datorn och fortsätta eller granska datorn för ytterligare information.
- **I allmänhet:** Möjligheten att ha en enda källa som automatiskt konfigurerar och distribuerar virtuella datorer, på en särskild takt, är användbar i många olika situationer. Det finns flera olika situationer där funktionen anspråk/frigöre hjälper användare att bli mer effektiva genom att skapa en automatiserad process för att bygga de virtuella datorerna i ett anspråks tillstånd med en angiven konfiguration. Konfiguration (er) kan innehålla olika operativ system, språk, diskar eller [annan program vara (artefakter)](devtest-lab-artifact-author.md) beroende på dina behov. Möjligheten att göra anspråk på en virtuell dator från labbet gör det möjligt för labb användaren att få ett korrekt konfigurerat system utan att behöva ägna tid åt att konfigurera datorn. Labb hanteraren kan använda det begärda läget för de virtuella datorerna för att förbättra antalet datorer som genereras, rensa datorer och fastställa prioritet för konfigurationer. [Avbildnings fabriken](image-factory-create.md) är ett användbart exempel på en automatiserad process för att bygga virtuella datorer och avbildningar för flera labb. Skripten kan ändras för att köra någon av följande situationer med lämpliga ändringar eller användas som referens för att skapa ett anpassat system.

## <a name="situational-use-of-claimable-machines"></a>Användning av användnings bara datorer i situationer

- Använd funktionen anspråk/avsluten anspråk som gör det möjligt för användarna att skicka kontroll över datorer från en till en annan och behöver inte uttryckligen känna till vilka som kommer att hämta datorn härnäst.
- Utveckling, testning och fel sökning av ett scenario där en speciell dator konfiguration kan återskapa ett fel, kan datorn återtas av en annan utvecklare som kan ansöka om datorn och fortsätta arbetet. Den här funktionen är särskilt användbar när fler personer arbetar på distans i olika delar av världen. 
- Grupp medlemmar kan arbeta med en enda miljö. Du kan till exempel manuellt konfigurera en komplex miljö som inte kan automatiseras eller skapa resurser som bara kan hantera ändringar för en enskild inmatare, t. ex. bilder. Tidigare hanterades det här problemet genom att en särskild dator körs. Den begärda funktionen är en förbättring av den manuella processen genom att ha inbyggd användar åtkomst kontroll och visuell identifiering när det är tillgängligt. När den virtuella datorn har frigjorts har den virtuella datorn avetablerades för att minska kostnaderna.
- Ha en datadisk som är ansluten till en virtuell dator. Varje disk upp till ~ 1 TB data tillåter att en stor mängd data skickas utan att behöva kopiera eller duplicera data. Den virtuella datorn skapas ursprungligen med en ansluten disk som hade stor mängd data.  Alla användare kan sedan angripa datorn och komma åt data. När du är färdig kan du frigöra den virtuella datorn så att andra användare kan använda datorn.

Det finns vissa villkor för att använda anspråk bara datorer, vilket vanligt vis ger åtkomst till datorn. Om datorn är domänansluten måste användaren som ansöker datorn redan ha beviljats åtkomst, vanligt vis görs det genom att bevilja åtkomst till en grupp som omfattar alla användare i labbet när den virtuella datorn skapas. Om datorn inte är domänansluten måste **återställnings** artefakten för den virtuella datorn i den offentliga lagrings platsen köras för att lägga till användaren som administratör.  Artefakter kan tillämpas även efter att datorn har startats eller begärts.

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [skapa och hantera virtuella datorer som går att frigöra i Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
