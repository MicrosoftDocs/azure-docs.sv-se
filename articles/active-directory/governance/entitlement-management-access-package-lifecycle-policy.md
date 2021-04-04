---
title: Ändra livs cykel inställningar för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du ändrar information om beställare & livs cykel inställningar för ett Access-paket i Azure Active Directory hantering av rättigheter.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980111"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra livs cykel inställningar för ett Access-paket i hantering av Azure AD-rättigheter

Som en Access Package Manager kan du när som helst ändra livs cykel inställningarna för ett Access-paket genom att redigera en befintlig princip. Om du ändrar utgångs datumet för en princip ändras inte förfallo datumet för begär Anden som redan har statusen väntar på godkännande eller godkänt.

I den här artikeln beskrivs hur du ändrar livs cykel inställningarna för ett befintligt Access-paket.

## <a name="open-requestor-information"></a>Öppna information om beställare
För att se till att användarna har rätt åtkomst till ett Access-paket kan anpassade frågor konfigureras för att be användare som begär åtkomst till vissa åtkomst paket. Konfigurations alternativen omfattar följande svars format: lokalisering, obligatoriska/valfria och text/flera alternativ. Fråge ställare ser frågorna när de begär att paketet och god kännarna ska se svaren på frågorna för att hjälpa dem fatta sitt beslut. Använd följande steg för att konfigurera frågor i ett Access-paket:

## <a name="open-lifecycle-settings"></a>Öppna livs cykel inställningar

Om du vill ändra livs cykel inställningarna för ett Access-paket måste du öppna motsvarande princip. Följ de här stegen för att öppna livs cykel inställningarna för ett Access-paket.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och sedan på den princip som har de livs cykel inställningar som du vill redigera.

    Fönstret princip information visas längst ned på sidan.

    ![Åtkomst paket – princip informations fönstret](./media/entitlement-management-shared/policy-details.png)

1. Redigera principen genom att klicka på **Redigera** .

    ![Åtkomst paket – redigera princip](./media/entitlement-management-shared/policy-edit.png)

1. Klicka på fliken **livs cykel** för att öppna inställningarna för livs cykeln.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Nästa steg

- [Inställningar för ändring av begäran och godkännande för ett Access-paket](entitlement-management-access-package-request-policy.md)