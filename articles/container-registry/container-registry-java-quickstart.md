---
title: Snabb start – bygga och skicka Java-behållar avbildningar till Azure Container Registry med Maven och kran
description: Bygg en Java-app i behållare och skicka den till Azure Container Registry med hjälp av maven kran-plugin-programmet.
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 9e400ee0bae2690a84f9cfd3f6a76359e08eabc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018346"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Snabb start: bygga och skicka Java-behållar avbildningar till Azure Container Registry

Den här snabb starten visar hur du skapar en Java-app i behållare och push-överför den till Azure Container Registry med hjälp av maven kran-plugin-programmet. Användningen av Maven och kran är ett exempel på att använda utvecklarverktyg för att interagera med ett Azure Container Registry.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmån](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial).
* [Azure Command-Line Interface (CLI)](/cli/azure/overview).
* Ett Java Development Kit (JDK) som stöds. Mer information om vilka JDK:s som är tillgängliga för användning när du utvecklar på Azure finns i <https://aka.ms/azure-jdks>.
* Apache: s [maven](http://maven.apache.org) build-verktyg (version 3 eller senare).
* En [Git](https://git-scm.com)-klient.
* En [Docker](https://www.docker.com)-klient.
* [Hjälp för ACR Docker-autentiseringsuppgifter](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Skapa webbappen Spring Boot on Docker Getting Started

Med följande steg skapar du en Spring Boot-webbapp och testar den lokalt.

1. Från kommando tolken använder du följande kommando för att klona [våren boot i docker komma igång](https://github.com/spring-guides/gs-spring-boot-docker) exempelprojektet.

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

Därefter skapar du en Azure-resurs grupp och din ACR med hjälp av följande steg:

1. Logga in på ditt Azure-konto med hjälp av följande kommando:

   ```azurecli
   az login
   ```

1. Ange vilken Azure-prenumeration som ska användas:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Skapa en resursgrupp för de Azure-resurser som används i den här självstudien. I följande kommando ser du till att ersätta plats hållarna med ditt eget resurs namn och en plats som `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Skapa ett privat Azure Container Registry i resurs gruppen med hjälp av följande kommando. Se till att ersätta plats hållarna med faktiska värden. Självstudien skickar exempelappen som en Docker-avbildning till det här registret under senare steg.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Skicka appen till containerregistret via Jib

Slutligen uppdaterar du projekt konfigurationen och använder kommando tolken för att bygga och distribuera avbildningen.

> [!NOTE]
> Om du vill logga in på Azure Container Registry som du nyss skapade måste du ha Docker-daemonen igång. [Här är den officiella Docker-dokumentationen](https://docs.docker.com/install/)för att installera Docker på datorn.

1. Logga in på din Azure Container Registry från Azure CLI med hjälp av följande kommando. Se till att ersätta plats hållaren med ditt eget register namn.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure`Kommandot anger standard register namnet som ska användas med- `az acr` kommandon.

1. Gå till det slutförda projektet för ditt Spring Boot-program (till exempel, "*C:\SpringBoot\gs-spring-boot-docker\complete*" eller "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") och öppna *pom.xml*-filen med en textredigerare.

1. Uppdatera `<properties>` samlingen i *pom.xml* -filen med följande XML. Ersätt plats hållaren med ditt register namn och Lägg till en `<jib-maven-plugin.version>` egenskap med värde `2.2.0` , eller en nyare version av [kran-maven-plugin-programmet](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Uppdatera `<plugins>` samlingen i *pom.xml* -filen så att `<plugin>` elementet innehåller och posten för `jib-maven-plugin` , som visas i följande exempel. Observera att vi använder en bas avbildning från Microsoft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine` , som innehåller en JDK som stöds officiellt för Azure. För andra MCR Base-avbildningar med officiellt stödd JDKs, se [Java Se JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java se JRE](https://hub.docker.com/_/microsoft-java-jre), [Java se konsol lös JRE](https://hub.docker.com/_/microsoft-java-jre-headless)och [Java Se JDK och maven](https://hub.docker.com/_/microsoft-java-maven).

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
> Av säkerhets skäl är de autentiseringsuppgifter som skapas av `az acr login` bara giltig i 1 timme. Om du får ett *401-otillåtet* fel kan du köra `az acr login -n <your registry name>` kommandot igen för att autentisera igen.

## <a name="verify-your-container-image"></a>Verifiera behållar avbildningen

Grattis! Nu har du en behållare för Java-appar i Azure som stöds JDK som skickas till din ACR. Nu kan du testa avbildningen genom att distribuera den till Azure App Service eller genom att dra den till en lokal med-kommando (ersätta plats hållaren):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Nästa steg

Andra versioner av de officiella Microsoft-baserade avbildningarna av Java Base finns i:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Konsol för Java SE JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK och maven](https://hub.docker.com/_/microsoft-java-maven)

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
