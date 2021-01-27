---
title: Felsöka Azure Automation konto problem
description: Den här artikeln beskriver hur du felsöker och löser problem med ett Azure-konto.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896587"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Felsöka Azure Automation konto problem

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder ett Azure Automation-konto. Allmän information om Automation-konton finns i [Översikt över Azure Automation konto verifiering](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: det går inte att registrera Automation Resource Provider för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med hanterings funktioner, till exempel Uppdateringshantering, i ditt Automation-konto, stöter du på följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Orsak

Den automatiska resurs leverantören är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Registrera automatiserings resurs leverantören genom att följa de här stegen i Azure Portal:

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

2. Gå till **prenumerationer** och välj din prenumeration.   

3. Under **Inställningar** väljer du **resurs leverantörer**.

4. Kontrol lera att **Microsoft. Automation** Resource Provider är registrerad i listan över resurs leverantörer.

5. Om providern inte finns med i listan registrerar du den enligt beskrivningen i [lösa fel för registrering av resurs leverantör](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Nästa steg

Om den här artikeln inte löser problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) . Detta är det officiella Microsoft Azure kontot för att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
