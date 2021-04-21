---
title: Återställa autentiseringsuppgifterna för ett kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du uppdaterar eller återställer autentiseringsuppgifterna för tjänstens huvudnamn eller AAD-program för ett Azure Kubernetes Service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 0b750eb9af7dfd7bcbada7500b6ef71b015db11f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767483"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Uppdatera eller rotera autentiseringsuppgifterna för Azure Kubernetes Service (AKS)

AKS-kluster som skapats med ett huvudnamn för tjänsten har en förfallotid på ett år. När du närmar dig förfallodatumet kan du återställa autentiseringsuppgifterna för att utöka tjänstens huvudnamn med ytterligare en tidsperiod. Du kanske också vill uppdatera eller rotera autentiseringsuppgifterna som en del av en definierad säkerhetsprincip. Den här artikeln beskriver hur du uppdaterar autentiseringsuppgifterna för ett AKS-kluster.

Du kan också ha [integrerat ditt AKS-kluster med Azure Active Directory][aad-integration]och använda det som autentiseringsprovider för klustret. I så fall har du ytterligare två identiteter som skapats för klustret, AAD-serverappen och AAD-klientappen, och du kan också återställa dessa autentiseringsuppgifter.

Du kan också använda en hanterad identitet för behörigheter i stället för tjänstens huvudnamn. Hanterade identiteter är enklare att hantera än tjänstens huvudnamn och kräver inte uppdateringar eller rotationer. Mer information finns i Använda [hanterade identiteter.](use-managed-identity.md)

## <a name="before-you-begin"></a>Innan du börjar

Azure CLI version 2.0.65 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Uppdatera eller skapa ett nytt huvudnamn för tjänsten för ditt AKS-kluster

När du vill uppdatera autentiseringsuppgifterna för ett AKS-kluster kan du välja att antingen:

* Uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvudnamn.
* Skapa ett nytt huvudnamn för tjänsten och uppdatera klustret så att de nya autentiseringsuppgifterna används. 

> [!WARNING]
> Om du väljer att skapa ett nytt *huvudnamn* för tjänsten kan det ta lång tid att uppdatera ett stort AKS-kluster för att använda dessa autentiseringsuppgifter.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Kontrollera utgångsdatumet för tjänstens huvudnamn

Om du vill kontrollera utgångsdatumet för tjänstens huvudnamn använder du kommandot [az ad sp credential list.][az-ad-sp-credential-list] I följande exempel hämtar tjänstens huvudnamns-ID för klustret med namnet *myAKSCluster* i resursgruppen *myResourceGroup* med kommandot [az aks show.][az-aks-show] ID:t för tjänstens huvudnamn anges som *en variabel SP_ID* som ska användas med kommandot az ad sp [credential list.][az-ad-sp-credential-list]

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Återställa autentiseringssuppgifter för tjänstens huvudnamn

Om du vill uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvudnamn hämtar du klustrets id för tjänstens huvudnamn med [kommandot az aks show.][az-aks-show] I följande exempel hämtar ID:t för klustret med namnet *myAKSCluster* i *resursgruppen myResourceGroup.* ID:t för tjänstens huvudnamn anges som en *variabel SP_ID* som ska användas i ytterligare kommando. De här kommandona använder Bash-syntax.

> [!WARNING]
> När du återställer dina klusterautentiseringsuppgifter i ett AKS-kluster som använder Azure Virtual Machine Scale Sets utförs en uppgradering av nodavbildningen för att uppdatera noderna med den nya autentiseringsuppgifterna. [][node-image-upgrade]

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Med en variabeluppsättning som innehåller tjänstens huvudnamns-ID återställer du nu autentiseringsuppgifterna [med az ad sp credential reset][az-ad-sp-credential-reset]. I följande exempel kan Azure-plattformen generera en ny säker hemlighet för tjänstens huvudnamn. Den här nya säkra hemligheten lagras också som en variabel.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Fortsätt nu med att uppdatera [AKS-klustret med nya autentiseringsuppgifter för tjänstens huvudnamn.](#update-aks-cluster-with-new-service-principal-credentials) Det här steget är nödvändigt för att ändringarna av tjänstens huvudnamn ska återspeglas i AKS-klustret.

### <a name="create-a-new-service-principal"></a>Skapa ett nytt huvudnamn för tjänsten

Om du valde att uppdatera de befintliga autentiseringsuppgifterna för tjänstens huvudnamn i föregående avsnitt hoppar du över det här steget. Fortsätt att [uppdatera AKS-klustret med nya autentiseringsuppgifter för tjänstens huvudnamn.](#update-aks-cluster-with-new-service-principal-credentials)

Om du vill skapa ett huvudnamn för tjänsten och sedan uppdatera AKS-klustret för att använda dessa nya autentiseringsuppgifter använder du kommandot [az ad sp create-for-rbac.][az-ad-sp-create] I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De utdata som genereras påminner om de i följande exempel. Anteckna dina egna `appId` och `password`. Dessa värden används i nästa steg.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definiera nu variabler för tjänstens huvudnamns-ID och klienthemlighet med hjälp av utdata från ditt eget [az ad sp create-for-rbac-kommando,][az-ad-sp-create] som du ser i följande exempel. Den *SP_ID* är *ditt appId* och *SP_SECRET* är ditt *lösenord:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Fortsätt nu med att uppdatera [AKS-klustret med nya autentiseringsuppgifter för tjänstens huvudnamn.](#update-aks-cluster-with-new-service-principal-credentials) Det här steget är nödvändigt för att ändringarna av tjänstens huvudnamn ska återspeglas i AKS-klustret.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänstens huvudnamn

> [!IMPORTANT]
> För stora kluster kan det ta lång tid att uppdatera AKS-klustret med ett nytt huvudnamn för tjänsten.

Oavsett om du valde att uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvudnamn eller skapa ett huvudnamn för tjänsten uppdaterar du nu AKS-klustret med dina nya autentiseringsuppgifter med hjälp av [kommandot az aks update-credentials.][az-aks-update-credentials] Variablerna för *--service-principal och* *--client-secret* används:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

För små och medelstora kluster tar det en stund innan autentiseringsuppgifterna för tjänstens huvudnamn uppdateras i AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter för AAD-program

Du kan skapa nya AAD-server- och klientprogram genom att följa [AAD-integreringsstegen][create-aad-app]. Eller återställ dina befintliga AAD-program med samma [metod som för återställning av tjänstens huvudnamn.](#reset-the-existing-service-principal-credential) Efter det behöver du bara uppdatera autentiseringsuppgifterna för klustrets AAD-program med samma [az aks update-credentials-kommando][az-aks-update-credentials] men med hjälp av *variablerna --reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Nästa steg

I den här artikeln har tjänstens huvudnamn för själva AKS-klustret och AAD-integreringsprogram uppdaterats. Mer information om hur du hanterar identiteter för arbetsbelastningar i ett kluster finns i Metodtips för [autentisering och auktorisering i AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
