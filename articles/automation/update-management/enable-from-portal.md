---
title: Aktivera Azure Automation Uppdateringshantering från Azure Portal
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054914"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Aktivera Uppdateringshantering i Azure-portalen

I den här artikeln beskrivs hur du kan aktivera den [uppdateringshantering](overview.md) funktionen för virtuella datorer genom att bläddra i Azure Portal. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell Azure-dator med hjälp av Uppdateringshantering.

Antalet resurs grupper som du kan använda för att hantera dina virtuella datorer begränsas av [distributions gränserna för Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). Resource Manager-distributioner, ska inte förväxlas med uppdaterings distributioner, är begränsade till fem resurs grupper per distribution. Två av dessa resurs grupper är reserverade för att konfigurera Log Analytics arbets yta, Automation-konto och relaterade resurser. Detta gör att du har tre resurs grupper som du kan välja för hantering genom att Uppdateringshantering. Den här gränsen gäller endast för samtidig installation, inte antalet resurs grupper som kan hanteras av en automatiserings funktion.

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../automation-security-overview.md) för att hantera datorer.
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

1. I Azure Portal navigerar du till **virtuella datorer**.

2. På sidan **virtuella datorer** använder du kryss rutorna för att välja de virtuella datorer som ska läggas till uppdateringshantering. Du kan lägga till datorer för upp till tre olika resurs grupper i taget. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett var ditt Automation-konto finns.

    ![Lista över virtuella datorer](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Använd filter kontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resurs grupper. Du kan klicka på den översta kryss rutan för att välja alla virtuella datorer i en lista.

    [![Aktivera uppdateringshantering](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Välj **tjänster** och välj **Uppdateringshantering** för funktionen uppdateringshantering.

4. Listan över virtuella datorer är filtrerad för att endast visa de virtuella datorer som finns i samma prenumeration och plats. Om dina virtuella datorer finns i fler än tre resurs grupper väljs de första tre resurs grupperna.

5. En befintlig Log Analytics arbets yta och ett Automation-konto väljs som standard. Om du vill använda en annan Log Analytics arbets yta och ett Automation-konto väljer du **anpassad** för att välja dem från sidan anpassad konfiguration. När du väljer en Log Analytics arbets yta görs en kontroll för att avgöra om den är länkad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärm. När du är klar väljer du **OK**.

    [![Välj arbets yta och konto](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Om den valda arbets ytan inte är länkad till ett Automation-konto visas följande skärm. Välj ett Automation-konto och välj **OK** när du är färdig.

    ![Ingen arbets yta](media/enable-from-portal/no-workspace.png)

7. Avmarkera alla virtuella datorer som du inte vill aktivera. Virtuella datorer som inte kan aktive ras avmarkeras redan.

8. Välj **Aktivera** för att aktivera funktionen. När du har aktiverat Uppdateringshantering kan det ta ungefär 15 minuter innan du kan visa uppdaterings utvärderingen från dem.

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella datorer](manage-updates-for-vm.md).
* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](../troubleshoot/update-management.md).
* Information om hur du felsöker problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](../troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux Update-agenten finns i [Felsöka problem med Linux Update Agent](../troubleshoot/update-agent-issues-linux.md).