---
title: 'Snabb start: skapa en Java-app på Azure App Service'
description: Distribuera din första Java-Hello World till Azure App Service på några minuter. Med plugin-programmet för Azure-Webbappar för maven kan du enkelt distribuera Java-appar.
keywords: Azure, App Service, Web App, Windows, Linux, Java, maven, snabb start
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701730"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Snabb start: skapa en Java-app på Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) med [plugin-programmet för Azure-Webbappar för maven för](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) att distribuera en. jar-fil eller en War-fil. Använd flikarna för att växla mellan Java SE och Tomcat-instruktioner.


> [!NOTE]
> Du kan också göra samma användning av populära IDE: er som IntelliJ och Sol förmörkelse. Ta en titt på våra liknande dokument i [Azure Toolkit for IntelliJ snabb start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) eller [Azure Toolkit for Eclipse snabb start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![Exempel app som körs i Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Skapa en Java-app

# <a name="java-se"></a>[Java SE](#tab/javase)

Klona exempel projektet " [våren Boot komma igång](https://github.com/spring-guides/gs-spring-boot) .

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Byt katalog till det slutförda projektet.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Kör följande Maven-kommando i Cloud Shell-prompten för att skapa en ny app med namnet `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ändra sedan arbets katalogen till projektmappen:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Konfigurera Maven-plugin-programmet

I distributionsprocessen till Azure App Service används dina Azure-autentiseringsuppgifter från Azure CLI automatiskt. Om Azure CLI inte installeras lokalt autentiseras maven-plugin-programmet med OAuth eller enhets inloggning. Mer information finns i [autentisering med maven-plugin](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)-program.

Kör nedanstående Maven-kommando för att konfigurera distributionen. Med det här kommandot kan du konfigurera App Service operativ system, Java-version och Tomcat-version.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** godkänner du alternativet defaut `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Windows** genom att ange `3` .
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    Sammanfattnings resultatet ser ut ungefär som i det stycke som visas nedan.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** godkänner du alternativet defaut `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Windows** genom att ange `3` .
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Använd standard webb behållaren, **Tomcat 8,5**, genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    Sammanfattnings resultatet ser ut ungefär som i det stycke som visas nedan.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** godkänner du alternativet defaut `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Linux** genom att trycka på RETUR.
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** godkänner du alternativet defaut `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Linux** genom att trycka på RETUR.
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Använd standard webb behållaren, **Tomcat 8,5**, genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Du kan ändra konfigurationerna för App Service direkt i `pom.xml` om så behövs. Några vanliga visas nedan:

Egenskap | Krävs | Beskrivning | Version
---|---|---|---
`<schemaVersion>` | falskt | Ange konfigurations schemats version. De värden som stöds är: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | falskt | Ange prenumerations-ID. | 0.1.0 +
`<resourceGroup>` | true | Azure-resurs grupp för din webbapp. | 0.1.0 +
`<appName>` | true | Namnet på din webbapp. | 0.1.0 +
`<region>` | true | Anger den region där din webbapp ska vara värd. Standardvärdet är **westeurope**. Avsnittet alla giltiga regioner i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | falskt | Pris nivån för din webbapp. Standardvärdet är **P1V2** för produktions arbets belastningen, medan **B2** är det rekommenderade minimi kravet för Java-utveckling/testning. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfiguration av körnings miljön kan du se informationen [här](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +
`<deployment>` | true | Distributions konfigurationen kan du se informationen [här](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +

Var försiktig med värdena i `<appName>` och `<resourceGroup>` ( `helloworld-1590394316693` och `helloworld-1590394316693-rg` därmed i demonstrationen). de kommer att användas senare.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Distribuera appen

Plugin-programmet maven använder kontoautentiseringsuppgifter från Azure CLI för att distribuera till App Services. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli) innan du fortsätter.

```azurecli
az login
```

Sedan kan du distribuera din Java-app till Azure med hjälp av följande kommando.

```bash
mvn package azure-webapp:deploy
```

När distributionen är färdig är appen redo på `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` i demonstrationen). Öppna webb adressen med din lokala webbläsare, du bör se

![Exempel app som körs i Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Grattis!** Du har distribuerat din första java-app till App Service.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver de här resurserna i framtiden ska du ta bort resursgruppen via portalen, eller med följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Det kan några minuter att köra kommandot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för PostgreSQL med Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Konfigurera CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Pris information](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Sammanställda loggar och mått](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Skala upp](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Resurser för Azure för Java-utvecklare](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurera din Java-app](configure-language-java.md)
