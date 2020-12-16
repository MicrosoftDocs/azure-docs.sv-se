---
title: Logga in MSAL-appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om loggning i MSAL-program (Microsoft Authentication Library).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: b53a12db9203121d12a69c10aaa81bceab5c1754
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584263"
---
# <a name="logging-in-msal-applications"></a>Logga in MSAL-program

MSAL-appar (Microsoft Authentication Library) genererar logg meddelanden som kan hjälpa dig att diagnostisera problem. En app kan konfigurera loggning med några rader kod och har anpassad kontroll över detalj nivån och huruvida personliga och organisatoriska data loggas. Vi rekommenderar att du skapar ett återanrop för MSAL-loggning och ger användarna möjlighet att skicka loggar när de har problem med autentiseringen.

## <a name="logging-levels"></a>Loggnings nivåer

MSAL tillhandahåller flera nivåer av loggnings information:

- Fel: indikerar att något har gått fel och ett fel genererades. Använd för fel sökning och identifiering av problem.
- Varning! det har inte nödvändigt vis uppstått något fel eller fel, men är avsedda för diagnostik och att hitta problem.
- Info: MSAL kommer att logga händelser som är avsedda för informations ändamål som inte nödvändigt vis är avsedda för fel sökning.
- Verbose: standard. MSAL loggar fullständig information om biblioteks beteendet.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data

Som standard fångar MSAL-loggaren inte upp mycket känsliga personliga eller organisatoriska data. Biblioteket ger möjlighet att aktivera loggning av personliga och organisatoriska data om du bestämmer dig för detta.

Om du vill ha mer information om MSAL-loggning på ett visst språk väljer du fliken som matchar ditt språk:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Loggning i MSAL.NET

 > [!NOTE]
 > Se [MSAL.net-wikin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) för exempel på MSAL.net-loggning med mera.

I MSAL 3. x anges loggning per program när appen skapas med hjälp av `.WithLogging` Builder-modifieraren. Den här metoden kräver valfria parametrar:

- `Level` gör att du kan bestämma vilken loggnings nivå du vill ha. Om du ställer in det på fel får du bara fel meddelanden
- `PiiLoggingEnabled` gör att du kan logga personliga och organisatoriska data om värdet är true. Som standard är detta inställt på falskt, så att programmet inte loggar personliga data.
- `LogCallback` har angetts till ett ombud som utför loggningen. Om `PiiLoggingEnabled` är sant får den här metoden meddelandena två gånger: När `containsPii` parametern är lika med falskt och meddelandet utan personliga data, och en andra gång med `containsPii` parametern lika med sant och meddelandet kan innehålla personliga data. I vissa fall (när meddelandet inte innehåller personliga data) är meddelandet samma.
- `DefaultLoggingEnabled` aktiverar standard loggning för plattformen. Som standard är det falskt. Om du anger värdet till sant används händelse spårning i Desktop/UWP-program, NSLog på iOS och logcat på Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="android"></a>[Android](#tab/android)

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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Exempel:

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

Som standard samlar MSAL inte in eller loggar personliga data (PII). Med biblioteket kan utvecklare aktivera detta genom en egenskap i MSALLogger-klassen. Genom att aktivera `pii.Enabled` , tar appen ansvar för säker hantering av mycket känsliga data och följande regler för krav.

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

Exempel:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Exempel:

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

Exempel:

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

Som standard samlar MSAL inte in eller loggar personliga data (PII). Med biblioteket kan utvecklare aktivera detta genom en egenskap i MSALLogger-klassen. Genom att aktivera `pii.Enabled` , tar appen ansvar för säker hantering av mycket känsliga data och följande regler för krav.

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

Exempel:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL för Java-loggning

Med MSAL för Java kan du använda det loggnings bibliotek som du redan använder med din app, så länge det är kompatibelt med SLF4J. MSAL for Java använder [fasad för enkel loggning för Java](http://www.slf4j.org/) (SLF4J) som en enkel fasad eller abstraktion för olika loggnings ramverk, till exempel [Java. util. logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [logback](http://logback.qos.ch/) och [log4j](https://logging.apache.org/log4j/2.x/). SLF4J gör att användaren kan ansluta till det önskade loggnings ramverket vid distributions tillfället.

Om du till exempel vill använda logback som loggnings ramverk i ditt program lägger du till logback-beroendet till maven Pom-filen för ditt program:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Lägg sedan till konfigurations filen logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J binder automatiskt till logback vid distributions tillfället. MSAL-loggar skrivs till-konsolen.

Instruktioner för hur du binder till andra loggnings ramverk finns i [hand boken för SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Personlig information och organisationsinformation

Som standard samlar MSAL inte in eller loggar personliga eller organisatoriska data. I följande exempel är loggning av personliga eller organisatoriska data inaktiverat som standard:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Aktivera personlig och organisatorisk data loggning genom att ställa in `logPii()` på klient programs Builder. Om du aktiverar personlig eller organisatorisk data loggning måste din app ta ansvar för säker hantering av mycket känsliga data och som uppfyller alla myndighets krav.

I följande exempel är loggning av personliga eller organisatoriska data aktiverade:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL för python-loggning

Loggning i MSAL python använder standard funktionen för python-loggning, till exempel `logging.info("msg")` kan du konfigurera MSAL-loggning på följande sätt (och se hur det fungerar i [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Aktivera fel söknings loggning för alla moduler

Som standard är loggningen i valfritt Python-skript inaktive rad. Om du vill aktivera fel söknings loggning för alla moduler i hela python-skriptet använder du:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Enbart tystnads MSAL loggning

Om du bara vill göra en MSAL-biblioteks loggning, samtidigt som du aktiverar fel söknings loggning i alla andra moduler i python-skriptet, inaktiverar du den loggning som används av MSAL python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Personliga och organisatoriska data i python

MSAL for python loggar inte personliga data eller organisations data. Det finns ingen egenskap för att aktivera eller inaktivera personlig eller organisations data inloggning.

Du kan använda standard-python-loggning för att logga vad du vill, men du är ansvarig för att säkert hantera känsliga data och följa regel krav.

Mer information om hur du loggar in python finns i python-  [loggning howto](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
