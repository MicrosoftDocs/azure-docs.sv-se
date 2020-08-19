---
title: Lägga till taggar i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar anpassade taggar i Azure DevTest Labs och använder taggar för att kategorisera resurser. Du kan se alla resurser i din prenumeration som har en-tagg.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270741"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Lägga till taggar i ett labb i Azure DevTest Labs

Du kan skapa anpassade taggar och tillämpa dem på dina DevTest Labs-resurser för att logiskt kategorisera dina resurser. Senare kan du snabbt och enkelt se alla resurser i din prenumeration som har den taggen. Taggar är användbara när du behöver organisera resurser för fakturering eller hantering.

Resurser som stöds av taggar är

* Beräkna virtuella datorer
* Nätverkskort
* IP-adresser
* Lastbalanserare
* Lagringskonton
* Hanterade diskar

Du kan använda taggar när du [skapar ett labb](devtest-lab-create-lab.md) och senare hanterar dem via bladet Taggar under konfiguration och inställningar.

Varje tagg består av ett **namn** / **värde** par. Du kan till exempel skapa en tagg med namnet *CostCenter* som har värdet *34543*. En tagg som detta kan hjälpa dig att senare identifiera labb resurser som är fakturerbara i det här avsnittet av din organisation. Du kan välja namn och värden som passar för hur du vill organisera din prenumeration.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Steg för att hantera Taggar i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **alla tjänster**och väljer sedan **DevTest Labs** i listan. Ditt labb kanske redan visas på instrument panelen under **alla resurser**.
1. I listan med labb väljer du det labb i vilket du vill lägga till eller hantera taggar.
1. I labb **översikts** avsnittet väljer du **konfiguration och principer**.

    ![Knappen konfiguration och principer](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Välj **taggar**till vänster under **Hantera**.
1. Om du vill skapa en ny tagg för det här labbet anger du ett **namn** / **värde** par och väljer **Spara**. Du kan också välja en befintlig tagg från listan för att visa eller hantera de resurser som är kopplade till den taggen.

    ![Hantera Taggar](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Taggar som skapats på labb nivå flödet genom alla fakturerbara resurser som labbet snurrar upp i din prenumeration. Till exempel kan taggar på labb nivå flöda till de underliggande virtuella datorerna för virtuella datorer med labb. du kan använda taggar i samband med kostnads hantering. Labb nivå taggar visas i tag-filtret för Cost Management.

## <a name="understanding-limitations-to-tags"></a>Förstå begränsningar för Taggar

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

[Använd taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md) ger mer information om hur du använder taggar i Azure, inklusive hur du hanterar Taggar med PowerShell eller Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan tillämpa begränsningar och konventioner i din prenumeration genom att använda anpassade principer. En princip som du definierar kan kräva att alla resurser har ett värde för en viss tagg. Mer information finns i [ange principer och scheman](devtest-lab-set-lab-policy.md).
* Utforska [DevTest Labs Azure Resource Manager snabb starts galleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
