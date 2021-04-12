---
title: Så här binder du en Azure Database for MySQL-instans till ditt Azure våren Cloud-program
description: Lär dig hur du binder en Azure Database for MySQL-instans till ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878371"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Bind en Azure Database for MySQL-instans till ditt Azure våren Cloud-program 

**Den här artikeln gäller för:** ✔️ Java

Med Azure våren Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att behöva konfigurera ditt våren Boot-program manuellt. Den här artikeln visar hur du binder ditt program till din Azure Database for MySQL-instans.

## <a name="prerequisites"></a>Förutsättningar

* En distribuerad Azure våren Cloud-instans
* Ett Azure Database for MySQL konto
* Azure CLI

Om du inte har en distribuerad Azure våren-moln instans följer du anvisningarna i [snabb start: starta ett Azure våren Cloud-program genom att använda Azure Portal](spring-cloud-quickstart.md) för att distribuera din första våren Cloud-App.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Bind din app till din Azure Database for MySQL-instans

1. Anteckna administratörens användar namn och lösen ord för ditt Azure Database for MySQL konto. 

1. Anslut till servern, skapa en databas med namnet **testdb** från en MySQL-klient och skapa sedan ett nytt konto som inte är administratör.

1. I projektets *pom.xml* -fil lägger du till följande beroende:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Ta bort alla egenskaper i filen *Application. Properties* `spring.datasource.*` .

1. Uppdatera den aktuella distributionen genom `az spring-cloud app update` att köra eller skapa en ny distribution för den här ändringen genom att köra `az spring-cloud app deployment create` .  Dessa kommandon antingen uppdaterar eller skapar programmet med det nya beroendet.

1. På sidan Azure Portal på sidan **Azure våren Cloud** Service letar du reda på **program instrument panelen** och väljer sedan det program som du vill binda till Azure Database for MySQL instansen.  Detta är samma program som du uppdaterade eller distribuerade i föregående steg. 

1. Välj **tjänst bindning** och välj sedan knappen **skapa bindning för tjänst** . 

1. Fyll i formuläret, Välj **Azure MySQL** som **bindnings typ**, med samma databas namn som du använde tidigare och Använd samma användar namn och lösen ord som du antecknade i det första steget.

1. Starta om appen så fungerar den här bindningen nu.

1. För att säkerställa att tjänst bindningen är korrekt väljer du bindnings namnet och kontrollerar sedan dess information. `property`Fältet bör se ut så här:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du binder ett Azure våren Cloud-program till en Azure Database for MySQL-instans. Mer information om att binda tjänster till ett program finns i [BIND en Azure Cosmos DB-databas till ett Azure våren Cloud-program](spring-cloud-howto-bind-cosmos.md).
