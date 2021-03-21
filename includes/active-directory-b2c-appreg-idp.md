---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 890b19cd8d6f4bc836f6f541a830c1a5feb8458f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026434"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *testapp1*.
1. Välj **konton i en organisations katalog eller en identitets leverantör**.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan `https://jwt.ms` i text rutan URL.
1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Register** (Registrera).

När program registreringen är klar aktiverar du det implicita tilldelnings flödet:

1. Under **Hantera** väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande** väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **program** och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *testapp1*.
1. För **webbapp/webb-API** väljer du **Ja**.
1. För **svars-URL** anger du `https://jwt.ms`
1. Välj **Skapa**.