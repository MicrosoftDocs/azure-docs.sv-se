---
title: Ta bort Azure Automation Starta/stoppa virtuella datorer när de inte används översikt
description: I den här artikeln beskrivs hur du tar bort Starta/stoppa virtuella datorer när de inte används-funktionen och avlänkar ett Automation-konto från arbets ytan Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2021
ms.topic: conceptual
ms.openlocfilehash: a201274bf0c06382da790dd051bf5023337970b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723851"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Ta bort Starta/stoppa virtuella datorer när de inte används från Automation-konto

När du har aktiverat funktionen Starta/stoppa virtuella datorer när de inte används för att hantera körnings statusen för dina virtuella Azure-datorer kan du sluta använda den. Borttagning av den här funktionen kan göras med hjälp av någon av följande metoder baserat på distributions modeller som stöds:

* Ta bort resurs gruppen som innehåller Automation-kontot och länkade Azure Monitor Log Analytics arbets ytan, var och en som är dedikerad för att stödja den här funktionen.
* Ta bort länken Log Analytics arbets ytan från Automation-kontot och ta bort det Automation-konto som är dedikerat för den här funktionen.
* Ta bort funktionen från ett Automation-konto och en länkad arbets yta som stöder andra hanterings-och övervaknings mål.

Om du tar bort den här funktionen tas endast de tillhör ande Runbooks bort, den tas inte bort scheman eller variabler som skapades under distributionen eller som har definierats av egna.

## <a name="delete-the-dedicated-resource-group"></a>Ta bort den dedikerade resurs gruppen

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. Gå till ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser**.

3. Välj **gå till arbets yta**.

4. Klicka på **lösningar** under **Allmänt**.

5. På sidan lösningar väljer du **Start-Stop-VM [arbets yta]**.

6. På sidan **VMManagementSolution [Workspace]** väljer du **ta bort** på menyn.

    ![Ta bort hanterings funktion för virtuell dator](media/automation-solution-vm-management/vm-management-solution-delete.png)

7. Om du vill ta bort resurs gruppen som skapats för att endast stödja Starta/stoppa virtuella datorer när de inte används följer du stegen som beskrivs i artikeln [Azure Resource Manager resurs grupp och resurs borttagning](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Ta bort Automation-kontot

Utför följande steg om du vill ta bort det Automation-konto som är dedikerat för Starta/stoppa virtuella datorer när de inte används.

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. Gå till ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser**.

3. Välj **gå till arbets yta**.

4. Klicka på **lösningar** under **Allmänt**.

5. På sidan lösningar väljer du **Start-Stop-VM [arbets yta]**.

6. På sidan **VMManagementSolution [Workspace]** väljer du **ta bort** på menyn.

7. Medan informationen är verifierad och funktionen tas bort, kan du följa förloppet under **meddelanden**, valt i menyn. Du kommer tillbaka till lösnings sidan efter borttagnings processen.

### <a name="unlink-workspace-from-automation-account"></a>Ta bort länk mellan arbetsyta och Automation-konto

Det finns två alternativ för att ta bort länken till Log Analytics arbets ytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbets ytan.

Utför följande steg för att ta bort länken från ditt Automation-konto.

1. I Azure Portal väljer du **Automation-konton**.

2. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

3. På sidan **ta bort länk till arbets yta** väljer du **ta bort länk till arbets yta** och svara på frågor.

   ![Sidan ta bort länk till arbets yta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När du försöker ta bort länken till Log Analytics arbets ytan kan du följa förloppet under **meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbets ytan.

1. I Azure Portal väljer du **Log Analytics arbets ytor**.

2. Från arbets ytan väljer du **Automation-konto** under **relaterade resurser**.

3. På sidan Automation-konto väljer du **ta bort länk till konto** och svara på frågor.

När det försöker ta bort länken till Automation-kontot kan du följa förloppet under **meddelanden** på menyn.

### <a name="delete-automation-account"></a>Ta bort Automation-konto

1. I Azure Portal väljer du **Automation-konton**.

2. Öppna ditt Automation-konto och välj **ta bort** på menyn.

Medan informationen verifieras och kontot tas bort, kan du följa förloppet under **meddelanden**, valt i menyn.

## <a name="delete-the-feature"></a>Ta bort funktionen

Utför följande steg för att ta bort Starta/stoppa virtuella datorer när de inte används från ditt Automation-konto. Automation-kontot och Log Analytics-arbetsytan tas inte bort som en del av den här processen. Om du inte vill behålla Log Analytics arbets ytan måste du ta bort den manuellt. Mer information om hur du tar bort din arbets yta finns i [ta bort och återställa Azure Log Analytics-arbetsytan](../azure-monitor/logs/delete-workspace.md).

1. Gå till ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser**.

2. Välj **gå till arbets yta**.

3. Klicka på **lösningar** under **Allmänt**.

4. På sidan lösningar väljer du **Start-Stop-VM [arbets yta]**.

5. På sidan **VMManagementSolution [Workspace]** väljer du **ta bort** på menyn.

    ![Ta bort hanterings funktion för virtuell dator](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. I fönstret ta bort lösning bekräftar du att du vill ta bort funktionen.

7. Medan informationen är verifierad och funktionen tas bort, kan du följa förloppet under **meddelanden**, valt i menyn. Du kommer tillbaka till lösnings sidan efter borttagnings processen.

8. Om du inte vill behålla [resurserna](automation-solution-vm-management.md#components) som skapats av funktionen eller efteråt (till exempel variabler, scheman osv.) måste du ta bort dem manuellt från kontot.

## <a name="next-steps"></a>Nästa steg

Om du vill aktivera den här funktionen kan du läsa [Aktivera start/stoppa vid andra tider](automation-solution-vm-management-enable.md).