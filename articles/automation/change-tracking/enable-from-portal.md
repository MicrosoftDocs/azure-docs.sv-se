---
title: Aktivera Azure Automation Ändringsspårning och inventering från Azure Portal
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning-och inventerings funktionen från Azure Portal.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 63041e0b1b6e12c765299b12f28aa3637b6a6ccb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052795"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Aktivera Ändringsspårning och inventering från Azure Portal

I den här artikeln beskrivs hur du kan aktivera [ändringsspårning och inventering](overview.md) för en eller flera virtuella Azure-datorer i Azure Portal. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering.

Antalet resurs grupper som du kan använda för att hantera dina virtuella datorer begränsas av [distributions gränserna för Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). Resource Manager-distributioner är begränsade till fem resurs grupper per distribution. Två av dessa resurs grupper är reserverade för att konfigurera Log Analytics arbets yta, Automation-konto och relaterade resurser. Detta gör att du har tre resurs grupper som du kan välja för hantering genom Ändringsspårning och inventering. Den här gränsen gäller endast för samtidig installation, inte antalet resurs grupper som kan hanteras av en automatiserings funktion.

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../automation-security-overview.md) för att hantera datorer.
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

1. I Azure Portal navigerar du till **virtuella datorer**.

2. Använd kryss rutorna för att välja de virtuella datorer som ska läggas till Ändringsspårning och inventering. Du kan lägga till datorer för upp till tre olika resurs grupper i taget. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett var ditt Automation-konto finns.

    ![Lista över virtuella datorer](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Använd filter kontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resurs grupper. Du kan klicka på den översta kryss rutan för att välja alla virtuella datorer i en lista.

3. Välj **ändrings spårning** eller **inventering** under **konfigurations hantering**.

4. Listan över virtuella datorer är filtrerad för att endast visa de virtuella datorer som finns i samma prenumeration och plats. Om dina virtuella datorer finns i fler än tre resurs grupper väljs de första tre resurs grupperna.

5. En befintlig Log Analytics arbets yta och ett Automation-konto väljs som standard. Om du vill använda en annan Log Analytics arbets yta och ett Automation-konto klickar du på **anpassad** och väljer dem på sidan anpassad konfiguration. När du väljer en Log Analytics arbets yta görs en kontroll för att avgöra om den är länkad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärm. När du är färdig klickar du på **OK**.

    ![Välj arbets yta och konto](media/enable-from-portal/select-workspace-and-account.png)

6. Om den valda arbets ytan inte är länkad till ett Automation-konto visas följande skärm. Välj ett Automation-konto och klicka på **OK** när du är färdig.

    ![Ingen arbets yta](media/enable-from-portal/no-workspace.png)

7. Avmarkera kryss rutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktive ras avmarkeras redan.

8. Klicka på **Aktivera** för att aktivera funktionen som du har valt. Det tar upp till 15 minuter att slutföra installationen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning](manage-change-tracking.md) och [Hantera inventering](manage-inventory-vms.md).
* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](../troubleshoot/change-tracking.md).