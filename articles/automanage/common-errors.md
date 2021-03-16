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
ms.openlocfilehash: 2bdf04143121e1286ffc7bfa86b4a9ee291ae6ef
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561874"
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
"Åtkomst nekad på grund av neka-tilldelningen med namnet" system neka tilldelning skapad av det hanterade programmet " | En [roleassignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) skapades på din resurs som förhindrade autohantering från att komma åt din resurs. Detta kan ha orsakats av antingen en [skiss](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) eller ett [hanterat program](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"Tilldelningen misslyckades; Det finns ingen ytterligare information tillgänglig " | Öppna ett ärende med Microsoft Azure support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)