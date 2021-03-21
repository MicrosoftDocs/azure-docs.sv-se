---
title: Azure våren Cloud CI/CD med GitHub-åtgärder
description: Skapa ett CI/CD-arbetsflöde för Azure våren Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210418"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure våren Cloud CI/CD med GitHub-åtgärder

GitHub-åtgärder har stöd för ett arbets flöde för automatisk program varu utvecklings livs cykel. Med GitHub-åtgärder för Azure våren Cloud kan du skapa arbets flöden i din lagrings plats för att skapa, testa, paketera, lansera och distribuera dem till Azure. 

## <a name="prerequisites"></a>Förutsättningar
Det här exemplet kräver [Azure CLI](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Konfigurera GitHub-lagringsplatsen och autentisera
Du behöver en Azure-tjänstens huvud namn för att godkänna Azure login-åtgärden. Kör följande kommandon på den lokala datorn för att få en Azure-autentiseringsuppgift:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

För att få åtkomst till en speciell resurs grupp kan du minska omfånget:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Kommandot ska generera ett JSON-objekt:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet används [steeltoe-exemplet på GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Förgrena databasen, öppna sidan GitHub-lagringsplats för förgrening och välj fliken **Inställningar** . Öppna **hemligheter** -menyn och välj **ny hemlighet**:

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet till `AZURE_CREDENTIALS` och dess värde till JSON-strängen som du hittade under rubriken *Konfigurera din GitHub-lagringsplats och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta autentiseringsuppgifter för Azure-inloggning från Key Vault i GitHub-åtgärder som beskrivs i [autentisera Azure-våren med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Etablera tjänst instans
För att etablera din Azure våren Cloud Service-instans kör du följande kommandon med hjälp av Azure CLI.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Bygg arbets flödet
Arbets flödet definieras med hjälp av följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbereda för distribution med Azure CLI

Kommandot `az spring-cloud app create` är för närvarande inte idempotenta. När du har kört det en gång får du ett fel meddelande om du kör samma kommando igen. Vi rekommenderar det här arbets flödet på befintliga Azure våren-molnappar och-instanser.

Använd följande Azure CLI-kommandon för att förbereda:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera med Azure CLI direkt

Skapa `.github/workflows/main.yml` filen i lagrings platsen med följande innehåll. Ersätt `<your resource group name>` och `<your service name>` med rätt värden.

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
Du behöver en Azure-tjänstens huvud namn för att godkänna Azure login-åtgärden. Kör följande kommandon på den lokala datorn för att få en Azure-autentiseringsuppgift:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
För att få åtkomst till en speciell resurs grupp kan du minska omfånget:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Kommandot ska generera ett JSON-objekt:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet används [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) -exemplet på GitHub.  Förgrena exemplet, öppna GitHub-lagringsplatsen och klicka på fliken **Inställningar** . Öppna **hemligheter** -menyn och klicka på **Lägg till en ny hemlighet**:

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet till `AZURE_CREDENTIALS` och dess värde till JSON-strängen som du hittade under rubriken *Konfigurera din GitHub-lagringsplats och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta autentiseringsuppgifter för Azure-inloggning från Key Vault i GitHub-åtgärder som beskrivs i [autentisera Azure-våren med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Etablera tjänst instans
För att etablera din Azure våren Cloud Service-instans kör du följande kommandon med hjälp av Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Bygg arbets flödet
Arbets flödet definieras med hjälp av följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbereda för distribution med Azure CLI
Kommandot `az spring-cloud app create` är för närvarande inte idempotenta.  Vi rekommenderar det här arbets flödet på befintliga Azure våren-molnappar och-instanser.

Använd följande Azure CLI-kommandon för att förbereda:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera med Azure CLI direkt
Skapa `.github/workflow/main.yml` filen på lagrings platsen:

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
AZ- `run` kommandot kommer att använda den senaste versionen av Azure CLI. Om det finns andra ändringar kan du även använda en speciell version av Azure CLI med Azure/CLI `action` . 

> [!Note] 
> Det här kommandot körs i en ny behållare, så `env` fungerar inte, och fil åtkomst för kors åtgärder kan ha extra begränsningar.

Skapa filen. GitHub/Workflow/main. yml på lagrings platsen:
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

## <a name="deploy-with-maven-plugin"></a>Distribuera med maven-plugin-programmet
Ett annat alternativ är att använda [maven-plugin-programmet](./spring-cloud-quickstart.md) för att distribuera jar-och uppdaterings inställningarna för appen. Kommandot `mvn azure-spring-cloud:deploy` är idempotenta och kommer automatiskt att skapa appar om det behövs. Du behöver inte skapa motsvarande appar i förväg.

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

## <a name="run-the-workflow"></a>Kör arbets flödet

GitHub- **åtgärder** ska aktive ras automatiskt när du push-överför `.github/workflow/main.yml` till GitHub. Åtgärden utlöses när du push-överför en ny incheckning. Om du skapar filen i webbläsaren bör åtgärden redan ha körts.

Verifiera att åtgärden har Aktiver ATS genom att klicka på fliken **åtgärder** på sidan GitHub-lagringsplats:

![Verifiera aktive rad åtgärd](./media/github-actions/actions3.png)

Om åtgärden körs som ett fel, till exempel om du inte har angett Azure-autentiseringsuppgiften, kan du köra kontrollerna igen efter att ha åtgärdat felet. På sidan GitHub-lagringsplats klickar du på **åtgärder**, väljer den angivna arbets flödes uppgiften och klickar sedan på knappen **Kör om kontroller** för att köra om kontrollerna:

![Kör om kontroller](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Nästa steg

* [Key Vault för GitHub-åtgärder för våren Cloud](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory tjänstens huvud namn](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [GitHub-åtgärder för Azure](https://github.com/Azure/actions/)
