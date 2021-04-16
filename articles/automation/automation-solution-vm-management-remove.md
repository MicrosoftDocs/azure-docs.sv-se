---
title: Ta Azure Automation Starta/stoppa virtuella datorer när de inte används översikt
description: Den här artikeln beskriver hur du tar bort Starta/stoppa virtuella datorer när de inte används och avlänkar ett Automation-konto från Log Analytics-arbetsytan.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533954"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Ta Starta/stoppa virtuella datorer när de inte används från Automation-konto

När du har Starta/stoppa virtuella datorer när de inte används funktionen för att hantera körningstillståndet för dina virtuella Azure-datorer kan du välja att sluta använda den. Du kan ta bort den här funktionen med någon av följande metoder baserat på de distributionsmodeller som stöds:

* Ta bort resursgruppen som innehåller Automation-kontot och länkade Azure Monitor Log Analytics-arbetsytan, som var och en är dedikerad för att stödja den här funktionen.
* Ta bort länken till Log Analytics-arbetsytan från Automation-kontot och ta bort automationskontot som är dedikerat för den här funktionen.
* Ta bort funktionen från ett Automation-konto och en länkad arbetsyta som har stöd för andra hanterings- och övervakningsmål.

Om du tar bort den här funktionen tas bara associerade runbooks bort. Scheman eller variabler som skapades under distributionen eller eventuella anpassade som skapats efter tas inte bort.

> [!NOTE]
> Innan du fortsätter bör du kontrollera att [det inte Resource Manager](../azure-resource-manager/management/lock-resources.md) några lås som tillämpas på prenumerationen, resursgruppen eller resursen, vilket förhindrar oavsiktlig borttagning eller ändring av kritiska resurser. När du distribuerar Starta/stoppa virtuella datorer när de inte används-lösningen anges låsnivån till **CanNotDelete** mot flera beroende resurser i Automation-kontot (särskilt dess runbooks och variabler). Alla lås måste tas bort innan du kan ta bort Automation-kontot.

## <a name="delete-the-dedicated-resource-group"></a>Ta bort den dedikerade resursgruppen

Om du vill ta bort resursgruppen följer du stegen som beskrivs i [artikeln Azure Resource Manager resursgrupp och resursborttagning.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="delete-the-automation-account"></a>Ta bort Automation-kontot

Utför följande steg för att ta Starta/stoppa virtuella datorer när de inte används Automation-kontot som är dedikerat för en Starta/stoppa virtuella datorer när de inte används.

1. Logga in på Azure på [https://portal.azure.com](https://portal.azure.com) .

2. Gå till ditt Automation-konto och välj **Länkad arbetsyta** under **Relaterade resurser.**

3. Välj **Gå till arbetsyta.**

4. Klicka **på Lösningar** under **Allmänt.**

5. På sidan Lösningar väljer du **Start-Stop-VM[Workspace]**.

6. På sidan **VMManagementSolution[Workspace]** väljer du **Ta bort** på menyn.

7. När informationen har verifierats och funktionen har tagits bort kan du följa förloppet under **Meddelanden** som valts på menyn. Du kommer tillbaka till sidan Lösningar efter borttagningsprocessen.

### <a name="unlink-workspace-from-automation-account"></a>Ta bort länk mellan arbetsyta och Automation-konto

Det finns två alternativ för att ta bort länken till Log Analytics-arbetsytan från ditt Automation-konto. Du kan utföra den här processen från Automation-kontot eller från den länkade arbetsytan.

Utför följande steg för att ta bort länken från ditt Automation-konto.

1. I den Azure Portal väljer du **Automation-konton**.

2. Öppna ditt Automation-konto och **välj Länkad** **arbetsyta under Relaterade** resurser till vänster.

3. På sidan **Ta bort länk till** arbetsyta väljer du Ta bort länk **till** arbetsyta och svarar på uppmaningar.

   ![Sidan Ta bort länk till arbetsyta](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    När den försöker ta bort länken till Log Analytics-arbetsytan kan du följa förloppet under **Meddelanden** på menyn.

Utför följande steg för att ta bort länken från arbetsytan.

1. I den Azure Portal väljer du **Log Analytics-arbetsytor**.

2. Välj Automation-konto under **Relaterade resurser** **på arbetsytan.**

3. På sidan Automation-konto väljer du **Ta bort länk till** konto och svarar på uppmaningar.

När den försöker ta bort länken till Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### <a name="delete-automation-account"></a>Ta bort Automation-konto

1. I Azure Portal väljer du **Automation-konton.**

2. Öppna ditt Automation-konto och välj **Ta** bort på menyn.

När informationen har verifierats och kontot har tagits bort kan du följa förloppet under **Meddelanden** som valts på menyn.

## <a name="delete-the-feature"></a>Ta bort funktionen

Utför följande steg Starta/stoppa virtuella datorer när de inte används att ta bort Starta/stoppa virtuella datorer när de inte används Automation-kontot. Automation-kontot och Log Analytics-arbetsytan tas inte bort som en del av den här processen. Om du inte vill behålla Log Analytics-arbetsytan måste du ta bort den manuellt. Mer information om hur du tar bort din arbetsyta finns i [Ta bort och återställa Azure Log Analytics-arbetsyta.](../azure-monitor/logs/delete-workspace.md)

1. Gå till ditt Automation-konto och välj **Länkad arbetsyta** under **Relaterade resurser.**

2. Välj **Gå till arbetsyta.**

3. Klicka **på Lösningar** under **Allmänt.**

4. På sidan Lösningar väljer du **Start-Stop-VM[Workspace]**.

5. På sidan **VMManagementSolution[Workspace]** väljer du **Ta** bort på menyn.

    ![Ta bort vm-hanteringsfunktion](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bekräfta att du vill ta bort funktionen i fönstret Ta bort lösning.

7. När informationen har verifierats och funktionen har tagits bort kan du följa förloppet under **Meddelanden** som valts på menyn. Du kommer tillbaka till sidan Lösningar efter borttagningsprocessen.

8. Om du inte vill behålla [](automation-solution-vm-management.md#components) de resurser som skapats av funktionen eller av dig efteråt (till exempel variabler, scheman osv.) måste du manuellt ta bort dem från kontot.

## <a name="next-steps"></a>Nästa steg

Om du vill återaktivera den här funktionen [kan du se Aktivera start/stopp när den inte används.](automation-solution-vm-management-enable.md)