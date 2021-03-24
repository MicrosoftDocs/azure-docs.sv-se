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
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955558"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Felsök vanliga fel vid autohantering av onboarding
Den automatiska hanteringen kan Miss lyckas med att publicera en dator på tjänsten. Det här dokumentet innehåller information om hur du felsöker distributions fel, delar några vanliga orsaker till att distributioner kan Miss lyckas och beskriver potentiella nästa steg i minskningen.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel
När en dator registreras på autohantering leder det till att en Azure Resource Manager distribution skapas. Om onboarding Miss lyckas kan det vara bra att kontakta distributionen för mer information om varför den misslyckades. Det finns länkar till distributionerna i disfällbar information som visas i bilden nedan.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Utfällbar information om autostyrning av problem.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Kontrol lera distributionerna för resurs gruppen som innehåller den felande virtuella datorn
Fel utfällning innehåller en länk till distributionerna i den resurs grupp som innehåller den dator som inte kunde registreras och ett prefixvärde som du kan använda för att filtrera distributioner med. När du klickar på länken går du till bladet distributioner, där du sedan kan filtrera distributioner för att se Hantera distributioner automatiskt på datorn. Om du distribuerar över flera regioner måste du se till att du klickar på distributionen i rätt region.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Kontrol lera distributionerna för prenumerationen som innehåller den felande virtuella datorn
Om du inte ser några fel i resurs grupps distributionen är nästa steg att titta på distributionerna i prenumerationen som innehåller den virtuella datorn som inte kunde registreras. Klicka på länken **distributioner för prenumeration** i felfällingen och filtrera distributioner med hjälp av filtret **automanage-DefaultResourceGroup** . Använd resurs grupps namnet från bladet om du vill filtrera distributioner. Distributions namnet kommer att suffixs med ett region namn. Om du distribuerar över flera regioner måste du se till att du klickar på distributionen i rätt region.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Kontrol lera distributioner i en prenumeration som är länkad till en Log Analytics arbets yta
Om du inte ser några misslyckade distributioner i resurs gruppen eller prenumerationen som innehåller den virtuella datorn som misslyckades, och om den virtuella datorn har anslutits till en Log Analytics arbets yta i en annan prenumeration, går du till den prenumeration som är länkad till din Log Analytics arbets yta och kontrollerar om det finns misslyckade distributioner.

## <a name="common-deployment-errors"></a>Vanliga distributionsfel

Fel |  Åtgärd
:-----|:-------------|
Fel vid autohantering av konto för otillräcklig behörighet | Detta kan inträffa om du nyligen har flyttat en prenumeration som innehåller ett nytt konto för autohantering till en ny klient. Steg för att lösa detta finns [här](./repair-automanage-account.md).
Området för arbets ytan matchar inte region mappnings kraven | Det gick inte att publicera datorn utan den Log Analytics arbets ytan som datorn är länkad till är inte mappad till en Automation-region som stöds. Se till att din befintliga Log Analytics arbets yta och Automation-konto finns i en [region mappning som stöds](../automation/how-to/region-mappings.md).
"Åtkomst nekad på grund av neka-tilldelningen med namnet" system neka tilldelning skapad av det hanterade programmet " | En [roleassignment](../role-based-access-control/deny-assignments.md) skapades på din resurs som förhindrade autohantering från att komma åt din resurs. Detta kan ha orsakats av antingen en [skiss](../governance/blueprints/concepts/resource-locking.md) eller ett [hanterat program](../azure-resource-manager/managed-applications/overview.md).
"OS-information: Name =" (null) ", ver =" (null) ", agent status =" inte redo ". | Kontrol lera att du kör en [agent version som stöds](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)och att agenten körs ([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) och [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) och att agenten är uppdaterad ([Linux](../virtual-machines/extensions/update-linux-agent.md) och [Windows](../virtual-machines/extensions/agent-windows.md)).
"Den virtuella datorn rapporterade ett problem när tillägget" IaaSAntimalware "bearbetades | Se till att du inte har något annat program mot skadlig kod/antivirus som redan är installerat på den virtuella datorn. Kontakta supporten om det inte går.
ASC-arbets yta: automanage stöder för närvarande inte Log Analytics tjänsten på _platsen_. | Kontrol lera att den virtuella datorn finns i en [region som stöds](./automanage-virtual-machines.md#supported-regions).
Mallen kunde inte distribueras på grund av en princip överträdelse. Mer information finns i informationen. | Det finns en princip som förhindrar autohantering från att publicera den virtuella datorn. Kontrol lera de principer som tillämpas på din prenumeration eller resurs grupp som innehåller den virtuella dator som du vill publicera för att hantera.
"Tilldelningen misslyckades; Det finns ingen ytterligare information tillgänglig " | Öppna ett ärende med Microsoft Azure support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)