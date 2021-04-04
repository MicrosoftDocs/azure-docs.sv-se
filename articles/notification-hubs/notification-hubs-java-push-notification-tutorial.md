---
title: Använda Azure Notification Hubs med Java
description: Lär dig hur du använder Azure Notification Hubs från en Java-backend.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87322949"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Använda Notification Hubs från Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

I det här avsnittet beskrivs viktiga funktioner i det nya fullt stödda officiella Azure Notification Hub Java SDK.
Projektet är ett projekt med öppen källkod och du kan visa hela SDK-koden i [Java SDK].

I allmänhet kan du komma åt alla Notification Hubs-funktioner från en Java/PHP/python/ruby-slutpunkt med hjälp av REST-gränssnittet i Notification Hub, enligt beskrivningen i MSDN-avsnittet [Notification HUBS REST-API: er](/previous-versions/azure/reference/dn223264(v=azure.100)). Java SDK är en tunn omslutning över dessa REST-gränssnitt i Java.

SDK stöder för närvarande:

* CRUD på Notification Hubs
* CRUD för registreringar
* Installations hantering
* Import/export-registreringar
* Vanliga sändningar
* Schemalagda sändningar
* Asynkrona åtgärder via Java-NIO
* Plattformar som stöds: APN (iOS), FCM (Android), WNS (Windows Store-appar), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android utan Google Services)

## <a name="sdk-usage"></a>SDK-användning

### <a name="compile-and-build"></a>Kompilera och bygg

Använd [maven]

För att bygga:

```cmd
mvn package
```

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>CRUDs för Notification Hub

**Skapa en NamespaceManager:**

```java
NamespaceManager namespaceManager = new NamespaceManager("connection string")
```

**Skapa Notification Hub:**

```java
NotificationHubDescription hub = new NotificationHubDescription("hubname");
hub.setWindowsCredential(new WindowsCredential("sid","key"));
hub = namespaceManager.createNotificationHub(hub);
```

 ELLER

```java
hub = new NotificationHub("connection string", "hubname");
```

**Hämta Notification Hub:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Uppdatera Notification Hub:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Ta bort Notification Hub:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>Registrerings CRUDs

**Skapa en Notification Hub-klient:**

```java
hub = new NotificationHub("connection string", "hubname");
```

**Skapa Windows-registrering:**

```java
WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

**Skapa iOS-registrering:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

På samma sätt kan du skapa registreringar för Android (FCM), Windows Phone (MPNS) och Kindle Fire (ADM).

**Skapa mall-registreringar:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Skapa registreringar med Create registration ID + upsert-mönster:**

Tar bort dubbletter på grund av eventuella borttappade svar om du lagrar registrerings-ID: n på enheten:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Uppdaterings registreringar:**

```java
hub.updateRegistration(reg);
```

**Ta bort registreringar:**

```java
hub.deleteRegistration(regid);
```

**Fråga efter registreringar:**

* **Hämta en enda registrering:**

```java
hub.getRegistration(regid);
```

* **Hämta alla registreringar i hubben:**

```java
hub.getRegistrations();
```

* **Hämta registreringar med tagg:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Hämta registreringar per kanal:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

Alla samlings frågor stöder $top och fortsättnings-token.

### <a name="installation-api-usage"></a>Användning av API för installation

Installations-API är en alternativ metod för registrerings hantering. I stället för att underhålla flera registreringar, som inte är enkla och som är enkla att göra felaktigt eller ineffektiva, är det nu möjligt att använda ett enda installations objekt.

Installationen innehåller allt du behöver: push-kanal (enhets-token), taggar, mallar, sekundära paneler (för WNS och APN). Du behöver inte anropa tjänsten för att hämta ID: t längre – skapa GUID eller någon annan identifierare, behåll den på enheten och skicka till Server delen tillsammans med push-kanal (enhets-token).

På Server delen bör du bara göra ett enda anrop till `CreateOrUpdateInstallation` . det är helt idempotenta, så du kan försöka igen om det behövs.

Som exempel för Amazon Kindle-brand:

```java
Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
hub.createOrUpdateInstallation(installation);
```

Om du vill uppdatera den:

```java
installation.addTag("foo");
installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
hub.createOrUpdateInstallation(installation);
```

För avancerade scenarier använder du funktionen delvis uppdatering, som gör att du bara kan ändra vissa egenskaper för objektet installation. Delvis uppdatering är en delmängd av JSON-patch-åtgärder som du kan köra mot installations objekt.

```java
PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
```

Ta bort installation:

```java
hub.deleteInstallation(installation.getInstallationId());
```

`CreateOrUpdate`, `Patch` och `Delete` är till slut konsekvent med `Get` . Den begärda åtgärden går bara till system kön under anropet och körs i bakgrunden. Get är inte avsett för huvud körnings scenariot, men bara för fel söknings-och fel söknings syfte begränsas tjänsten.

Skicka flöde för installationer är detsamma som för registreringar. Rikta meddelande till den specifika installationen – Använd bara taggen "InstallationId: {Desired-ID}". I det här fallet är koden:

```java
Notification n = Notification.createWindowsNotification("WNS body");
hub.sendNotification(n, "InstallationId:{installation-id}");
```

För en av flera mallar:

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("value3", "some value");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
```

