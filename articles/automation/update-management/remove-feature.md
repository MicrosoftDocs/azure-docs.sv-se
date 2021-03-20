---
title: Ta bort Azure Automation Uppdateringshantering funktionen
description: Den här artikeln beskriver hur du slutar använda Uppdateringshantering och avlänka ett Automation-konto från arbets ytan Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222582"
---
# <a name="remove-update-management-from-automation-account"></a>Ta bort Uppdateringshantering från Automation-konto

När du har aktiverat hantering av uppdateringar på dina virtuella datorer med hjälp av Azure Automation Uppdateringshantering, kan du välja att sluta använda den och ta bort konfigurationen från kontot och den länkade Log Analytics-arbetsytan.  Den här artikeln beskriver hur du helt tar bort Uppdateringshantering från de hanterade virtuella datorerna, ditt Automation-konto och Log Analytics arbets ytan.

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Ta bort hantering av virtuella datorer

Innan du tar bort Uppdateringshantering måste du först stoppa hanteringen av dina virtuella datorer. Se [ta bort virtuella datorer från uppdateringshantering](remove-vms.md) för att avregistrera dem från funktionen.

## <a name="remove-updatemanagement-solution"></a>Ta bort UpdateManagement-lösning

Innan du kan ta bort länken till Automation-kontot från arbets ytan måste du följa de här stegen för att helt ta bort Uppdateringshantering. Du tar bort **uppdaterings** lösningen från arbets ytan.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Log Analytics**.

3. I listan med Log Analytics arbets ytor väljer du den arbets yta som du valde när du aktiverade Uppdateringshantering.

4. Välj **lösningar** till vänster.  

5. I listan med lösningar väljer du **uppdateringar (arbets ytans namn)**. På sidan **Översikt** för lösningen väljer du **ta bort**. När du uppmanas att bekräfta väljer du **Ja**.

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

## <a name="cleanup-automation-account"></a>Rensa Automation-konto

Om Uppdateringshantering har kon figurer ATS för att stödja tidigare versioner av Azure SQL-övervakning kan installationen av funktionen ha skapat Automation-resurser som du bör ta bort. För Uppdateringshantering kanske du vill ta bort följande objekt som inte längre behövs:

   * Uppdaterings scheman – varje har ett namn som matchar den uppdaterings distribution som du har skapat.
   * Hybrid Worker-grupper som har skapats för Uppdateringshantering – varje namn namnges på samma sätt som *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Nästa steg

Om du vill aktivera den här funktionen igen går du till [aktivera uppdateringshantering från ett Automation-konto](enable-from-automation-account.md), [aktiverar uppdateringshantering genom att bläddra i Azure Portal](enable-from-portal.md), [Aktivera uppdateringshantering från en Runbook](enable-from-runbook.md)eller [Aktivera uppdateringshantering från en virtuell Azure-dator](enable-from-vm.md).
