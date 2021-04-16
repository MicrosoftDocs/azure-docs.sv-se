---
title: Snabbstart – Skapa och push-skicka Java-containeravbildningar till Azure Container Registry maven och Jib
description: Skapa en ContainerIzed Java-app och skicka den till Azure Container Registry med maven Jib-plugin-programmet.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537295"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Snabbstart: Skapa och push-skicka Java-containeravbildningar till Azure Container Registry

Den här snabbstarten visar hur du skapar en Java-app i en container och push-Azure Container Registry med maven Jib-plugin-programmet. Maven och Jib är ett exempel på hur du använder utvecklarverktyg för att interagera med ett Azure-containerregister.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmån](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial).
* [Azure Command-Line Interface (CLI).](/cli/azure/overview)
* Ett Java Development Kit (JDK) som stöds. Mer information om vilka JDK:s som är tillgängliga för användning när du utvecklar på Azure finns i <https://aka.ms/azure-jdks>.
* Apaches [Maven-byggverktyg](http://maven.apache.org) (version 3 eller senare).
* En [Git](https://git-scm.com)-klient.
* En [Docker](https://www.docker.com)-klient.
* [ACR Docker-autentiseringshjälpen](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Skapa webbappen Spring Boot on Docker Getting Started

Med följande steg skapar du en Spring Boot-webbapp och testar den lokalt.

1. Från kommandotolken använder du följande kommando för att klona [Spring Boot docker Komma igång](https://github.com/spring-guides/gs-spring-boot-docker) exempelprojekt.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Byt katalog till det slutförda projektet.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Använd Maven till att kompilera och köra exempelappen.

   ```bash
   mvn package spring-boot:run
   ```

1. Testa webbappen genom att gå till `http://localhost:8080`, eller med följande `curl`-kommando:

   ```bash
   curl http://localhost:8080
   ```

Du bör se följande meddelande: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Skapa ett Azure-containerregister med hjälp av Azure CLI

Nu ska du skapa en Azure-resursgrupp och din ACR med följande steg:

1. Logga in på ditt Azure-konto med följande kommando:

   ```azurecli
   az login
   ```

1. Ange den Azure-prenumeration som ska användas:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Skapa en resursgrupp för de Azure-resurser som används i den här självstudien. I följande kommando ska du ersätta platshållarna med ditt eget resursnamn och en plats som `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Skapa ett privat Azure-containerregister i resursgruppen med följande kommando. Se till att ersätta platshållarna med faktiska värden. Självstudien skickar exempelappen som en Docker-avbildning till det här registret under senare steg.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Skicka appen till containerregistret via Jib

Slutligen uppdaterar du projektkonfigurationen och använder kommandotolken för att skapa och distribuera avbildningen.

> [!NOTE]
> Om du vill logga in i Azure-containerregistret som du nyss skapade måste docker-daemonen köras. Om du vill installera Docker på datorn finns [den officiella Docker-dokumentationen här.](https://docs.docker.com/install/)

1. Logga in på Azure Container Registry från Azure CLI med följande kommando. Se till att ersätta platshållaren med ditt eget registernamn.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Kommandot `az configure` anger det standardregisternamn som ska användas med `az acr` kommandon.

1. Gå till det slutförda projektet för ditt Spring Boot-program (till exempel, "*C:\SpringBoot\gs-spring-boot-docker\complete*" eller "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") och öppna *pom.xml*-filen med en textredigerare.

1. Uppdatera samlingen `<properties>` i pom.xmlmed följande XML. Ersätt platshållaren med ditt registernamn och lägg till en egenskap med värdet , eller en `<jib-maven-plugin.version>` `2.2.0` nyare version av [jib-maven-plugin.](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Uppdatera samlingen `<plugins>` i pom.xmlfilen så att `<plugin>` elementet innehåller och posten för , som du ser i `jib-maven-plugin` följande exempel. Observera att vi använder en basavbildning från Microsoft Container Registry (MCR): , som innehåller en `mcr.microsoft.com/java/jdk:8-zulu-alpine` JDK som stöds officiellt för Azure. Andra MCR-basavbildningar med JDK:er som stöds officiellt finns i [Java SE JDK,](https://hub.docker.com/_/microsoft-java-jdk) [Java SE JRE,](https://hub.docker.com/_/microsoft-java-jre) [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)och [Java SE JDK och Maven.](https://hub.docker.com/_/microsoft-java-maven)

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navigera till den slutförda projektkatalogen för ditt Spring Boot-program och kör följande kommando för att skapa avbildningen och push-överföra den till registret:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Av säkerhetsskäl är de autentiseringsuppgifter som skapats av `az acr login` endast giltiga i 1 timme. Om du får felet *401 Unauthorized* (Obehörig) kan du köra `az acr login -n <your registry name>` kommandot igen för att återauktorera.

## <a name="verify-your-container-image"></a>Verifiera containeravbildningen

Grattis! Nu har du en containeriserad Java-app som bygger på JDK som stöds av Azure och push-installerats till din ACR. Du kan nu testa avbildningen genom att distribuera den till Azure App Service eller hämta den lokalt med kommandot (ersätt platshållaren):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Nästa steg

För andra versioner av de officiella Java-basavbildningar som stöds av Microsoft, se:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK och Maven](https://hub.docker.com/_/microsoft-java-maven)

Om du vill veta mer om Spring och Azure kan du fortsätta till dokumentationscentret för Spring i Azure.

> [!div class="nextstepaction"]
> [Spring på Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande resurser:

* [Azure för Java-utvecklare](/azure/java)
* [Arbeta med Azure DevOps och Java](/azure/devops/java)
* [Spring Boot on Docker Getting Started](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Distribuera ett Spring Boot-program till Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Använda en anpassad Docker-avbildning för Azure Web App i Linux](../app-service/tutorial-custom-container.md)
