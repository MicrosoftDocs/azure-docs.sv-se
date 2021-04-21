---
title: Hantera ett Azure Kubernetes Service kluster med webbinstrumentpanelen
description: Lär dig hur du använder den inbyggda instrumentpanelen för Kubernetes-webbgränssnitt för att hantera ett Azure Kubernetes Service-kluster (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 0d872a60c4aea89e621fe25ade45697244a74fa8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779731"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Åtkomst till Kubernetes-webbinstrumentpanelen i Azure Kubernetes Service (AKS)

Kubernetes innehåller en webbinstrumentpanel som kan användas för grundläggande hanteringsåtgärder. Med den här instrumentpanelen kan du visa grundläggande hälsostatus och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och vägleder dig sedan genom några grundläggande instrumentpanelsåtgärder.

Mer information om Kubernetes-instrumentpanelen finns i [Kubernetes Web UI Dashboard][kubernetes-dashboard]. AKS använder version 2.0 och senare av instrumentpanelen med öppen källkod.

> [!WARNING]
> **AKS-instrumentpanelens tillägg har angetts för utfasning. Använd [Kubernetes-resursvyn i Azure Portal (förhandsversion)][kubernetes-portal] i stället.** 
> * Kubernetes-instrumentpanelen är aktiverad som standard för kluster som kör en Kubernetes-version som är mindre än 1.18.
> * Instrumentpanelens tillägg inaktiveras som standard för alla nya kluster som skapas på Kubernetes 1.18 eller högre. 
 > * Från och med Kubernetes 1.19 i förhandsversionen stöder AKS inte längre installation av det hanterade kube-dashboard-tillägget. 
 > * Befintliga kluster med tillägget aktiverat påverkas inte. Användarna kommer att fortsätta att kunna installera instrumentpanelen med öppen källkod manuellt som användarinstallerad programvara.

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat `kubectl` en anslutning till klustret. Om du behöver skapa ett AKS-kluster kan du gå [till Snabbstart: Distribuera ett Azure Kubernetes Service kluster med Hjälp av Azure CLI.][aks-quickstart]

Azure CLI version 2.6.0 eller senare måste också vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Inaktivera Kubernetes-instrumentpanelen

Kube-dashboard-tillägget är aktiverat som standard på kluster **som är äldre än K8s 1.18**. Tillägget kan inaktiveras genom att köra följande kommando.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

> [!WARNING]
> AKS-instrumentpanelens tillägg är inaktuellt för version 1.19+. Använd [Kubernetes-resursvyn i Azure Portal (förhandsversion)][kubernetes-portal] i stället. 
> * Följande kommando öppnar nu Azure Portal-resursvyn i stället för Kubernetes-instrumentpanelen för version 1.19 och senare.

Om du vill starta Kubernetes-instrumentpanelen i ett kluster använder du [kommandot az aks browse.][az-aks-browse] Det här kommandot kräver installation av kube-dashboard-tillägget i klustret, vilket ingår som standard i kluster som kör en version som är äldre än Kubernetes 1.18.

I följande exempel öppnas instrumentpanelen för klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan ditt utvecklingssystem och Kubernetes-API:et och öppnar en webbläsare på Kubernetes-instrumentpanelen. Om en webbläsare inte öppnas på Kubernetes-instrumentpanelen kopierar du och klistrar in url-adressen som anges i Azure CLI, vanligtvis `http://127.0.0.1:8001` .

> [!NOTE]
> Om du inte ser instrumentpanelen på `http://127.0.0.1:8001` kan du manuellt dirigera till följande adresser. Kluster på 1.16 eller högre använder https och kräver en separat slutpunkt.
> * K8s 1.16 eller högre: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 och lägre: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Logga in på instrumentpanelen (kubernetes 1.16+)

> [!IMPORTANT]
> Från [och med v1.10.1 på Kubernetes-instrumentpanelen](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) eller kubernetes v1.16+ kan tjänstkontot "kubernetes-dashboard" inte längre användas för att hämta resurser på grund av en säkerhetskorrigering i den [versionen](https://github.com/kubernetes/dashboard/pull/3400). Därför returnerar begäranden utan autentiseringsinformation felet [401 unauthorized (obehörig).](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998) En bearer-token som hämtats från ett tjänstkonto kan fortfarande användas som i det här [Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)Dashboard-exemplet, men detta påverkar inloggningsflödet för instrumentpanelens tillägg jämfört med äldre versioner.
>
>Om du fortfarande kör en tidigare version än 1.16 kan du fortfarande ge behörighet till tjänstkontot "kubernetes-dashboard", men detta **rekommenderas inte:**
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Den första skärmen som visas kräver en kubeconfig eller token. Båda alternativen kräver resursbehörigheter för att visa resurserna på instrumentpanelen.

![inloggningsskärm](./media/kubernetes-dashboard/login.png)

**Använda en kubeconfig**

För både Azure AD-aktiverade och icke-Azure AD-aktiverade kluster kan en kubeconfig skickas in. Se till att åtkomsttoken är giltiga. Om dina token har upphört att gälla kan du uppdatera token via kubectl.

1. Ange admin kubeconfig med `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Välj `Kubeconfig` och klicka för att öppna `Choose kubeconfig file` filväljaren
1. Välj kubeconfig-filen (som standard $HOME/.kube/config)
1. Klicka på `Sign In`

**Använda en token**

1. Kör **och kopiera den token som är** associerad med klustrets användarkonto för kluster som inte är Azure `kubectl config view` AD-aktiverat.
1. Klistra in i tokenalternativet vid inloggning.    
1. Klicka på `Sign In`

För Azure AD-aktiverade kluster hämtar du din AAD-token med följande kommando. Kontrollera att du har ersatt resursgruppen och klusternamnet i kommandot .

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

När det har lyckats visas en sida som liknar nedanstående.

![Översiktssidan för Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Skapa ett program

Följande steg kräver att användaren har behörighet till respektive resurser. 

För att se hur Kubernetes-instrumentpanelen kan minska komplexiteten för hanteringsuppgifter skapar vi ett program. Du kan skapa ett program från Kubernetes-instrumentpanelen genom att ange textinmatning, en YAML-fil eller via en grafisk guide.

Skapa ett program genom att utföra följande steg:

1. Välj knappen **Skapa** i det övre högra fönstret.
1. Om du vill använda den grafiska guiden väljer du **Skapa en app.**
1. Ange ett namn för distributionen, till exempel *nginx*
1. Ange namnet på containeravbildningen som ska användas, till exempel *nginx:1.15.5*
1. Om du vill exponera port 80 för webbtrafik skapar du en Kubernetes-tjänst. Under **Tjänst** väljer du **Extern** och anger **sedan 80** för både porten och målporten.
1. När du är klar väljer **du Distribuera** för att skapa appen.

![Distribuera en app i Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/create-app.png)

Det tar någon minut att tilldela en offentlig extern IP-adress till Kubernetes-tjänsten. Till vänster under Identifiering  och belastningsutjämning väljer du **Tjänster.** Programmets tjänst visas, inklusive externa *slutpunkter*, enligt följande exempel:

![Visa lista över tjänster och slutpunkter](./media/kubernetes-dashboard/view-services.png)

Välj slutpunktsadressen för att öppna ett webbläsarfönster på NGINX-standardsidan:

![Visa NGINX-standardsidan för det distribuerade programmet](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visa poddinformation

Kubernetes-instrumentpanelen kan tillhandahålla grundläggande övervakningsmått och felsökningsinformation, till exempel loggar.

Om du vill se mer information om dina programpoddar väljer **du Poddar** i den vänstra menyn. Listan över tillgängliga poddar visas. Välj din *nginx-podd* för att visa information, till exempel resursförbrukning:

![Visa poddinformation](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och visa program kan Kubernetes-instrumentpanelen användas för att redigera och uppdatera programdistributioner. För att ge ytterligare redundans för programmet ska vi öka antalet NGINX-repliker.

Så här redigerar du en distribution:

1. Välj **Distributioner** på menyn till vänster och välj sedan din *nginx-distribution.*
1. Välj **Redigera** i det övre högra navigeringsfältet.
1. Leta upp `spec.replica` värdet runt rad 20. Om du vill öka antalet repliker för programmet ändrar du det här värdet från *1* till *3*.
1. Välj **Uppdatera när** du är klar.

![Redigera distributionen för att uppdatera antalet repliker](./media/kubernetes-dashboard/edit-deployment.png)

Det tar en stund innan de nya poddarna skapas i en replikuppsättning. På den vänstra menyn väljer du **Replikuppsättningar och** sedan din *nginx-replikuppsättning.* Listan över poddar återspeglar nu det uppdaterade replikantalet, som du ser i följande exempelutdata:

![Visa information om replikuppsättningen](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-instrumentpanelen finns i [Kubernetes Web UI Dashboard][kubernetes-dashboard].

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
[az-account-get-access-token]: /cli/azure/account#az_account_get-access-token
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
