---
title: Ta bort ditt Azure Automation-konto
description: Den här artikeln beskriver hur du tar bort ditt Automation-konto i olika konfigurations scenarier.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735115"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Ta bort ditt Azure Automation-konto

När du har aktiverat ett Azure Automation-konto för att automatisera IT-eller affärs processen eller aktivera andra funktioner för att stödja drift hantering av dina Azure-och icke-Azure-datorer, till exempel Uppdateringshantering, kan du välja att sluta använda Automation-kontot. Om du har aktiverat funktioner som är beroende av integreringen med en Azure Monitor Log Analytics arbets yta finns det fler steg som krävs för att slutföra åtgärden.

Borttagning av ditt Automation-konto kan göras med någon av följande metoder baserat på distributions modeller som stöds:

* Ta bort resurs gruppen som innehåller Automation-kontot.
* Ta bort resurs gruppen som innehåller Automation-kontot och länkade Azure Monitor Log Analytics arbets yta, om:

    * Kontot och arbets ytan är avsedd att stödja Uppdateringshantering, Ändringsspårning och inventering och/eller Starta/stoppa virtuella datorer när de inte används.
    * Kontot är dedikerat för att bearbeta automatisering och integrerat med en arbets yta för att skicka Runbook-jobbets status och jobb strömmar.

* Ta bort länken till Log Analytics arbets ytan från Automation-kontot och ta bort Automation-kontot.
* Ta bort funktionen från den länkade arbets ytan, ta bort länken till kontot från arbets ytan och ta sedan bort Automation-kontot.

Den här artikeln beskriver hur du tar bort Automation-kontot fullständigt via Azure Portal, PowerShell, Azure CLI eller REST API.

## <a name="delete-the-dedicated-resource-group"></a>Ta bort den dedikerade resurs gruppen

Om du vill ta bort Automation-kontot och även Log Analytics arbets ytan om den är länkad till kontot, som skapats i samma resurs grupp som är dedikerad till kontot, följer du stegen som beskrivs i artikeln [Azure Resource Manager resurs grupp och resurs borttagning](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-a-standalone-automation-account"></a>Ta bort ett fristående Automation-konto

Om ditt Automation-konto inte är länkat till en Log Analytics arbets yta utför du följande steg för att ta bort det.

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. I Azure Portal navigerar du till **Automation-konton**.

3. Öppna ditt Automation-konto och välj **ta bort** på menyn.

Medan informationen verifieras och kontot tas bort, kan du följa förloppet under **meddelanden**, valt i menyn.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Det här kommandot tar bort Automation-kontot utan att begära validering.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Ta bort ett fristående Automation-konto som är länkat till arbets ytan

Om ditt Automation-konto är länkat till en Log Analytics arbets yta för att samla in jobb strömmar och jobb loggar utför du följande steg för att ta bort kontot.

Det finns två alternativ för att ta bort länken till Log Analytics arbets ytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbets ytan.

Utför följande steg för att ta bort länken från ditt Automation-konto.

1. I Azure Portal navigerar du till **Automation-konton**.

2. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

3. På sidan **ta bort länk till arbets yta** väljer du **ta bort arbets ytan** för att länka och svara på frågor.

   ![Sidan ta bort länk till arbets yta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När du försöker ta bort länken till Log Analytics arbets ytan kan du följa förloppet under **meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbets ytan.

1. I Azure Portal navigerar du till **Log Analytics arbets ytor**.

2. Från arbets ytan väljer du **Automation-konto** under **relaterade resurser**.

3. På sidan Automation-konto väljer du **ta bort länk till konto** och svarar på frågor.

När det försöker ta bort länken till Automation-kontot kan du följa förloppet under **meddelanden** på menyn.

När Automation-kontot har avlänkats från arbets ytan utför du stegen i avsnittet [fristående Automation-konto](#delete-a-standalone-automation-account) för att ta bort kontot.

## <a name="delete-a-shared-capability-automation-account"></a>Ta bort ett Automation-konto för delad kapacitet

Om du vill ta bort ett Automation-konto som är kopplat till en Log Analytics arbets yta som stöd för Uppdateringshantering, Ändringsspårning och inventering, och/eller Starta/stoppa virtuella datorer när de inte används, utför du följande steg.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Steg 1. Ta bort lösningen från den länkade arbets ytan

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. Gå till ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser**.

3. Välj **gå till arbets yta**.

4. Klicka på **lösningar** under **Allmänt**.

5. På sidan lösningar väljer du något av följande baserat på de funktioner som distribuerats i kontot:

    * För Starta/stoppa virtuella datorer när de inte används väljer du **Start-Stop-VM [arbetsyte namn]**.
    * För Uppdateringshantering väljer du **uppdateringar (arbets ytans namn)**.
    * För Ändringsspårning och inventering väljer du **ChangeTracking (arbets ytans namn)**.

6. På sidan **lösning** väljer du **ta bort** på menyn. Om fler än en av ovanstående funktioner har distribuerats till Automation-kontot och den länkade arbets ytan måste du markera och ta bort dem innan du fortsätter.

7. Medan informationen är verifierad och funktionen tas bort, kan du följa förloppet under **meddelanden**, valt i menyn. Du kommer tillbaka till lösnings sidan efter borttagnings processen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort en installerad lösning med hjälp av Azure PowerShell använder du cmdleten [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Steg 2. Ta bort länk mellan arbetsyta och Automation-konto

Det finns två alternativ för att ta bort länken till Log Analytics arbets ytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbets ytan.

Utför följande steg för att ta bort länken från ditt Automation-konto.

1. I Azure Portal navigerar du till **Automation-konton**.

2. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

3. På sidan **ta bort länk till arbets yta** väljer du **ta bort arbets ytan** för att länka och svara på frågor.

   ![Sidan ta bort länk till arbets yta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När du försöker ta bort länken till Log Analytics arbets ytan kan du följa förloppet under **meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbets ytan.

1. I Azure Portal navigerar du till **Log Analytics arbets ytor**.

2. Från arbets ytan väljer du **Automation-konto** under **relaterade resurser**.

3. På sidan Automation-konto väljer du **ta bort länk till konto** och svarar på frågor.

När det försöker ta bort länken till Automation-kontot kan du följa förloppet under **meddelanden** på menyn.

### <a name="step-3-delete-automation-account"></a>Steg 3. Ta bort Automation-konto

När Automation-kontot har avlänkats från arbets ytan utför du stegen i avsnittet [fristående Automation-konto](#delete-a-standalone-automation-account) för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar ett Automation-konto från Azure Portal finns i [skapa ett fristående Azure Automation-konto](automation-create-standalone-account.md). Om du föredrar att skapa ditt konto med hjälp av en mall kan du läsa [skapa ett Automation-konto med hjälp av en Azure Resource Manager-mall](quickstart-create-automation-account-template.md).