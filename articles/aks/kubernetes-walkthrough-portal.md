---
title: 'Snabb start: Distribuera ett AKS-kluster med hjälp av Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du snabbt kan skapa ett Kubernetes-kluster, distribuera ett program och övervaka prestanda i Azure Kubernetes Service (AKS) med hjälp av Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 1371fb22b3474e37e50fe0eb67541d9ced69555f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771880"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure Portal

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabb starten kommer du att:
* Distribuera ett AKS-kluster med hjälp av Azure Portal. 
* Köra ett program med flera behållare med en webb klient del och en Redis-instans i klustret. 
* Övervaka hälso tillståndet för klustret och poddar som kör ditt program.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

2. Välj **Containrar** > **Kubernetes Service**.

3. På sidan **Grunder** konfigurerar du följande alternativ:
    - **Projekt information**: 
        * Välj en Azure-**prenumeration**.
        * Välj eller skapa en Azure- **resurs grupp**, till exempel *myResourceGroup*.
    - **Kluster information**: 
        * Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*. 
        * Välj en **region** och en **Kubernetes-version** för AKS-klustret.
    - **Primär Node-pool**: 
        * Välj en **Nodstorlek** för virtuell dator för AKS-noderna. VM-storleken *kan inte* ändras efter att ett AKS-kluster har distribuerats.
        * Välj även det antal noder som ska distribueras till klustret. För den här snabbstarten ställer du in **Nodantal** till *1*. Antalet noder *kan* justeras efter att klustret har distribuerats.
    
    ![Skapa AKS-kluster – ange grundläggande information](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Välj **Nästa: Node-pooler** när du är klar.

5. Behåll standardvärdena för **Node-pooler** . Längst ned på skärmen klickar du på **Nästa: autentisering**.
    > [!CAUTION]
    > Nyligen skapade Azure AD-tjänstens huvud namn kan ta flera minuter att sprida och bli tillgängliga, vilket innebär att "tjänstens huvud namn inte hittas"-fel och validerings fel i Azure Portal. Om du når den här ojämnheten kan du gå till [vår fel söknings artikel](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) för att undvika problemet.

6. På sidan **Autentisering** konfigurerar du följande alternativ:
    - Skapa en ny kluster identitet genom att antingen:
        * Lämna fältet **Authentication** med **systemtilldelad hanterad identitet** eller
        * Välj **tjänstens huvud namn** för att använda ett huvud namn för tjänsten. 
            * Välj *(nytt) standard tjänstens huvud namn* för att skapa ett standard huvud namn för tjänsten, eller
            * Välj *Konfigurera tjänstens huvud namn* för att använda en befintlig. Du måste ange det befintliga huvud kontots SPN-klient-ID och hemlighet.
    - Aktivera alternativet Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) för att ge mer detaljerad kontroll över åtkomsten till de Kubernetes-resurser som distribueras i ditt AKS-kluster.

    Som standard används *Grundläggande* nätverk och Azure Monitor för container är aktiverat. 

7. Klicka på **Granska + skapa** och sedan **Skapa** när valideringen är klar. 


