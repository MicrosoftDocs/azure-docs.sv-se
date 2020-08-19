---
title: Snabb start för att lära dig att använda Azure App konfiguration
description: I den här snabb starten skapar du en Java våren-app med Azure App konfiguration för att centralisera lagring och hantering av program inställningar separat från din kod.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 10b38d5c5802ec4f6bc33b15b3ddef12e8256a78
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586461"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Snabb start: skapa en Java våren-app med Azure App konfiguration

I den här snabb starten inkluderar du Azure App konfiguration i en Java våren-app för att centralisera lagring och hantering av program inställningar separat från din kod.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- En [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) som stöds med version 8.
- [Apache maven](https://maven.apache.org/download.cgi) version 3,0 eller senare.

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Configuration Explorer**  >  **+ skapa**  >  **nyckel värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | /application/config.message | Hello |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

7. Välj **Tillämpa**.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Använd [vår Initializr](https://start.spring.io/) för att skapa ett nytt fjäder Boot-projekt.

1. Gå till <https://start.spring.io/>.

1. Ange följande alternativ:

   - Generera ett **Maven**-projekt med **Java**.
   - Ange en **våren Boot** -version som är lika med eller större än 2,0.
   - Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   - Lägg till **våren-** webbberoendet.

1. När du har angett föregående alternativ väljer du **generera projekt**. När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. När du har extraherat filerna i det lokala systemet är ditt vanliga start program redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

1. Öppna filen *pom.xml* i ett redigeringsprogram och lägg till Spring Cloud Azure Config-starter i listan över `<dependencies>`:

    **Våren Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Våren Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Öppna huvudprogrammets Java-fil och lägg till `@EnableConfigurationProperties` för att aktivera den här funktionen.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Skapa en ny fil med namnet `bootstrap.properties` under katalogen resurser i din app och Lägg till följande rader i filen. Ersätt exempel värdena med lämpliga egenskaper för appens konfigurations arkiv.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Ange en miljö variabel med namnet **APP_CONFIGURATION_CONNECTION_STRING**och ange den som åtkomst nyckel till appens konfigurations arkiv. Kör följande kommando på kommando raden och starta om kommando tolken för att ändringarna ska börja gälla:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa ditt våren Boot-program med Maven och kör det, till exempel:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. När programmet har körts använder du *sväng* för att testa programmet, till exempel:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Du ser meddelandet som du angav i appens konfigurations arkiv.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det med en Java våren-app. Mer information finns i [våren på Azure](https://docs.microsoft.com/java/azure/spring-framework/). Fortsätt till nästa självstudie om du vill lära dig hur du aktiverar din Java våren-app för att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-java-spring-app.md)
