---
title: Azure Spring Cloud CI/CD med GitHub Actions
description: Så här skapar du ETT CI/CD-arbetsflöde för Azure Spring Cloud med GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791363"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD med GitHub Actions

GitHub Actions stöd för ett arbetsflöde för automatiserad programutvecklingslivscykel. Med GitHub Actions för Azure Spring Cloud kan du skapa arbetsflöden på din lagringsplats för att skapa, testa, paketera, släppa och distribuera till Azure. 

## <a name="prerequisites"></a>Förutsättningar
Det här exemplet kräver [Azure CLI](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Konfigurera GitHub-lagringsplatsen och autentisera
Du behöver autentiseringsuppgifter för Azure-tjänstens huvudnamn för att auktorisera Azure-inloggningsåtgärden. Kör följande kommandon på den lokala datorn för att hämta en Azure-autentiseringsidentifiering:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Om du vill komma åt en specifik resursgrupp kan du minska omfånget:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Kommandot ska mata ut ett JSON-objekt:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet [används exemplet på metalltå på GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Förförför lagringsplatsen öppnar du gitHub-lagringsplatsens sida för förk. Välj **sedan fliken** Inställningar. Öppna menyn **Hemligheter** och välj **Ny hemlighet:**

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet till och dess värde till den JSON-sträng som du hittade `AZURE_CREDENTIALS` under rubriken Konfigurera din *GitHub-lagringsplats och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta Autentiseringsuppgifter för Azure-inloggning från Key Vault i GitHub actions enligt förklaringen i Autentisera [Azure Spring med Key Vault i GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Etablera tjänstinstans
Om du vill Azure Spring Cloud instans av tjänsten kör du följande kommandon med hjälp av Azure CLI.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Skapa arbetsflödet
Arbetsflödet definieras med följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbereda för distribution med Azure CLI

Kommandot är `az spring-cloud app create` för närvarande inte idempotent. När du har kört den en gång får du ett felmeddelande om du kör samma kommando igen. Vi rekommenderar det här arbetsflödet på Azure Spring Cloud appar och instanser.

Använd följande Azure CLI-kommandon för förberedelse:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera direkt med Azure CLI

Skapa `.github/workflows/main.yml` filen på lagringsplatsen med följande innehåll. Ersätt `<your resource group name>` `<your service name>` och med rätt värden.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Konfigurera GitHub-lagringsplatsen och autentisera
Du behöver autentiseringsuppgifter för Azure-tjänstens huvudnamn för att auktorisera Azure-inloggningsåtgärden. Kör följande kommandon på den lokala datorn för att hämta en Azure-autentiseringsidentifiering:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Om du vill komma åt en specifik resursgrupp kan du minska omfånget:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Kommandot ska mata ut ett JSON-objekt:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet [används Exempel på SymmetryMetrics](https://github.com/Azure-Samples/piggymetrics) på GitHub.  Förkampa exemplet, öppna sidan gitHub-lagringsplats och klicka **på fliken** Inställningar. Öppna **menyn Hemligheter** och klicka på Lägg till en ny **hemlighet:**

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet till och dess värde till den JSON-sträng som du hittade under rubriken Konfigurera din `AZURE_CREDENTIALS` *GitHub-lagringsplats och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta autentiseringsuppgifterna för Azure-Key Vault i GitHub-åtgärder enligt förklaringen i Autentisera [Azure Spring med Key Vault i GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Etablera tjänstinstans
Om du vill Azure Spring Cloud-tjänstinstansen kör du följande kommandon med hjälp av Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Skapa arbetsflödet
Arbetsflödet definieras med följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbereda för distribution med Azure CLI
Kommandot är `az spring-cloud app create` för närvarande inte idempotent.  Vi rekommenderar det här arbetsflödet på Azure Spring Cloud appar och instanser.

Använd följande Azure CLI-kommandon för förberedelse:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera direkt med Azure CLI
Skapa filen `.github/workflow/main.yml` på lagringsplatsen:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Distribuera med Azure CLI-åtgärd
Kommandot az `run` använder den senaste versionen av Azure CLI. Om det sker större ändringar kan du också använda en specifik version av Azure CLI med `action` azure/CLI. 

> [!Note] 
> Det här kommandot körs i en ny container, så det fungerar inte, och `env` åtkomst mellan åtgärder kan ha extra begränsningar.

Skapa filen .github/workflow/main.yml på lagringsplatsen:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Distribuera med Maven-plugin-programmet
Ett annat alternativ är att använda [Maven-plugin-programmet](./spring-cloud-quickstart.md) för att distribuera Jar och uppdatera appinställningarna. Kommandot är `mvn azure-spring-cloud:deploy` idempotent och skapar automatiskt Appar om det behövs. Du behöver inte skapa motsvarande appar i förväg.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Kör arbetsflödet

GitHub **Actions** ska aktiveras automatiskt när du har `.github/workflow/main.yml` push-aktiverat till GitHub. Åtgärden utlöses när du push-genomför en ny. Om du skapar den här filen i webbläsaren bör åtgärden redan ha körts.

Kontrollera att åtgärden har aktiverats genom att klicka på **fliken Åtgärder** på gitHub-lagringsplatsens sida:

![Kontrollera att åtgärden är aktiverad](./media/github-actions/actions3.png)

Om åtgärden körs med ett fel, till exempel om du inte har angett Azure-autentiseringssuppgifter, kan du köra kontroller igen när du har åtgärdat felet. På gitHub-lagringsplatsens sida klickar du på **Åtgärder,** väljer den specifika arbetsflödesuppgiften och klickar sedan på knappen Kör **om kontroller** för att köra kontroller igen:

![Köra kontroller igen](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Nästa steg

* [Key Vault för Spring Cloud GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory tjänstens huvudnamn](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [GitHub-åtgärder för Azure](https://github.com/Azure/actions/)
