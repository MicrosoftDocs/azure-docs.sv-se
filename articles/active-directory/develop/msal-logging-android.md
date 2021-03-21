---
title: Loggnings fel och undantag i MSAL för Android.
titleSuffix: Microsoft identity platform
description: Lär dig hur du loggar fel och undantag i MSAL för Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954892"
---
# <a name="logging-in-msal-for-android"></a>Loggning i MSAL för Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Logga in MSAL för Android med Java

Aktivera loggning när appen skapas genom att skapa en loggning för motringning. Återanropet använder följande parametrar:

- `tag` är en sträng som skickas till återanropet av biblioteket. Den är kopplad till logg posten och kan användas för att sortera loggnings meddelanden.
- `logLevel` gör att du kan bestämma vilken loggnings nivå du vill ha. De logg nivåer som stöds är: `Error` , `Warning` , `Info` och `Verbose` .
- `message` är innehållet i logg posten.
- `containsPII` Anger om meddelanden som innehåller personliga data eller organisations data loggas. Som standard är detta inställt på falskt, så att programmet inte loggar personliga data. Om `containsPII` är `true` , kommer den här metoden att ta emot meddelanden två gånger: När `containsPII` parametern har angetts till `false` och `message` utan personliga data, och en andra gång med `containsPii` parametern inställt på `true` och meddelandet kan innehålla personliga data. I vissa fall (när meddelandet inte innehåller personliga data) är meddelandet samma.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Som standard kommer MSAL-loggaren inte att samla in personlig identifierbar information eller information som är identifierbar för organisationen.
Så här aktiverar du loggning av personlig identifierbar information eller organisatorisk identifierbar information:

```java
Logger.getInstance().setEnablePII(true);
```

Så här inaktiverar du loggning av personliga data och organisations data:

```java
Logger.getInstance().setEnablePII(false);
```

Som standard är loggning till logcat inaktive rad. För att aktivera:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).