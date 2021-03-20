---
title: Beteende för interaktiv fråga (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar uppmanings beteende i interaktiva anrop med Microsoft Authentication Library för Java Script (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85477557"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Fråga om beteende i MSAL.js interaktiva begär Anden

När en användare har upprättat en aktiv Azure AD-session med flera användar konton uppmanas inloggnings sidan för Azure AD som standard att användaren väljer ett konto innan du loggar in. Användarna ser ingen upplevelse för val av konto om det bara finns en enda autentiserad session med Azure AD.

MSAL.js-biblioteket (från och med v-0.2.4) skickar inte någon prompt-parameter under de interaktiva begär Anden ( `loginRedirect` , `loginPopup` , `acquireTokenRedirect` och `acquireTokenPopup` ) och kommer därmed inte att tillämpa några uppfrågade beteenden. För tysta token-begäranden med hjälp av `acquireTokenSilent` metoden, MSAL.js skickar en prompt-parameter inställd på `none` .

Baserat på ditt program scenario kan du kontrol lera frågans beteende för de interaktiva förfrågningarna genom att ställa in prompt-parametern i parametrarna för begäran som skickas till metoderna. Om du till exempel vill starta upplevelsen för konto val:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Följande prompt-värden kan skickas vid autentisering med Azure AD:

**inloggning:** Det här värdet tvingar användaren att ange autentiseringsuppgifter för autentiseringsbegäran.

**select_account:** Med det här värdet får användaren ett konto val som visar alla konton i sessionen.

**medgivande:** Med det här värdet anropas dialog rutan OAuth-medgivande som gör det möjligt för användarna att bevilja behörighet till appen.

**Ingen:** Det här värdet ser till att användaren inte ser någon interaktiv prompt. Vi rekommenderar att du inte skickar det här värdet till interaktiva metoder i MSAL.js eftersom det kan ha oväntade beteenden. Använd i stället `acquireTokenSilent` metoden för att få tysta samtal.

## <a name="next-steps"></a>Nästa steg

Läs mer om `prompt` parametern i det [implicita beviljande protokollet OAuth 2,0](v2-oauth2-implicit-grant-flow.md) som MSAL.js-biblioteket använder.
