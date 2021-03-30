---
title: Snabb start för att lägga till funktions flaggor till våren boot med Azure App konfiguration
description: Lägg till funktions flaggor till våren Boot Apps och hantera dem med hjälp av Azure App konfiguration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 08/06/2020
ms.author: alkemper
ms.custom: devx-track-java
ms.openlocfilehash: e931b974fc48d07237b2698e7782c48be20745d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930756"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Snabb start: Lägg till funktions flaggor i en våren Boot-app

I den här snabb starten inkluderar du Azure App konfiguration i en våren Boot-webbapp för att skapa en slut punkt till slutpunkt-implementering av funktions hantering. Du kan använda app Configuration service för att centralt lagra alla funktions flaggor och kontrol lera deras tillstånd.

Biblioteken våren Boot Feature Management utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek har **inget** beroende av några Azure-bibliotek. De integreras sömlöst med app-konfigurationen via sin våren Boot Configuration Provider.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* En [Java Development Kit SDK](/java/azure/jdk) som stöds med version 8.
* [Apache maven](https://maven.apache.org/download.cgi) version 3,0 eller senare.

## <a name="create-an-app-configuration-instance"></a>Skapa en app Configuration-instans

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **funktions hanteraren**  >  **+ Lägg** till för att lägga till en funktions flagga som kallas `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktions flagga med namnet beta](media/add-beta-feature-flag.png)

    Lämna `label` odefinierat för tillfället.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Använd [vår Initializr](https://start.spring.io/) för att skapa ett nytt fjäder Boot-projekt.

1. Gå till <https://start.spring.io/>.

1. Ange följande alternativ:

   * Generera ett **Maven**-projekt med **Java**.
   * Ange en **våren Boot** -version som är lika med eller större än 2,0.
   * Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.  I den här artikeln används `com.example` och `demo` .
   * Lägg till **våren-** webbberoendet.

1. När du har angett föregående alternativ väljer du **generera projekt**. När du uppmanas att ladda ned projektet till den lokala datorn.

## <a name="add-feature-management"></a>Lägg till funktions hantering

1. När du har extraherat filerna i det lokala systemet är ditt våren Boot-program klart för redigering. Leta upp  *pom.xml* i appens rot Katalog.

1. Öppna *pom.xml* -filen i en text redigerare och Lägg till följande i listan över `<dependencies>` :

    **Våren Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Våren Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Det finns ett bibliotek för funktions hantering utanför webben som inte är beroende av våren-webben. Läs mer om skillnaderna i GitHub- [dokumentationen](https://github.com/microsoft/spring-cloud-azure) .

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Navigera till `resources` katalogen för appen och öppna `bootstrap.properties` .  Om filen inte finns skapar du den. Lägg till följande rad i filen.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Välj från sid panelen i konfigurations portalen för appen för konfigurations arkivet `Access keys` . Välj fliken skrivskyddade nycklar. Kopiera värdet för den primära anslutnings strängen.

1. Lägg till den primära anslutnings strängen som en miljö variabel med variabel namnet `APP_CONFIGURATION_CONNECTION_STRING` .

1. Öppna huvudprogrammets Java-fil och lägg till `@EnableConfigurationProperties` för att aktivera den här funktionen.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. Skapa en ny HTML-fil med namnet *welcome.html* i katalogen mallar i appen.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/appconfiguration/azure-spring-cloud-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Skapa en ny mapp med namnet CSS under `static` och inuti den till en ny CSS-fil med namnet *main. CSS*.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa Spring Boot-appen med Maven och kör den:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Öppna ett webbläsarfönster och gå till URL: en: `http://localhost:8080/welcome` .

    ![Skärm bild som visar ett webb läsar fönster med ett välkomst meddelande.](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. I konfigurations portalen för app väljer du **funktions hanteraren** och ändrar status för **beta** nyckeln till **på**:

    | Nyckel | Tillstånd |
    |---|---|
    | Beta | På |

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Skärm bild som visar ett webb läsar fönster med ett välkomst meddelande och en beta-länk som kallas.](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det för att hantera funktioner i en våren Boot-webbapp via [biblioteken för funktions hantering](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

* Läs mer om [funktions hantering](./concept-feature-management.md).
* [Hantera funktions flaggor](./manage-feature-flags.md).
* [Använd funktions flaggor i en webbapp Boot Core-app](./use-feature-flags-spring-boot.md).