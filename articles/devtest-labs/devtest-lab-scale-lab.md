---
title: Skala kvoter och begränsningar i din labb Azure DevTest Labs | Microsoft Docs
description: I den här artikeln beskrivs hur du kan skala ditt labb i Azure DevTest Labs. Visa användnings kvoter och-gränser och begär en ökning.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87533962"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalnings kvoter och begränsningar i DevTest Labs
När du arbetar i DevTest Labs kanske du märker att det finns vissa standard gränser för vissa Azure-resurser, vilket kan påverka DevTest Labs-tjänsten. Dessa gränser kallas **kvoter**.

> [!NOTE]
> DevTest Labs-tjänsten tillämpar inga kvoter. Alla kvoter som du kan stöta på är standard begränsningar för den övergripande Azure-prenumerationen.

Du kan använda varje Azure-resurs tills du når kvoten. Varje prenumeration har separata kvoter och användningen spåras per prenumeration.

Varje prenumeration har till exempel en standard kvot på 20 kärnor. Så om du skapar virtuella datorer i labbet med fyra kärnor var och en, kan du bara skapa fem virtuella datorer.

[Azure-prenumerations-och tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) listar några av de vanligaste kvoterna för Azure-resurser. De resurser som används oftast i ett labb, och för vilka du kan stöta på kvoter, inkludera VM-kärnor, offentliga IP-adresser, nätverks gränssnitt, hanterade diskar, roll tilldelning i Azure och ExpressRoute-kretsar.

## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter
De här stegen visar hur du visar de aktuella kvoterna i din prenumeration för vissa Azure-resurser och hur många procent av varje kvot du har använt.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster** och välj sedan **fakturering** i listan.
1. Välj en prenumeration på bladet fakturering.
4. Välj **användning + kvoter**.

   ![Knappen användning och kvoter](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   Bladet användning och kvoter visas och visar olika tillgängliga resurser i den prenumerationen och den procent andel av kvoten som används per resurs.

   ![Kvoter och användning](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Begära fler resurser i din prenumeration
Om du når en kvot gräns kan standard gränsen för en resurs i en prenumeration ökas till en maxgräns, enligt beskrivningen i [Azure-prenumeration och tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

De här stegen visar hur du begär en kvot ökning via [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **fler tjänster**, Välj **fakturering** och välj sedan **användning + kvoter**.
1. På bladet användning och kvoter väljer du knappen för att **öka begäran** .

   ![Begär öknings knapp](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Slutför och skicka begäran genom att fylla i den information som krävs på alla tre flikarna i formuläret för den **nya support förfrågningen** .

   ![Begär öknings formulär](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

Att [förstå Azure-gränser och ökningar](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) ger mer information om hur du kontaktar Azure-supporten för att begära en kvot ökning.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nästa steg
* Utforska [DevTest Labs Azure Resource Manager snabb starts galleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
