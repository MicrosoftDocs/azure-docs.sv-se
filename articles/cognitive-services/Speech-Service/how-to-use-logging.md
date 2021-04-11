---
title: Tal SDK-loggning – tal tjänst
titleSuffix: Azure Cognitive Services
description: 'Lär dig mer om hur du aktiverar loggning i tal-SDK: n (C++, C#, python, mål-C, Java).'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 32715ad1a01366d7d56e6fa8129151b15c315e1d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504183"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Aktivera loggning i tal-SDK

Loggning till filen är en valfri funktion för tal-SDK: n. Under utvecklings loggningen finns ytterligare information och diagnostik från tal SDK: s kärn komponenter. Den kan aktive ras genom att ange egenskapen `Speech_LogFilename` för ett tal konfigurations objekt till platsen och namnet på logg filen. Loggningen hanteras av en statisk klass i det interna biblioteket för tal-SDK. Du kan aktivera loggning för valfri Speech SDK-tolk eller synthesizers-instans. Alla instanser i samma process skriver logg poster till samma loggfil.

> [!NOTE]
> Loggning är tillgängligt eftersom tal SDK-versionen 1.4.0 i alla API: er som stöds i tal SDK, med undantag för Java Script.

## <a name="sample"></a>Exempel

Logg filens namn anges i ett konfigurations objekt. Ta med `SpeechConfig` som ett exempel och förutsatt att du har skapat en instans med namnet `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Du kan skapa en tolk från konfigurationsobjektet konfiguration. Detta aktiverar loggning för alla identifierare.

> [!NOTE]
> Om du skapar en `SpeechSynthesizer` från config-objektet kommer den inte att aktivera loggning. Om loggning har Aktiver ATS kan du också få diagnostik från `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Skapa en loggfil på olika plattformar

För Windows eller Linux kan logg filen finnas i valfri sökväg som användaren har Skriv behörighet för. Skriv behörighet till fil system platser i andra operativ system kan vara begränsad eller begränsad som standard.

### <a name="universal-windows-platform-uwp"></a>Universal Windows Platform (UWP)

UWP-program måste placera loggfiler på någon av program data platserna (lokal, nätverks växling eller tillfällig). En loggfil kan skapas i den lokala programmappen:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mer information om fil åtkomst behörighet för UWP-program finns [här](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Du kan spara en loggfil till antingen intern lagring, extern lagring eller cache-katalogen. Filer som skapas i den interna lagringen eller i cachekatalogen är privata för programmet. Det är bättre att skapa en loggfil i extern lagring.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Koden ovan sparar en loggfil till den externa lagringen i roten i en programspecifik katalog. En användare kan komma åt filen med fil hanteraren (vanligt vis i `Android/data/ApplicationName/logfile.txt` ). Filen tas bort när programmet avinstalleras.

Du måste också begära `WRITE_EXTERNAL_STORAGE` behörighet i manifest filen:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mer information om data och fil lagring för Android-program finns [här](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Endast kataloger i begränsat läge för program är tillgängliga. Filer kan skapas i dokument, bibliotek och temporära kataloger. Filer i katalogen dokument kan göras tillgängliga för en användare. Följande kodfragment visar hur du skapar en loggfil i program dokument katalogen:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Om du vill komma åt en skapad fil lägger du till nedanstående egenskaper i `Info.plist` programmets egenskaps lista:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mer information om iOS-filsystem finns [här](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)