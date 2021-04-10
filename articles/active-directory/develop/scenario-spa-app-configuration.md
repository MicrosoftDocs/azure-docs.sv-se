---
title: Konfigurera en app med en enda sida | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett program med en enda sida (appens kod konfiguration)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010718"
---
# <a name="single-page-application-code-configuration"></a>Program med en sida: kod konfiguration

Lär dig hur du konfigurerar koden för en Enkels Ides applikation (SPA).

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Microsoft-bibliotek som stöder appar med en sida 

Följande Microsoft-bibliotek stöder appar med en sida:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>Program kod konfiguration

I ett MSAL-bibliotek skickas programmets registrerings information som konfiguration under biblioteks initieringen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Mer information om konfigurerbara alternativ finns i avsnittet [om att initiera program med MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](scenario-spa-sign-in.md).
