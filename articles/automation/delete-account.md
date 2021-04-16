---
title: Ta bort ditt Azure Automation konto
description: Den här artikeln beskriver hur du tar bort ditt Automation-konto i olika konfigurationsscenarier.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: d088f3adc391068de5e337c10ab52dc3d3a2dd07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535556"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Ta bort ditt Azure Automation konto

När du har aktiverar ett Azure Automation-konto för att automatisera IT- eller affärsprocesser eller aktiverar dess andra funktioner för driftshantering av dina Azure- och icke-Azure-datorer, till exempel Uppdateringshantering, kan du välja att sluta använda Automation-kontot. Om du har aktiverat funktioner som är beroende av integrering med en Azure Monitor Log Analytics-arbetsyta krävs fler steg för att slutföra den här åtgärden.

Du kan ta bort ditt Automation-konto med någon av följande metoder baserat på de distributionsmodeller som stöds:

* Ta bort resursgruppen som innehåller Automation-kontot.
* Ta bort resursgruppen som innehåller Automation-kontot och den länkade Azure Monitor Log Analytics-arbetsytan, om:

    * Kontot och arbetsytan är dedikerade för att stödja Uppdateringshantering, Ändringsspårning och inventering och/eller Starta/stoppa virtuella datorer när de inte används.
    * Kontot är dedikerat för att bearbeta automatisering och integreras med en arbetsyta för att skicka runbook-jobbstatus och jobbströmmar.

* Ta bort länken till Log Analytics-arbetsytan från Automation-kontot och ta bort Automation-kontot.
* Ta bort funktionen från den länkade arbetsytan, ta bort länken till kontot från arbetsytan och ta sedan bort Automation-kontot.

Den här artikeln beskriver hur du tar bort ditt Automation-konto helt via Azure Portal, med Azure PowerShell, Azure CLI eller REST API.

> [!NOTE]
> Innan du fortsätter bör du kontrollera att [det inte Resource Manager några](../azure-resource-manager/management/lock-resources.md) lås som tillämpas på prenumerationen, resursgruppen eller resursen, vilket förhindrar oavsiktlig borttagning eller ändring av kritiska resurser. Om du har distribuerat Starta/stoppa virtuella datorer när de inte används-lösningen anges låsnivån till **CanNotDelete** mot flera beroende resurser i Automation-kontot (särskilt dess runbooks och variabler). Alla lås måste tas bort innan du kan ta bort Automation-kontot.

## <a name="delete-the-dedicated-resource-group"></a>Ta bort den dedikerade resursgruppen

Om du vill ta bort ditt Automation-konto och även Log Analytics-arbetsytan om du är länkad till kontot, som skapats i samma resursgrupp som är dedikerad till kontot, följer du stegen som beskrivs i artikeln [Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md) resursgrupp och resursborttagning.

## <a name="delete-a-standalone-automation-account"></a>Ta bort ett fristående Automation-konto

Om ditt Automation-konto inte är länkat till en Log Analytics-arbetsyta utför du följande steg för att ta bort det.

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. I Azure Portal du till **Automation-konton.**

3. Öppna ditt Automation-konto och välj **Ta** bort på menyn.

När informationen har verifierats och kontot har tagits bort kan du följa förloppet under **Meddelanden** som valts på menyn.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Det här kommandot tar bort Automation-kontot utan att fråga efter verifiering.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Ta bort ett fristående Automation-konto som är länkat till arbetsytan

Om ditt Automation-konto är länkat till en Log Analytics-arbetsyta för att samla in jobbströmmar och jobbloggar utför du följande steg för att ta bort kontot.

Det finns två alternativ för att ta bort länken till Log Analytics-arbetsytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbetsytan.

Om du vill ta bort länken från Automation-kontot utför du följande steg.

1. I Azure Portal du till **Automation-konton.**

2. Öppna ditt Automation-konto och **välj Länkad** **arbetsyta under Relaterade** resurser till vänster.

