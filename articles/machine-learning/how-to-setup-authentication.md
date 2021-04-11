---
title: Konfigurera autentisering
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbets flöden i Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: ee767cbeafcce604ecb4a79ce0fdf21c70bbb47c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219645"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurera autentisering för Azure Machine Learning-resurser och -arbetsflöden


Lär dig hur du konfigurerar autentisering till din Azure Machine Learning-arbetsyta. Autentisering till din Azure Machine Learning-arbetsyta baseras på __Azure Active Directory__ (Azure AD) för de flesta saker. I allmänhet finns det tre arbets flöden för autentisering som du kan använda när du ansluter till arbets ytan:

* __Interaktiv__: du använder ditt konto i Azure Active Directory för att antingen autentisera dig direkt eller hämta en token som används för autentisering. Interaktiv autentisering används vid _experimentering och iterativ utveckling_. Med interaktiv autentisering kan du kontrol lera åtkomsten till resurser (till exempel en webb tjänst) per användare.

* __Tjänstens huvud namn__: du skapar ett tjänst huvud namns konto i Azure Active Directory och använder det för att autentisera eller hämta en token. Ett huvud namn för tjänsten används när du behöver en _automatiserad process för att autentisera_ till tjänsten utan att det krävs några åtgärder från användaren. Till exempel ett kontinuerligt integrerings-och distributions skript som tågen och testar en modell varje gång inlärnings koden ändras.

* __Hanterad identitet__: när du använder Azure Machine Learning SDK _på en virtuell Azure-dator_ kan du använda en hanterad identitet för Azure. Det här arbets flödet gör att den virtuella datorn kan ansluta till arbets ytan med hjälp av den hanterade identiteten, utan att lagra autentiseringsuppgifter i python-kod eller fråga användaren om att autentisera. Azure Machine Learning beräknings kluster kan också konfigureras för att använda en hanterad identitet för att komma åt arbets ytan när du _tränar modeller_.

