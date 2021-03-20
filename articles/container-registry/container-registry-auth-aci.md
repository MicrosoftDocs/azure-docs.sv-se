---
title: Åtkomst från Container Instances
description: Lär dig hur du ger åtkomst till avbildningar i ditt privata behållar register från Azure Container Instances med hjälp av ett Azure Active Directory tjänstens huvud namn.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456515"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autentisera med Azure Container Registry från Azure Container Instances

Du kan använda ett Azure Active Directory (Azure AD) tjänstens huvud namn för att ge åtkomst till dina register för privata behållare i Azure Container Registry.

I den här artikeln lär du dig hur du skapar och konfigurerar ett Azure AD-tjänstens huvud namn med *pull* -behörighet till registret. Sedan startar du en behållare i Azure Container Instances (ACI) som hämtar avbildningen från ditt privata register med hjälp av tjänstens huvud namn för autentisering.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvud namn för tjänsten

Du bör använda ett huvud namn för tjänsten för autentisering från ACI i **konsolbaserade scenarier**, t. ex. i program eller tjänster som skapar behållar instanser på ett automatiserat eller på annat sätt obevakat sätt.

Om du till exempel har ett automatiserat skript som körs natt och skapar en [uppgifts baserad behållar instans](../container-instances/container-instances-restart-policy.md) för att bearbeta vissa data, kan den använda ett huvud namn för tjänsten med endast pull-behörighet för att autentisera till registret. Du kan sedan rotera autentiseringsuppgifterna för tjänstens huvud namn eller återkalla åtkomsten helt utan att påverka andra tjänster och program.

Tjänstens huvud namn bör också användas när register [Administratörs användaren](container-registry-authentication.md#admin-account) är inaktive rad.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autentisera med hjälp av tjänstens huvud namn

Om du vill starta en behållare i Azure Container Instances med ett huvud namn för tjänsten anger du dess ID för `--registry-username` och lösen ordet för `--registry-password` .

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Exempelskript

Du kan hitta de föregående exempel skripten för Azure CLI på GitHub, och även Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

Följande artiklar innehåller ytterligare information om hur du arbetar med tjänstens huvud namn och ACR:

* [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md)
* [Autentisera med Azure Container Registry från Azure Kubernetes service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
