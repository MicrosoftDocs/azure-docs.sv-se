---
title: Använd GitHub-åtgärder för att göra kod uppdateringar i Azure Functions
description: Lär dig hur du använder GitHub-åtgärder för att definiera ett arbets flöde för att skapa och Distribuera Azure Functions-projekt i GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: 0018b7bafa8ff2ecd328a150df752082c85484e2
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608518"
---
# <a name="continuous-delivery-by-using-github-action"></a>Kontinuerlig leverans med hjälp av GitHub-åtgärd

Använd [GitHub-åtgärder](https://github.com/features/actions) för att definiera ett arbets flöde som automatiskt skapar och distribuerar kod till din Azure Function-app. 

I GitHub-åtgärder är ett [arbets flöde](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) en automatiserad process som du definierar i din GitHub-lagringsplats. Den här processen visar GitHub hur du skapar och distribuerar dina Functions-AppData på GitHub. 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet. 

För ett Azure Functions-arbetsflöde har filen tre delar: 

| Avsnitt | Aktiviteter |
| ------- | ----- |
| **Autentisering** | Ladda ned en publicerings profil.<br/>Skapa en GitHub-hemlighet.|
| **Skapa** | Konfigurera miljön.<br/>Bygg in Function-appen.|
| **Distribuera** | Distribuera Function-appen.|

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  
- En fungerande app som finns på Azure med en GitHub-lagringsplats.   
    - [Snabbstart: Skapa en funktion i Azure med hjälp av Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Det rekommenderade sättet att autentisera med Azure Functions för GitHub-åtgärder är genom att använda en publicerings profil. Du kan också autentisera med ett huvud namn för tjänsten. Läs mer i [den här GitHub Actions-lagringsplatsen](https://github.com/Azure/functions-action). 

När du har sparat autentiseringsuppgifterna för publicerings profilen som [GitHub-hemlighet](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets)använder du den här hemligheten i arbets flödet för att autentisera med Azure. 

#### <a name="download-your-publish-profile"></a>Ladda ned din publicerings profil

Så här hämtar du publicerings profilen för din Function-app:

1. Välj sidan **Översikt** för Function-appen och välj sedan **Hämta publicerings profil**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Ladda ned publicerings profil":::

1. Spara och kopiera innehållet i filen.


### <a name="add-the-github-secret"></a>Lägg till GitHub-hemligheten

1. I [GitHub](https://github.com), bläddra till din lagrings plats, Välj **Inställningar**  >  **hemligheter**  >  **Lägg till en ny hemlighet**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Lägg till hemlighet":::

1. Lägg till en ny hemlighet `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` med **namn**, innehållet i publicerings profil filen för **värdet** och välj sedan **Lägg till hemlighet**.

GitHub kan nu autentisera till din Function-app i Azure.

## <a name="create-the-environment"></a>Skapa miljön 

Konfigurationen av miljön görs med hjälp av en språkspecifik publicerings konfigurations åtgärd.

# <a name="net"></a>[.NET](#tab/dotnet)

.NET (inklusive ASP.NET) använder `actions/setup-dotnet` åtgärden.  
I följande exempel visas den del av arbets flödet som konfigurerar miljön:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Java använder  `actions/setup-java` åtgärden.  
I följande exempel visas den del av arbets flödet som konfigurerar miljön:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Java Script (Node.js) använder `actions/setup-node` åtgärden.  
I följande exempel visas den del av arbets flödet som konfigurerar miljön:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Python använder `actions/setup-python` åtgärden.  
I följande exempel visas den del av arbets flödet som konfigurerar miljön:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Bygg in Function-appen

Detta beror på språket och för språk som stöds av Azure Functions, bör det här avsnittet vara standard stegen för version av varje språk.

I följande exempel visas den del av arbets flödet som skapar Function-appen, som är språkspecifik:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Distribuera funktionsappen
Använd `Azure/functions-action` åtgärden för att distribuera din kod till en Function-app. Den här åtgärden har tre parametrar:

|Parameter |Förklaring  |
|---------|---------|
|_**App-Name**_ | Erforderlig Namnet på din Function-app. |
|_**plats namn**_ | Valfritt Namnet på den [distributions plats](functions-deployment-slots.md) som du vill distribuera till. Facket måste redan vara definierat i din Function-app. |
|_**publicera – profil**_ | Valfritt Namnet på GitHub-hemligheten för din publicerings profil. |

I följande exempel används version 1 av `functions-action` and- `publish profile` autentiseringen 

# <a name="net"></a>[.NET](#tab/dotnet)

Konfigurera ett .NET Linux-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Konfigurera ett .NET Windows-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Konfigurera ett Java Linux-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Konfigurera ett Java Windows-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Konfigurera ett Node.JS Linux-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Konfigurera ett Node.JS Windows-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Konfigurera ett python Linux-arbetsflöde som använder en publicerings profil.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure och GitHub-integrering](/azure/developer/github/)