> [!IMPORTANT]
> Oavsett vilket arbets flöde för autentisering som används, används Azures rollbaserade åtkomst kontroll (Azure RBAC) för att begränsa den åtkomst nivå som tillåts för resurserna. En administratörs-eller Automation-process kan till exempel ha åtkomst för att skapa en beräknings instans, men inte använda den, medan en data expert kan använda den, men inte ta bort eller skapa den. Mer information finns i [Hantera åtkomst till Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-azure-machine-learning-architecture.md#compute-instance) med SDK redan installerad.

## <a name="azure-active-directory"></a>Azure Active Directory

Alla autentiserings-arbetsflöden för arbets ytan förlitar sig på Azure Active Directory. Om du vill att användarna ska autentiseras med hjälp av enskilda konton måste de ha konton i din Azure AD. Om du vill använda tjänstens huvud namn måste de finnas i din Azure AD. Hanterade identiteter är också en funktion i Azure AD. 

Mer information om Azure AD finns i [Vad är Azure Active Directory autentisering](..//active-directory/authentication/overview-authentication.md).

När du har skapat Azure AD-kontona läser du [Hantera åtkomst till Azure Machine Learning arbets ytan](how-to-assign-roles.md) för information om hur du beviljar dem åtkomst till arbets ytan och andra åtgärder i Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Konfigurera ett huvudnamn för tjänsten

Om du vill använda ett huvud namn för tjänsten (SP) måste du först skapa SP och bevilja åtkomst till din arbets yta. Som tidigare nämnts används Azure rollbaserad åtkomst kontroll (Azure RBAC) för att kontrol lera åtkomsten, så du måste också bestämma vilken åtkomst som ska ge SP.

> [!IMPORTANT]
> När du använder ett huvud namn för tjänsten ger du det den __lägsta åtkomst som krävs för den aktivitet__ som används för. Du skulle till exempel inte bevilja tjänstens huvud namn ägare eller deltagar åtkomst om all den används för läser in åtkomsttoken för en webb distribution.
>
> Skälet till att bevilja den lägsta åtkomsten är att tjänstens huvud namn använder ett lösen ord för att autentisera, och lösen ordet kan lagras som en del av ett Automation-skript. Om lösen ordet läcker, minimeras den skadliga användningen av SP för en speciell uppgift.

Det enklaste sättet att skapa en SP-och bevilja åtkomst till din arbets yta är genom att använda [Azure CLI](/cli/azure/install-azure-cli). Använd följande steg för att skapa ett huvud namn för tjänsten och ge det åtkomst till din arbets yta:

> [!NOTE]
> Du måste vara administratör för prenumerationen för att utföra alla de här stegen.

1. Autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

    Om du har flera Azure-prenumerationer kan du använda `az account set -s <subscription name or ID>` kommandot för att ställa in prenumerationen. Mer information finns i [använda flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).

    För andra metoder för autentisering, se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

1. Skapa tjänstens huvud namn. I följande exempel skapas en SP-namngiven **ml-autentisering** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Utdata är en JSON som liknar följande. Anteckna `clientId` `clientSecret` fälten, och `tenantId` eftersom du behöver dem för andra steg i den här artikeln.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Hämta information om tjänstens huvud namn med hjälp av `clientId` värdet som returnerades i föregående steg:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Följande JSON är ett förenklat exempel på utdata från kommandot. Anteckna `objectId` fältet, eftersom du kommer att behöva värdet för nästa steg.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Tillåt att SP får åtkomst till din Azure Machine Learning-arbetsyta. Du behöver namnet på din arbets yta och dess resurs grupps namn för `-w` `-g` parametrarna respektive. För `--user` parametern använder du `objectId` värdet från föregående steg. `--role`Parametern låter dig ange åtkomst rollen för tjänstens huvud namn. I följande exempel tilldelas SP till **ägar** rollen. 

    > [!IMPORTANT]
    > Ägar åtkomst innebär att tjänstens huvud namn kan utföra i princip alla åtgärder i din arbets yta. Den används i det här dokumentet för att demonstrera hur du beviljar åtkomst. i en produktions miljö rekommenderar Microsoft att du beviljar tjänstens huvud namn den lägsta åtkomst som krävs för att utföra den roll du avsåg. Information om hur du skapar en anpassad roll med den åtkomst som krävs för ditt scenario finns i [Hantera åtkomst till Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Det här anropet genererar inga utdata om det lyckas.

## <a name="configure-a-managed-identity"></a>Konfigurera en hanterad identitet

> [!IMPORTANT]
> Hanterad identitet stöds bara när du använder Azure Machine Learning SDK från en virtuell Azure-dator eller med ett Azure Machine Learning beräknings kluster. Att använda en hanterad identitet med ett beräknings kluster är för närvarande en för hands version.

### <a name="managed-identity-with-a-vm"></a>Hanterad identitet med en virtuell dator

1. Aktivera en [systemtilldelad hanterad identitet för Azure-resurser på den virtuella datorn](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. Från [Azure Portal](https://portal.azure.com)väljer du din arbets yta och väljer sedan __Access Control (IAM)__, __Lägg till roll tilldelning__ och välj __virtuell dator__ i list rutan __tilldela åtkomst till__ . Slutligen väljer du den virtuella datorns identitet.

1. Välj den roll som du vill tilldela den här identiteten. Till exempel deltagare eller en anpassad roll. Mer information finns i, [kontrol lera åtkomst till resurser](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Hanterad identitet med beräknings kluster

Mer information finns i [Konfigurera hanterad identitet för beräknings kluster](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Använd interaktiv autentisering

> [!IMPORTANT]
> Interaktiv autentisering använder webbläsaren och kräver cookies (inklusive cookies från tredje part). Om du har inaktiverat cookies kan du få ett fel meddelande som "vi kunde inte logga in dig". Det här felet kan också inträffa om du har aktiverat [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md).

De flesta exempel i dokumentationen och exemplen använder interaktiv autentisering. Om du exempelvis använder SDK finns det två funktions anrop som automatiskt kommer att fråga dig om ett UI-baserat autentiseringspaket:

* Anrop till `from_config()` funktionen kommer att utfärda prompten.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Funktionen `from_config()` söker efter en JSON-fil som innehåller din anslutningsinformation för arbetsytan.

* Om du använder `Workspace` konstruktorn för att tillhandahålla information om prenumeration, resurs grupp och arbets yta, kommer även att uppmanas att använda interaktiv autentisering.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Om du har åtkomst till flera klienter kan du behöva importera-klassen och uttryckligen definiera vilken klient som du är mål för. Genom att anropa konstruktorn för `InteractiveLoginAuthentication` kan du också uppmanas att logga in på liknande sätt som anropen ovan.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

När du använder Azure CLI `az login` används kommandot för att AUTENTISERA CLI-sessionen. Mer information finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Om du använder SDK från en miljö där du tidigare har autentiserat interaktivt med Azure CLI, kan du använda- `AzureCliAuthentication` klassen för att autentisera till arbets ytan med autentiseringsuppgifterna som cachelagras av CLI:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Använd tjänstens huvud namns autentisering

Om du vill autentisera till din arbets yta från SDK: n med hjälp av ett huvud namn för tjänsten använder du `ServicePrincipalAuthentication` konstruktorn Class. Använd de värden som du fick när du skapade tjänst leverantören som parametrar. `tenant_id`Parametern mappar till `tenantId` från ovan, `service_principal_id` mappar till `clientId` och `service_principal_password` mappar till `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Variabeln innehåller nu ett autentiseringscertifikat som du använder direkt i SDK. I allmänhet är det en bra idé att lagra de ID/hemligheter som används ovan i miljövariabler som du ser i följande kod. Genom att lagra i miljövariabler förhindrar du att informationen oavsiktligt checkas in i en GitHub-lagrings platsen.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

För automatiserade arbets flöden som körs i python och använder SDK: n främst kan du använda det här objektet som det är i de flesta fall för autentisering. Följande kod autentiserar till din arbets yta med hjälp av auth-objektet som du skapade.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Använda ett huvud namn för tjänsten från Azure CLI

Du kan använda ett tjänst huvud namn för Azure CLI-kommandon. Mer information finns i [Logga in med ett huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Använd ett huvud namn för tjänsten med REST API (för hands version)

Tjänstens huvud namn kan också användas för att autentisera till Azure Machine Learning [REST API](/rest/api/azureml/) (för hands version). Du använder den Azure Active Directory [tilldelningen av autentiseringsuppgifter för klient](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md), som tillåter tjänst-till-tjänst-anrop för konsol lös autentisering i automatiserade arbets flöden. Exemplen implementeras med [ADAL-biblioteket](../active-directory/azuread-dev/active-directory-authentication-libraries.md) i både Python och Node.js, men du kan också använda ett bibliotek med öppen källkod som stöder OpenID Connect 1,0.

> [!NOTE]
> MSAL.js är ett nyare bibliotek än ADAL, men du kan inte utföra tjänst-till-tjänst-autentisering med hjälp av klientautentiseringsuppgifter med MSAL.js, eftersom det främst är ett bibliotek på klient sidan som är avsett för interaktivt/UI-autentisering som är kopplat till en speciell användare. Vi rekommenderar att du använder ADAL så som visas nedan för att bygga automatiserade arbets flöden med REST API.

#### <a name="nodejs"></a>Node.js

Använd följande steg för att skapa en auth-token med hjälp av Node.js. Kör i din miljö `npm install adal-node` . Använd sedan, `tenantId` `clientId` och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för de matchande variablerna i följande skript.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Variabeln `tokenResponse` är ett objekt som inkluderar token och associerade metadata som till exempel förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande kodfragment är ett exempel svar.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Använd `accessToken` egenskapen för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

#### <a name="python"></a>Python

Använd följande steg för att generera en auth-token med python. Kör i din miljö `pip install adal` . Använd sedan, `tenantId` `clientId` och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för lämpliga variabler i följande skript.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Variabeln `token_response` är en ord lista som inkluderar token och associerade metadata som till exempel förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande kodfragment är ett exempel svar.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Används `token_response["accessToken"]` för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

#### <a name="java"></a>Java

I Java hämtar du Bearer-token med ett standard REST-anrop:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Föregående kod skulle behöva hantera undantag och status koder förutom `200 OK` , men visar mönstret: 

- Använd klient-ID och hemlighet för att kontrol lera att programmet har åtkomst
- Använd ditt klient-ID för att ange var du vill `login.microsoftonline.com` söka
- Använd Azure Resource Manager som källa för autentiseringstoken

## <a name="use-managed-identity-authentication"></a>Använd hanterad identitets autentisering

Använd klassen om du vill autentisera till arbets ytan från en virtuell dator eller ett beräknings kluster som har kon figurer ATS med en hanterad identitet `MsiAuthentication` . Följande exempel visar hur du använder den här klassen för att autentisera till en arbets yta:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Nästa steg

* [Hur du använder hemligheter i träning](how-to-use-secrets-in-runs.md).
* [Konfigurera autentisering för modeller som distribueras som en webb tjänst](how-to-authenticate-web-service.md).
* [Konsumera en Azure Machine Learning-modell som distribuerats som en webbtjänst](how-to-consume-web-service.md).
