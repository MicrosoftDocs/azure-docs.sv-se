---
title: Hantera ett Azure Kubernetes service-kluster med webb instrument panelen
description: Lär dig hur du använder det inbyggda Kubernetes-webbgränssnittets instrument panel för att hantera ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: acaeaa2e5338c86fa59d0e2941719f8fa2708ef1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176829"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Få åtkomst till Kubernetes-webbinstrumentpanelen i Azure Kubernetes service (AKS)

Kubernetes innehåller en webb instrument panel som kan användas för grundläggande hanterings åtgärder. På den här instrument panelen kan du visa grundläggande hälso status och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och vägleder dig genom några grundläggande instrument panels åtgärder.

Mer information om instrument panelen för Kubernetes finns i [webb gränssnitts instrument panelen för Kubernetes][kubernetes-dashboard]. AKS använder version 2,0 och senare av instrument panelen med öppen källkod.

> [!WARNING]
> **AKS instrument panels tillägg är inställt på utfasning. Använd vyn [Kubernetes i Azure Portal (för hands version)][kubernetes-portal] i stället.** 
> * Kubernetes-instrumentpanelen är aktive rad som standard för kluster som kör en Kubernetes-version som är mindre än 1,18.
> * Instrument panels tillägget är inaktiverat som standard för alla nya kluster som skapats på Kubernetes 1,18 eller senare. 
 > * Från och med Kubernetes 1,19 i för hands versionen stöder AKS inte längre installationen av det hanterade Kube-instrumentpanelen. 
 > * Befintliga kluster med aktiverade tillägg påverkas inte. Användarna fortsätter att kunna installera instrument panelen med öppen källkod manuellt som användar installerad program vara.

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en `kubectl` anslutning till klustret. Om du behöver skapa ett AKS-kluster, se [snabb start: Distribuera ett Azure Kubernetes service-kluster med Azure CLI][aks-quickstart].

Du måste också ha Azure CLI-versionen 2.6.0 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Inaktivera Kubernetes-instrumentpanelen

Kube-instrumentpanelen är **aktiverat som standard i kluster som är äldre än K8s 1,18**. Tillägget kan inaktive ras genom att köra följande kommando.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

> [!WARNING]
> AKS Dashboard-tillägget är inaktuellt för versioner 1.19 +. Använd vyn [Kubernetes i Azure Portal (för hands version) i][kubernetes-portal] stället. 
> * Följande kommando öppnar nu vyn Azure-portalwebbplats i stället för Kubernetes-instrumentpanelen för version 1,19 och senare.

Om du vill starta Kubernetes-instrumentpanelen på ett kluster använder du kommandot [AZ AKS Browse][az-aks-browse] . Det här kommandot kräver installation av Kube på klustret, vilket ingår som standard i kluster som kör en version som är äldre än Kubernetes 1,18.

I följande exempel öppnas instrument panelen för klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklings systemet och Kubernetes-API: et och öppnar en webbläsare till Kubernetes-instrumentpanelen. Om en webbläsare inte öppnas på Kubernetes-instrumentpanelen kopierar du och klistrar in URL-adressen som anges i Azure CLI, vanligt vis `http://127.0.0.1:8001` .

> [!NOTE]
> Om du inte ser instrument panelen på `http://127.0.0.1:8001` kan du manuellt dirigera till följande adresser. Kluster på 1,16 eller mer använder https och kräver en separat slut punkt.
> * K8s 1,16 eller senare: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 och nedåt: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Logga in på instrument panelen (Kubernetes 1.16 +)

