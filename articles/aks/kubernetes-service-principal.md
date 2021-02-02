---
title: Tjänstens huvudnamn för Azure Kubernetes Service (AKS)
description: Skapa och hantera ett tjänstobjekt för Azure Active Directory för ett kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: b7f8060666612049026f2602ab7c8511aea22757
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475445"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Tjänstens huvudnamn med Azure Kubernetes Service (AKS)

För att interagera med Azure-API: er, kräver ett AKS-kluster antingen ett [huvud namn för Azure Active Directory (AD)][aad-service-principal] eller en [hanterad identitet](use-managed-identity.md). Ett tjänst huvud namn eller en hanterad identitet krävs för att dynamiskt skapa och hantera andra Azure-resurser, till exempel en Azure Load Balancer eller container Registry (ACR).

Den här artikeln visar hur du skapar och använder ett tjänstens huvudnamn för AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

För att skapa ett Azure AD-huvudnamn för tjänsten måste du ha behörighet att registrera ett program med din Azure AD-klientorganisation, samt behörighet att tilldela programmet till en roll i din prenumeration. Om du inte har de behörigheter som du behöver kan du be din Azure AD- eller prenumerationsadministratör att tilldela de nödvändiga behörigheterna eller att skapa ett tjänstens huvudnamn att använda med AKS-klustret.

Om du använder ett huvud namn för tjänsten från en annan Azure AD-klient finns det ytterligare överväganden kring de behörigheter som är tillgängliga när du distribuerar klustret. Du kanske inte har rätt behörighet för att läsa och skriva katalog information. Mer information finns i [Vad är standard användar behörigheter i Azure Active Directory?][azure-ad-permissions]

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Skapa och använda ett tjänstens huvudnamn automatiskt

När du skapar ett AKS-kluster i Azure Portal eller använder kommandot [az aks create][az-aks-create] så kan Azure automatiskt generera ett tjänstens huvudnamn.

Ett tjänstens huvudnamn har inte angetts i följande Azure CLI-exempel. I det här scenariot skapar Azure CLI ett tjänstens huvudnamn för AKS-klustret. Om ditt Azure-konto ska kunna slutföra åtgärden måste det ha rätt behörigheter för att skapa ett tjänstens huvudnamn.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Skapa ett tjänstens huvudnamn manuellt

Använd kommandot [az ad sp create-for-rbac][az-ad-sp-create] om du vill skapa ett tjänstens huvudnamn med Azure CLI manuellt. I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

De utdata som genereras påminner om de i följande exempel. Anteckna dina egna `appId` och `password`. De här värdena används när du skapar ett AKS-kluster i nästa avsnitt.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Ange ett tjänstens huvudnamn för ett AKS-kluster

