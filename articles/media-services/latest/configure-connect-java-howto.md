---
title: Ansluta till Azure Media Services v3 API-Java
description: Den här artikeln beskriver hur du ansluter till Azure Media Services v3 API med Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 769b4bc431040ee4d872fa60270196db96978ed2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94748190"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ansluta till Media Services v3 API-Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3 Java SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

I den här artikeln används Visual Studio Code för att utveckla exempel appen.

## <a name="prerequisites"></a>Förutsättningar

- Följ [Skriv Java med Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) för att installera:

   - JDK
   - Apache maven
   - Java Extension Pack
- Se till att ställa in `JAVA_HOME` och `PATH` miljövariabler.
- [Skapa ett Media Services-konto](./create-account-howto.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.
- Följ stegen i avsnittet [åtkomst-API: er](./access-api-howto.md) . Registrera prenumerations-ID, program-ID (klient-ID), den autentiseringsnyckel (hemlighet) och klient-ID som du behöver i ett senare steg.

Granska även:

- [Java i Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java projekt hantering i VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Öppna ett kommando rads verktyg och `cd` till en katalog där du vill skapa projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

När du kör kommandot `pom.xml` `App.java` skapas,, och andra filer. 

## <a name="add-dependencies"></a>Lägg till beroenden

1. Öppna mappen där projektet finns i Visual Studio Code
1. Hitta och öppna `pom.xml`
1. Lägg till nödvändiga beroenden.

   Se `pom.xml` i exempel på [video kodning](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) .

## <a name="connect-to-the-java-client"></a>Ansluta till Java-klienten

1. Öppna `App.java` filen under `src\main\java\com\azure\ams` och kontrol lera att paketet ingår längst upp:

    ```java
    package com.azure.ams;
    ```
1. Lägg till följande import uttryck under paket instruktionen:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Om du vill skapa de Active Directory autentiseringsuppgifter som du behöver för att göra förfrågningar lägger du till följande kod till main-metoden i klassen app och anger de värden som du har fått från [åtkomst till API: er](./access-api-howto.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Kör appen.

## <a name="see-also"></a>Se även

- [Media Services begrepp](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referens](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01: Azure-MGMT-Media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Nästa steg

Nu kan du ta med `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` och börja ändra entiteter.

Fler kod exempel finns i [Java SDK-exempel](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) lagrings platsen.
