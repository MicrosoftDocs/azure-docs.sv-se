---
title: Felsöka vanliga Azure Automanage onboarding-fel
description: Vanliga onboarding-fel vid automatisk registrering och hur du felsöker dem
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862983"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Felsöka vanliga onboarding-fel vid automatisk registrering
Automanage kan misslyckas med att registrera en dator i tjänsten. Det här dokumentet beskriver hur du felsöker distributionsfel, delar några vanliga orsaker till varför distributioner kan misslyckas och beskriver potentiella nästa steg för att åtgärda problemet.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributionsfel
Om du skapar en dator för automatisk Azure Resource Manager distributionen. Mer information finns i distributionen för ytterligare information om varför det misslyckades. Det finns länkar till distributionerna i den utfällda feldetaljinformation som visas nedan.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Information om automanagefel utfällt.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Kontrollera distributionerna för resursgruppen som innehåller den felande virtuella datorn
Utfällt fel innehåller en länk till distributionerna i resursgruppen som innehåller den dator som misslyckades med onboarding. Den utfälla innehåller också ett prefixnamn som du kan använda för att filtrera distributioner med. Om du klickar på distributionslänken kommer du till bladet distributioner, där du sedan kan filtrera distributioner för att se Automanage deployments to your machine (Automanage deployments to your machine). Om du distribuerar över flera regioner ser du till att du klickar på distributionen i rätt region.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Kontrollera distributionerna för prenumerationen som innehåller den misslyckade virtuella datorn
Om du inte ser några fel i resursgruppsdistributionen är nästa steg att titta på distributionerna i din prenumeration som innehåller den virtuella dator som misslyckades med registrering. Klicka på **länken Distributioner för prenumeration** i utfällt fel och filtrera distributioner med filtret **Automanage-DefaultResourceGroup.** Använd resursgruppens namn från felbladet för att filtrera distributioner. Distributionsnamnet får ett regionsnamn. Om du distribuerar över flera regioner ser du till att du klickar på distributionen i rätt region.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Kontrollera distributioner i en prenumeration som är länkad till en Log Analytics-arbetsyta
Om du inte ser några misslyckade distributioner i resursgruppen eller prenumerationen som innehåller den virtuella datorn som misslyckades, och om den virtuella datorn som misslyckades är ansluten till en Log Analytics-arbetsyta i en annan prenumeration, går du till prenumerationen som är länkad till Log Analytics-arbetsytan och söker efter misslyckade distributioner.

## <a name="common-deployment-errors"></a>Vanliga distributionsfel

Fel |  Åtgärd
:-----|:-------------|
Fel med otillräcklig behörighet vid automatisk kontomanagemanering | Det här felet kan inträffa om du nyligen har flyttat en prenumeration som innehåller ett nytt automanagekonto till en ny klientorganisation. Stegen för att lösa det här felet finns [här](./repair-automanage-account.md).
Arbetsyteregionen matchar inte regionmappningskraven | Automanage kunde inte publicera datorn eftersom Den Log Analytics-arbetsyta som datorn är länkad till för närvarande inte är mappad till en Automation-region som stöds. Kontrollera att din befintliga Log Analytics-arbetsyta och Automation-kontot finns i en [regionmappning som stöds.](../automation/how-to/region-mappings.md)
"Åtkomst nekad på grund av tilldelningsnekande med namnet "Tilldelning av systemnekande som skapats av hanterat program" | En [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) skapades på resursen, vilket förhindrade att Automanage kom åt resursen. Den här denyAssignment kan ha skapats av antingen en [skiss](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) eller ett [hanterat program](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"OS-information: Name='(null)', ver='(null)', agentstatus='Not Ready'." | Kontrollera att du kör en [lägsta agentversion](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)som stöds , att agenten körs ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) och [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) och att agenten är uppdaterad ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) och [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Det går inte att fastställa operativsystemet för vm-operativsystemets namn:, ver . Kontrollera att VM-agenten körs, att den aktuella statusen är Klar." | Kontrollera att du kör en [lägsta agentversion som](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)stöds , att agenten körs ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) och [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) och att agenten är uppdaterad ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) och [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Den virtuella datorn rapporterade ett fel vid bearbetning av tillägget "IaaSAntimalware" | Se till att du inte redan har något annat program mot skadlig programvara/antivirusprogram installerat på den virtuella datorn. Kontakta supporten om det misslyckas.
ASC-arbetsyta: Automanage stöder för närvarande inte Log Analytics-tjänsten på _plats_. | Kontrollera att den virtuella datorn finns i en [region som stöds.](./automanage-virtual-machines.md#supported-regions)
Malldistributionen misslyckades på grund av en principöverträdelse. Mer information finns i informationen. | Det finns en princip som förhindrar att Automanage kan registrera den virtuella datorn. Kontrollera de principer som tillämpas på din prenumeration eller resursgrupp som innehåller den virtuella dator som du vill registrera för automatiskhantering.
"Tilldelningen misslyckades. det finns ingen ytterligare information tillgänglig" | Öppna ett ärende med Microsoft Azure support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)