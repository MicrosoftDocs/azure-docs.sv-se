---
title: Uppdatera Azure PowerShell moduler i Azure Automation
description: Den här artikeln beskriver hur du uppdaterar vanliga Azure PowerShell-moduler som anges som standard i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: f05c3247ecdd77fe4cf02db70c7ba4b2c534f959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055356"
---
# <a name="update-azure-powershell-modules"></a>Uppdatera Azure PowerShell-moduler

De vanligaste PowerShell-modulerna anges som standard i varje Automation-konto. Se [standardmoduler](shared-resources/modules.md#default-modules). När Azure-teamet uppdaterar Azure-moduler regelbundet kan ändringar ske med de inkluderade cmdletarna. De här ändringarna, till exempel att byta namn på en parameter eller att använda en helt aktuell cmdlet, kan påverka dina runbooks negativt. 

> [!NOTE]
> Du kan inte ta bort globala moduler, som är moduler som Automation tillhandahåller i rutan.

## <a name="set-up-an-automation-account"></a>Konfigurera ett Automation-konto

För att undvika att dina runbooks påverkas och de processer de automatiseras, måste du testa och kontrol lera när du gör uppdateringar. Om du inte har ett dedikerat Automation-konto som är avsett för detta ändamål kan du överväga att skapa ett så att du kan testa många olika scenarier när du utvecklar dina runbooks. Det här testet bör omfatta upprepnings ändringar, till exempel uppdatera PowerShell-modulerna.

Kontrol lera att ditt Automation-konto har ett [Kör som-konto för Azure](automation-security-overview.md#run-as-accounts) skapat.

Om du utvecklar dina skript lokalt rekommenderar vi att du har samma versioner av modulen lokalt som du har i ditt Automation-konto när du testar för att säkerställa att du får samma resultat. När resultaten har verifierats och du har tillämpat eventuella ändringar som krävs kan du flytta ändringarna till produktionen.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Hämta en Runbook som ska användas för uppdateringar

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [Update-AutomationAzureModulesForAccount-](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbooken, som är tillgängligt som öppen källkod. Du börjar använda denna Runbook för att uppdatera Azure-moduler genom att ladda ned den från GitHub-lagringsplatsen. Du kan sedan importera den till ditt Automation-konto eller köra det som ett skript. Information om hur du importerar en Runbook i ditt Automation-konto finns i [Importera en Runbook](manage-runbooks.md#import-a-runbook).

**AutomationAzureModulesForAccount** Runbook stöder uppdatering av modulerna Azure, AzureRM och AZ som standard. Läs igenom [uppdateringen för Azure-modulerna i Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) om du vill ha mer information om hur du uppdaterar AZ. Automation-moduler med denna Runbook. Det finns ytterligare viktiga faktorer som du måste ta med i beräkningen när du använder AZ-modulerna i ditt Automation-konto. Mer information finns [i hantera moduler i Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Använda Update Run Book Code som ett vanligt PowerShell-skript

Du kan använda Runbook-koden som ett vanligt PowerShell-skript i stället för en Runbook. Det gör du genom att logga in på Azure med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) först och sedan skicka `-Login $false` till skriptet.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Använd uppdaterings-Runbook på suveräna moln

Om du vill använda denna Runbook på suveräna moln använder du `AzEnvironment` parametern för att skicka rätt miljö till Runbook. Godkända värden är AzureCloud (Azures offentliga moln), AzureChinaCloud, AzureGermanCloud och Azureusgovernment eller. Dessa värden kan hämtas med hjälp av `Get-AzEnvironment | select Name` . Om du inte skickar ett värde till den här cmdleten använder Runbook standardvärdet AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Använd uppdaterings-Runbook för att uppdatera en angiven version av en modul

Om du vill använda en speciell Azure PowerShell-modul i stället för den senaste modulen som finns på PowerShell-galleriet, måste du skicka dessa versioner till den valfria `ModuleVersionOverrides` parametern för **Update-AutomationAzureModulesForAccount-** runbooken. Exempel finns i  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook. Azure PowerShell moduler som inte nämns i `ModuleVersionOverrides` parametern uppdateras med de senaste modulerna på PowerShell-galleriet. Om du inte skickar något till `ModuleVersionOverrides` parametern, uppdateras alla moduler med de senaste modulerna på PowerShell-galleriet. Detta är samma sak som knappen **Uppdatera Azure-moduler** i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder moduler finns [i hantera moduler i Azure Automation](shared-resources/modules.md).
* Information om uppdaterings-Runbook finns i [Uppdatera Azure-moduler Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
