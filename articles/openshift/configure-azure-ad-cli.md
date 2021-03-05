---
title: Azure Red Hat OpenShift som kör OpenShift 4 – Konfigurera Azure Active Directory autentisering med hjälp av kommando raden
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering för ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 med hjälp av kommando raden
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 06f7bfea9a88627733eb9ce9166e05d05790e23a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213070"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurera Azure Active Directory autentisering för ett Azure Red Hat OpenShift 4-kluster (CLI)

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Hämta dina företagsspecifika URL: er som ska användas för att konfigurera Azure Active Directory-programmet.

Ange variabler för resurs grupp och kluster namn.

Ersätt **\<resource_group>** med resurs gruppens namn och **\<aro_cluster>** med klustrets namn.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Konstruera klustrets OAuth-callback-URL och lagra den i en variabel **oauthCallbackURL**. 

> [!NOTE]
> `AAD`Avsnittet i OAuth-callback-URL: en ska matcha namnet på den OAuth-identitetsprovider som du kommer att konfigurera senare.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

Formatet på oauthCallbackURL skiljer sig något från anpassade domäner:

* Kör följande kommando om du använder en anpassad domän, `contoso.com` t. ex.. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Om du inte använder en anpassad domän `$domain` kommer det att vara en sträng med åtta tecken alnum och utökas av `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> `AAD`Avsnittet i OAuth-callback-URL: en ska matcha namnet på den OAuth-identitetsprovider som du kommer att konfigurera senare.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Skapa ett Azure Active Directory-program för autentisering

Ersätt **\<client_secret>** med ett säkert lösen ord för programmet.

```azurecli-interactive
client_secret=<client_secret>
```

Skapa ett Azure Active Directory program och hämta den skapade program identifieraren.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Hämta klient-ID: t för den prenumeration som äger programmet.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Skapa en manifest fil för att definiera de valfria anspråk som ska ingå i ID-token

Programutvecklare kan använda [valfria anspråk](../active-directory/develop/active-directory-optional-claims.md) i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program.

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

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Uppdatera behörigheterna för Azure Active Directory program omfattning

Vi måste definiera rätt omfattningar för att kunna läsa användar informationen från Azure Active Directory.

Lägg till behörighet för **Azure Active Directory graf. user. Read** -omfång för att aktivera inloggning och läsa användar profil.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Du kan på ett säkert sätt Ignorera meddelandet för att bevilja medgivande, såvida du inte autentiseras som global administratör för den här Azure Active Directory. Standard domän användare uppmanas att bevilja medgivande när de först loggar in på klustret med sina AAD-autentiseringsuppgifter.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Tilldela användare och grupper till klustret (valfritt)

Program som är registrerade i en Azure Active Directory-klient (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiseras korrekt. Med Azure AD kan klient organisations administratörer och utvecklare begränsa en app till en särskild uppsättning användare eller säkerhets grupper i klienten.

Följ anvisningarna i Azure Active Directory-dokumentationen för att [tilldela användare och grupper till appen](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurera OpenID-autentisering för OpenShift

Hämta `kubeadmin` autentiseringsuppgifterna. Kör följande kommando för att hitta lösen ordet för `kubeadmin` användaren.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Logga in på OpenShift-klustrets API-server med hjälp av följande kommando. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Skapa en OpenShift-hemlighet för att lagra Azure Active Directory program hemligheten.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Skapa en **OIDC. yaml** -fil för att konfigurera OpenShift OpenID-autentisering mot Azure Active Directory. 

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
      clientID: $app_id
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
      issuer: https://login.microsoftonline.com/$tenant_id
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
