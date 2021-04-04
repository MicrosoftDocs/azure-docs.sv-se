---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026287"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *nativeapp1*.
1. Under **konto typer som stöds** väljer du **konton i valfri organisations katalog eller någon identitets leverantör**.
1. Under **omdirigerings-URI** använder du List rutan för att välja **offentlig klient/ursprunglig (mobil & Desktop)**.
1. Ange en omdirigerings-URI med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns viktiga överväganden när du väljer en omdirigerings-URI:
    * **Utveckling** För att utveckla användningen kan du ange omdirigerings-URI: n till `http://localhost` och Azure AD B2C att respektera vilken port som helst i begäran. Om den registrerade URI: n innehåller en port använder Azure AD B2C endast den porten. Om t. ex. den registrerade omdirigerings-URI: n är `http://localhost` , kan omdirigerings-URI: n i begäran vara `http://localhost:<randomport>` . Om den registrerade omdirigerings-URI: n är `http://localhost:8080` , måste omdirigerings-URI: n i begäran vara `http://localhost:8080` .
    * **Unikt**: schemat för omdirigerings-URI: n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt går det inte att logga in.
    * **Slutfört**: omdirigerings-URI: n måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar det `//oauth` inte. Ta inte med specialtecken i URI, till exempel under streck.
1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
2. Välj **Register** (Registrera).

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *nativeapp1*.
1. För **Native Client** väljer du **Ja**.
1. Ange en **anpassad omdirigerings-URI** med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    * **Unikt**: schemat för omdirigerings-URI: n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt går det inte att logga in.
    * **Slutfört**: omdirigerings-URI: n måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar det `//oauth` inte. Ta inte med specialtecken i URI, till exempel under streck.
1. Välj **Skapa**.