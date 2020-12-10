---
title: Hanterade identiteter
description: Lär dig hur hanterade identiteter fungerar i Azure App Service och Azure Functions, hur du konfigurerar en hanterad identitet och hur du skapar en token för en server dels resurs.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8e1f7bb2faaf9d2c706e63ae73b9e265eb95d09b
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007219"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Använda hanterade identiteter för App Service och Azure Functions

Det här avsnittet visar hur du skapar en hanterad identitet för App Service och Azure Functions program och hur du använder den för att få åtkomst till andra resurser. 

> [!Important] 
> Hanterade identiteter för App Service och Azure Functions fungerar inte som förväntat om din app migreras mellan prenumerationer/klienter. Appen behöver skaffa en ny identitet, vilket görs genom att inaktivera och aktivera funktionen på nytt. Se [ta bort en identitet](#remove) nedan. Underordnade resurser måste också ha åtkomst principer uppdaterade för att använda den nya identiteten.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet

Att skapa en app med en tilldelad identitet kräver att ytterligare en egenskap anges för programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen.

1. Skapa en app i portalen på vanligt sätt. Gå till den i portalen.

2. Om du använder en Function-app navigerar du till **plattforms funktioner**. För andra typer av appar rullar du ned till **inställnings** gruppen i det vänstra navigerings fältet.

3. Välj **identitet**.

4. Växla **status** till **på på** fliken **systemtilldelad** . Klicka på **Spara**.

    ![Skärm bild som visar var du växlar status till på och väljer sedan Spara.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Om du vill hitta den hanterade identiteten för din webbapp eller fack-app i Azure Portal, under **företags program**, tittar du i avsnittet **användar inställningar** . Normalt liknar plats namnet `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI måste du använda `az webapp identity assign` kommandot mot ett befintligt program. Det finns tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "testa", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol. 

Följande steg vägleder dig genom att skapa en webbapp och tilldela den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera programmet:

    ```azurecli-interactive
    az login
    ```

2. Skapa ett webb program med hjälp av CLI. Fler exempel på hur du använder CLI med App Service finns i [App Service CLI-exempel](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Kör `identity assign` kommandot för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg vägleder dig genom att skapa en app och tilldela den en identitet med hjälp av Azure PowerShell. Instruktionerna för att skapa en webbapp och en Function-app skiljer sig åt.

#### <a name="using-azure-powershell-for-a-web-app"></a>Använda Azure PowerShell för en webbapp

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Login-AzAccount` för att skapa en anslutning till Azure.

2. Skapa ett webb program med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med App Service finns i [App Service PowerShell-exempel](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Kör `Set-AzWebApp -AssignIdentity` kommandot för att skapa identiteten för det här programmet:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Använda Azure PowerShell för en Function-app

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Login-AzAccount` för att skapa en anslutning till Azure.

2. Skapa en Function-app med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med Azure Functions finns i [AZ. Functions Reference](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Du kan också uppdatera en befintlig Function-app med `Update-AzFunctionApp` i stället.

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och funktioner finns i [Automatisera resurs distribution i App Service](../app-service/deploy-complex-application-predictably.md) och [Automatisera resurs distribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

En resurs av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande egenskap i resurs definitionen:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Ett program kan ha både tilldelade och användarspecifika identiteter på samma tidpunkt. I det här fallet `type` skulle egenskapen vara `SystemAssigned,UserAssigned`

Genom att lägga till den systemtilldelade typen kan Azure Skapa och hantera identiteten för ditt program.

En webbapp kan till exempel se ut så här:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

När platsen har skapats har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Egenskapen tenantId identifierar vilken Azure AD-klient identiteten tillhör. PrincipalId är en unik identifierare för programmets nya identitet. I Azure AD har tjänstens huvud namn samma namn som du gav App Service-eller Azure Functions-instansen.

Om du behöver referera till dessa egenskaper i ett senare skede i mallen kan du göra det via [ `reference()` funktionen mall](../azure-resource-manager/templates/template-functions-resource.md#reference) med `'Full'` flaggan, som i det här exemplet:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Lägg till en användardefinierad identitet

Om du skapar en app med en användardefinierad identitet måste du skapa identiteten och sedan lägga till dess resurs-ID i appens konfiguration.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Först måste du skapa en användardefinierad identitets resurs.

1. Skapa en användare som tilldelats en hanterad identitets resurs enligt [dessa anvisningar](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Skapa en app i portalen på vanligt sätt. Gå till den i portalen.

3. Om du använder en Function-app navigerar du till **plattforms funktioner**. För andra typer av appar rullar du ned till **inställnings** gruppen i det vänstra navigerings fältet.

4. Välj **identitet**.

5. Klicka på **Lägg till** i fliken **tilldelade användare** .

6. Sök efter den identitet som du skapade tidigare och markera den. Klicka på **Lägg till**.

    ![Hanterad identitet i App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg vägleder dig genom att skapa en app och tilldela den en identitet med hjälp av Azure PowerShell.

> [!NOTE]
> Den aktuella versionen av Azure PowerShell-cmdletarna för Azure App Service stöder inte användarspecifika identiteter. Anvisningarna nedan är för Azure Functions.

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Login-AzAccount` för att skapa en anslutning till Azure.

2. Skapa en Function-app med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med Azure Functions finns i [referensen AZ. Functions](/powershell/module/az.functions/#functions). Skriptet nedan använder också det `New-AzUserAssignedIdentity` som måste installeras separat som per [skapa, lista eller ta bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Du kan också uppdatera en befintlig Function-app med `Update-AzFunctionApp` i stället.

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och funktioner finns i [Automatisera resurs distribution i App Service](../app-service/deploy-complex-application-predictably.md) och [Automatisera resurs distribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

En resurs av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande block i resurs definitionen och ersätta `<RESOURCEID>` med resurs-ID: t för den önskade identiteten:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Ett program kan ha både tilldelade och användarspecifika identiteter på samma tidpunkt. I det här fallet `type` skulle egenskapen vara `SystemAssigned,UserAssigned`

Genom att lägga till en användardefinierad typ anger Azure att använda den användar tilldelnings identitet som angetts för ditt program.

En webbapp kan till exempel se ut så här:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

När platsen har skapats har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId är en unik identifierare för den identitet som används för Azure AD-administration. ClientId är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas vid körnings anrop.

## <a name="obtain-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin hanterade identitet för att hämta token för att få åtkomst till andra resurser som skyddas av Azure AD, till exempel Azure Key Vault. Dessa tokens representerar programmet som använder resursen och inte någon specifik användare av programmet. 

Du kan behöva konfigurera mål resursen för att tillåta åtkomst från ditt program. Om du till exempel begär en token för att få åtkomst till Key Vault måste du kontrol lera att du har lagt till en åtkomst princip som innehåller programmets identitet. Annars avvisas anrop till Key Vault, även om de inkluderar token. Mer information om vilka resurser som stöder Azure Active Directory tokens finns i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Server dels tjänsterna för hanterade identiteter upprätthåller en cache per resurs-URI i cirka 24 timmar. Om du uppdaterar åtkomst principen för en viss mål resurs och omedelbart hämtar en token för resursen kan du fortsätta att hämta en cachelagrad token med inaktuella behörigheter tills token upphör att gälla. Det finns för närvarande inget sätt att framtvinga en uppdatering av token.

Det finns ett enkelt REST-protokoll för att hämta en token i App Service och Azure Functions. Detta kan användas för alla program och språk. För .NET och Java tillhandahåller Azure SDK en abstraktion över det här protokollet och underlättar en lokal utvecklings upplevelse.

### <a name="using-the-rest-protocol"></a>Använda REST-protokollet

> [!NOTE]
> En äldre version av det här protokollet, med hjälp av API-versionen "2017-09-01", använde `secret` rubriken i stället för `X-IDENTITY-HEADER` och accepterar bara `clientid` egenskapen för användar tilldelning. Den returnerade också `expires_on` i ett tidsstämpel-format. MSI_ENDPOINT kan användas som ett alias för IDENTITY_ENDPOINT och MSI_SECRET kan användas som ett alias för IDENTITY_HEADER. Den här versionen av protokollet krävs för närvarande för värd planer för Linux-konsumtion.

En app med en hanterad identitet har två miljövariabler definierade:

- IDENTITY_ENDPOINT-URL: en till den lokala token-tjänsten.
- IDENTITY_HEADER – ett-sidhuvud som används för att minska risken för förfalskning av SSRF-attacker (Server sidans begäran). Värdet roteras av plattformen.

**IDENTITY_ENDPOINT** är en lokal URL som din app kan begära token från. Om du vill hämta en token för en resurs gör du en HTTP GET-begäran till den här slut punkten, inklusive följande parametrar:

> | Parameternamn    | I     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Söka i data  | Azure AD-resurs-URI för resursen som en token ska hämtas för. Detta kan vara en av de [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) eller andra resurs-URI: er.    |
> | api-version       | Söka i data  | Den version av token API som ska användas. Använd "2019-08-01" eller senare (om du inte använder Linux-förbrukning, som för närvarande bara erbjuder "2017-09-01", se kommentaren ovan).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Huvud | Värdet för IDENTITY_HEADER-miljövariabeln. Den här rubriken används för att minska risken för förfalskning av SSRF-attacker (Server sidans begäran).                                                                                                                                                                                                    |
> | client_id         | Söka i data  | Valfritt Klient-ID för den användar tilldelnings identitet som ska användas. Kan inte användas på en begäran som innehåller `principal_id` , `mi_res_id` eller `object_id` . Om alla ID-parametrar ( `client_id` , `principal_id` ,, `object_id` och `mi_res_id` ) utelämnas används den systemtilldelade identiteten.                                             |
> | principal_id      | Söka i data  | Valfritt Ägar-ID för den användar tilldelnings identitet som ska användas. `object_id` är ett alias som kan användas i stället. Kan inte användas på en begäran som innehåller client_id, mi_res_id eller object_id. Om alla ID-parametrar ( `client_id` , `principal_id` ,, `object_id` och `mi_res_id` ) utelämnas används den systemtilldelade identiteten. |
> | mi_res_id         | Söka i data  | Valfritt Azure-resurs-ID för den användar tilldelnings identitet som ska användas. Kan inte användas på en begäran som innehåller `principal_id` , `client_id` eller `object_id` . Om alla ID-parametrar ( `client_id` , `principal_id` ,, `object_id` och `mi_res_id` ) utelämnas används den systemtilldelade identiteten.                                      |

> [!IMPORTANT]
> Om du försöker hämta tokens för användarspecifika identiteter måste du inkludera en av de valfria egenskaperna. Annars försöker token-tjänsten hämta en token för en tilldelad identitet, som kan vara eller inte finns.

Ett lyckat 200 OK-svar innehåller en JSON-text med följande egenskaper:

> | Egenskapsnamn | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Den begärda åtkomsttoken. Den anropande webb tjänsten kan använda denna token för att autentisera till den mottagande webb tjänsten.                                                                                                                               |
> | client_id     | Klient-ID för den identitet som användes.                                                                                                                                                                                                       |
> | expires_on    | TimeSpan när åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `exp` anspråk).                                                                                |
> | not_before    | TimeSpan när åtkomsttoken börjar gälla och kan godkännas. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `nbf` anspråk).                                                      |
> | resource      | Resursen som åtkomsttoken begärdes för, som matchar `resource` frågesträngparametern för begäran.                                                                                                                               |
> | token_type    | Anger värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: Bearer token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Svaret är detsamma som [svaret på Azure AD service-till-service-](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)åtkomsttoken.

### <a name="rest-protocol-examples"></a>Exempel på REST-protokoll

En exempel förfrågan kan se ut så här:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Och ett exempel svar kan se ut ungefär så här:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Kodexempel

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> För .NET-språk kan du också använda [Microsoft. Azure. Services. AppAuthentication](#asal) i stället för att göra den här begäran själv.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Använda Microsoft. Azure. Services. AppAuthentication-biblioteket för .NET

För .NET-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via paketet Microsoft. Azure. Services. AppAuthentication. Med det här biblioteket kan du också testa din kod lokalt på din utvecklings dator med ditt användar konto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory integrerad autentisering. När den finns i molnet används en tilldelad identitet, men du kan anpassa det här beteendet med en miljö variabel för anslutnings sträng som refererar till klient-ID: t för en användardefinierad identitet. Mer information om utvecklings alternativ i det här biblioteket finns i [referens för Microsoft. Azure. Services. AppAuthentication]. Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och andra nödvändiga NuGet-paket till ditt program. I exemplet nedan används även [Microsoft. Azure. nyckel valv](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Lägg till följande kod i programmet och ändra till målet för rätt resurs. I det här exemplet visas två sätt att arbeta med Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Om du vill använda en användardefinierad hanterad identitet kan du ange `AzureServicesAuthConnectionString` program inställningen till `RunAs=App;AppId=<clientId-guid>` . Ersätt `<clientId-guid>` med klient-ID: t för den identitet som du vill använda. Du kan definiera flera sådana anslutnings strängar genom att använda anpassade program inställningar och skicka värdena till AzureServiceTokenProvider-konstruktorn.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Mer information om hur du konfigurerar AzureServiceTokenProvider och vilka åtgärder den visar finns i [referensen för Microsoft. Azure. Services. AppAuthentication] och [app service-och nyckel VALVET med MSI .net-exemplet](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Använda Azure SDK för Java

För Java-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java). Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till en referens till [Azure SDK-biblioteket](https://mvnrepository.com/artifact/com.microsoft.azure/azure). För Maven-projekt kan du lägga till det här kodfragmentet i `dependencies` avsnittet i projektets Pom-fil:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Använd `AppServiceMSICredentials` objektet för autentisering. Det här exemplet visar hur den här mekanismen kan användas för att arbeta med Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Ta bort en identitet

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med hjälp av portalen, PowerShell eller CLI på samma sätt som den skapades. Användare-tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du identitets typen till "ingen".

Om du tar bort en tilldelad identitet på det här sättet tas även den bort från Azure AD. Systemtilldelade identiteter tas också automatiskt bort från Azure AD när app-resursen tas bort.

Ta bort alla identiteter i en [arm-mall](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Så här tar du bort alla identiteter i Azure PowerShell (endast Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Det finns också en program inställning som kan ställas in WEBSITE_DISABLE_MSI, som bara inaktiverar den lokala token-tjänsten. Men den lämnar identiteten på plats, och om du fortsätter med verktyg visas den hanterade identiteten som "på" eller "aktive rad". Därför rekommenderas inte användning av den här inställningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Åtkomst SQL Database på ett säkert sätt med en hanterad identitet](app-service-web-tutorial-connect-msi.md)

[Referens för Microsoft. Azure. Services. AppAuthentication]: ../key-vault/general/service-to-service-authentication.md