Om du vill använda ett befintligt huvudnamn för tjänsten när du skapar ett AKS-kluster med kommandot [az aks create][az-aks-create] så använd parametrarna `--service-principal` och `--client-secret` för att ange `appId` och `password` från utdata från kommandot [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Om du använder ett befintligt huvud namn för tjänsten med anpassad hemlighet, se till att hemligheten inte är längre än 190 byte.

Om du distribuerar ett AKS-kluster med hjälp av Azure Portal, så välj **Konfigurera tjänstens huvudnamn** i dialogrutan **Skapa Kubernetes-kluster** på sidan *Autentisering*. Välj **Använd befintlig** och ange följande värden:

- **Klient-ID för tjänstens huvudnamn** är *appId*
- **Klienthemligheten för tjänstens huvudnamn** är värdet *lösenord*

![Bild som illustrerar hur du navigerar till Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegera åtkomst till andra Azure-resurser

Tjänstens huvudnamn för AKS-klustret kan användas för att komma åt andra resurser. Om du till exempel vill distribuera ditt AKS-kluster till ett befintligt Azure Virtual Network-undernät eller ansluta till Azure Container Registry (ACR), måste du delegera åtkomst till dessa resurser till tjänstens huvud namn.

Om du vill delegera behörigheter skapar du en roll tilldelning med hjälp av kommandot [AZ roll tilldelning skapa][az-role-assignment-create] . Tilldela `appId` till ett visst omfång, till exempel en resurs grupp eller en virtuell nätverks resurs. En roll definierar därefter vilka behörigheter som tjänstens huvudnamn har på resursen, som visas i följande exempel:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

`--scope`För en resurs måste vara ett fullständigt resurs-ID, t. ex. */Subscriptions/ \<guid\> /ResourceGroups/myResourceGroup* eller */Subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Om du har tagit bort deltagar roll tilldelningen från resurs gruppen för noden kan åtgärderna nedan Miss lyckas.  

Följande avsnitt beskriver vanliga delegeringar som du kan behöva göra.

### <a name="azure-container-registry"></a>Azure Container Registry

Om du använder Azure Container Registry (ACR) som behållar avbildnings arkiv måste du bevilja behörighet till tjänstens huvud namn för ditt AKS-kluster för att läsa och hämta avbildningar. För närvarande är den rekommenderade konfigurationen att använda kommandot [AZ AKS Create][az-aks-create] eller [AZ AKS Update][az-aks-update] för att integrera med ett register och tilldela lämplig roll för tjänstens huvud namn. Detaljerade anvisningar finns i [autentisera med Azure Container Registry från Azure Kubernetes-tjänsten][aks-to-acr].

### <a name="networking"></a>Nätverk

Du kan använda avancerade nätverk där det virtuella nätverket och undernätet eller offentliga IP-adresser finns i en annan resursgrupp. Tilldela den inbyggda rollen [nätverks deltagare][rbac-network-contributor] i under nätet i det virtuella nätverket. Du kan också skapa en [anpassad roll][rbac-custom-role] med behörigheter för att komma åt nätverks resurserna i den resurs gruppen. Mer information finns i [AKS-tjänstens behörigheter][aks-permissions] .

### <a name="storage"></a>Storage

Du kan behöva åtkomst till befintliga diskresurser i en annan resursgrupp. Tilldela behörigheter till någon av följande uppsättningar:

- Skapa en [anpassad roll][rbac-custom-role] och definiera följande rollbehörigheter:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Eller tilldela den inbyggda rollen [Lagringskontodeltagare][rbac-storage-contributor] i resursgruppen

### <a name="azure-container-instances"></a>Azure Container Instances

Om du använder Virtual Kubelet för att integrera med AKS och väljer att köra Azure Container Instances (ACI) i resursgruppen separat från AKS-klustret måste AKS-huvudnamn beviljas *deltagarbehörigheter* för ACI-resursgruppen.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Tänk på följande när du använder AKS och Azure AD-tjänstens huvudnamn.

- Tjänstobjektet för Kubernetes är en del av klusterkonfigurationen. Men använd inte identiteten för att distribuera klustret.
- Som standard är autentiseringsuppgifterna för tjänstens huvud namn giltiga i ett år. Du kan när som helst [Uppdatera eller rotera autentiseringsuppgifterna för tjänstens huvud namn][update-credentials] .
- Varje tjänstobjekt är associerat med ett Azure AD-program. Tjänstens huvud namn för ett Kubernetes-kluster kan associeras med ett giltigt namn för Azure AD-program (till exempel: *https://www.contoso.org/example* ). URL:en för programmet behöver inte vara en verklig slutpunkt.
- När du anger **Klient-ID** för tjänstens huvudnamn använder du värdet för `appId`.
- På virtuella datorer i agent-noden i Kubernetes-klustret lagras autentiseringsuppgifterna för tjänstens huvud namn i filen `/etc/kubernetes/azure.json`
- Om du använder kommandot [az aks create][az-aks-create] för att generera tjänstobjektet automatiskt skrivs autentiseringsuppgifterna för tjänstobjektet till filen `~/.azure/aksServicePrincipal.json` på den dator som används för att köra kommandot.
- Om du inte specifikt skickar ett huvud namn för tjänsten i ytterligare AKS CLI-kommandon används det standard tjänst huvud namn som finns på `~/.azure/aksServicePrincipal.json` .  
- Du kan också ta bort aksServicePrincipal.jspå filen och AKS skapar ett nytt huvud namn för tjänsten.
- När du tar bort ett AKS-kluster som skapats av [az aks create][az-aks-create] tas tjänstens huvudnamn som skapades automatiskt inte bort.
    - För att ta bort tjänstens huvud namn, fråga efter klustrets *servicePrincipalProfile. clientId* och ta sedan bort med [AZ AD SP Delete] [AZ-AD-SP-Delete]. Ersätt följande resursgruppsnamn och klisternamn med dina egna värden:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Felsöka

Autentiseringsuppgifterna för tjänstens huvud namn för ett AKS-kluster cachelagras av Azure CLI. Om autentiseringsuppgifterna har upphört att gälla uppstår fel vid distribution av AKS-kluster. Följande fel meddelande visas när du kör [AZ AKS Create][az-aks-create] kan tyda på ett problem med de cachelagrade tjänstens huvud namns uppgifter:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Kontrol lera åldern på filen med autentiseringsuppgifter med följande kommando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Standard förfallo tiden för autentiseringsuppgifterna för tjänstens huvud namn är ett år. Om *aksServicePrincipal.jspå* filen är äldre än ett år tar du bort filen och försöker distribuera ett AKS-kluster igen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory tjänstens huvud namn finns i [objekt för program-och tjänst objekt][service-principal].

Information om hur du uppdaterar autentiseringsuppgifterna finns i [Uppdatera eller rotera autentiseringsuppgifterna för ett huvud namn för tjänsten i AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
