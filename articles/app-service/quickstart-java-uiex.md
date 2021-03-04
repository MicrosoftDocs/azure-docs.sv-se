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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050383"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Snabb start: skapa en Java-app på Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) med [plugin-programmet för Azure-Webbappar för maven för](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) att distribuera en. jar-fil eller en War-fil. 

Det finns även IDE-versioner av den här artikeln. Kolla [Azure Toolkit for IntelliJ snabb start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) eller [Azure Toolkit for Eclipse snabb start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar måste du ha följande:

+ En <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">Azure-konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure CLI](/cli/azure/install-azure-cli).

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

Plugin-programmet maven använder kontoautentiseringsuppgifter från Azure CLI för att distribuera till App Services. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli) innan du fortsätter. Mer information finns i [autentisering med maven-plugin](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)-program.

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. skapa en Java-app

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

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. Konfigurera maven-plugin-programmet

Kör nedanstående Maven-kommando för att konfigurera distributionen. Med det här kommandot kan du konfigurera App Service operativ system, Java-version och Tomcat-version.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** accepterar du standard alternativet `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Windows** genom att ange `3` .
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    Sammanfattnings resultatet ser ut ungefär som i det stycke som visas nedan.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** accepterar du standard alternativet `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Windows** genom att ange `3` .
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Använd standard webb behållaren, **Tomcat 8,5**, genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    Sammanfattnings resultatet ser ut ungefär som i det stycke som visas nedan.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** accepterar du standard alternativet `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Linux** genom att trycka på RETUR.
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. När du uppmanas att ange alternativet **Subscription** väljer du rätt `Subscription` genom att skriva in numret på raden.
1. När du uppmanas att ange alternativet för **webbapp** accepterar du standard alternativet `<create>` genom att trycka på RETUR eller välja en befintlig app.
1. När du uppmanas med alternativet **OS** väljer du **Linux** genom att trycka på RETUR.
1. När du tillfrågas om **pris nivå** väljer du **B2** genom att ange `2` .
1. Använd Java-standard versionen **Java 8** genom att trycka på RETUR.
1. Använd standard webb behållaren, **Tomcat 8,5**, genom att trycka på RETUR.
1. Tryck slutligen på Retur igen för att bekräfta dina val.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end

Du kan ändra konfigurationerna för App Service direkt i `pom.xml` om så behövs. Några vanliga visas nedan:

Egenskap | Krävs | Beskrivning | Version
---|---|---|---
`<schemaVersion>` | falskt | Ange konfigurations schemats version. De värden som stöds är: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | falskt | Ange prenumerations-ID. | 0.1.0 +
`<resourceGroup>` | true | Azure <abbr title="En logisk behållare för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr> för din webbapp. | 0.1.0 +
`<appName>` | true | Namnet på din webbapp. | 0.1.0 +
`<region>` | true | Anger den region där din webbapp ska vara värd. Standardvärdet är **westeurope**. Avsnittet alla giltiga regioner i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | falskt | Pris nivån för din webbapp. Standardvärdet är **P1V2** för produktions arbets belastningen, medan **B2** är det rekommenderade minimi kravet för Java-utveckling/testning. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfiguration av körnings miljön kan du se informationen [här](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +
`<deployment>` | true | Distributions konfigurationen kan du se informationen [här](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +

Var försiktig med värdena i `<appName>` och `<resourceGroup>` ( `helloworld-1590394316693` och `helloworld-1590394316693-rg` därmed i exemplet på utdata) kommer de att användas senare.

[Rapportera ett problem](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. distribuera appen

Distribuera din Java-app till Azure med hjälp av följande kommando.

```bash
mvn package azure-webapp:deploy
```

När distributionen är klar är ditt program klart på `http://<appName>.azurewebsites.net/` . Öppna webb adressen med din lokala webbläsare. Du bör se följande visning:

![Exempel app som körs i Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Grattis!** Du har distribuerat din första java-app till App Service.

[Rapportera ett problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Rensa resurser

Ta bort Java-appen och dess relaterade resurser för att undvika ytterligare kostnader.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Det kan några minuter att köra kommandot.

<br>
<hr/>

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
