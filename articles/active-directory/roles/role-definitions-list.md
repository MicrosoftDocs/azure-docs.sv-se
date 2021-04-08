---
title: Visa lista över Azure AD-roll definitioner – Azure AD
description: Lär dig hur du visar inbyggda och anpassade Azure-roller i Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467923"
---
# <a name="list-azure-ad-role-definitions"></a>Visa lista över Azure AD-roll definitioner

En roll definition är en samling behörigheter som kan utföras, till exempel läsa, skriva och ta bort. Den brukar bara kallas en roll. Azure Active Directory har över 60 inbyggda roller eller så kan du skapa egna anpassade roller. Om du har undrat "Vad gör dessa roller egentligen?" kan du se en detaljerad lista över behörigheter för varje roll.

Den här artikeln beskriver hur du visar en lista över inbyggda och anpassade Azure AD-roller tillsammans med deras behörigheter.

## <a name="list-all-roles"></a>Lista alla roller

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com) och välj **Azure Active Directory**.

1. Välj **roller och administratörer** om du vill se en lista över alla tillgängliga roller.

    ![lista över roller i Azure AD Portal](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Till höger väljer du ellipsen och sedan **Beskrivning** för att se en fullständig lista över behörigheter för en roll.

    Sidan innehåller länkar till relevant dokumentation för att hjälpa dig att hantera roller.

    ![Skärm bild som visar sidan "global administratörs Beskrivning".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