8. Det tar några minuter att skapa AKS-klustret. När distributionen är klar går du till din resurs genom att antingen:
    * Klicka på **gå till resurs** eller
    * Bläddra till kluster resurs gruppen AKS och välj AKS-resursen. 
        * Per exempel kluster instrument panel nedan: Bläddra efter *myResourceGroup* och välja *myAKSCluster* -resurs.

        ![Exempel på AKS-instrumentpanel i Azure-portalen](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du kommando rads klienten Kubernetes, [kubectl][kubectl]. `kubectl` är redan installerat om du använder Azure Cloud Shell. 

1. Öppna Cloud Shell med `>_` knappen överst i Azure Portal.

    ![Öppna Azure Cloud Shell i portalen](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Utföra dessa åtgärder i en lokal gränssnitts installation:
    > 1. Verifiera att Azure CLI är installerat.
    > 2. Anslut till Azure via `az login` kommandot.

2. Konfigurera `kubectl` för att ansluta till ditt Kubernetes-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . Följande kommando hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Kontrol lera anslutningen till klustret med hjälp av `kubectl get` för att returnera en lista över klusternoderna.

    ```console
    kubectl get nodes
    ```

    Utdata visar den enskilda noden som skapades i föregående steg. Kontrol lera att nodens status är *klar*:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Kör programmet

En Kubernetes manifest fil definierar ett klusters önskade tillstånd, till exempel vilka behållar avbildningar som ska köras. 

I den här snabb starten ska du använda ett manifest för att skapa alla objekt som behövs för att köra Azures röst program. Detta manifest innehåller två Kubernetes-distributioner:
* Azures exempel på python-program för Azure-röstning.
* En Redis-instans. 

Två Kubernetes-tjänster skapas också:
* En intern tjänst för Redis-instansen.
* En extern tjänst för att få åtkomst till Azures röst program från Internet.

1. I Cloud Shell använder du en redigerare för att skapa en fil med namnet `azure-vote.yaml` , t. ex.:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` eller  
    * `vi azure-vote.yaml`. 

1. Kopiera i följande YAML-definition:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          name: azure-vote-back
        spec:
          ports:
          - port: 6379
          selector:
            app: azure-vote-back
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: azure-vote-front
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: azure-vote-front
          template:
            metadata:
              labels:
                app: azure-vote-front
            spec:
              nodeSelector:
                "beta.kubernetes.io/os": linux
              containers:
              - name: azure-vote-front
                image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
                resources:
                  requests:
                    cpu: 100m
                    memory: 128Mi
                  limits:
                    cpu: 250m
                    memory: 256Mi
                ports:
                - containerPort: 80
                env:
                - name: REDIS
                  value: "azure-vote-back"
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: azure-vote-front
        spec:
          type: LoadBalancer
          ports:
          - port: 80
          selector:
            app: azure-vote-front
    ```

1. Distribuera programmet med `kubectl apply` kommandot och ange namnet på ditt yaml-manifest:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Utdata visar de distributioner och tjänster som har skapats:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Om du vill övervaka förloppet använder du `kubectl get service` kommandot med `--watch` argumentet.

```console
kubectl get service azure-vote-front --watch
```

Utdata från den **externa IP-adressen** för `azure-vote-front` tjänsten visas i början som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När den **externa IP** -adressen ändras från *väntande* till en verklig offentlig IP-adress, använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Övervaka hälsotillstånd och loggar

När du skapade klustret aktiverades Azure Monitor för container. Azure Monitor för behållare tillhandahåller hälso mått för både AKS-klustret och poddar som körs i klustret.

Mått data tar några minuter att fylla i Azure Portal. Så här visar du aktuell hälso status, drift tid och resursanvändning för Azures röst poddar:

1. Gå tillbaka till AKS-resursen i Azure Portal.
1. Under **övervakning** till vänster väljer du **insikter**.
1. Välj till **+ Lägg till filter** högst upp.
1. Välj **namn område** som egenskap och välj sedan *\<All but kube-system\>* .
1. Välj **behållare** för att visa dem.

`azure-vote-back`-Och `azure-vote-front` -behållare visas, som du ser i följande exempel:

![Visa hälsan för containrar som körs i AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Om du vill visa loggar för `azure-vote-front` Pod väljer du **Visa behållar loggar** i list rutan behållare lista. Dessa loggar inkluderar *STDOUT* -och *stderr* -strömmar från behållaren.

![Visa containerloggarna i AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Ta bort klustret

Ta bort onödiga resurser för att undvika Azure-avgifter. Välj knappen **ta bort** på instrument panelen för AKS-klustret. Du kan också använda kommandot [AZ AKS Delete][az-aks-delete] i Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

Befintliga behållar avbildningar användes i den här snabb starten för att skapa en Kubernetes-distribution. Den relaterade program koden, Dockerfile och Kubernetes manifest filen [finns på GitHub.][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett Kubernetes-kluster och sedan distribuerat ett program med flera behållare till det. Få åtkomst till Kubernetes-webbinstrumentpanelen för ditt AKS-kluster.


Om du vill veta mer om AKS genom att gå igenom ett komplett exempel, till exempel skapa ett program, distribuera från Azure Container Registry, uppdatera ett program som körs och skala och uppgradera klustret, fortsätter du till självstudien för Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations