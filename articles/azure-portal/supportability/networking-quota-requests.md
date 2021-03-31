---
title: Höjning av nätverksgräns
description: Höjning av nätverksgräns
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745459"
---
# <a name="networking-limit-increase"></a>Höjning av nätverksgräns

Använd [Azure Portal](https://portal.azure.com) för att öka nätverks kvoten.

Om du vill visa din aktuella nätverks användning och kvot i Azure Portal öppnar du din prenumeration och väljer sedan **användnings områden + kvoter**. Du kan också använda följande alternativ för att Visa nätverks användning och-gränser.

* [Användnings-CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API för nätverks användning](/rest/api/virtualnetwork/virtualnetworks/listusage)

Du kan begära en ökning genom **att använda hjälp + support** eller i **användnings** -och kvoter i portalen.

> [!Note]
> Om du vill ändra standard storleken för **offentliga IP-prefix** väljer du den **minsta offentliga IP-prefixets längd** i list rutan.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Begär nätverks kvot öka på prenumerations nivå med hjälp + support

Följ instruktionerna nedan om du vill skapa en supportbegäran med **Hjälp + Support** i Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj sedan **Hjälp + Support** på Azure Portal-menyn eller Sök efter och välj **Hjälp + Support**.

    ![Hjälp + Support](./media/networking-quota-request/help-plus-support.png)

1. Välj **Ny supportbegäran**.

    ![Ny supportbegäran](./media/networking-quota-request/new-support-request.png)

1. För **typ av problem** väljer du **tjänst-och prenumerations gränser (kvoter)**.

    ![List rutan för att välja prenumerations gränser från ärende typ](./media/networking-quota-request/select-quota-issue-type.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj prenumerations nyheter](./media/networking-quota-request/select-subscription-support-request.png)

1. Under **typ av kvot** väljer du **nätverk**. Välj **Nästa: lösningar**.

    ![Välj typ av kvot](./media/networking-quota-request/select-quota-type-network.png)

1. I **problem information** väljer du **Ange information** och fyller i ytterligare information som kan hjälpa dig att bearbeta din begäran.

    ![Ange information](./media/networking-quota-request/provide-details-link.png)

1. I panelen **kvot information** väljer du en distributions modell, en plats och de resurser som ska ingå i din begäran.

    ![Kvot information DM](./media/networking-quota-request/quota-details-network.png)

1. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du resursen från menyn **resurser** eller väljer ikonen Ta bort x. När du har angett kvoten för varje resurs väljer du **Spara och fortsätter** med att skapa support förfrågan.

    ![Nya gränser](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Begär nätverks kvot öka på prenumerations nivå med användning + kvoter

Följ de här anvisningarna för att skapa en supportbegäran med hjälp av **användnings** -och kvot i Azure Portal.

1. Från https://portal.azure.com söker du efter och väljer **prenumerationer**.

    ![Prenumerationer](./media/networking-quota-request/search-for-suscriptions.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj en prenumeration](./media/networking-quota-request/select-subscription-change-quota.png)

1. Välj **användning + kvoter**

    ![Välj användning och kvoter](./media/networking-quota-request/select-usage-plus-quotas.png)

1. I det övre högra hörnet väljer du **begär ökning**.

    ![Begär ökning](./media/networking-quota-request/request-increase-from-subscription.png)

1. Följ stegen som börjar med steg 3 i [begäran nätverks kvot öka på prenumerations nivå](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Om nätverks begränsningar

Mer information om nätverks begränsningar finns i [avsnittet nätverk](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) på sidan gränser eller vanliga frågor och svar om nätverks begränsningar.
