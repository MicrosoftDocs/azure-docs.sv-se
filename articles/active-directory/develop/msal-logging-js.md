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
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763498"
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