> [!IMPORTANT]
> Från och med [v 1.10.1 av Kubernetes-instrumentpanelen](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) eller Kubernetes v-1.16 + tjänst kontot "Kubernetes-instrumentpanel" kan inte längre användas för att hämta resurser på grund av en [säkerhets korrigering i den versionen](https://github.com/kubernetes/dashboard/pull/3400). Det innebär att förfrågningar utan auth-information returnerar ett [401-otillåtet fel](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). En Bearer-token som hämtats från ett tjänst konto kan fortfarande användas som i det här [Kubernetes-Instrumentpanels exemplet](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), men detta påverkar inloggnings flödet för instrument panels tillägget jämfört med äldre versioner.
>
>Om du fortfarande kör en version före 1,16 kan du fortfarande ge behörighet till tjänst kontot "Kubernetes-Dashboard", men detta **rekommenderas inte**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Den första skärmen som visas kräver en kubeconfig eller token. Båda alternativen kräver resurs behörigheter för att visa dessa resurser på instrument panelen.

![inloggnings skärm](./media/kubernetes-dashboard/login.png)

**Använd en kubeconfig**

För både Azure AD-aktiverade och icke-Azure AD-aktiverade kluster, kan en kubeconfig skickas. Se till att åtkomsttoken är giltiga, om dina token har upphört att gälla kan du uppdatera tokens via kubectl.

1. Ange admin-kubeconfig med `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Välj `Kubeconfig` och klicka `Choose kubeconfig file` för att öppna fil väljaren
1. Välj din kubeconfig-fil (standard $HOME/.Kube/config)
1. Klicka på `Sign In`

**Använd en token**

1. För **AD-kluster som inte är Azure AD-aktiverat** kör `kubectl config view` och kopierar du den token som är associerad med användar kontot för klustret.
1. Klistra in i alternativet token vid inloggning.    
1. Klicka på `Sign In`

För Azure AD-aktiverade kluster hämtar du din AAD-token med följande kommando. Verifiera att du har ersatt resurs gruppen och kluster namnet i kommandot.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

När åtgärden är klar visas en sida som liknar den nedan.

![Sidan översikt på Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Skapa ett program

Följande steg kräver att användaren har behörighet till respektive resurser. 

För att se hur Kubernetes-instrumentpanelen kan minska komplexiteten med hanterings uppgifter ska vi skapa ett program. Du kan skapa ett program från Kubernetes-instrumentpanelen genom att tillhandahålla text indata, en YAML-fil eller en grafisk guide.

Utför följande steg för att skapa ett program:

1. Välj knappen **skapa** i det övre högra fönstret.
1. Om du vill använda den grafiska guiden väljer du att **skapa en app**.
1. Ange ett namn för distributionen, till exempel *nginx*
1. Ange namnet på behållar avbildningen som ska användas, t. ex. *nginx: 1.15.5*
1. För att exponera port 80 för webb trafik skapar du en Kubernetes-tjänst. Under **tjänst** väljer du **extern** och anger sedan **80** för både port-och mål porten.
1. När du är klar väljer du **distribuera** för att skapa appen.

![Distribuera en app i Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/create-app.png)

Det tar en minut eller två för en offentlig extern IP-adress som ska tilldelas till Kubernetes-tjänsten. På den vänstra storleken under **identifiering och belastnings utjämning** väljer du **tjänster**. Programmets tjänst visas, inklusive *externa slut punkter*, som du ser i följande exempel:

![Visa lista över tjänster och slut punkter](./media/kubernetes-dashboard/view-services.png)

Välj slut punkts adress för att öppna ett webbläsarfönster till standard NGINX-sidan:

![Visa standard NGINX-sidan för det distribuerade programmet](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visa information om Pod

Kubernetes-instrumentpanelen kan ge grundläggande övervaknings mått och felsöknings information, till exempel loggar.

Om du vill se mer information om din program poddar väljer du **poddar** i den vänstra menyn. Listan över tillgängliga poddar visas. Välj din *nginx* -Pod för att visa information, till exempel resursförbrukning:

![Visa information om Pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och Visa program kan Kubernetes-instrumentpanelen användas för att redigera och uppdatera program distributioner. För att ge ytterligare redundans för programmet ska vi öka antalet NGINX-repliker.

Redigera en distribution:

1. Välj **distributioner** på menyn till vänster och välj sedan din *nginx* -distribution.
1. Välj **Redigera** i det övre högra navigerings fältet.
1. Leta upp `spec.replica` värdet på rad 20. Om du vill öka antalet repliker för programmet ändrar du värdet från *1* till *3*.
1. Välj **Uppdatera** när du är klar.

![Redigera distributionen för att uppdatera antalet repliker](./media/kubernetes-dashboard/edit-deployment.png)

Det tar en stund för den nya poddar att skapas i en replik uppsättning. Välj **replik uppsättningar** på den vänstra menyn och välj sedan *nginx* replik uppsättning. Listan över poddar visar nu det uppdaterade antalet repliker, vilket visas i följande exempel på utdata:

![Visa information om replik uppsättningen](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nästa steg

Mer information om instrument panelen för Kubernetes finns på [instrument panelen för Kubernetes-WEBBgränssnittet][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
