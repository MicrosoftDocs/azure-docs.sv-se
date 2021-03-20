---
title: Ta bort Azure Automation Ändringsspårning och inventerings funktion
description: Den här artikeln beskriver hur du slutar att använda Ändringsspårning och inventering och avlänka ett Automation-konto från arbets ytan Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92210269"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Ta bort Ändringsspårning och inventering från Automation-konto

När du har aktiverat hanteringen av dina virtuella datorer med Azure Automation Ändringsspårning och inventering kan du välja att sluta använda den och ta bort konfigurationen från kontot och den länkade Log Analytics-arbetsytan. Den här artikeln beskriver hur du helt tar bort Ändringsspårning och inventering från de hanterade virtuella datorerna, ditt Automation-konto och Log Analytics-arbetsytan.

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Ta bort hantering av virtuella datorer

Innan du tar bort Ändringsspårning och inventering måste du först stoppa hanteringen av dina virtuella datorer. Se [ta bort virtuella datorer från ändringsspårning](remove-vms-from-change-tracking.md) för att avregistrera dem från funktionen.

## <a name="remove-changetracking-solution"></a>Ta bort ChangeTracking-lösning

Innan du kan ta bort länken till Automation-kontot från arbets ytan måste du följa de här stegen för att helt ta bort Ändringsspårning och inventering. Du tar bort **ChangeTracking** -lösningen från arbets ytan.

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Log Analytics**.

2. I listan med Log Analytics arbets ytor väljer du den arbets yta som du valde när du aktiverade Ändringsspårning och inventering.

3. Välj **lösningar** till vänster.  

4. I listan med lösningar väljer du **ChangeTracking (arbets ytans namn)**. På sidan **Översikt** för lösningen väljer du **ta bort**. När du uppmanas att bekräfta väljer du **Ja**.

## <a name="unlink-workspace-from-automation-account"></a>Ta bort länk mellan arbetsyta och Automation-konto

1. I Azure Portal väljer du **Automation-konton**.

2. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

3. På sidan **ta bort länk till arbets yta** väljer du **ta bort länk till arbets yta** och svara på frågor.

   ![Sidan ta bort länk till arbets yta](media/remove-feature/automation-unlink-workspace-blade.png)

När du försöker ta bort länken till Log Analytics arbets ytan kan du följa förloppet under **meddelanden** på menyn.

Du kan också ta bort länken till Log Analytics arbets ytan från ditt Automation-konto från arbets ytan:

1. I Azure-portalen väljer du **Log Analytics**.

2. Från arbets ytan väljer du **Automation-konto** under **relaterade resurser**.

3. På sidan Automation-konto väljer du **ta bort länk till konto**.

När det försöker ta bort länken till Automation-kontot kan du följa förloppet under **meddelanden** på menyn.

## <a name="next-steps"></a>Nästa steg

Om du vill aktivera den här funktionen igen går du till [aktivera ändringsspårning och inventering från ett Automation-konto](enable-from-automation-account.md), [aktiverar ändringsspårning och inventering genom att bläddra i Azure Portal](enable-from-portal.md), [Aktivera ändringsspårning och inventering från en Runbook](enable-from-runbook.md)eller [Aktivera ändringsspårning och inventering från en virtuell Azure-dator](enable-from-vm.md).
