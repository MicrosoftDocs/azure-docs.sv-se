---
title: 'Snabbstart: Distribuera ett AKS-kluster med hjälp av Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du snabbt kan skapa ett Kubernetes-kluster, distribuera ett program och övervaka prestanda i Azure Kubernetes Service (AKS) med hjälp av Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779713"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Snabbstart: Distribuera ett Azure Kubernetes Service(AKS)-kluster med hjälp av Azure Portal

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabbstarten kommer du att:
* Distribuera ett AKS-kluster med hjälp av Azure Portal. 
* Kör ett program med flera containrar med en webb-frontend och en Redis-instans i klustret. 
* Övervaka hälsotillståndet för klustret och poddar som kör ditt program.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

2. Välj **Containrar** > **Kubernetes Service**.

3. På sidan **Grunder** konfigurerar du följande alternativ:
    - **Projektinformation:** 
        * Välj en Azure-**prenumeration**.
        * Välj eller skapa en **Azure-resursgrupp,** till exempel *myResourceGroup*.
    - **Klusterinformation:** 
        * Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*. 
        * Välj en **Region** och **En Kubernetes-version** för AKS-klustret.
    - **Primär nodpool:** 
        * Välj en **Nodstorlek** för virtuell dator för AKS-noderna. VM-storleken *kan inte* ändras efter att ett AKS-kluster har distribuerats.
        * Välj även det antal noder som ska distribueras till klustret. För den här snabbstarten ställer du in **Nodantal** till *1*. Antalet noder *kan* justeras efter att klustret har distribuerats.
    
    ![Skapa AKS-kluster – ange grundläggande information](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Välj **Nästa: Nodpooler** när du är klar.

5. Behåll standardalternativen **för Nodpooler.** Klicka på Nästa: Autentisering längst **ned på skärmen.**
    > [!CAUTION]
    > Det kan ta flera minuter för nyligen skapade Azure AD-tjänsthuvudnamn att spridas och bli tillgängliga, vilket orsakar "det gick inte att hitta tjänstens huvudnamn" och verifieringsfel i Azure Portal. Om du stöter på den här guppen kan du [gå till vår felsökningsartikel](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) för att åtgärda problemet.

6. På sidan **Autentisering** konfigurerar du följande alternativ:
    - Skapa en ny klusteridentitet genom att antingen:
        * Lämna fältet **Autentisering** med **System-assinged managed identity (System-assinged hanterad identitet)** eller
        * Välja **tjänstens huvudnamn** för att använda tjänstens huvudnamn. 
            * Välj *(nytt) standardtjänsthuvudnamn för* att skapa ett standardobjekt för tjänsten, eller
            * Välj *Konfigurera tjänstens huvudnamn* för att använda ett befintligt. Du måste ange det befintliga huvudnamns SPN-klient-ID och -hemlighet.
    - Aktivera kubernetes-alternativet för rollbaserad åtkomstkontroll (Kubernetes RBAC) för att ge mer fullständig kontroll över åtkomsten till Kubernetes-resurser som distribueras i ditt AKS-kluster.

    Som standard används *Grundläggande* nätverk och Azure Monitor för container är aktiverat. 

7. Klicka på **Granska + skapa** och sedan **Skapa** när valideringen är klar. 


8. Det tar några minuter att skapa AKS-klustret. När distributionen är klar navigerar du till resursen genom att antingen:
    * Klicka **på Gå till resurs**, eller
    * Bläddra till AKS-klusterresursgruppen och välja AKS-resursen. 
        * Klusterinstrumentpanelen per exempel nedan: bläddrar *efter myResourceGroup* och väljer *myAKSCluster-resurs.*

        ![Exempel på AKS-instrumentpanel i Azure-portalen](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du Kubernetes-kommandoradsklienten [kubectl][kubectl]. `kubectl` har redan installerats om du använder Azure Cloud Shell. 

1. Öppna Cloud Shell med `>_` knappen längst upp i Azure Portal.

    ![Öppna Azure Cloud Shell i portalen](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Så här utför du dessa åtgärder i en lokal shell-installation:
    > 1. Kontrollera att Azure CLI är installerat.
    > 2. Anslut till Azure via `az login` kommandot .

2. Konfigurera `kubectl` för att ansluta till kubernetes-klustret med kommandot az [aks get-credentials.][az-aks-get-credentials] Följande kommando laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifiera anslutningen till klustret med för `kubectl get` att returnera en lista över klusternoderna.

    ```console
    kubectl get nodes
    ```

    Utdata visar den enda nod som skapades i föregående steg. Kontrollera att nodstatusen är *Klar:*

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Kör programmet

En Kubernetes-manifestfil definierar ett klusters önskade tillstånd, som vilka containeravbildningar som ska köras. 

I den här snabbstarten använder du ett manifest för att skapa alla objekt som behövs för att köra Azure Vote-programmet. Det här manifestet innehåller två Kubernetes-distributioner:
* Python-exempelprogrammen för Azure Vote.
* En Redis-instans. 

Två Kubernetes-tjänster skapas också:
* En intern tjänst för Redis-instansen.
* En extern tjänst för att få åtkomst till Azure Vote-programmet från Internet.

1. I Cloud Shell du en redigerare för att skapa en fil med `azure-vote.yaml` namnet , till exempel:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` eller  
    * `vi azure-vote.yaml`. 

1. Kopiera följande YAML-definition:

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
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
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

1. Distribuera programmet med kommandot `kubectl apply` och ange namnet på ditt YAML-manifest:

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

Om du vill övervaka förloppet `kubectl get service` använder du kommandot med argumentet `--watch` .

```console
kubectl get service azure-vote-front --watch
```

**EXTERNAL-IP-utdata** för `azure-vote-front` tjänsten visas inledningsvis som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När **EXTERNAL-IP-adressen** ändras från *väntande till* en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl` bevakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Övervaka hälsotillstånd och loggar

När du skapade klustret aktiverades Azure Monitor för container. Azure Monitor för containrar tillhandahåller hälsomått för både AKS-klustret och poddar som körs i klustret.

Det tar några minuter att fylla i måttdata i Azure Portal. Så här visar du aktuell hälsostatus, drifttid och resursanvändning för Azure Vote-poddarna:

1. Bläddra tillbaka till AKS-resursen i Azure Portal.
1. Under **Övervakning** till vänster väljer du **Insights**.
1. Längst upp väljer du + **Lägg till filter**.
1. Välj **Namnområde** som egenskap och välj sedan *\<All but kube-system\>* .
1. Välj **Containrar** för att visa dem.

Containrarna `azure-vote-back` `azure-vote-front` och visas, som du ser i följande exempel:

![Visa hälsan för containrar som körs i AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Om du vill visa loggar för `azure-vote-front` podden väljer **du Visa containerloggar** i listrutan för containrar. Loggarna innehåller *strömmarna stdout* *och stderr* från containern.

![Visa containerloggarna i AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Ta bort klustret

Du kan undvika Azure-avgifter genom att rensa bort onödiga resurser. Välj knappen **Ta bort** på Instrumentpanelen för AKS-klustret. Du kan också använda [kommandot az aks delete][az-aks-delete] i Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

Befintliga containeravbildningar användes i den här snabbstarten för att skapa en Kubernetes-distribution. Relaterad programkod, Dockerfile och Kubernetes-manifestfil [finns på GitHub.][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du ett Kubernetes-kluster och distribuerade sedan ett program med flera containrar till det. Öppna Kubernetes-webbinstrumentpanelen för ditt AKS-kluster.


Om du vill veta mer om AKS genom att gå igenom ett komplett exempel, inklusive att skapa ett program, distribuera från Azure Container Registry, uppdatera ett program som körs och skala och uppgradera klustret, fortsätter du till självstudien om Kubernetes-kluster.

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations