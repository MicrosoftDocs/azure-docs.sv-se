---
title: Aktivera Azure Automation Ändringsspårning och inventering från Automation-konto
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från ett Automation-konto.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 32fb95c88d632cc2c51cd2390f0244e9c1927051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585890"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Aktivera Ändringsspårning och inventering från ett Automation-konto

I den här artikeln beskrivs hur du kan använda ditt Automation-konto för att aktivera [ändringsspårning och inventering](overview.md) för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering.

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../automation-security-overview.md) för att hantera datorer.
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

1. Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera ändringsspårning och inventering. Det tar upp till 15 minuter att slutföra installationen.

    ![Aktivera Ändringsspårning och inventering](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Aktivera virtuella Azure-datorer

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Klicka på **+ Lägg till virtuella Azure-datorer** och välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktive ras är nedtonade och kan inte väljas. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. 

3. Klicka på **Aktivera** för att lägga till de valda virtuella datorerna i dator gruppens sparade sökning efter funktionen. Mer information finns i [begränsa ändringsspårning och distributions omfång för lager](manage-scope-configurations.md).

      [![Aktivera virtuella Azure-datorer](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Aktivera virtuella datorer som inte är från Azure

Datorer som inte i Azure måste läggas till manuellt. Vi rekommenderar att du installerar Log Analytics agent för Windows eller Linux genom att först ansluta datorn till [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md)och sedan använda Azure policy för att tilldela den inbyggda principen [distribuera Log Analytics agent till *Linux* eller *Windows* Azure Arc Machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Om du också planerar att övervaka datorerna med Azure Monitor for VMs använder du i stället [aktivera Azure Monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) initiativ.

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Klicka på **Lägg till icke-Azure-dator**. Den här åtgärden öppnar ett nytt webbläsarfönster med [instruktioner för att installera och konfigurera Log Analytics agent för Windows](../../azure-monitor/agents/log-analytics-agent.md) så att datorn kan börja rapportera ändringsspårning-och inventerings åtgärder. Om du aktiverar en dator som för närvarande hanteras av Operations Manager krävs inte en ny agent och informationen i arbets ytan anges i den befintliga agenten.

## <a name="enable-machines-in-the-workspace"></a>Aktivera datorer i arbets ytan

Manuellt installerade datorer eller datorer som redan rapporterar till din arbets yta måste läggas till Azure Automation för att Ändringsspårning och inventering ska kunna aktive ras.

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj **hantera datorer**. Alternativet **hantera datorer** kan vara nedtonat om du tidigare har valt alternativet **Aktivera på alla tillgängliga och framtida datorer**

    ![Sparade sökningar](media/enable-from-automation-account/manage-machines.png)

3. Om du vill aktivera Ändringsspårning och inventering för alla tillgängliga datorer väljer du **Aktivera på alla tillgängliga datorer** på sidan **hantera datorer** . Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt och lägger till alla datorer som rapporterar till arbets ytan till dator gruppens sparade Sök fråga. När det här alternativet är markerat inaktiverar den här åtgärden alternativet **hantera datorer** .

4. Om du vill aktivera funktionen för alla tillgängliga datorer och framtida datorer väljer du **Aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort den sparade Sök-och omfattnings konfigurationen från arbets ytan och öppnar funktionen för alla Azure-och icke-Azure-datorer som rapporterar till arbets ytan. När det här alternativet är markerat inaktiverar den här åtgärden alternativet **hantera datorer** permanent, eftersom det inte finns någon omfattnings konfiguration kvar.

    > [!NOTE]
    > Eftersom det här alternativet tar bort den sparade Sök-och omfattnings konfigurationen inom Log Analytics, är det viktigt att ta bort eventuella borttagnings lås på arbets ytan Log Analytics innan du väljer det här alternativet. Om du inte gör det kan inte alternativet ta bort konfigurationerna och du måste ta bort dem manuellt.

5. Om det behövs kan du lägga till omfattnings konfigurationen igen genom att lägga till den första sparade sökningen igen. Mer information finns i [begränsa ändringsspårning och distributions omfång för lager](manage-scope-configurations.md).

6. Om du vill aktivera funktionen för en eller flera datorer väljer du **Aktivera på valda datorer** och klickar på **Lägg till** bredvid varje dator för att aktivera funktionen. Den här uppgiften lägger till de valda dator namnen i den dator grupp sparade Sök frågan för funktionen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning](manage-change-tracking.md) och [Hantera inventering](manage-inventory-vms.md).

* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](../troubleshoot/change-tracking.md).