### <a name="schedule-notifications-available-for-standard-tier"></a>Schemalägg meddelanden (tillgängligt för STANDARD nivån)

Samma som vanlig sändning men med ytterligare en parameter-scheduledTime, som står när en avisering ska skickas. Tjänsten accepterar alla tidpunkter mellan nu + 5 minuter och nu + 7 dagar.

**Schemalägg en inbyggd Windows-avisering:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Import/export (tillgängligt för STANDARD nivån)

Du kan behöva utföra Mass åtgärder mot registreringar. Det är vanligt vis för integrering med ett annat system eller en enorm korrigering för att uppdatera taggarna. Vi rekommenderar inte att du använder flödet Hämta/uppdatera om tusentals registreringar är inblandade. Systemets import/export-kapacitet är utformad för att avse scenariot. Du får åtkomst till en BLOB-behållare under ditt lagrings konto som källa för inkommande data och plats för utdata.

**Skicka ett export jobb:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Skicka in ett import jobb:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
job.setImportFileUri("input file uri with SAS signature");
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Vänta tills jobbet har slutförts:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Hämta alla jobb:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**URI med SAS-signatur:**

 URL: en är URL: en för en BLOB-fil eller BLOB-behållare plus en uppsättning parametrar som behörigheter och förfallo tid plus signaturen för alla dessa saker som gjorts med kontots SAS-nyckel. Azure Storage Java SDK har omfattande funktioner, inklusive att skapa dessa URI: er. Det är enkelt alternativ att ta en titt på `ImportExportE2E` test klassen (från GitHub-platsen) som har grundläggande och kompakt implementering av signeringsalgoritmen.

### <a name="send-notifications"></a>Skicka meddelanden

Notification-objektet är helt enkelt en brödtext med rubriker, vissa verktygs metoder hjälper till med att skapa egna meddelanden och objekt i mal linne-meddelanden.

* **Windows Store och Windows Phone 8,1 (ej Silverlight)**

```java
String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
Notification n = Notification.createWindowsNotification(toast);
hub.sendNotification(n);
```

* **iOS**

```java
String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
Notification n = Notification.createAppleNotification(alert);
hub.sendNotification(n);
```

* **Android**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createFcmNotification(message);
hub.sendNotification(n);
```

* **Windows Phone 8,0 och 8,1 Silverlight**

```java
String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
            "<wp:Notification xmlns:wp=\"WPNotification\">" +
                "<wp:Toast>" +
                    "<wp:Text1>Hello from Java!</wp:Text1>" +
                "</wp:Toast> " +
            "</wp:Notification>";
Notification n = Notification.createMpnsNotification(toast);
hub.sendNotification(n);
```

* **Kindle-brand**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createAdmNotification(message);
hub.sendNotification(n);
```

* **Skicka till taggar**
  
```java
Set<String> tags = new HashSet<String>();
tags.add("boo");
tags.add("foo");
hub.sendNotification(n, tags);
```

* **Skicka till tagg-uttryck**

```java
hub.sendNotification(n, "foo && ! bar");
```

* **Skicka mal Lav meddelande**

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("prop1", "v1");
prop.put("prop2", "v2");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n);
```

Om du kör Java-koden bör du nu skapa ett meddelande som visas på mål enheten.

## <a name="next-steps"></a><a name="next-steps"></a>Nästa steg

I det här avsnittet visas hur du skapar en enkel Java REST-klient för Notification Hubs. Här kan göra du följande:

* Hämta hela [Java SDK], som innehåller hela SDK-koden.
* Spela med exemplen:
  * [Kom igång med Notification Hubs]
  * [Skicka de senaste nyheterna]
  * [Skicka lokaliserade brytnings nyheter]
  * [Skicka meddelanden till autentiserade användare]
  * [Skicka meddelanden mellan plattformar till autentiserade användare]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Kom igång med Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Skicka de senaste nyheterna]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Skicka lokaliserade brytnings nyheter]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Skicka meddelanden till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Skicka meddelanden mellan plattformar till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
