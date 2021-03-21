---
title: Loggnings fel och undantag i MSAL.js
titleSuffix: Microsoft identity platform
description: Lär dig hur du loggar fel och undantag i MSAL.js
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954825"
---
# <a name="logging-in-msaljs"></a>Loggning in MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Konfigurera loggning i MSAL.js

Aktivera loggning i MSAL.js (Java Script) genom att skicka ett loggnings objekt under konfigurationen för att skapa en `UserAgentApplication` instans. Detta loggnings objekt har följande egenskaper:

- `localCallback`: en callback-instans som kan tillhandahållas av utvecklaren för att använda och publicera loggar på ett anpassat sätt. Implementera localCallback-metoden beroende på hur du vill dirigera om loggarna.
- `level` (valfritt): den konfigurerbara logg nivån. De logg nivåer som stöds är: `Error` , `Warning` , `Info` och `Verbose` . Standardvärdet är `Info`.
- `piiLoggingEnabled` (valfritt): om värdet är sant loggas personliga och organisatoriska data. Som standard är detta falskt så att programmet inte loggar personliga data. Personliga data loggar skrivs aldrig till standardutdata som konsol, logcat eller NSLog.
- `correlationId` (valfritt): en unik identifierare som används för att mappa begäran med svar på fel söknings syfte. Standardvärdet är RFC4122 version 4 GUID (128 bitar).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).