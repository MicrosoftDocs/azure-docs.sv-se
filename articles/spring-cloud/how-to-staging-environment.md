---
title: Konfigurera en utvecklings miljö i Azure våren Cloud | Microsoft Docs
description: Lär dig hur du använder blå-grön-distribution med Azure våren Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9b3a5659e91ca90d31500b10526e3e2179d4e7da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046121"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurera en utvecklings miljö i Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln förklarar hur du konfigurerar en mellanlagrings distribution med hjälp av ett blått-grönt distributions mönster i Azure våren Cloud. Blue-grön-distribution är ett mönster för kontinuerlig leverans i Azure DevOps som förlitar sig på att hålla en befintlig (blå) version i drift samtidigt som en ny (grön) som är distribuerad. Den här artikeln visar hur du kan använda mellanlagrings distributionen i produktion utan att ändra produktions distributionen.

## <a name="prerequisites"></a>Förutsättningar

* Azure våren Cloud-instans på en standard pris nivå
* [Azure våren Cloud-tillägg](/cli/azure/azure-cli-extensions-overview) för Azure CLI

Den här artikeln använder ett program som skapats av vår Initializr. Om du vill använda ett annat program för det här exemplet måste du göra en enkel ändring i en offentlig del av programmet för att skilja din mellanlagrings distribution från produktionen.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra instruktionerna i den här artikeln.  Den har vanliga, förinstallerade Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du Cloud Shell-instansen. Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

Följ anvisningarna i nästa avsnitt om du vill konfigurera en blå, grön distribution i Azure våren Cloud.

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Förbereda appen och distributionerna
Följ dessa steg om du vill bygga programmet:

1. Generera koden för exempel appen genom att använda våren Initializr med [den här konfigurationen](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Ladda ned koden.
3. Lägg till följande HelloController. java-källfil i mappen `\src\main\java\com\example\hellospring\` :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. Bygg. jar-filen:

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Skapa appen i Azure våren Cloud-instansen:

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Distribuera appen till Azure våren-molnet:

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. Ändra koden för din mellanlagrings distribution:

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. Återskapa. jar-filen:

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. Skapa den gröna distributionen: 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>Visa appar och distributioner

Visa distribuerade appar med hjälp av följande procedur:

1. Gå till Azure våren Cloud-instansen i Azure Portal.

1. Öppna fönstret **appar** i den vänstra rutan för att visa appar för din tjänst instans.

   ![Skärm bild av fönstret öppna appar.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Du kan välja en app och Visa information.

   ![Skärm bild av information om en app.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Öppna **distributioner** för att se alla distributioner av appen. Rutnätet visar både produktions-och mellanlagrings distributioner.

   ![Skärm bild som visar appars distributioner i listan.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Välj URL: en för att öppna det aktuella distribuerade programmet.
    
   ![Skärm bild som visar U R L för det distribuerade programmet.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Välj **produktion** i kolumnen **tillstånd** om du vill se standard-appen.
    
   ![Skärm bild som visar U R L för standard-appen.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Välj **mellanlagring** i kolumnen **tillstånd** för att se mellanlagringsplatsen.
    
   ![Skärm bild som visar U R L för mellanlagringsplatsen.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Bekräfta att test slut punkten slutar med ett snedstreck (/) för att säkerställa att CSS-filen läses in på rätt sätt.  
> * Om din webbläsare kräver att du anger inloggnings uppgifter för att visa sidan använder du [URL-avkodning](https://www.urldecoder.org/) för att avkoda test slut punkten. URL-avkodning returnerar en URL i formatet *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green*. Använd det här formatet för att få åtkomst till din slut punkt.

>[!NOTE]    
> Konfigurations Server inställningarna gäller för både din utvecklings miljö och din produktions miljö. Om du till exempel anger kontext Sök vägen (*Server. servlet. context-Path*) för din app-gateway i konfigurations servern som *somepath* ändras sökvägen till den gröna distributionen till *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green/somepath/...*.
 
Om du besöker den offentliga app-gatewayen bör du se den gamla sidan utan din nya ändring.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Ange den gröna distributionen som produktions miljö

1. När du har verifierat din ändring i din mellanlagrings miljö kan du skicka den till produktion. På sidan **appar**  >  **distributioner** väljer du det program som för närvarande finns i **produktion**.

1. Välj ellipsen efter **registrerings status** för den gröna distributionen och välj sedan **Ange som produktion**. 

   ![Skärm bild som visar alternativ för att ställa in mellanlagrings versionen till produktion.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Bekräfta att appens URL visar dina ändringar.

   ![Skärm bild som visar U R L i appen nu i produktion.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> När du har angett den gröna distributionen som produktions miljö blir den tidigare distributionen den mellanlagrings distributionen.

## <a name="modify-the-staging-deployment"></a>Ändra mellanlagrings distributionen

Om du inte är nöjd med ändringen kan du ändra program koden, bygga ett nytt. jar-paket och ladda upp det till din gröna distribution med hjälp av Azure CLI:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Ta bort mellanlagrings distributionen

Om du vill ta bort mellanlagrings distributionen från Azure Portal går du till sidan för din mellanlagrings distribution och klickar på knappen **ta bort** .

Du kan också ta bort mellanlagrings distributionen från Azure CLI genom att köra följande kommando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Nästa steg

* [CI/CD för Azure våren Cloud](./spring-cloud-howto-cicd.md?pivots=programming-language-java)