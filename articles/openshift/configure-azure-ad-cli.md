---
title: Azure Red Hat OpenShift som kör OpenShift 4 – Konfigurera Azure Active Directory autentisering med hjälp av kommando raden
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering för ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 med hjälp av kommando raden
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 393185d2167e18df3f8c1319e7367efbc437de1a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590344"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurera Azure Active Directory autentisering för ett Azure Red Hat OpenShift 4-kluster (CLI)

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Hämta dina företagsspecifika URL: er som ska användas för att konfigurera Azure Active Directory-programmet.

Konstruera klustrets OAuth-callback-URL och lagra den i en variabel **oauthCallbackURL**. Se till att ersätta **Aro-RG** med resurs gruppens namn och **Aro-kluster** med ditt kluster namn.

> [!NOTE]
> `AAD`Avsnittet i OAuth-callback-URL: en ska matcha namnet på den OAuth-identitetsprovider som du kommer att konfigurera senare.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Skapa ett Azure Active Directory-program för autentisering

Skapa ett Azure Active Directory program och hämta den skapade program identifieraren. Ersätt **\<ClientSecret>** med ett säkert lösen ord.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Du bör få tillbaka något som liknar det här. Anteckna det som är det **AppId** du behöver i senare steg.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Hämta klient-ID: t för den prenumeration som äger programmet.

```azure
az account show --query tenantId -o tsv
```

Du bör få tillbaka något som liknar det här. Anteckna det eftersom det här är det **TenantId** du behöver i senare steg.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Skapa en manifest fil för att definiera de valfria anspråk som ska ingå i ID-token

Programutvecklare kan använda [valfria anspråk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program.

Du kan använda valfria anspråk för att:

- Välj ytterligare anspråk som ska inkluderas i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program.

Vi konfigurerar OpenShift för att använda `email` anspråket och återgår till `upn` att ange önskat användar namn genom att lägga till `upn` som en del av den ID-token som returneras av Azure Active Directory.

Skapa en **manifest.jspå** fil för att konfigurera Azure Active Directory programmet.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Uppdatera Azure Active Directory programmets optionalClaims med ett manifest

Ersätt **\<AppID>** med det ID du fick tidigare.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Uppdatera behörigheterna för Azure Active Directory program omfattning

Vi måste definiera rätt omfattningar för att kunna läsa användar informationen från Azure Active Directory.

Ersätt **\<AppID>** med det ID du fick tidigare.

Lägg till behörighet för **Azure Active Directory graf. user. Read** -omfång för att aktivera inloggning och läsa användar profil.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> Om du inte autentiseras som global administratör för den här Azure Active Directory kan du ignorera meddelandet för att bevilja medgivande, eftersom du kommer att uppmanas att göra det när du har loggat in på ditt eget konto.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Tilldela användare och grupper till klustret (valfritt)

Program som är registrerade i en Azure Active Directory-klient (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiseras korrekt. Med Azure AD kan klient organisations administratörer och utvecklare begränsa en app till en särskild uppsättning användare eller säkerhets grupper i klienten.

Följ anvisningarna i Azure Active Directory-dokumentationen för att [tilldela användare och grupper till appen](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurera OpenID-autentisering för OpenShift

Hämta `kubeadmin` autentiseringsuppgifterna. Kör följande kommando för att hitta lösen ordet för `kubeadmin` användaren.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Följande exempel på utdata visar att lösen ordet är i `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Logga in på OpenShift-klustrets API-server med hjälp av följande kommando. `$apiServer`Variabeln angavs [tidigare](). Ersätt **\<kubeadmin password>** med det lösen ord du hämtade.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Skapa en OpenShift-hemlighet för att lagra Azure Active Directory program hemligheten och Ersätt **\<ClientSecret>** med den hemlighet som du hämtade tidigare.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```

Skapa en **OIDC. yaml** -fil för att konfigurera OpenShift OpenID-autentisering mot Azure Active Directory. Ersätt **\<AppID>** och **\<TenantId>** med de värden som du hämtade tidigare.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Tillämpa konfigurationen på klustret.

```azurecli-interactive
oc apply -f oidc.yaml
```

Du får tillbaka ett svar som liknar följande.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verifiera inloggning via Azure Active Directory

Om du nu loggar ut från OpenShift-webbkonsolen och försöker logga in igen visas ett nytt alternativ för att logga in med **AAD**. Du kan behöva vänta några minuter.

![Inloggnings skärmen med Azure Active Directory alternativ](media/aro4-login-2.png)
