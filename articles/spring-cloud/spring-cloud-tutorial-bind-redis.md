---
title: Bind Azure-cache för Redis till ditt Azure våren Cloud-program
description: Lär dig hur du binder Azure-cache för Redis till ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ef77ba6e34f2a699c8c4f06fde8cb602ae98c728
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90885661"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Bind Azure-cache för Redis till ditt Azure våren Cloud-program 

**Den här artikeln gäller för:** ✔️ Java

I stället för att manuellt konfigurera dina våren Boot-program kan du automatiskt binda utvalda Azure-tjänster till dina program med hjälp av Azure våren Cloud. Den här artikeln visar hur du binder ditt program till Azure cache för Redis.

## <a name="prerequisites"></a>Förutsättningar

* En distribuerad Azure våren Cloud-instans
* En Azure-cache för Redis-tjänstinstans
* Azure våren Cloud-tillägget för Azure CLI

Om du inte har en distribuerad Azure våren-moln instans följer du stegen i [snabb starten för att distribuera en Azure våren Cloud-App](spring-cloud-quickstart.md).

## <a name="bind-azure-cache-for-redis"></a>Bind Azure-cache för Redis

1. Lägg till följande beroende till projektets pom.xml-fil:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Ta bort alla `spring.redis.*` Egenskaper från `application.properties` filen

1. Uppdatera den aktuella distributionen med `az spring-cloud app update` eller skapa en ny distribution med hjälp av `az spring-cloud app deployment create` .

1. Gå till sidan Azure våren Cloud service i Azure Portal. Gå till **instrument panelen för program** och välj programmet som ska bindas till Azure cache för Redis. Det här programmet är samma som du uppdaterade eller distribuerade i föregående steg.

1. Välj **tjänst bindning** och välj **skapa tjänst bindning**. Fyll i formuläret, kontrol lera att du väljer värdet **bindnings typ** värde **Azure cache för Redis**, ditt Azure-cache för redis-server och alternativet **primär** nyckel.

1. Starta om appen. Bindningen bör nu fungera.

1. För att säkerställa att tjänst bindningen är korrekt väljer du bindnings namnet och kontrollerar dess information. `property`Fältet bör se ut så här:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du binder ditt Azure våren Cloud-program till Azure cache för Redis. Mer information om att binda tjänster till ditt program finns i [binda till en Azure Database for MySQL instans](spring-cloud-tutorial-bind-mysql.md).
