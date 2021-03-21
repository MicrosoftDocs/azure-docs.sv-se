---
title: Återställa autentiseringsuppgifterna för ett kluster
titleSuffix: Azure Kubernetes Service
description: Läs om hur du uppdaterar eller återställer autentiseringsuppgifterna för tjänstens huvud namn eller AAD-program för ett Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 84cadc54a11c2e57d73fd1f06e98deb3d599d648
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501183"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Uppdatera eller rotera autentiseringsuppgifterna för Azure Kubernetes service (AKS)

AKS-kluster som skapats med ett huvud namn för tjänsten har en giltighets tid på ett år. När du nära förfallo datumet kan du återställa autentiseringsuppgifterna för att utöka tjänstens huvud namn under ytterligare en tids period. Du kanske också vill uppdatera eller rotera autentiseringsuppgifterna som en del av en definierad säkerhets princip. Den här artikeln beskriver hur du uppdaterar autentiseringsuppgifterna för ett AKS-kluster.

Du kan också ha [integrerat ditt AKS-kluster med Azure Active Directory][aad-integration]och använda det som autentiseringsprovider för klustret. I så fall kommer du att ha två fler identiteter som skapats för klustret, AAD-serverprogrammet och AAD-klientprogramvaran. du kan också återställa dessa autentiseringsuppgifter.

Alternativt kan du använda en hanterad identitet för behörigheter i stället för ett huvud namn för tjänsten. Hanterade identiteter är enklare att hantera än tjänstens huvud namn och kräver inte uppdateringar eller rotationer. Mer information finns i [använda hanterade identiteter](use-managed-identity.md).

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.65 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Uppdatera eller skapa ett nytt huvud namn för tjänsten för ditt AKS-kluster

När du vill uppdatera autentiseringsuppgifterna för ett AKS-kluster kan du antingen välja att:

* Uppdatera autentiseringsuppgifterna för det befintliga huvud namnet för tjänsten.
* Skapa ett nytt huvud namn för tjänsten och uppdatera klustret för att använda dessa nya autentiseringsuppgifter. 

> [!WARNING]
> Om du väljer att skapa ett *nytt* huvud namn för tjänsten kan det ta lång tid att uppdatera ett stort AKS-kluster för att använda dessa autentiseringsuppgifter.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Kontrol lera förfallo datumet för tjänstens huvud namn

Om du vill kontrol lera förfallo datumet för tjänstens huvud namn använder du kommandot [AZ AD SP Credential List][az-ad-sp-credential-list] . I följande exempel hämtas tjänstens huvud namn-ID för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* med kommandot [AZ AKS show][az-aks-show] . Tjänstens huvud namn-ID anges som en variabel med namnet *SP_ID* för användning med kommandot [AZ AD SP Credential List][az-ad-sp-credential-list] .

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Återställ de befintliga autentiseringsuppgifterna för tjänstens huvud namn

Om du vill uppdatera autentiseringsuppgifterna för det befintliga huvud namnet för tjänsten hämtar du tjänstens huvud namn-ID för klustret med hjälp av kommandot [AZ AKS show][az-aks-show] . I följande exempel hämtas ID: t för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* . Tjänstens huvud namn-ID anges som en variabel med namnet *SP_ID* som ska användas i ytterligare kommando. Dessa kommandon använder bash-syntax.

> [!WARNING]
> När du återställer dina autentiseringsuppgifter för ditt kluster i ett AKS-kluster som använder Azure Virtual Machine Scale Sets, utförs en uppgradering av en [nod][node-image-upgrade] för att uppdatera noderna med den nya autentiseringsinformation.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Med en variabel uppsättning som innehåller tjänstens huvud namns-ID, Återställ nu autentiseringsuppgifterna med [AZ AD SP Credential reset][az-ad-sp-credential-reset]. I följande exempel kan Azure-plattformen generera en ny säker hemlighet för tjänstens huvud namn. Den nya säkra hemligheten lagras också som en variabel.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Fortsätt nu med att [Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänstens huvud namn](#update-aks-cluster-with-new-service-principal-credentials). Det här steget krävs för att tjänstens huvud namn ska avspeglas i AKS-klustret.

### <a name="create-a-new-service-principal"></a>Skapa ett nytt huvud namn för tjänsten

Hoppa över det här steget om du väljer att uppdatera de befintliga autentiseringsuppgifterna för tjänstens huvud namn i föregående avsnitt. Fortsätt att [Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänstens huvud namn](#update-aks-cluster-with-new-service-principal-credentials).

Om du vill skapa ett huvud namn för tjänsten och sedan uppdatera AKS-klustret för att använda dessa nya autentiseringsuppgifter, använder du kommandot [AZ AD SP Create-for-RBAC][az-ad-sp-create] . I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

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

Definiera nu variabler för tjänstens huvud namns-ID och klient hemligheten med hjälp av utdata från ditt eget [AZ AD SP Create-for-RBAC][az-ad-sp-create] -kommando, som visas i följande exempel. *SP_ID* är ditt *appId* och *SP_SECRET* är ditt *lösen ord*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Fortsätt nu med att [Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänstens huvud namn](#update-aks-cluster-with-new-service-principal-credentials). Det här steget krävs för att tjänstens huvud namn ska avspeglas i AKS-klustret.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänstens huvud namn

> [!IMPORTANT]
> För stora kluster kan det ta lång tid att uppdatera AKS-klustret med ett nytt huvud namn för tjänsten.

Oavsett om du väljer att uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvud namn eller skapa ett huvud namn för tjänsten, uppdaterar du nu AKS-klustret med dina nya autentiseringsuppgifter med hjälp av kommandot [AZ AKS Update-credentials][az-aks-update-credentials] . Variablerna för *--Service-Principal* och *--client-Secret* används:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

För små och medel stora kluster tar det en stund innan autentiseringsuppgifterna för tjänstens huvud namn uppdateras i AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Uppdatera AKS-kluster med nya AAD-programautentiseringsuppgifter

Du kan skapa nya AAD-servrar och klient program genom att följa [anvisningarna för AAD-integrering][create-aad-app]. Eller Återställ dina befintliga AAD-program på [samma sätt som för tjänstens huvud namns återställning](#reset-the-existing-service-principal-credential). Efter det behöver du bara uppdatera dina autentiseringsuppgifter för ditt AAD-program med samma [AZ AKS Update-credentials][az-aks-update-credentials] -kommando, men med hjälp av *--Reset-AAD-* variablerna.

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

I den här artikeln har tjänstens huvud namn för själva AKS-klustret och AAD-integrerings programmen uppdaterats. Mer information om hur du hanterar identitet för arbets belastningar i ett kluster finns i [metod tips för autentisering och auktorisering i AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
