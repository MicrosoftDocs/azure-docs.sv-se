---
title: Snabb start – bygga och distribuera appar till Azure våren Cloud
description: Beskriver app-distribution till Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 727354593488cd5af77408eaa7c8d9e747a0b071
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879345"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Snabb start: bygga och distribuera appar till Azure våren Cloud

::: zone pivot="programming-language-csharp"
I den här snabb starten skapar och distribuerar du mikrotjänstprogram till Azure våren-molnet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* Slutför föregående snabb starter i den här serien:

  * [Etablera Azure våren Cloud Service](spring-cloud-quickstart-provision-service-instance.md).
  * [Konfigurera konfigurations servern för Azure våren Cloud](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>Ladda ned exempelprogrammet

Om du har använt Azure Cloud Shell fram till den här punkten växlar du till en lokal kommando tolk för följande steg.

1. Skapa en ny mapp och klona exempel App-lagringsplatsen.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Navigera till databasens katalog.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>Distribuera PlanetWeatherProvider

1. Skapa en app för PlanetWeatherProvider-projektet i Azure våren Cloud-instansen.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Om du vill aktivera automatisk tjänst registrering har du gett appen samma namn som värdet för `spring.application.name` i projektets *appsettings.js* fil:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Det kan ta flera minuter att köra det här kommandot.

1. Ändra katalogen till `PlanetWeatherProvider` projektmappen.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Skapa binärfilerna och *. zip* -filen som ska distribueras.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > Projekt filen innehåller följande XML-kod för att paketera binärfilerna i en *. zip* -fil när de skrivs till mappen *./Publish* :
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Distribuera till Azure.

   Kontrol lera att kommando tolken finns i projektmappen innan du kör följande kommando.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   `--main-entry`Alternativet anger den relativa sökvägen från *. zip* -filens rotmapp till den *DLL* -fil som innehåller programmets start punkt. När tjänsten överför *zip* -filen extraheras alla filer och mappar och försök görs att köra start punkten i den angivna *DLL* -filen.

   Det kan ta flera minuter att köra det här kommandot.

## <a name="deploy-solarsystemweather"></a>Distribuera SolarSystemWeather

1. Skapa en annan app i din Azure våren Cloud-instans, den här gången för SolarSystemWeather-projektet:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` är det namn som anges i `SolarSystemWeather` projektets *appsettings.jsi* filen.

   Det kan ta flera minuter att köra det här kommandot.

1. Ändra katalog till `SolarSystemWeather` projektet.

   ```console
   cd ../solar-system-weather
   ```

1. Skapa binärfiler och *. zip* -filen som ska distribueras.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Distribuera till Azure.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Det kan ta flera minuter att köra det här kommandot.

## <a name="assign-public-endpoint"></a>Tilldela offentlig slut punkt

Testa programmet genom att skicka en HTTP GET-begäran till `solar-system-weather` programmet från en webbläsare.  Om du vill göra det behöver du en offentlig slut punkt för begäran.

1. Kör följande kommando för att tilldela slut punkten.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --assign-endpoint true
   ```

1. Kör följande kommando för att hämta slut punktens URL.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Testa programmet

Skicka en GET-begäran till `solar-system-weather` appen. I en webbläsare navigerar du till den offentliga URL: en som har `/weatherforecast` lagts till i slutet av den. Exempel:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

Utdata är JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Detta svar visar att både mikrotjänst-appar fungerar. `SolarSystemWeather`Appen returnerar data som hämtats från `PlanetWeatherProvider` appen.
::: zone-end

::: zone pivot="programming-language-java"
Det här dokumentet beskriver hur du skapar och distribuerar program för mikrotjänster till Azure våren Cloud med:
* Azure CLI
* Maven-plugin
* IntelliJ

Innan du distribuerar med Azure CLI eller Maven ska du slutföra exemplen som [etablerar en instans av Azure våren Cloud](spring-cloud-quickstart-provision-service-instance.md) och [konfigurerar konfigurations servern](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](/java/azure/jdk/)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [Logga](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) in

## <a name="deployment-procedures"></a>Distributions procedurer

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Bygg appar för mikrotjänster lokalt

1. Klona exempel App-lagringsplatsen till ditt Azure Cloud-konto.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Ändra katalog och bygg projektet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Kompilering av projektet tar cirka 5 minuter. När du är klar bör du ha enskilda JAR-filer för varje tjänst i respektive mapp.

### <a name="create-and-deploy-the-apps"></a>Skapa och distribuera apparna

1. Ange standard resurs grupps namnet och kluster namnet med hjälp av följande kommandon:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Skapa Azure våren Cloud-mikrotjänster med hjälp av JAR-filerna som skapats i föregående steg. Du kommer att skapa tre appar: **Gateway**, **auth-service** och **Account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Vi måste distribuera program som skapats i föregående steg till Azure. Använd följande kommandon för att distribuera alla tre programmen:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Tilldela offentlig slut punkt till gateway

Vi behöver ett sätt att komma åt programmet via en webbläsare. Vårt Gateway-program behöver en offentlig slut punkt.

1. Tilldela slut punkten med följande kommando:

    ```azurecli
    az spring-cloud app update -n gateway --assign-endpoint true
    ```

2. Fråga **Gateway** -appen efter dess offentliga IP-adress så att du kan kontrol lera att programmet körs:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Klona och bygga exempel program databasen

1. Klona git-lagringsplatsen genom att köra följande kommando:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Ändra katalog och bygg projektet genom att köra följande kommando:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generera konfigurationer och distribuera till Azure våren-molnet

1. Generera konfigurationer genom att köra följande kommando i rotmappen för PiggyMetrics som innehåller den överordnade POM. Om du redan har loggat in med Azure CLI kommer kommandot automatiskt att hämta autentiseringsuppgifterna. Annars loggas du in med anvisningarna. Mer information finns på vår [wiki-sida](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    Du uppmanas att välja:
    * **Moduler:** Välj `gateway` , `auth-service` och `account-service` .
    * **Prenumeration:** Det här är din prenumeration som används för att skapa en Azure våren Cloud-instans.
    * **Tjänst instans:** Det här är namnet på din Azure våren Cloud-instans.
    * **Offentlig slut punkt:** I listan med tillhandahållna projekt anger du det nummer som motsvarar `gateway` .  Detta ger IT-offentlig åtkomst.

1. POM innehåller nu plugin-beroenden och konfigurationer. Distribuera apparna med hjälp av följande kommando. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importera exempel projekt i IntelliJ

1. Ladda ned och zippa upp käll databasen för den här själv studie kursen eller klona den med git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Öppna dialog rutan **Välkommen** IntelliJ, Välj **Importera projekt** för att öppna import guiden.

1. Välj `piggymetric` mapp.

    ![Importera projekt](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Distribuera Gateway-appen till Azure våren Cloud
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto med Azure Toolkit for IntelliJ och välja din prenumeration. Information om inloggning finns i [installation och inloggning](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ Project Explorer och välj **Azure**  ->  **Deploy till Azure våren Cloud**.

    ![Distribuera till Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. I fältet **namn** lägger du till *: Gateway* till det befintliga **namnet**.
1. I text rutan **artefakt** väljer du *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. I text rutan **prenumeration** verifierar du din prenumeration.
1. I text rutan **fjäder moln** väljer du instansen av Azure våren-molnet som du skapade i [etablera Azure våren Cloud-instansen](./spring-cloud-quickstart-provision-service-instance.md).
1. Ange den **offentliga slut punkten** som ska *aktive ras*.
1. I text rutan **app:** väljer du **skapa app...**.
1. Ange *Gateway* och klicka sedan på **OK**.

    ![Distribuera till Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. I avsnittet **före start** i dialog rutan dubbelklickar du på *Kör maven-mål*.
1. I text rutan **arbets katalog** navigerar du till mappen *piggymetrics/Gateway* .
1. I text rutan **kommando rad** anger du *Package-DskipTests*. Klicka på **OK**.
1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . Plugin-programmet kör kommandot `mvn package` på `gateway` appen och distribuerar jar som genereras av `package` kommandot.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Distribuera auth-service-och konto-service-appar till Azure våren Cloud
Du kan upprepa stegen ovan för att distribuera `auth-service` och `account-service` apparna till Azure våren-molnet:

1. Ändra **namnet** och **artefakten** för att identifiera `auth-service` appen.
1. I text rutan **app:** väljer du **skapa app...** för att skapa `auth-service` appar.
1. Kontrol lera att alternativet **offentlig slut punkt** är inställt på *inaktive rad*.
1. I avsnittet **före start** i dialog rutan byter du **arbets katalog** till mappen *piggymetrics/auth-service* .
1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . 
1. Upprepa dessa procedurer för att konfigurera och distribuera `account-service` .
---

Navigera till URL: en som anges i de föregående stegen för att komma åt PiggyMetrics-programmet. Exempelvis: `https://<service instance name>-gateway.azuremicroservices.io`

![Åtkomst PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Du kan också navigera Azure Portal för att hitta URL: en. 
1. Navigera till tjänsten
2. Välj **appar**
3. Välj **Gateway**

    ![Navigera i app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Hitta URL: en på **gatewayen | Översikts** sida

    ![Navigera i app Second](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du Azure-resurser som kommer fortsätta att debiteras om de finns kvar i din prenumeration. Om du inte tänker fortsätta till nästa snabb start, se [Rensa resurser](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Annars går du vidare till nästa snabb start:

> [!div class="nextstepaction"]
> [Loggar, statistik och spårning](spring-cloud-quickstart-logs-metrics-tracing.md)
