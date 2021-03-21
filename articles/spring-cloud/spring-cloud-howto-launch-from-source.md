---
title: Så här startar du ditt våren Cloud-program från käll koden
description: I den här snabb starten får du lära dig hur du startar ditt Azure våren Cloud-program direkt från din käll kod
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210333"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Så här startar du ditt våren Cloud-program från käll koden

**Den här artikeln gäller för:** ✔️ Java

Azure våren Cloud ger till gång till molnbaserade mikrotjänster-program på Azure.

Du kan starta program direkt från Java-källkod eller från en redan inbyggd JAR-Server. I den här artikeln beskrivs distributions procedurerna.

Den här snabb starten förklarar hur du:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att din Azure-prenumeration har de nödvändiga beroendena:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI](/cli/azure/install-azure-cli)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med följande kommando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Etablera en tjänst instans med hjälp av Azure CLI

Logga in på Azure CLI och välj din aktiva prenumeration. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Skapa en resurs grupp som innehåller din Azure våren Cloud-tjänst. Du kan lära dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Kör följande kommandon för att etablera en instans av Azure våren Cloud. Förbered ett namn för din Azure våren Cloud-tjänst. Namnet måste innehålla mellan 4 och 32 tecken och får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Tjänst instansen tar cirka fem minuter att distribuera.

Ange standard resurs grupps namnet och namnet på Azure våren Cloud-instansen med hjälp av följande kommandon:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Skapa moln programmet Azure våren

Följande kommando skapar ett Azure våren Cloud-program i din prenumeration.  Detta skapar en tom tjänst till vilken vi kan ladda upp programmet.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Distribuera ditt våren Cloud-program

Du kan distribuera ditt program från en fördefinierad JAR-eller Gradle-eller Maven-lagringsplats.  Hitta instruktioner för varje ärende nedan.

### <a name="deploy-a-built-jar"></a>Distribuera en inbyggd JAR

Om du vill distribuera från en JAR-version som bygger på din lokala dator måste du se till att din version tillverkar en [fat-jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Distribuera fat-JAR till en aktiv distribution

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

Distribuera fat-JAR till en speciell distribution

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Distribuera från käll kod

Azure våren Cloud använder [kpack](https://github.com/pivotal/kpack) för att bygga projektet.  Du kan använda Azure CLI för att ladda upp din källkod, bygga projektet med kpack och distribuera det till mål programmet.

> [!WARNING]
> Projektet måste bara producera en JAR-fil med en `main-class` post i `MANIFEST.MF` `target` (för maven-distributioner) eller `build/libs` (för Gradle-distributioner).  Om du har flera JAR-filer med `main-class` poster går det inte att distribuera.

För en modul maven/Gradle-projekt:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

För maven/Gradle-projekt med flera moduler upprepar du för varje modul:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Visa distributions loggar

Granska kpack-build-loggarna med följande kommando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Kpack-loggarna visar endast den senaste distributionen om distributionen skapades från källan med kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slut punkt till gateway

1. Öppna sidan för **program instrument panelen** .
2. Välj `gateway` programmet för att visa sidan med **program information** .
3. Välj **tilldela slut punkt** för att tilldela en offentlig slut punkt till gateway. Det kan ta några minuter. 
4. Ange den tilldelade offentliga IP-adressen i webbläsaren för att visa det program som körs.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Redigera miljövariabler för program
> * Tilldela en offentlig IP-adress för din Application Gateway

> [!div class="nextstepaction"]
> [Vår moln loggar, statistik, spårning](spring-cloud-quickstart-logs-metrics-tracing.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).