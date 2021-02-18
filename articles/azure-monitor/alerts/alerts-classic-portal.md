---
title: Skapa och hantera klassiska mått varningar med Azure Monitor
description: Lär dig hur du använder Azure Portal, CLI eller PowerShell för att skapa, Visa och hantera klassiska mått för varnings regler.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: 015a84ab6f81875407f460c4a45423f3f5e22b7f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625840"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Skapa, Visa och hantera klassiska mått varningar med Azure Monitor

> [!WARNING]
> Den här artikeln beskriver hur du skapar äldre klassiska mått varningar. Azure Monitor har nu stöd för [nyare aviseringar i nästan real tid och nya aviserings upplevelser](../platform/alerts-overview.md). Klassiska aviseringar [dras tillbaka](../platform/monitoring-classic-retirement.md) för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna.
>

Klassiska mått varningar i Azure Monitor ger ett sätt att få ett meddelande när ett av dina mått överskrider ett tröskelvärde. Klassiska mått varningar är en äldre funktion som endast tillåter aviseringar på icke-dimensionella mått. Det finns en befintlig ny funktion som kallas mått aviseringar som har förbättrade funktioner i klassiska mått varningar. Du kan lära dig mer om de nya funktionerna för mått varningar i [Översikt över mått aviseringar](../platform/alerts-metric-overview.md). I den här artikeln beskriver vi hur du skapar, visar och hanterar klassiska mått för aviserings regler via Azure Portal, Azure CLI och PowerShell.

## <a name="with-azure-portal"></a>Med Azure Portal

1. Leta upp den resurs som du vill övervaka i [portalen](https://portal.azure.com/)och välj den.

2. I avsnittet **övervakning** väljer du **aviseringar (klassisk)**. Texten och ikonen kan variera något för olika resurser. Om du inte hittar **aviseringar (klassisk)** här kan du hitta dem i **aviseringar** eller **varnings regler**.

    ![Övervakning](media/alerts-classic-portal/AlertRulesButton.png)

3. Välj kommandot **Lägg till mått varning (klassisk)** och fyll sedan i fälten.

    ![Lägg till avisering](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Namnge** din aviserings regel. Välj sedan en **Beskrivning**, som också visas i e-postmeddelanden.

5. Välj det **mått** som du vill övervaka. Välj sedan ett **villkor** och ett **tröskel** värde för måttet. Välj också den tids **period** som mått regeln måste uppfylla innan aviseringen utlöses. Om du till exempel använder perioden "de senaste 5 minuterna" och din avisering söker efter en processor över 80%, utlöses aviseringen när processorn har varit konsekvent över 80% i 5 minuter. När den första utlösaren sker utlöses den igen när CPU: n stannar under 80% i 5 minuter. Måttet för processor mått sker varje minut.

6. Välj **e-postägare...** om du vill att administratörer och medadministratörer ska kunna ta emot e-postaviseringar när aviseringen utlöses.

7. Om du vill skicka meddelanden till ytterligare e-postadresser när aviseringen utlöses, lägger du till dem i fältet **ytterligare administratörs e-post (er)** . Separera flera e-postmeddelanden med semikolon i följande format: *e-post \@ contoso. com; email2 \@ contoso.com*

8. Placera i en giltig URI i fältet **webhook** om du vill att det ska anropas när aviseringen utlöses.

9. Om du använder Azure Automation kan du välja en Runbook som ska köras när aviseringen utlöses.

10. Skapa aviseringen genom att välja **OK**.

Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

När du har skapat en avisering kan du välja den och utföra en av följande uppgifter:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **Aktivera** det om du tillfälligt vill stoppa eller återuppta mottagning av aviseringar.

## <a name="with-azure-cli"></a>Med Azure CLI

I föregående avsnitt beskrivs hur du skapar, visar och hanterar mått varnings regler med hjälp av Azure Portal. Det här avsnittet beskriver hur du gör på samma sätt som med plattforms oberoende [Azure CLI](/cli/azure/get-started-with-azure-cli). Det snabbaste sättet att börja använda Azure CLI är genom [Azure Cloud Shell](../../cloud-shell/overview.md).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Hämta alla klassiska mått för varnings regler i en resurs grupp

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Se information om en viss klassisk måtta aviserings regel

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Skapa en klassisk mått varnings regel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Ta bort en klassisk måtta aviserings regel

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I det här avsnittet visas hur du använder PowerShell-kommandon för att skapa, Visa och hantera klassiska mått varningar. I exemplen i artikeln visas hur du kan använda Azure Monitor-cmdletar för klassiska mått varningar.

1. Om du inte redan har gjort det konfigurerar du PowerShell att köras på datorn. Mer information finns i [så här installerar och konfigurerar du PowerShell](/powershell/azure/). Du kan också granska hela listan med Azure Monitor PowerShell-cmdletar i [Azure Monitor (insikter)-cmdletar](/powershell/module/az.applicationinsights).

2. Börja med att logga in på din Azure-prenumeration.

    ```powershell
    Connect-AzAccount
    ```

3. Du ser en inloggnings skärm. När du har loggat in på kontot visas TenantID och standard prenumerations-ID. Alla Azure-cmdletar fungerar i kontexten för din standard prenumeration. Använd följande kommando om du vill visa en lista över prenumerationer som du har åtkomst till:

    ```powershell
    Get-AzSubscription
    ```

4. Om du vill ändra din arbets kontext till en annan prenumeration använder du följande kommando:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Du kan hämta alla klassiska mått varnings regler för en resurs grupp:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Du kan visa information om en klassisk mått varnings regel

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Du kan hämta alla varnings regler som har angetts för en mål resurs. Till exempel alla varnings regler som har angetts på en virtuell dator.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Det går inte längre att skapa klassiska varnings regler via PowerShell. Om du vill skapa en aviserings regel måste du använda det nya kommandot [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) .

## <a name="next-steps"></a>Nästa steg

- [Skapa en klassisk måtta-avisering med en Resource Manager-mall](../platform/alerts-enable-template.md).
- [Ha en klassisk mått avisering som meddelar ett icke-Azure-system med en webhook](../platform/alerts-webhooks.md).

