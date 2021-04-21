---
title: Hanterade identiteter
description: Lär dig hur hanterade identiteter fungerar i Azure App Service och Azure Functions, hur du konfigurerar en hanterad identitet och genererar en token för en backend-resurs.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: badc6b6f1b45938e950ffadeefe30d81ed383440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762451"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Använda hanterade identiteter för App Service och Azure Functions

Det här avsnittet visar hur du skapar en hanterad identitet för App Service och Azure Functions program och hur du använder den för att få åtkomst till andra resurser. 

> [!Important] 
> Hanterade identiteter för App Service och Azure Functions fungerar inte som förväntat om din app migreras över prenumerationer/klienter. Appen måste skaffa en ny identitet, vilket görs genom att inaktivera och återaktivera funktionen. Se [Ta bort en identitet](#remove) nedan. Underordnade resurser måste också ha åtkomstprinciper uppdaterade för att använda den nya identiteten.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Lägga till en system tilldelad identitet

Om du vill skapa en app med en system tilldelad identitet måste ytterligare en egenskap anges för programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen.

1. Skapa en app i portalen som vanligt. Gå till den i portalen.

2. Om du använder en funktionsapp går du till **Plattformsfunktioner**. För andra apptyper rullar du ned till **gruppen Inställningar** i det vänstra navigeringsfönstret.

3. Välj **Identitet.**

4. På fliken **System tilldelad** växlar du **Status** till **På.** Klicka på **Spara**.

    ![Skärmbild som visar var du växlar Status till På och väljer sedan Spara.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Du hittar den hanterade identiteten för webbappen eller fackappen **i** Azure Portal under Företagsprogram i avsnittet **Användarinställningar.** Facknamnet liknar vanligtvis `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med hjälp av Azure CLI måste du använda `az webapp identity assign` kommandot mot ett befintligt program. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol. 

Följande steg visar hur du skapar en webbapp och tilldelar den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerat med den Azure-prenumeration som du vill distribuera programmet under:

    ```azurecli-interactive
    az login
    ```

2. Skapa en webbapp med hjälp av CLI. Fler exempel på hur du använder CLI med App Service finns i App Service [CLI-exempel:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Kör kommandot `identity assign` för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg visar hur du skapar en app och tilldelar den en identitet med hjälp av Azure PowerShell. Anvisningarna för att skapa en webbapp och en funktionsapp skiljer sig åt.

#### <a name="using-azure-powershell-for-a-web-app"></a>Använda Azure PowerShell för en webbapp

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [guiden Azure PowerShell](/powershell/azure/)och kör sedan för att skapa en anslutning `Login-AzAccount` till Azure.

2. Skapa en webbapp med Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med App Service finns i [App Service PowerShell-exempel:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Kör kommandot `Set-AzWebApp -AssignIdentity` för att skapa identiteten för det här programmet:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Använda Azure PowerShell för en funktionsapp

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [guiden Azure PowerShell](/powershell/azure/)och kör sedan för att skapa en anslutning `Login-AzAccount` till Azure.

2. Skapa en funktionsapp med Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med Azure Functions finns i [Az.Functions-referensen](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Du kan också uppdatera en befintlig funktionsapp med hjälp av `Update-AzFunctionApp` i stället.

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och Functions finns i Automatisera resursdistribution [i App Service](../app-service/deploy-complex-application-predictably.md) och Automatisera [resursdistribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Du kan skapa en `Microsoft.Web/sites` resurs av typen med en identitet genom att inkludera följande egenskap i resursdefinitionen:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Ett program kan ha både system-tilldelade och användar-tilldelade identiteter på samma gång. I det här fallet `type` skulle egenskapen vara `SystemAssigned,UserAssigned`

Om du lägger till den system tilldelade typen anger du att Azure ska skapa och hantera identiteten för ditt program.

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

Egenskapen tenantId identifierar vilken Azure AD-klientorganisation identiteten tillhör. principalId är en unik identifierare för programmets nya identitet. I Azure AD har tjänstens huvudnamn samma namn som du gav till din App Service eller Azure Functions instans.

Om du behöver referera till dessa egenskaper i ett senare skede [ `reference()` ](../azure-resource-manager/templates/template-functions-resource.md#reference) i mallen kan du göra det via mallfunktionen med `'Full'` flaggan , som i det här exemplet:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Lägga till en användar tilldelad identitet

När du skapar en app med en användar tilldelad identitet måste du skapa identiteten och sedan lägga till dess resursidentifierare i appkonfigurationen.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Först måste du skapa en användar tilldelad identitetsresurs.

1. Skapa en användar tilldelad hanterad identitetsresurs enligt [dessa instruktioner.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

2. Skapa en app i portalen som vanligt. Gå till den i portalen.

3. Om du använder en funktionsapp går du till **Plattformsfunktioner**. För andra apptyper rullar du ned till **gruppen Inställningar** i det vänstra navigeringsfönstret.

4. Välj **Identitet.**

5. Klicka på **Lägg till på** fliken Användar **tilldelad.**

6. Sök efter den identitet som du skapade tidigare och välj den. Klicka på **Lägg till**.

    ![Hanterad identitet i App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg visar hur du skapar en app och tilldelar den en identitet med hjälp av Azure PowerShell.

> [!NOTE]
> Den aktuella versionen av Azure PowerShell för Azure App Service stöder inte användar tilldelade identiteter. Anvisningarna nedan är till för Azure Functions.

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [guiden Azure PowerShell](/powershell/azure/)och kör sedan för att skapa en anslutning `Login-AzAccount` till Azure.

2. Skapa en funktionsapp med Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med Azure Functions finns i [Az.Functions-referensen](/powershell/module/az.functions/#functions). Skriptet nedan använder också som måste installeras separat enligt Skapa, lista eller ta bort en användar tilldelad hanterad identitet med hjälp `New-AzUserAssignedIdentity` [av Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

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

Du kan också uppdatera en befintlig funktionsapp med hjälp av `Update-AzFunctionApp` i stället.

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och Functions finns i Automatisera resursdistribution [i App Service](../app-service/deploy-complex-application-predictably.md) och Automatisera [resursdistribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Du kan skapa valfri resurs av typen med en identitet genom att inkludera följande block i resursdefinitionen och ersätta med `Microsoft.Web/sites` `<RESOURCEID>` resurs-ID:t för den önskade identiteten:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Ett program kan ha både system- och användar tilldelade identiteter samtidigt. I det här fallet `type` skulle egenskapen vara `SystemAssigned,UserAssigned`

Om du lägger till den användar tilldelade typen anger azure att den användar tilldelade identiteten som angetts för ditt program ska användas.

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

principalId är en unik identifierare för den identitet som används för Azure AD-administration. clientId är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas under körningsanrop.

## <a name="obtain-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin hanterade identitet för att hämta token för att få åtkomst till andra resurser som skyddas av Azure AD, till exempel Azure Key Vault. Dessa token representerar programmet som har åtkomst till resursen och inte någon specifik användare av programmet. 

Du kan behöva konfigurera målresursen för att tillåta åtkomst från ditt program. Om du till exempel begär en token för åtkomst Key Vault måste du se till att du har lagt till en åtkomstprincip som innehåller programmets identitet. Annars kommer dina anrop Key Vault att avvisas, även om de innehåller token. Mer information om vilka resurser som stöder Azure Active Directory-token finns i [Azure-tjänster som stöder Azure AD-autentisering.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Backend-tjänsterna för hanterade identiteter har ett cacheminne per resurs-URI i cirka 24 timmar. Om du uppdaterar åtkomstprincipen för en viss målresurs och omedelbart hämtar en token för resursen kan du fortsätta att få en cachelagrad token med inaktuella behörigheter tills denna token upphör att gälla. Det finns för närvarande inget sätt att tvinga fram en tokenuppdatering.

Det finns ett enkelt REST-protokoll för att hämta en token i App Service och Azure Functions. Detta kan användas för alla program och språk. För .NET och Java tillhandahåller Azure SDK en abstraktion över det här protokollet och underlättar en lokal utvecklingsupplevelse.

### <a name="using-the-rest-protocol"></a>Använda REST-protokollet

> [!NOTE]
> En äldre version av det här protokollet, med API-versionen "2017-09-01", använde -huvudet i stället för och godkände bara egenskapen för `secret` `X-IDENTITY-HEADER` `clientid` användar-tilldelad. Den returnerade också `expires_on` i ett tidsstämpelformat. MSI_ENDPOINT kan användas som ett alias för IDENTITY_ENDPOINT och MSI_SECRET kan användas som ett alias för IDENTITY_HEADER. Den här versionen av protokollet krävs för närvarande Linux-förbrukning värdplaner.

En app med en hanterad identitet har två definierade miljövariabler:

- IDENTITY_ENDPOINT – URL:en till den lokala tokentjänsten.
- IDENTITY_HEADER – ett huvud som används för att minimera SSRF-attacker (förfalskning av begäran på serversidan). Värdet roteras av plattformen.

Den **IDENTITY_ENDPOINT** är en lokal URL som din app kan begära token från. Om du vill hämta en token för en resurs gör du en HTTP GET-begäran till den här slutpunkten, inklusive följande parametrar:

> | Parameternamn    | I     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Fråga  | Azure AD-resurs-URI för resursen som en token ska hämtas för. Detta kan vara en av de [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) eller någon annan resurs-URI.    |
> | api-version       | Fråga  | Den version av token-API:et som ska användas. Använd "2019-08-01" eller senare (om du inte använder Linux-förbrukning, som för närvarande endast erbjuder "2017-09-01" – se kommentaren ovan).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Huvud | Värdet för IDENTITY_HEADER miljövariabeln. Det här huvudet används för att minska risken för SSRF-attacker (förfalskning av begäran på serversidan).                                                                                                                                                                                                    |
> | client_id         | Fråga  | (Valfritt) Klient-ID för den användar tilldelade identitet som ska användas. Kan inte användas på en begäran som `principal_id` innehåller `mi_res_id` , eller `object_id` . Om alla `client_id` `principal_id` ID-parametrar ( , , och ) `object_id` `mi_res_id` utelämnas används den system tilldelade identiteten.                                             |
> | principal_id      | Fråga  | (Valfritt) Huvud-ID för den användar tilldelade identitet som ska användas. `object_id` är ett alias som kan användas i stället. Kan inte användas på en begäran som innehåller client_id, mi_res_id eller object_id. Om alla `client_id` `principal_id` ID-parametrar ( , , och ) `object_id` `mi_res_id` utelämnas används den system tilldelade identiteten. |
> | mi_res_id         | Fråga  | (Valfritt) Azure-resurs-ID för den användartilldelningsidentitet som ska användas. Kan inte användas på en begäran som `principal_id` innehåller `client_id` , eller `object_id` . Om alla `client_id` `principal_id` ID-parametrar ( , , och ) `object_id` `mi_res_id` utelämnas används den system tilldelade identiteten.                                      |

> [!IMPORTANT]
> Om du försöker hämta token för användar tilldelade identiteter måste du inkludera en av de valfria egenskaperna. Annars försöker tokentjänsten hämta en token för en system tilldelad identitet, som kan finnas eller inte.

Ett lyckat 200 OK-svar innehåller en JSON-brödtext med följande egenskaper:

> | Egenskapsnamn | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Den begärda åtkomsttoken. Den anropande webbtjänsten kan använda denna token för att autentisera mot den mottagande webbtjänsten.                                                                                                                               |
> | client_id     | Klient-ID:t för den identitet som användes.                                                                                                                                                                                                       |
> | expires_on    | Tidsspann när åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `exp` anspråk).                                                                                |
> | not_before    | Det tidsspann då åtkomsttoken har verkställs och kan godkännas. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `nbf` anspråk).                                                      |
> | resource      | Resursen som åtkomsttoken begärdes för, som matchar `resource` frågesträngparametern för begäran.                                                                                                                               |
> | token_type    | Anger värdet för tokentyp. Den enda typ som Azure AD stöder är Bearer. Mer information om bearer-token finns [i OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Det här svaret är samma som svaret [för azure AD-begäran om tjänst-till-tjänst-åtkomsttoken.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

### <a name="rest-protocol-examples"></a>REST-protokollexempel

En exempelbegäran kan se ut så här:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Och ett exempelsvar kan se ut så här:

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
> För .NET-språk kan du också använda [Microsoft.Azure.Services.AppAuthentication](#asal) i stället för att skapa den här begäran själv.

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

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Använda Microsoft.Azure.Services.AppAuthentication-biblioteket för .NET

För .NET-program och -funktioner är det enklaste sättet att arbeta med en hanterad identitet via paketet Microsoft.Azure.Services.AppAuthentication. Med det här biblioteket kan du också testa koden lokalt på utvecklingsdatorn med ditt användarkonto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory-integrerad autentisering. När den finns i molnet används som standard en system tilldelad identitet, men du kan anpassa det här beteendet med hjälp av en miljövariabel för anslutningssträngar som refererar till klient-ID:t för en användar tilldelad identitet. Mer information om utvecklingsalternativ med det här biblioteket finns i referensen [Microsoft.Azure.Services.AppAuthentication.] Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och andra nödvändiga NuGet-paket i ditt program. I exemplet nedan används även [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Lägg till följande kod i programmet och ändra för att rikta in dig på rätt resurs. Det här exemplet visar två sätt att arbeta med Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Om du vill använda en användar tilldelad hanterad identitet kan du ange `AzureServicesAuthConnectionString` programinställningen till `RunAs=App;AppId=<clientId-guid>` . Ersätt `<clientId-guid>` med klient-ID:t för den identitet som du vill använda. Du kan definiera flera sådana anslutningssträngar med hjälp av anpassade programinställningar och skicka deras värden till konstruktorn AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Mer information om hur du konfigurerar AzureServiceTokenProvider och vilka åtgärder som exponeras finns i [referensen Microsoft.Azure.Services.AppAuthentication] och App Service och [KeyVault med MSI .NET-exempel](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Använda Azure SDK för Java

För Java-program och -funktioner är det enklaste sättet att arbeta med en hanterad identitet via [Azure SDK för Java.](https://github.com/Azure/azure-sdk-for-java) Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till en referens till [Azure SDK-biblioteket](https://mvnrepository.com/artifact/com.microsoft.azure/azure). För Maven-projekt kan du lägga till det här `dependencies` kodfragmentet i avsnittet i projektets POM-fil:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Använd `AppServiceMSICredentials` -objektet för autentisering. Det här exemplet visar hur den här mekanismen kan användas för att arbeta med Azure Key Vault:

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

Du kan ta bort en system tilldelad identitet genom att inaktivera funktionen med hjälp av portalen, PowerShell eller CLI på samma sätt som den skapades. Användar tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du identitetstypen till "Ingen".

Om du tar bort en system tilldelad identitet på det här sättet tas den även bort från Azure AD. Systemtilldelningar tas också bort automatiskt från Azure AD när appresursen tas bort.

Så här tar du bort alla identiteter i en [ARM-mall:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Ta bort alla identiteter i Azure PowerShell (Azure Functions endast):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Det finns också en programinställning som kan anges, WEBSITE_DISABLE_MSI, som bara inaktiverar den lokala tokentjänsten. Den lämnar dock identiteten på plats och verktyg visar fortfarande den hanterade identiteten som "på" eller "aktiverad". Därför rekommenderas inte att du använder den här inställningen.

## <a name="next-steps"></a>Nästa steg

- [Åtkomst SQL Database säkert med hjälp av en hanterad identitet](app-service-web-tutorial-connect-msi.md)
- [Åtkomst Azure Storage säkert med hjälp av en hanterad identitet](scenario-secure-app-access-storage.md)
- [Anropa Microsoft Graph säkert med hjälp av en hanterad identitet](scenario-secure-app-access-microsoft-graph-as-app.md)

[Referens för Microsoft.Azure.Services.AppAuthentication]: /dotnet/api/overview/azure/service-to-service-authentication