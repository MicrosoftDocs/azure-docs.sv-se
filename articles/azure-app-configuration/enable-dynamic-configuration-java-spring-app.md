---
title: Använd dynamisk konfiguration i en våren Boot-app
titleSuffix: Azure App Configuration
description: Lär dig hur du dynamiskt uppdaterar konfigurations data för våren Boot Apps
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625525"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Självstudie: Använd dynamisk konfiguration i en Java våren-app

App-konfigurationen har två bibliotek för våren. `spring-cloud-azure-appconfiguration-config` kräver våren boot och tar ett beroende på `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` kräver våren Web tillsammans med vår start. Båda biblioteken stöder manuell utlösare för att kontrol lera om det finns uppdaterade konfigurations värden. `spring-cloud-azure-appconfiguration-config-web` lägger också till stöd för automatisk kontroll av konfigurations uppdatering.

Med uppdatera kan du uppdatera konfigurations värden utan att behöva starta om programmet, men det gör att alla bönor i `@RefreshScope` som ska återskapas. Klient biblioteket cachelagrar ett hash-ID för de för tillfället laddade konfigurationerna för att undvika för många anrop till konfigurations arkivet. Uppdaterings åtgärden uppdaterar inte värdet förrän det cachelagrade värdet har upphört att gälla, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder. Den kan åsidosättas om det behövs.

`spring-cloud-azure-appconfiguration-config-web`den automatiserade uppdateringen utlöses baserat på aktivitet, speciellt våren Web `ServletRequestHandledEvent` . Om en `ServletRequestHandledEvent` inte utlöses `spring-cloud-azure-appconfiguration-config-web` utlöses automatisk uppdatering även om förfallo tiden för cachen har upphört att gälla.

## <a name="use-manual-refresh"></a>Använd manuell uppdatering

App-konfigurationen visar `AppConfigurationRefresh` vilka som kan användas för att kontrol lera om cacheminnet har upphört att gälla och om det har upphört att gälla en uppdatering.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`returnerar ett värde `Future` som är sant om en uppdatering har Aktiver ATS och falskt om den inte är det. Falskt innebär att förfallo tiden för cachen inte har gått ut, inga ändringar har utförts eller att en annan tråd för närvarande söker efter en uppdatering.

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
