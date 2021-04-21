---
title: Höjning av nätverksgräns
description: Höjning av nätverksgräns
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775573"
---
# <a name="networking-limit-increase"></a>Höjning av nätverksgräns

Använd Azure Portal [för](https://portal.azure.com) att öka din nätverkskvot.

Om du vill visa din aktuella nätverksanvändning och kvot i Azure Portal öppnar du din prenumeration och väljer **sedan Användningar + kvoter.** Du kan också använda följande alternativ för att visa nätverksanvändning och nätverksgränser.

* [Användnings-CLI](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API:et för nätverksanvändning](/rest/api/virtualnetwork/virtualnetworks/listusage)

Du kan begära en ökning med **hjälp av Hjälp + support** eller i **Användningar + kvoter** i portalen.

> [!Note]
> Om du vill ändra standardstorleken **för offentliga IP-prefix** väljer du Minsta längd på offentligt **IP-internätverksprefix** i listrutan.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Begära ökad nätverkskvot på prenumerationsnivå med hjälp av Hjälp + support

Följ anvisningarna nedan för att skapa en supportbegäran med **hjälp av Hjälp + support** i Azure Portal.

1. Logga in [på Azure Portal](https://portal.azure.com)och välj sedan Hjälp + **support** på Azure Portal eller sök efter och välj Hjälp **+ support.**

    ![Hjälp + Support](./media/networking-quota-request/help-plus-support.png)

1. Välj **Ny supportbegäran**.

    ![Ny supportbegäran](./media/networking-quota-request/new-support-request.png)

1. För **Typ av problem** väljer du **Tjänst- och prenumerationsgränser (kvoter).**

    ![Välj prenumerationsbegränsningar från listrutan för problemtyp](./media/networking-quota-request/select-quota-issue-type.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj prenumeration newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. Under **Kvottyp** väljer du **Nätverk.** Välj **Nästa: Lösningar**.

    ![Välj kvottyp](./media/networking-quota-request/select-quota-type-network.png)

1. I **PROBLEMINFORMATION** väljer du **Ange information och** fyller i ytterligare information för att bearbeta din begäran.

    ![Ange information](./media/networking-quota-request/provide-details-link.png)

1. I panelen **Kvotinformation** väljer du en distributionsmodell, en plats och de resurser som ska ingå i din begäran.

    ![DM för kvotinformation](./media/networking-quota-request/quota-details-network.png)

1. Ange de nya begränsningar som du vill ha för prenumerationen. Om du vill ta bort en rad avmarkerar du resursen från **menyn Resurser** eller väljer ikonen för att ta bort "x". När du har angett kvoten för varje resurs väljer **du Spara och fortsätter** att skapa supportbegäran.

    ![Nya gränser](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Begära ökad nätverkskvot på prenumerationsnivå med användning och kvoter

Följ de här anvisningarna för att skapa en supportbegäran **med hjälp av Användning +** kvot i Azure Portal.

1. Från https://portal.azure.com söker du efter och väljer **Prenumerationer.**

    ![Prenumerationer](./media/networking-quota-request/search-for-suscriptions.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj en prenumeration](./media/networking-quota-request/select-subscription-change-quota.png)

1. Välj **Användning + kvoter**

    ![Välj användning och kvoter](./media/networking-quota-request/select-usage-plus-quotas.png)

1. I det övre högra hörnet väljer du **Begär ökning.**

    ![Begärandeökning](./media/networking-quota-request/request-increase-from-subscription.png)

1. Följ stegen som börjar med steg 3 i [Öka kvoten för nätverksbegäran på prenumerationsnivå.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>Om nätverksgränser

Mer information om nätverksgränser finns i avsnittet [Nätverk på sidan](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) med begränsningar eller vanliga frågor och svar om nätverksgränser.
