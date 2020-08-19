---
title: Använd GitHub-åtgärder för att göra kod uppdateringar i Azure Functions
description: Lär dig hur du använder GitHub-åtgärder för att definiera ett arbets flöde för att skapa och Distribuera Azure Functions-projekt i GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 02f5399e89900a438fb94f973c497a54dc05cfee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210173"
---
# <a name="continuous-delivery-by-using-github-action"></a>Kontinuerlig leverans med hjälp av GitHub-åtgärd

Med [GitHub-åtgärder](https://github.com/features/actions) kan du definiera ett arbets flöde för att automatiskt skapa och distribuera din funktions kod till Function-appen i Azure. 

I GitHub-åtgärder är ett [arbets flöde](https://help.github.com/articles/about-github-actions#workflow) en automatiserad process som du definierar i din GitHub-lagringsplats. Den här processen visar GitHub hur du skapar och distribuerar dina Functions-AppData på GitHub. 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet. 

För ett Azure Functions-arbetsflöde har filen tre delar: 

| Avsnitt | Aktiviteter |
| ------- | ----- |
| **Autentisering** | <ol><li>Definiera ett huvud namn för tjänsten.</li><li>Ladda ned publicerings profil.</li><li>Skapa en GitHub-hemlighet.</li></ol>|
| **Skapa** | <ol><li>Konfigurera miljön.</li><li>Bygg in Function-appen.</li></ol> |
| **Distribuera** | <ol><li>Distribuera Function-appen.</li></ol>|

> [!NOTE]
> Du behöver inte skapa ett huvud namn för tjänsten om du bestämmer dig för att använda publicerings profilen för autentisering.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

I det här exemplet ersätter du plats hållarna i resursen med ditt prenumerations-ID, resurs grupp och funktionens program namn. Utdata är de autentiseringsuppgifter för roll tilldelning som ger åtkomst till din Function-app. Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minimal åtkomst. Detta är anledningen till att omfånget i föregående exempel är begränsat till den särskilda Function-appen och inte hela resurs gruppen.

## <a name="download-the-publishing-profile"></a>Ladda ned publicerings profilen

Så här hämtar du publicerings profilen för din Function-app:

1. Välj sidan **Översikt** för Function-appen och välj sedan **Hämta publicerings profil**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Ladda ned publicerings profil":::

1. Spara och kopiera innehållet i filen med publicerings inställningar.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I [GitHub](https://github.com), bläddra till din lagrings plats, Välj **Inställningar**  >  **hemligheter**  >  **Lägg till en ny hemlighet**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Lägg till hemlighet":::

1. Lägg till en ny hemlighet.

   * Om du använder tjänstens huvud namn som du skapade med Azure CLI använder du `AZURE_CREDENTIALS` som **namn**. Klistra sedan in det kopierade JSON-objektets utdata för **värde**och välj **Lägg till hemlighet**.
   * Om du använder en publicerings profil använder du `SCM_CREDENTIALS` som **namn**. Använd sedan publicerings profilens fil innehåll för **värde**och välj **Lägg till hemlighet**.

GitHub kan nu autentisera till din Function-app i Azure.

## <a name="set-up-the-environment"></a>Konfigurera miljön 

Konfigurationen av miljön görs med hjälp av en språkspecifik publicerings konfigurations åtgärd.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas den del av arbets flödet som använder `actions/setup-node` åtgärden för att konfigurera miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas den del av arbets flödet som använder `actions/setup-python` åtgärden för att konfigurera miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas den del av arbets flödet som använder `actions/setup-dotnet` åtgärden för att konfigurera miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas den del av arbets flödet som använder  `actions/setup-java` åtgärden för att konfigurera miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Bygg in Function-appen

Detta beror på språket och för språk som stöds av Azure Functions, bör det här avsnittet vara standard stegen för version av varje språk.

I följande exempel visas den del av arbets flödet som bygger på Function-appen, vilket är språkspecifikt:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Distribuera funktionsappen

Om du vill distribuera din kod till en Function-app måste du använda `Azure/functions-action` åtgärden. Den här åtgärden har två parametrar:

|Parameter |Förklaring  |
|---------|---------|
|**_App-Name_** | Erforderlig Namnet på din Function-app. |
|_**plats namn**_ | Valfritt Namnet på den [distributions plats](functions-deployment-slots.md) som du vill distribuera till. Facket måste redan vara definierat i din Function-app. |


I följande exempel används version 1 av `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Nästa steg

Om du vill visa en fullständig Workflow. yaml-fil, se en av filerna i [arbets flödes exemplen för Azure GitHub-åtgärder lagrings platsen](https://aka.ms/functions-actions-samples) som har `functionapp` i namnet. Du kan använda dessa exempel som start punkt för arbets flödet.

> [!div class="nextstepaction"]
> [Lär dig mer om GitHub Actions](https://help.github.com/en/articles/about-github-actions)
