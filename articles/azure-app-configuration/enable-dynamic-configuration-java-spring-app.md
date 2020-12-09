---
title: Använd dynamisk konfiguration i en våren Boot-app
titleSuffix: Azure App Configuration
description: Lär dig hur du dynamiskt uppdaterar konfigurations data för våren Boot Apps
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929166"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Självstudie: Använd dynamisk konfiguration i en Java våren-app

App-konfigurationen för att starta klient biblioteket stöder uppdatering av en uppsättning konfigurations inställningar på begäran, utan att ett program startas om. Klient biblioteket cachelagrar varje inställning för att undvika för många anrop till konfigurations arkivet. Uppdaterings åtgärden uppdaterar inte värdet förrän det cachelagrade värdet har upphört att gälla, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder. Den kan åsidosättas om det behövs.

Du kan kontrol lera om det finns uppdaterade inställningar på begäran genom att anropa `AppConfigurationRefresh` `refreshConfigurations()` metoden.

Du kan också använda `spring-cloud-azure-appconfiguration-config-web` paketet, som tar ett beroende på `spring-web` för att hantera automatisk uppdatering.

## <a name="use-automated-refresh"></a>Använd automatisk uppdatering

Om du vill använda automatisk uppdatering börjar du med en våren Boot-app som använder appens konfiguration, till exempel den app som du skapar genom att följa [Start programmet för att starta snabb start för appar](quickstart-java-spring-app.md).

Öppna sedan *pom.xml* -filen i en text redigerare och Lägg till en `<dependency>` for `spring-cloud-azure-appconfiguration-config-web` .

**Våren Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Våren Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Kör och testa appen lokalt

1. Skapa Spring Boot-appen med Maven och kör den:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Öppna ett webbläsarfönster och gå till URL: en: `http://localhost:8080` .  Du ser det meddelande som är kopplat till din nyckel. 

    Du kan också använda *sväng* för att testa ditt program, till exempel: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Om du vill testa dynamisk konfiguration öppnar du Azure App konfigurations portal som är kopplad till ditt program. Välj **Configuration Explorer** och uppdatera värdet för nyckeln som visas, till exempel:
    | Tangent | Värde |
    |---|---|
    | program/config. Message | Hello-uppdaterat |

1. Uppdatera webb sidan för att se det nya meddelandet som visas.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat din våren Boot-app för att dynamiskt uppdatera konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
