---
title: Azure Red Hat OpenShift som kör OpenShift 4 – Konfigurera Azure Active Directory autentisering med hjälp av Azure Portal och webb konsolen OpenShift
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering för ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 med hjälp av Azure Portal och webb konsolen OpenShift
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 10a7dc662993327b71d43c27f44d22166a3f3611
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590327"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Konfigurera Azure Active Directory autentisering för ett Azure Red Hat OpenShift 4-kluster (portal)

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Innan du börjar

Konstruera klustrets **OAuth-callback-URL** och anteckna den. Se till att ersätta **Aro-RG** med resurs gruppens namn och **Aro-kluster** med ditt kluster namn.

> [!NOTE]
> `AAD`Avsnittet i OAuth-callback-URL: en ska matcha namnet på den OAuth-identitetsprovider som du kommer att konfigurera senare.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Skapa ett Azure Active Directory-program för autentisering

Logga in på Azure Portal och navigera till [bladet Appregistreringar](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)och klicka sedan på **ny registrering** för att skapa ett nytt program.

Ange ett namn för programmet, till exempel **Aro-azuread-auth**, och fyll i **omdirigerings-URI: n** med värdet för den OAuth-callback-URL som du hämtade tidigare.

![Ny programregistrering](media/aro4-ad-registerapp.png)

Navigera till **certifikat & hemligheter** och klicka på **ny klient hemlighet** och fyll i informationen. Anteckna värdet för nyckeln eftersom du kommer att använda det i ett senare skede. Du kommer inte att kunna hämta den igen.

![Skapa en hemlighet](media/aro4-ad-clientsecret.png)

Gå till **översikten** och anteckna **program** -ID: t och katalog (klient) **-ID: t**. Du behöver dem i ett senare skede.

![Hämta program (klient) och katalog (klient) ID: n](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Konfigurera valfria anspråk

Programutvecklare kan använda [valfria anspråk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program.

Du kan använda valfria anspråk för att:

* Välj ytterligare anspråk som ska inkluderas i token för ditt program.
* Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
* Lägg till och få till gång till anpassade anspråk för ditt program.

Vi konfigurerar OpenShift för att använda `email` anspråket och återgår till `upn` att ange önskat användar namn genom att lägga till `upn` som en del av den ID-token som returneras av Azure Active Directory.

Navigera till **token-konfiguration (för hands version)** och klicka på **Lägg till valfritt anspråk**. Välj **ID** och kontrol lera **e-postmeddelandena** och **UPN** -anspråk.

![Skapa en hemlighet](media/aro4-ad-tokens.png)

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

Du kan hitta kluster konsolens URL genom att köra följande kommando, som ser ut så här: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Starta konsol-URL: en i en webbläsare och logga in med `kubeadmin` autentiseringsuppgifterna.

Navigera till **Administration**, klicka på **kluster inställningar**och välj sedan fliken **global konfiguration** . Rulla för att välja **OAuth**.

Rulla ned till Välj **Lägg till** under **identitets leverantörer** och välj **OpenID Anslut**.
![Välj OpenID Anslut från List rutan identitets leverantörer](media/aro4-oauth-idpdrop.png)

Fyll i namnet som **AAD**, **klient-ID** : t som **program-ID** och **klient hemlighet**. **Utfärdar-URL: en** är formaterad som sådan: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Ersätt plats hållaren med klient-ID: t som du hämtade tidigare.

![Fyll i OAuth-information](media/aro4-oauth-idp-1.png)

Rulla ned till avsnittet **anspråk** och uppdatera det **önskade användar namnet** så att det använder värdet från **UPN** -anspråket.

![Fyll i anspråks information](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Verifiera inloggning via Azure Active Directory

Om du nu loggar ut från OpenShift-webbkonsolen och försöker logga in igen visas ett nytt alternativ för inloggning med **AAD**. Du kan behöva vänta några minuter.

![Inloggnings skärmen med Azure Active Directory alternativ](media/aro4-login-2.png)
