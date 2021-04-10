---
title: Aktivera Azure Automation Ändringsspårning och inventering från en Runbook
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från en Runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585929"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Aktivera Ändringsspårning och inventering från en runbook

I den här artikeln beskrivs hur du kan använda en Runbook för att aktivera [ändringsspårning och inventering](overview.md) för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering.

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

Den här metoden använder två Runbooks:

* **Enable-MultipleSolution** – den primära Runbook som frågar efter konfigurations information, frågar den angivna virtuella datorn och utför andra verifierings kontroller och anropar sedan **AutomationSolution-** runbooken för att konfigurera ändringsspårning och inventering för varje virtuell dator i den angivna resurs gruppen.
* **Enable-AutomationSolution** – aktiverar ändringsspårning och inventering för en eller flera virtuella datorer som anges i mål resurs gruppen. Den verifierar att kraven är uppfyllda, verifierar att Log Analytics VM-tillägget är installerat och installeras om det inte hittas och lägger till de virtuella datorerna i omfattnings konfigurationen i den angivna Log Analytics arbets ytan som är länkad till Automation-kontot.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../automation-security-overview.md) för att hantera datorer.
* [Log Analytics arbets yta](../../azure-monitor/logs/design-logs-deployment.md)
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).
* Två Automation-tillgångar som används av **Enable-AutomationSolution-** runbooken. Denna Runbook, om den inte redan finns i ditt Automation-konto, importeras automatiskt av **Enable-MultipleSolution-** runbooken under den första körningen.
    * *LASolutionSubscriptionId*: PRENUMERATIONS-ID för den plats där Log Analytics arbets ytan finns.
    * *LASolutionWorkspaceId*: arbetsyte-ID för Log Analytics arbets ytan som är länkad till ditt Automation-konto.

    Dessa variabler används för att konfigurera arbets ytan för den virtuella datorn. Om dessa inte anges söker skriptet först efter virtuella datorer som har registrerats för att Ändringsspårning och inventeras i prenumerationen, följt av prenumerationen på Automation-kontot, följt av alla andra prenumerationer som ditt användar konto har åtkomst till. Om de inte är korrekt konfigurerade kan det leda till att dina datorer registreras på vissa slumpmässiga Log Analytics-arbetsytan.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

1. I Azure Portal navigerar du till **Automation-konton**. På sidan **Automation-konton** väljer du ditt konto i listan.

1. I ditt Automation-konto väljer du **inventering** eller **ändringsspårning** under **konfigurations hantering**.

1. Välj arbets ytan Log Analytics och klicka sedan på **Aktivera**. När inventering eller Ändringsspårning aktive ras visas en banderoll.

    ![Aktivera Ändringsspårning och inventering](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Du måste uppdatera till de senaste Azure-modulerna och importera modulen [AZ. OperationalInsights](/powershell/module/az.operationalinsights) för att kunna aktivera uppdateringshantering för dina virtuella datorer med runbooken.

1. I ditt Automation-konto väljer du **moduler** under **delade resurser**.

2. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen.

3. Klicka på **Ja** om du vill uppdatera alla befintliga Azure-moduler till den senaste versionen.

    ![Uppdatera moduler](media/enable-from-runbook/update-modules.png)

4. Återgå till **moduler** under **delade resurser**.

5. Välj **Bläddra i galleriet** för att öppna modulens Galleri.

6. Sök efter `Az.OperationalInsights` och importera modulen till ditt Automation-konto.

    ![Importera modulen OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Välj den virtuella Azure-dator som ska hanteras

Med Ändringsspårning och inventering aktive rad kan du lägga till en virtuell Azure-dator för hantering via funktionen.

1. Från ditt Automation-konto väljer du **ändrings spårning** eller **inventering** under **konfigurations hantering**.

2. Klicka på **Lägg till virtuella Azure-datorer** för att lägga till den virtuella datorn.

3. Välj den virtuella datorn i listan och klicka på **Aktivera**. Den här åtgärden aktiverar Ändringsspårning och inventering för den virtuella datorn.

   ![Aktivera Ändringsspårning och inventering för virtuell dator](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Om du försöker aktivera en annan funktion innan installationen av Ändringsspårning och inventeringen har slutförts visas följande meddelande: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importera en Runbook för att aktivera Ändringsspårning och inventering

1. I ditt Automation-konto väljer du **Runbooks** under **process automatisering**.

2. Välj **Sök i galleri**.

3. Sök efter **uppdatering och ändrings spårning**.

4. Välj Runbook och klicka på **Importera** på sidan **Visa källa** .

5. Klicka på **OK** för att importera runbooken till Automation-kontot.

   ![Importera Runbook för installation](media/enable-from-runbook/import-from-gallery.png)

6. På sidan **Runbook** väljer du Runbook **-flödet Enable-MultipleSolution** och klickar sedan på **Redigera**. Välj  **publicera** i text redigeraren.

7. När du uppmanas att bekräfta klickar du på **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha aktiverat Ändringsspårning och inventering för att en virtuell Azure-dator ska kunna starta denna Runbook. Den kräver en befintlig virtuell dator och resurs grupp med funktionen aktive rad för att konfigurera en eller flera virtuella datorer i mål resurs gruppen.

1. Öppna runbooken **Enable-MultipleSolution** .

   ![Runbook med flera lösningar](media/enable-from-runbook/runbook-overview.png)

2. Klicka på Start-knappen och ange parameter värden i följande fält:

   * **VMNAME** – namnet på en befintlig virtuell dator som ska läggas till i ändringsspårning och inventering. Lämna fältet tomt om du vill lägga till alla virtuella datorer i resurs gruppen.
   * **VMRESOURCEGROUP** – namnet på resurs gruppen för de virtuella datorer som ska aktive ras.
   * **SUBSCRIPTIONID** -PRENUMERATIONS-ID för den nya virtuella dator som ska aktive ras. Lämna fältet tomt om du vill använda arbets ytans prenumeration. När du använder ett annat prenumerations-ID lägger du till kör som-kontot för ditt Automation-konto som deltagare för prenumerationen.
   * **ALREADYONBOARDEDVM** – namnet på den virtuella dator som redan har Aktiver ATS manuellt för uppdateringar.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – namnet på den resurs grupp som den virtuella datorn tillhör.
   * **SOLUTIONTYPE** – ange **ChangeTracking**.

   ![Runbookparametrarna Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Välj **OK** för att starta runbookjobbet.

4. Övervaka förloppet för Runbook-jobbet och eventuella fel från **jobb** sidan.

## <a name="next-steps"></a>Nästa steg

* Information om hur du schemalägger en Runbook finns i [hantera scheman i Azure Automation](../shared-resources/schedules.md).

* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning](manage-change-tracking.md) och [Hantera inventering](manage-inventory-vms.md).

* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](../troubleshoot/change-tracking.md).


