---
title: Regioner som stöder länkade Log Analytics-arbetsytor
description: I den här artikeln beskrivs de region mappningar som stöds mellan ett Automation-konto och en Log Analytics arbets yta som relaterar till vissa funktioner i Azure Automation.
ms.date: 01/21/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 3b744127b51b0e0db63c158feaf463af4cd0bdcf
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704329"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Regioner som stöder länkade Log Analytics-arbetsytor

I Azure Automation kan du aktivera Uppdateringshantering, Ändringsspårning och inventering och Starta/stoppa virtuella datorer när de inte används funktioner för dina servrar och virtuella datorer. Dessa funktioner är beroende av en Log Analytics arbets yta och måste därför länka arbets ytan till ett Automation-konto. Men endast vissa regioner stöds för att länka ihop dem. I allmänhet gäller mappningen *inte* om du planerar att länka ett Automation-konto till en arbets yta som inte har dessa funktioner aktiverade.

Den här artikeln innehåller mappningar som stöds för att kunna aktivera och använda de här funktionerna i ditt Automation-konto.

Mer information finns i [Log Analytics arbets yta och Automation-konto](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mappningar som stöds

> [!NOTE]
> Som du ser i följande tabell kan endast en mappning finnas mellan Log Analytics och Azure Automation.

Följande tabell visar de mappningar som stöds:

|**Region för Log Analytics arbets yta**|**Azure Automation region**|
|---|---|
|**USA**||
|Östra USA<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|Platsen eastus|
|WestUS|WestUS|
|WestUS2|WestUS2|
|Centra lus|Centra lus|
|Usasödracentrala|Usasödracentrala|
|WestCentralUS|WestCentralUS|
|**Kanada**||
|Indiensödra|Indiensödra|
|**Asien och stillahavsområdet**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|Asienöstra|Asienöstra|
|Sydostasien|Sydostasien|
|Kanada|Kanada|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|Japanöstra|Japanöstra|
|**Europa**||
|Europanorra|Europanorra|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|Västeuropa|Västeuropa|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> -mappning av öster för Log Analytics arbets ytor till Automation-konton är inte en exakt mappning mellan regioner och regioner, men är rätt mappning.

<sup>2</sup> EastUS2-mappningen för Log Analytics-arbetsytor till Automation-konton är inte en exakt mappning mellan regioner och regioner, men är rätt mappning.

<sup>3</sup> i den här regionen stöds endast uppdateringshantering och andra funktioner som ändringsspårning och inventering är inte tillgängliga för tillfället.

## <a name="unlink-a-workspace"></a>Ta bort länk till en arbets yta

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken för ditt konto direkt från Azure Portal. Innan du fortsätter måste du först [ta bort](move-account.md#remove-features) Uppdateringshantering, ändringsspårning och inventering och starta/stoppa virtuella datorer när de inte används om du använder dem. Om du inte tar bort dem kan du inte slutföra länknings åtgärden.

När funktionerna har tagits bort kan du följa stegen nedan för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa funktioner, inklusive tidigare versioner av Azure SQL Monitoring-lösningen, kan ha skapat Automation-till gångar som måste tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal. På sidan Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

2. På sidan ta bort länk till arbets yta väljer du **ta bort arbets ytan ta bort länk**. Du får ett meddelande om att du måste kontrol lera om du vill fortsätta.

3. Medan Azure Automation tar bort länkningen av kontot från arbets ytan Log Analytics, kan du följa förloppet under **meddelanden** på menyn.

4. Om du använde Uppdateringshantering kanske du vill ta bort följande objekt som inte längre behövs:

    * Uppdaterings scheman: varje har ett namn som matchar en uppdaterings distribution som du har skapat.
    * Hybrid Worker-grupper som har skapats för funktionen: var och en har ett namn som liknar  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Om du använder Starta/stoppa virtuella datorer när de inte används kan du, om du vill, ta bort följande objekt som inte längre behövs:

    * Starta och stoppa virtuella dator Runbook-scheman
    * Starta och stoppa VM-Runbooks
    * Variabler

Du kan också ta bort länken för arbets ytan från ditt Automation-konto på arbets ytan.

1. I arbets ytan väljer du **Automation-konto** under **relaterade resurser**.
2. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Uppdateringshantering i [uppdateringshantering översikt](../update-management/overview.md).
* Läs mer om Ändringsspårning och inventering i [ändringsspårning och inventerings översikt](../change-tracking/overview.md).
* Läs mer om Starta/stoppa virtuella datorer när de inte används i [Starta/stoppa virtuella datorer när de inte används översikt](../automation-solution-vm-management.md).