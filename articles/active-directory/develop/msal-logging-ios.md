---
title: Loggnings fel och undantag i MSAL för iOS/macOS
titleSuffix: Microsoft identity platform
description: Lär dig hur du loggar fel och undantag i MSAL för iOS/macOS
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763487"
---
# <a name="logging-in-msal-for-iosmacos"></a>Loggning i MSAL för iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL för iOS och macOS-loggning – ObjC

Ange ett återanrop för att avbilda MSAL-loggning och införliva den i ditt eget programs loggning. Signaturen för återanropet ser ut så här:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Ett exempel:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Personuppgifter

Som standard samlar MSAL inte in eller loggar personliga data. Med biblioteket kan utvecklare aktivera detta genom en egenskap i MSALLogger-klassen. Genom att aktivera `pii.Enabled` , tar appen ansvar för säker hantering av mycket känsliga data och följande regler för krav.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Loggnings nivåer

Använd något av följande värden om du vill ange loggnings nivå när du loggar med MSAL för iOS och macOS:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standard nivå skrivs bara ut information när fel uppstår |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteks start punkter, med parametrar och olika nyckel rings åtgärder |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL för iOS och macOS-loggning – Swift

Ange ett återanrop för att avbilda MSAL-loggning och införliva den i ditt eget programs loggning. Signaturen (representeras i mål-C) för återanropet ser ut så här:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Ett exempel:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Personuppgifter

Som standard samlar MSAL inte in eller loggar personliga data. Med biblioteket kan utvecklare aktivera detta genom en egenskap i MSALLogger-klassen. Genom att aktivera `pii.Enabled` , tar appen ansvar för säker hantering av mycket känsliga data och följande regler för krav.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Loggnings nivåer

Använd något av följande värden om du vill ange loggnings nivå när du loggar med MSAL för iOS och macOS:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standard nivå skrivs bara ut information när fel uppstår |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteks start punkter, med parametrar och olika nyckel rings åtgärder |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.

---

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).