3. På sidan **Ta bort länk till** arbetsyta väljer du Ta bort länk **till** arbetsyta och svarar på uppmaningar.

   ![Sidan Ta bort länk till arbetsyta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När den försöker ta bort länken till Log Analytics-arbetsytan kan du följa förloppet under **Meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbetsytan.

1. I Azure Portal du till **Log Analytics-arbetsytor**.

2. Från arbetsytan väljer du **Automation-konto** under **Relaterade resurser.**

3. På sidan Automation-konto väljer du **Ta bort länk till** konto och svarar på uppmaningar.

När den försöker ta bort länken till Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

När Automation-kontot har avlänkats från arbetsytan utför du stegen i avsnittet [fristående Automation-konto](#delete-a-standalone-automation-account) för att ta bort kontot.

## <a name="delete-a-shared-capability-automation-account"></a>Ta bort ett Automation-konto med delad kapacitet

Utför följande steg för att ta bort ditt Automation-konto som är länkat till en Log Analytics-arbetsyta som stöd för Uppdateringshantering, Ändringsspårning och inventering och/eller Starta/stoppa virtuella datorer när de inte används genom att utföra följande steg.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Steg 1. Ta bort lösningen från den länkade arbetsytan

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. Gå till ditt Automation-konto och välj **Länkad arbetsyta** under **Relaterade resurser.**

3. Välj **Gå till arbetsyta.**

4. Klicka **på Lösningar** under **Allmänt.**

5. På sidan Lösningar väljer du något av följande baserat på de funktioner som distribueras i kontot:

    * För Starta/stoppa virtuella datorer när de inte används väljer du **Start-Stop-VM[namn på arbetsyta]**.
    * För Uppdateringshantering väljer du **Uppdateringar(arbetsytans namn)**.
    * För Ändringsspårning och inventering du **ChangeTracking(arbetsytans namn)**.

6. På sidan **Lösning** väljer du **Ta** bort på menyn. Om fler än en av ovanstående funktioner distribueras till Automation-kontot och den länkade arbetsytan måste du välja och ta bort var och en av dem innan du fortsätter.

7. När informationen har verifierats och funktionen har tagits bort kan du följa förloppet under **Meddelanden** som valts på menyn. Du kommer tillbaka till sidan Lösningar efter borttagningsprocessen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort en installerad Azure PowerShell med hjälp av cmdleten [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Steg 2. Ta bort länk mellan arbetsyta och Automation-konto

Det finns två alternativ för att ta bort länken till Log Analytics-arbetsytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbetsytan.

Om du vill ta bort länken från ditt Automation-konto utför du följande steg.

1. I Azure Portal du till **Automation-konton.**

2. Öppna ditt Automation-konto och **välj Länkad** **arbetsyta under Relaterade** resurser till vänster.

3. På sidan **Ta bort länk till** arbetsyta väljer du Ta bort länk **till** arbetsyta och svarar på uppmaningar.

   ![Sidan Ta bort länk till arbetsyta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När den försöker ta bort länken till Log Analytics-arbetsytan kan du följa förloppet under **Meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbetsytan.

1. I Azure Portal du till **Log Analytics-arbetsytor**.

2. Välj Automation-konto under **Relaterade resurser** **på arbetsytan.**

3. På sidan Automation-konto väljer du **Ta bort länk till** konto och svarar på uppmaningar.

När den försöker ta bort länken till Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### <a name="step-3-delete-automation-account"></a>Steg 3. Ta bort Automation-konto

När Automation-kontot har avlänkats från arbetsytan utför du stegen i avsnittet fristående [Automation-konto](#delete-a-standalone-automation-account) för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar ett Automation-Azure Portal finns i [Skapa ett fristående Azure Automation konto](automation-create-standalone-account.md). Om du föredrar att skapa ditt konto med hjälp av en mall kan du gå [till Skapa ett Automation-konto med en Azure Resource Manager mall](quickstart-create-automation-account-template.md).