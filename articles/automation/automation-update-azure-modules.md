---
title: Uppdatera Azure PowerShell moduler i Azure Automation
description: Den här artikeln beskriver hur du uppdaterar Azure PowerShell moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c1632da35864fc6822b385adac06d7f124aea061
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830420"
---
# <a name="update-azure-powershell-modules"></a>Uppdatera Azure PowerShell-moduler

De vanligaste PowerShell-modulerna tillhandahålls som standard i varje Automation-konto. Se [Standardmoduler](shared-resources/modules.md#default-modules). När Azure-teamet uppdaterar Azure-modulerna regelbundet kan ändringar ske med de inkluderade cmdletarna. Dessa ändringar, till exempel att byta namn på en parameter eller göra en cmdlet helt inaktuell, kan påverka dina runbooks negativt. 

> [!NOTE]
> Du kan inte ta bort globala moduler, det vill säga moduler som Automation tillhandahåller.

## <a name="set-up-an-automation-account"></a>Konfigurera ett Automation-konto

För att undvika att påverka dina runbooks och de processer som de automatiserar måste du testa och validera när du gör uppdateringar. Om du inte har något dedikerat Automation-konto avsett för det här ändamålet kan du skapa ett så att du kan testa många olika scenarier under utvecklingen av dina runbooks. Det här testet bör innehålla iterativa ändringar, till exempel uppdatering av PowerShell-modulerna.

Kontrollera att automationskontot har skapat ett [Kör som-konto i Azure.](automation-security-overview.md#run-as-accounts)

Om du utvecklar skripten lokalt rekommenderar vi att du har samma modulversioner lokalt som du har i Ditt Automation-konto vid testning för att säkerställa att du får samma resultat. När resultaten har verifierats och du har tillämpat alla ändringar som krävs kan du flytta ändringarna till produktion.

> [!NOTE]
> Ett nytt Automation-konto kanske inte innehåller de senaste modulerna.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Hämta en runbook som ska användas för uppdateringar

Om du vill uppdatera Azure-modulerna i ditt Automation-konto måste du använda [runbooken Update-AutomationAzureModulesForAccount,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) som är tillgänglig som öppen källkod. Om du vill börja använda denna runbook för att uppdatera dina Azure-moduler laddar du ned den från GitHub-lagringsplatsen. Du kan sedan importera det till ditt Automation-konto eller köra det som ett skript. Information om hur du importerar en runbook i ditt Automation-konto finns i [Importera en runbook.](manage-runbooks.md#import-a-runbook)

**Runbooken Update-AutomationAzureModulesForAccount** stöder uppdatering av Azure-, AzureRM- och Az-modulerna som standard. Mer information [om hur du uppdaterar Az.Automation-moduler med](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) den här runbooken finns i Uppdatera Azure-moduler med README. Det finns ytterligare viktiga faktorer som du måste ta hänsyn till när du använder Az-modulerna i ditt Automation-konto. Mer information finns i [Hantera moduler i Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Använda uppdatera runbook-kod som ett vanligt PowerShell-skript

Du kan använda runbook-koden som ett vanligt PowerShell-skript i stället för en runbook. Det gör du genom att först logga in på Azure med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och sedan `-Login $false` skicka till skriptet.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Använda uppdaterings-runbooken i nationella moln

Om du vill använda den här runbooken i nationella moln använder du `AzEnvironment` parametern för att skicka rätt miljö till runbooken. Godkända värden är AzureCloud (offentligt Azure-moln), AzureChinaCloud, AzureGermanCloud och AzureUSGovernment. Dessa värden kan hämtas med hjälp av `Get-AzEnvironment | select Name` . Om du inte skickar ett värde till den här cmdleten används AzureCloud som standard för runbooken.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Använda uppdaterings-runbooken för att uppdatera en specifik modulversion

Om du vill använda en specifik Azure PowerShell-modulversion i stället för den senaste tillgängliga modulen på PowerShell-galleriet skickar du dessa versioner till den valfria parametern för `ModuleVersionOverrides` **runbooken Update-AutomationAzureModulesForAccount.** Exempel finns i  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) runbook. Azure PowerShell moduler som inte nämns i `ModuleVersionOverrides` parametern uppdateras med de senaste modulversionerna på PowerShell-galleriet. Om du inte skickar något `ModuleVersionOverrides` till parametern uppdateras alla moduler med de senaste modulversionerna på PowerShell-galleriet. Det här beteendet är detsamma för **knappen Uppdatera Azure-moduler** i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder moduler finns [i Hantera moduler i Azure Automation](shared-resources/modules.md).
* Information om uppdaterings-runbooken finns i Update Azure modules runbook ( [Uppdatera Azure-modulers runbook).](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
