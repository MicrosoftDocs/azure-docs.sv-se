---
title: Konfigurera CI/CD med GitHub-åtgärder
description: Lär dig hur du distribuerar din kod till Azure App Service från en CI/CD-pipeline med GitHub-åtgärder. Anpassa Bygg aktiviteterna och kör komplexa distributioner.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 59eb56dd188edf258c3631cde957c0864454ad76
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762652"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Distribuera till App Service med GitHub Actions

Kom igång med [GitHub-åtgärder](https://docs.github.com/en/actions/learn-github-actions) för att automatisera arbets flödet och distribuera till [Azure App Service](overview.md) från GitHub. 

## <a name="prerequisites"></a>Krav 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  
- En fungerande Azure App Service-app. 
    - .NET: [skapa en ASP.net Core webbapp i Azure](quickstart-dotnetcore.md)
    - ASP.NET: [skapa en ASP.NET Framework-webbapp i Azure](quickstart-dotnet-framework.md)
    - Java Script: [skapa ett Node.js-webbprogram i Azure App Service](quickstart-nodejs.md)  
    - Java: [skapa en Java-app på Azure App Service](quickstart-java.md)
    - Python: [skapa en python-app i Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

Filen har tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten eller en publicerings profil. <br /> 2. skapa en GitHub-hemlighet. |
|**Skapa** | 1. Konfigurera miljön. <br /> 2. Bygg webb programmet. |
|**Distribuera** | 1. distribuera webbappen. |

## <a name="use-the-deployment-center"></a>Använda distributions Center

Du kan snabbt komma igång med GitHub-åtgärder med hjälp av App Service Deployment Center. Detta genererar automatiskt en arbets flödes fil baserat på din programs tack och skickar den till din GitHub-lagringsplats i rätt katalog.

1. Navigera till din webapp i Azure Portal
1. Klicka på **Deployment Center** på vänster sida
1. Under **kontinuerlig distribution (CI/CD)** väljer du **GitHub**
1. Välj sedan **GitHub-åtgärder**
1. Använd List rutorna för att välja din GitHub-lagringsplats, gren och programs tack
    - Om den valda grenen är skyddad kan du fortfarande fortsätta att lägga till arbets flödes filen. Se till att granska dina gren skydd innan du fortsätter.
1. På den sista skärmen kan du granska dina val och förhandsgranska arbets flödes filen som ska allokeras till lagrings platsen. Om inställningarna är korrekta klickar du på **Slutför**

Detta kommer att spara arbets flödes filen på lagrings platsen. Arbets flödet som används för att skapa och distribuera din app startar omedelbart.

## <a name="set-up-a-workflow-manually"></a>Konfigurera ett arbets flöde manuellt

Du kan också distribuera ett arbets flöde utan att använda distributions Center. För att göra det måste du först generera autentiseringsuppgifter för distributionen. 

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Det rekommenderade sättet att autentisera med Azure App tjänster för GitHub-åtgärder är med en publicerings profil. Du kan också autentisera med ett huvud namn för tjänsten, men processen kräver fler steg. 

Spara dina autentiseringsuppgifter för din publicerings profil eller tjänstens huvud namn som [GitHub-hemlighet](https://docs.github.com/en/actions/reference/encrypted-secrets) för att autentisera med Azure. Du kommer att få åtkomst till hemligheten i ditt arbets flöde. 

# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

En publicerings profil är en autentiseringsuppgift på program nivå. Konfigurera din publicerings profil som GitHub-hemlighet. 

1. Gå till App Service i Azure Portal. 

1. På sidan **Översikt** väljer du **Hämta publicerings profil**.

1. Spara den hämtade filen. Du använder filens innehåll för att skapa en GitHub-hemlighet.

> [!NOTE]
> Från och med oktober 2020 behöver Linux-webbapparna appens inställning `WEBSITE_WEBDEPLOY_USE_SCM` inställt på `true` **innan publicerings profilen laddas ned**. Detta krav kommer att tas bort i framtiden.

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/userlevel)

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID, resurs grupp namn och app-namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare.

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
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.

---

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten


# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för program nivå](#generate-deployment-credentials)klistrar du in innehållet i den hämtade publicerings profil filen i fältet hemligt värde. Namnge hemligheten `AZURE_WEBAPP_PUBLISH_PROFILE` .

När du konfigurerar ditt GitHub-arbetsflöde använder du `AZURE_WEBAPP_PUBLISH_PROFILE` åtgärden för att distribuera Azure Web App. Ett exempel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/userlevel)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för användar nivå](#generate-deployment-credentials)klistrar du in hela JSON-utdata från Azure CLI-kommandot till fältet hemligt värde. Ge hemligheten namnet `AZURE_CREDENTIALS` .

När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Ett exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Konfigurera miljön

Konfigurationen av miljön kan göras med hjälp av en av installations åtgärderna.

|**Språk**  |**Installations åtgärd**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

I följande exempel visas hur du konfigurerar miljön för de olika språk som stöds:

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

## <a name="build-the-web-app"></a>Bygg webb programmet

Processen för att skapa en webbapp och distribuera till Azure App Service ändringar beroende på språket. 

I följande exempel visas den del av arbets flödet som bygger webbappen på olika språk som stöds.

För alla språk kan du ange rot katalogen för webbapp med `working-directory` . 

**.NET**

Miljövariabeln `AZURE_WEBAPP_PACKAGE_PATH` anger sökvägen till ditt webbapp. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Du kan återställa NuGet-beroenden och köra MSBuild med `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

För Node.js kan du ange `working-directory` eller ändra för NPM-katalogen i `pushd` . 

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

Om du vill distribuera din kod till en App Service-app använder du `azure/webapps-deploy@v2` åtgärden. Den här åtgärden har fyra parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namnet på App Service-appen | 
| **publicera – profil** | Valfritt Publicera profil filens innehåll med webb distributions hemligheter |
| **paketfilerna** | Valfritt Sökväg till paket eller mapp. Sökvägen kan innehålla *. zip, *. War, *. jar eller en mapp för distribution |
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions [platsen](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Publicera profil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Bygg och distribuera en .NET Core-app till Azure med hjälp av en Azure Publish-profil. `publish-profile`Inmatarna refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

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

Bygg och distribuera en ASP.NET MVC-app som använder NuGet och `publish-profile` för autentisering. 


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
      uses: microsoft/setup-msbuild@v1.0.0

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

Bygg och distribuera en Java våren-app till Azure med hjälp av en Azure Publish-profil. `publish-profile`Inmatarna refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

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

Om du vill distribuera en `war` i stället för en `jar` ändrar du `package` värdet. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Bygg och distribuera en Node.js-app till Azure med appens publicerings profil. `publish-profile`Inmatarna refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

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

Bygg och distribuera en python-app till Azure med appens publicerings profil. Observera att `publish-profile` indatamängden refererar till `AZURE_WEBAPP_PUBLISH_PROFILE` hemligheten som du skapade tidigare.

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

Bygg och distribuera en .NET Core-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.


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

Bygg och distribuera en ASP.NET MVC-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

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
      uses: microsoft/setup-msbuild@v1.0.0

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

Bygg och distribuera en Java våren-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

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

Bygg och distribuera en Node.js-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

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

Bygg och distribuera en python-app till Azure med hjälp av ett Azure-tjänstens huvud namn. Observera att `creds` indatamängden refererar till `AZURE_CREDENTIALS` hemligheten som du skapade tidigare.

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

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Åtgärder arbets flöden att distribuera till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp för behållare](https://github.com/Azure/webapps-container-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Start arbets flöden](https://github.com/actions/starter-workflows)
