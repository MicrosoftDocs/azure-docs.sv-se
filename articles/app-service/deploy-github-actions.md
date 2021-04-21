---
title: Konfigurera CI/CD med GitHub Actions
description: Lär dig hur du distribuerar din kod Azure App Service från en CI/CD-pipeline med GitHub Actions. Anpassa bygguppgifterna och kör komplexa distributioner.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: aa4475ccedfb19ece540337f493bcc5ed64af035
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832472"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Distribuera till App Service med GitHub Actions

Kom igång med [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) att automatisera arbetsflödet och distribuera [till](overview.md) Azure App Service från GitHub. 

## <a name="prerequisites"></a>Förutsättningar 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnadsfritt.](https://github.com/join)  
- En fungerande Azure App Service app. 
    - .NET: [Skapa en ASP.NET Core-webbapp i Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Skapa en ASP.NET Framework-webbapp i Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Skapa en Node.js-webbapp i Azure App Service](quickstart-nodejs.md)  
    - Java: [Skapa en Java-app på Azure App Service](quickstart-java.md)
    - Python: [Skapa en Python-app i Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på lagringsplatsen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

Filen har tre avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten eller en publiceringsprofil. <br /> 2. Skapa en GitHub-hemlighet. |
|**Skapa** | 1. Konfigurera miljön. <br /> 2. Skapa webbappen. |
|**Distribuera** | 1. Distribuera webbappen. |

## <a name="use-the-deployment-center"></a>Använda Distributionscenter

Du kan snabbt komma igång GitHub Actions med hjälp av App Service Deployment Center. Detta genererar automatiskt en arbetsflödesfil baserat på din programstack och sparar den på GitHub-lagringsplatsen i rätt katalog.

1. Gå till webbappen i Azure Portal
1. Till vänster klickar du på **Distributionscenter**
1. Under **Kontinuerlig distribution (CI/CD)** väljer du **GitHub**
1. Välj **sedan GitHub Actions**
1. Använd listrutan för att välja din GitHub-lagringsplats, gren och programstack
    - Om den valda grenen är skyddad kan du fortfarande fortsätta att lägga till arbetsflödesfilen. Se till att granska dina grenskydd innan du fortsätter.
1. På den sista skärmen kan du granska dina val och förhandsgranska arbetsflödesfilen som kommer att finnas på lagringsplatsen. Om valen är korrekta klickar du på **Slutför**

Detta kommer att genomföra arbetsflödesfilen till lagringsplatsen. Arbetsflödet för att skapa och distribuera din app startar omedelbart.

## <a name="set-up-a-workflow-manually"></a>Konfigurera ett arbetsflöde manuellt

Du kan också distribuera ett arbetsflöde utan att använda Distributionscenter. För att göra det måste du först generera autentiseringsuppgifter för distribution. 

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Det rekommenderade sättet att autentisera med Azure App Services för GitHub Actions är med en publiceringsprofil. Du kan också autentisera med ett huvudnamn för tjänsten, men processen kräver fler steg. 

Spara dina autentiseringsuppgifter för publiceringsprofilen eller tjänstens huvudnamn som [en GitHub-hemlighet](https://docs.github.com/en/actions/reference/encrypted-secrets) för att autentisera med Azure. Du kommer åt hemligheten i arbetsflödet. 

# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

En publiceringsprofil är autentiseringsuppgifter på appnivå. Konfigurera din publiceringsprofil som en GitHub-hemlighet. 

1. Gå till apptjänsten i Azure Portal. 

1. På sidan **Översikt** väljer du **Hämta publiceringsprofil.**

1. Spara den hämtade filen. Du använder innehållet i filen för att skapa en GitHub-hemlighet.

> [!NOTE]
> Från och med oktober 2020 behöver Linux-webbappar appinställningen inställd `WEBSITE_WEBDEPLOY_USE_SCM` på `true` **innan publiceringsprofilen laddas ned.** Det här kravet kommer att tas bort i framtiden.

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/userlevel)

Du kan skapa ett [huvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du platshållarna med ditt prenumerations-ID, resursgruppens namn och appnamnet. Utdata är ett JSON-objekt med autentiseringsuppgifter för rolltilldelning som ger åtkomst till din App Service som liknar nedan. Kopiera det här JSON-objektet till senare.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja lägsta åtkomst. Omfånget i föregående exempel är begränsat till den App Service appen och inte hela resursgruppen.

---

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten


# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

I [GitHub](https://github.com/)bläddrar du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

Om du [vill använda autentiseringsuppgifter på](#generate-deployment-credentials)appnivå klistrar du in innehållet i den nedladdade publiceringsprofilfilen i hemlighetens värdefält. Ge hemligheten namnet `AZURE_WEBAPP_PUBLISH_PROFILE` .

När du konfigurerar ditt GitHub-arbetsflöde använder du `AZURE_WEBAPP_PUBLISH_PROFILE` åtgärden för att distribuera Azure Web App. Exempel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/userlevel)

I [GitHub](https://github.com/)bläddrar du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

Om du vill använda autentiseringsuppgifter på användarnivå klistrar du in hela JSON-utdata från Azure [CLI-kommandot](#generate-deployment-credentials)i hemlighetens värdefält. Ge hemligheten namnet `AZURE_CREDENTIALS` .

När du konfigurerar arbetsflödesfilen senare använder du hemligheten som indata `creds` för azure-inloggningsåtgärden. Exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Konfigurera miljön

Du kan konfigurera miljön med någon av konfigurationsåtgärderna.

|**Språk**  |**Installationsåtgärd**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

I följande exempel visas hur du ställer in miljön för olika språk som stöds:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Skapa webbappen

Processen för att skapa en webbapp och distribuera till Azure App Service ändras beroende på språk. 

I följande exempel visas den del av arbetsflödet som skapar webbappen på olika språk som stöds.

För alla språk kan du ange webbappens rotkatalog med `working-directory` . 

**.NET**

Miljövariabeln `AZURE_WEBAPP_PACKAGE_PATH` anger sökvägen till webbappsprojektet. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Du kan återställa NuGet-beroenden och köra msbuild med `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.2

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Du Node.js ange eller ändra `working-directory` för npm-katalogen i `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Distribuera till App Service

Om du vill distribuera din kod App Service en app använder du `azure/webapps-deploy@v2` åtgärden . Den här åtgärden har fyra parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **appnamn** | (Krävs) Namnet på App Service appen | 
| **publicera profil** | (Valfritt) Publicera profilfilinnehåll med Web Deploy-hemligheter |
| **Paket** | (Valfritt) Sökväg till paket eller mapp. Sökvägen kan innehålla *.zip, *.war, *.jar eller en mapp som ska distribueras |
| **facknamn** | (Valfritt) Ange en annan plats än [produktionsplatsen](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Skapa och distribuera en .NET Core-app till Azure med hjälp av en Azure-publiceringsprofil. Indata `publish-profile` refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Skapa och distribuera en ASP.NET MVC-app som använder NuGet `publish-profile` och för autentisering. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Skapa och distribuera en Java Spring-app till Azure med hjälp av en Azure-publiceringsprofil. Indata `publish-profile` refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Om du vill `war` distribuera en i stället för en ändrar du `jar` `package` värdet. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Skapa och distribuera Node.js till Azure med appens publiceringsprofil. Indata `publish-profile` refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Skapa och distribuera en Python-app till Azure med appens publiceringsprofil. Observera hur `publish-profile` indata refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Skapa och distribuera en .NET Core-app till Azure med hjälp av azure-tjänstens huvudnamn. Observera hur `creds` indata refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Skapa och distribuera en ASP.NET MVC-app till Azure med hjälp av azure-tjänstens huvudnamn. Observera hur `creds` indata refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Skapa och distribuera en Java Spring-app till Azure med hjälp av azure-tjänstens huvudnamn. Observera hur `creds` indata refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Skapa och distribuera en Node.js till Azure med hjälp av azure-tjänstens huvudnamn. Observera hur `creds` indata refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Skapa och distribuera en Python-app till Azure med hjälp av azure-tjänstens huvudnamn. Observera hur `creds` indata refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Nästa steg

Du hittar vår uppsättning åtgärder grupperade i olika lagringsplatsen på GitHub, där var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Arbetsflöden för åtgärder som ska distribueras till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för containrar](https://github.com/Azure/webapps-container-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Startarbetsflöden](https://github.com/actions/starter-workflows)
