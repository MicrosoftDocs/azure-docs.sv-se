---
title: Hantera prenumerations principer för Azure
description: Lär dig hur du hanterar Azures prenumerations principer för att styra flyttningen av Azure-prenumerationer från och till kataloger.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631878"
---
# <a name="manage-azure-subscription-policies"></a>Hantera prenumerations principer för Azure

>[!NOTE]
>Den här funktionen är för närvarande en för hands version och är gradvis distribuerad, så att inte alla kan se den här upplevelsen på Azure Portal ännu.

Den här artikeln hjälper dig att konfigurera prenumerations principer för Azure för prenumerations åtgärder för att styra flyttningen av Azure-prenumerationer från och till kataloger.

## <a name="prerequisites"></a>Förutsättningar

- Endast [globala katalog administratörer](../../active-directory/roles/permissions-reference.md#global-administrator) kan redigera prenumerations principer. Innan du redigerar prenumerations principer måste den globala administratören [höja åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md). Sedan kan de redigera prenumerations principer.
- Alla andra användare kan bara läsa den aktuella princip inställningen.

## <a name="available-subscription-policy-settings"></a>Tillgängliga prenumerations princip inställningar

Använd följande princip inställningar för att styra flyttningen av Azure-prenumerationer från och till kataloger.

### <a name="subscriptions-leaving-aad-directory"></a>Prenumerationer som lämnar AAD-katalogen

Principen tillåter eller hindrar användare från att flytta prenumerationer från den aktuella katalogen. [Prenumerations ägare](../../role-based-access-control/built-in-roles.md#owner) kan [ändra katalogen för en Azure-prenumeration](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) till en annan där de är medlemmar. Den utgör styrnings utmaningar, så globala administratörer kan tillåta eller neka katalog användare från att ändra katalogen.

### <a name="subscriptions-entering-aad-directory"></a>Prenumerationer som använder AAD-katalog

Principen tillåter eller stoppar användare från andra kataloger, som har åtkomst till den aktuella katalogen, för att flytta prenumerationer till den aktuella katalogen. [Prenumerations ägare](../../role-based-access-control/built-in-roles.md#owner) kan [ändra katalogen för en Azure-prenumeration](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) till en annan där de är medlemmar. Den utgör styrnings utmaningar, så globala administratörer kan tillåta eller neka katalog användare från att ändra katalogen.

### <a name="exempted-users"></a>Undantagna användare

Av styrnings skäl kan globala administratörer blockera alla prenumerations kataloger som flyttas – in i vår nuvarande katalog. Det kan dock vara bra att tillåta vissa användare att utföra båda åtgärderna. I båda situationer kan de konfigurera en lista över undantagna användare som gör det möjligt för användarna att kringgå den princip inställning som gäller för alla andra.

## <a name="setting-subscription-policy"></a>Ställer in prenumerations princip

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till **prenumerationer**. **Hantera principer** visas i kommando fältet.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Skärm bild som visar hantera principer i prenumerationer." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Välj **Hantera principer** om du vill visa information om aktuella prenumerations principer som har angetts för katalogen. En global administratör med [utökade behörigheter](../../role-based-access-control/elevate-access-global-admin.md) kan göra ändringar i inställningarna, inklusive lägga till eller ta bort undantagna användare.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Skärm bild som visar vissa princip inställningar och undantagna användare." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Välj **Spara ändringar** längst ned för att spara ändringarna. Ändringarna träder i kraft omedelbart.

## <a name="read-subscription-policy"></a>Läs prenumerations princip

Icke-globala administratörer kan fortfarande navigera till prenumerations principens region för att Visa katalogens princip inställningar. De kan inte göra några ändringar. De kan inte se listan över undantagna användare av sekretess skäl. De kan visa sina globala administratörer för att skicka förfrågningar om princip ändringar, så länge katalog inställningarna tillåter dem.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Skärm bild som visar hantera principer i prenumerationer som en läsare." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Nästa steg

- Läs [Cost Management + fakturerings dokumentation](../index.yml)