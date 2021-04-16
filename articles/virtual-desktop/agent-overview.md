---
title: Kom igång med Windows Virtual Desktop Agent
description: En översikt över Windows Virtual Desktop agent och uppdateringsprocesser.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520113"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Kom igång med Windows Virtual Desktop Agent

I Windows Virtual Desktop Service-ramverket finns det tre huvudkomponenter: fjärrskrivbordsklienten, tjänsten och de virtuella datorerna. De här virtuella datorerna finns i kundprenumerationen där Windows Virtual Desktop och agentens startlastare är installerade. Agenten fungerar som mellanliggande kommunicerare mellan tjänsten och de virtuella datorerna, vilket möjliggör anslutning. Om du har problem med agentinstallationen, uppdateringen eller konfigurationen kan de virtuella datorerna därför inte ansluta till tjänsten. Agentstartladdaren är den körbara fil som läser in agenten. 

Den här artikeln ger dig en kort översikt över agentens installation och uppdateringsprocesser.

>[!NOTE]
>Den här dokumentationen gäller inte för FSLogix-agenten eller fjärrskrivbordsklientagenten.


## <a name="initial-installation-process"></a>Inledande installationsprocess

Agenten Windows Virtual Desktop installeras från början på ett av två sätt. Om du etablerar virtuella datorer (VM) i Azure Portal och Azure Marketplace installeras agenten och agentens startlast automatiskt. Om du etablerar virtuella datorer med Hjälp av PowerShell måste du manuellt ladda ned MSI-filerna för agenten och agentens startlastare när du skapar en [Windows Virtual Desktop-värdpool med PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). När agenten har installerats installeras Windows Virtual Desktop sida-vid-sida-stacken och Genève-övervakningsagenten. Stackkomponenten sida vid sida krävs för att användarna på ett säkert sätt ska kunna upprätta omvända server-till-klient-anslutningar. Genèveövervakningsagenten övervakar agentens hälsotillstånd. Alla tre av dessa komponenter är nödvändiga för att slutanvändaranslutningen ska fungera korrekt.

>[!IMPORTANT]
>Om du vill Windows Virtual Desktop, sida vid sida-stacken och Genève-övervakningsagenten måste du avblockera alla URL:er som anges i listan [Över url:er som krävs.](safe-url-list.md#virtual-machines) Avblockering av dessa URL:er krävs för att använda Windows Virtual Desktop tjänsten.

## <a name="agent-update-process"></a>Agentuppdateringsprocess

Tjänsten Windows Virtual Desktop uppdaterar agenten när en uppdatering blir tillgänglig. Agentuppdateringar kan innehålla nya funktioner eller korrigeringar för tidigare problem. Du måste alltid ha den senaste stabila versionen av agenten installerad så att dina virtuella datorer inte förlorar anslutningen eller säkerheten. När den första versionen av Windows Virtual Desktop-agenten har installerats frågar agenten regelbundet Windows Virtual Desktop-tjänsten för att avgöra om det finns en nyare version av agenten, stacken eller övervakningskomponenten tillgänglig. Om en nyare version av någon av komponenterna redan har distribuerats installeras den uppdaterade komponenten automatiskt av flygsystemet.

Nya versioner av agenten distribueras med jämna mellanrum under veckotid till alla Azure-prenumerationer. Dessa uppdateringsperioder kallas "flighter". När en flygresa inträffar kan du se att virtuella datorer i värdpoolen får agentuppdateringen vid olika tidpunkter. Alla VM-agenter i alla prenumerationer uppdateras i slutet av distributionsperioden. Den Windows Virtual Desktop systemet förbättrar tjänstens tillförlitlighet genom att säkerställa stabiliteten och kvaliteten på agentuppdateringen.


Andra viktiga saker som du bör tänka på:

- Eftersom virtuella datorer i värdpoolen kan ta emot agentuppdateringar vid olika tidpunkter måste du kunna se skillnaden mellan flygproblem och misslyckade agentuppdateringar. Om du går till händelseloggarna för den virtuella datorn **på Loggboken** Windows Logs Application och ser en händelse med etiketten  >    >   "ID 3277" innebär det att agentuppdateringen inte fungerade. Om du inte ser den händelsen är den virtuella datorn i en annan flygresa och kommer att uppdateras senare.
- När Genève-övervakningsagenten uppdateras till den senaste versionen finns och inaktiveras den gamla GenèveTask-uppgiften innan du skapar en ny uppgift för den nya övervakningsagenten. Den tidigare versionen av övervakningsagenten tas inte bort om den senaste versionen av övervakningsagenten har ett problem som kräver att du återgår till den tidigare versionen för att åtgärda problemet. Om den senaste versionen har problem återaktiveras den gamla övervakningsagenten för att fortsätta att leverera övervakningsdata. Alla versioner av övervakaren som är tidigare än den senaste som du installerade innan uppdateringen tas bort från den virtuella datorn.
- Den virtuella datorn behåller tre versioner av stacken sida vid sida i taget. På så sätt kan du snabbt återställa om något går fel med uppdateringen. Den tidigaste versionen av stacken tas bort från den virtuella datorn när stacken uppdateras.

Agentuppdateringen varar normalt 2–3 minuter på en ny virtuell dator och bör inte orsaka att den virtuella datorn förlorar anslutningen eller stängs av. Den här uppdateringsprocessen gäller både för Windows Virtual Desktop (klassisk) och den senaste versionen av Windows Virtual Desktop med Azure Resource Manager.

## <a name="next-steps"></a>Nästa steg

Nu när du har en bättre förståelse för Windows Virtual Desktop agenten finns här några resurser som kan hjälpa dig:

- Om du har agent- eller anslutningsrelaterade problem kan du läsa [felsökningsguiden Windows Virtual Desktop agentproblem.](troubleshoot-agent.md)
