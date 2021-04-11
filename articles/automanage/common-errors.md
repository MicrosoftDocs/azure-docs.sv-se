---
title: Felsök vanliga problem med att hantera onboarding i Azure
description: Vanliga fel vid autohantering av registrerings fel och fel sökning
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 3c9f1b76bb707a296da00ac503482efe6a22385b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278345"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Felsök vanliga fel vid autohantering av onboarding
Den automatiska hanteringen kan Miss lyckas med att publicera en dator på tjänsten. Det här dokumentet innehåller information om hur du felsöker distributions fel, delar några vanliga orsaker till att distributioner kan Miss lyckas och beskriver potentiella nästa steg i minskningen.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel
När en dator registreras på autohantering leder det till att en Azure Resource Manager distribution skapas. Mer information finns i distributionen för mer information om varför den misslyckades. Det finns länkar till distributionerna i disfällbar information som visas i bilden nedan.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Utfällbar information om autostyrning av problem.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Kontrol lera distributionerna för resurs gruppen som innehåller den felande virtuella datorn
Fel utfällning innehåller en länk till distributionerna i resurs gruppen som innehåller den dator som inte kunde onboarding. Utfällaren innehåller också ett prefix som du kan använda för att filtrera distributioner med. Genom att klicka på distributions länken går du till bladet distributioner, där du sedan kan filtrera distributioner för att se Hantera distributioner automatiskt på datorn. Om du distribuerar över flera regioner måste du se till att du klickar på distributionen i rätt region.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Kontrol lera distributionerna för prenumerationen som innehåller den felande virtuella datorn
Om du inte ser några fel i resurs grupps distributionen är nästa steg att titta på distributionerna i prenumerationen som innehåller den virtuella datorn som inte kunde registreras. Klicka på länken **distributioner för prenumeration** i felfällingen och filtrera distributioner med hjälp av filtret **automanage-DefaultResourceGroup** . Använd resurs grupps namnet från bladet om du vill filtrera distributioner. Distributions namnet kommer att suffixs med ett region namn. Om du distribuerar över flera regioner måste du se till att du klickar på distributionen i rätt region.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Kontrol lera distributioner i en prenumeration som är länkad till en Log Analytics arbets yta
Om du inte ser några misslyckade distributioner i resurs gruppen eller prenumerationen som innehåller den virtuella datorn som misslyckades, och om den virtuella datorn har anslutits till en Log Analytics arbets yta i en annan prenumeration, går du till den prenumeration som är länkad till din Log Analytics arbets yta och kontrollerar om det finns misslyckade distributioner.

## <a name="common-deployment-errors"></a>Vanliga distributionsfel

Fel |  Åtgärd
:-----|:-------------|
Fel vid autohantering av konto för otillräcklig behörighet | Det här felet kan inträffa om du nyligen har flyttat en prenumeration som innehåller ett nytt konto för autohantering till en ny klient. Steg för att lösa det här felet finns [här](./repair-automanage-account.md).
Området för arbets ytan matchar inte region mappnings kraven | Det gick inte att publicera datorn eftersom den Log Analytics arbets ytan som datorn är länkad till inte är mappad till en Automation-region som stöds. Se till att din befintliga Log Analytics arbets yta och Automation-konto finns i en [region mappning som stöds](../automation/how-to/region-mappings.md).
"Åtkomst nekad på grund av neka-tilldelningen med namnet" system neka tilldelning skapad av det hanterade programmet " | En [roleassignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) har skapats på din resurs, vilket förhindrade autohantering från att komma åt din resurs. Den här Roleassignment kan ha skapats av en [skiss](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) eller ett [hanterat program](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"OS-information: Name =" (null) ", ver =" (null) ", agent status =" inte redo ". | Kontrol lera att du kör en [agent version som stöds](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)och att agenten körs ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) och [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) och att agenten är uppdaterad ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) och [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Det gick inte att fastställa operativ systemet för OS-namnet för den virtuella datorn:, ver. Kontrol lera att den virtuella dator agenten körs, att aktuell status är klar. " | Kontrol lera att du kör en [agent version som stöds](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)och att agenten körs ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) och [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) och att agenten är uppdaterad ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) och [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).

"Det uppstod ett problem med den virtuella datorn vid bearbetning av tillägget" IaaSAntimalware "| Se till att du inte har något annat program mot skadlig kod/antivirus som redan är installerat på den virtuella datorn. Kontakta supporten om det inte går.
ASC-arbets yta: automanage stöder för närvarande inte Log Analytics tjänsten på _platsen_. | Kontrol lera att den virtuella datorn finns i en [region som stöds](./automanage-virtual-machines.md#supported-regions).
Mallen kunde inte distribueras på grund av en princip överträdelse. Mer information finns i informationen. | Det finns en princip som förhindrar autohantering från att publicera den virtuella datorn. Kontrol lera de principer som tillämpas på din prenumeration eller resurs grupp som innehåller den virtuella dator som du vill publicera för att hantera.
"Tilldelningen misslyckades; Det finns ingen ytterligare information tillgänglig | Öppna ett ärende med Microsoft Azure support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)