---
title: Skapa ett Kör som-konto för Azure Automation
description: Den här artikeln beskriver hur du skapar ett Kör som-konto med PowerShell eller från Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: d6c8e96c325cfa6422ceb4efc55ef0c5cc149ae3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056154"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Så här skapar du ett Kör som-konto för Azure Automation

Kör som-konton i Azure Automation tillhandahåller autentisering för att hantera resurser i den klassiska distributions modellen Azure Resource Manager eller Azure med hjälp av Automation-runbooks och andra automatiserings funktioner. Den här artikeln beskriver hur du skapar ett Kör som-konto eller ett klassiskt kör som-konto från Azure Portal eller Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Skapa konto i Azure Portal

Utför följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Kör som-och klassiska kör som-kontona skapas separat. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.

2. Sök efter och välj **Automation-konton**.

3. På sidan **Automation-konton** väljer du ditt Automation-konto i listan.

4. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet **konto inställningar** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Välj alternativet Kör som-konto.":::

5. Beroende på vilket konto du behöver använder du fönstret **+ Kör som-konto** eller ett **klassiskt kör som-konto** i Azure. När du har granskat översikts informationen klickar du på **skapa**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Välj alternativet för att skapa ett Kör som-konto":::

6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll visas också som anger att kontot skapas. Processen kan ta några minuter att slutföra.

## <a name="create-account-using-powershell"></a>Skapa konto med PowerShell

I följande lista finns kraven för att skapa ett Kör som-konto i PowerShell med ett tillhandahållet skript. Dessa krav gäller för båda typerna av kör som-konton.

* Windows 10 eller Windows Server 2016 med Azure Resource Manager modulerna 3.4.1 och senare. PowerShell-skriptet har inte stöd för tidigare versioner av Windows.
* Azure PowerShell PowerShell-6.2.4 eller senare. Mer information finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps).
* Ett Automation-konto, som refereras till som värde för `AutomationAccountName` parametrarna och `ApplicationDisplayName` .
* Behörigheter som motsvarar de som anges i de [behörigheter som krävs för att konfigurera kör som-konton](automation-security-overview.md#permissions).

Utför följande steg för att hämta värdena för `AutomationAccountName` , `SubscriptionId` , och `ResourceGroupName` , som är obligatoriska parametrar för PowerShell-skriptet.

1. Logga in på Azure-portalen.

1. Sök efter och välj **Automation-konton**.

1. På sidan Automation-konton väljer du ditt Automation-konto i listan.

1. I det vänstra fönstret väljer du **Egenskaper**.

1. Anteckna värdena för **namn**, **prenumerations-ID** och **resurs grupp** på sidan **Egenskaper** .

   ![Egenskaps sida för Automation-konto](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-skript för att skapa ett Kör som-konto

PowerShell-skriptet innehåller stöd för flera konfigurationer.

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och/eller ett klassiskt kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och/eller ett klassiskt kör som-konto med hjälp av ett certifikat som utfärdats av din företags certifikat utfärdare (CA).
* Skapa ett Kör som-konto och/eller ett klassiskt kör som-konto med hjälp av ett självsignerat certifikat i Azure Government molnet.

1. Hämta och Spara skriptet till en lokal mapp med hjälp av följande kommando.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Starta PowerShell med utökade användar rättigheter och navigera till den mapp som innehåller skriptet.

3. Kör något av följande kommandon för att skapa ett Kör som-och/eller klassiskt kör som-konto baserat på dina behov.

    * Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Om du har skapat ett klassiskt kör som-konto med ett offentligt företags certifikat (CER-fil) använder du det här certifikatet. Skriptet skapar och sparar det i mappen för temporära filer på datorn under den användar profil som `%USERPROFILE%\AppData\Local\Temp` du använde för att köra PowerShell-sessionen. Se [Ladda upp ett hanterings-API-certifikat till Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Skapa ett Kör som-konto och ett klassiskt kör som-konto med hjälp av ett självsignerat certifikat i Azure Government molnet

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen prenumerations administratörer. Om du skapar ett klassiskt kör som-konto måste ditt konto vara en medadministratör för prenumerationen.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i Redigera grafiska runbooks i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Information om hur du kommer igång med en python 3-Runbook finns i [Självstudier: skapa en python 3-Runbook](learn/automation-tutorial-runbook-textual-python-3.md).