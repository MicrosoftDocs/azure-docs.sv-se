---
title: Distribuera en Azure Arc-datakontrollant på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du distribuerar en Azure Arc-datakontrollant och Azure-Data Services på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 53058d27e94c9fdf18d726369f6a1b75a9f34db9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567550"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Distribuera Azure Data Services på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver processen för att skapa en Azure Arc-datakontrollant och sedan distribuera Azure Data Services på din Azure Stack Edge Pro GPU-enhet. 

Azure Arc data Controller är det lokala kontroll planet som gör det möjligt för Azure Data Services i Kundhanterade miljöer. När du har skapat Azure Arc-datakontrollanten på Kubernetes-klustret som körs på din Azure Stack Edge Pro-enhet kan du Distribuera Azure Data Services som SQL-hanterad instans (för hands version) på den data styrenheten.

Proceduren för att skapa datakontrollant och sedan distribuera en SQL-hanterad instans innebär att du använder PowerShell och `kubectl` -ett inbyggt verktyg som ger kommando rads åtkomst till Kubernetes-klustret på enheten.


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en Azure Stack Edge Pro-enhet och du har aktiverat enheten enligt beskrivningen i [aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Du har aktiverat Compute-rollen på enheten. Ett Kubernetes-kluster skapades också på enheten när du konfigurerade beräkningen på enheten enligt anvisningarna i [Konfigurera Compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Du har Kubernetes API-slutpunkten från **enhets** sidan i det lokala webb gränssnittet. Mer information finns i anvisningarna i [Get KUBERNETES API-slutpunkt](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Du har åtkomst till en klient som ska ansluta till din enhet. 
    1. Den här artikeln använder ett Windows-klientcertifikat som kör PowerShell 5,0 eller senare för att få åtkomst till enheten. Du kan använda andra klienter med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Installera `kubectl` på klienten. För klient versionen:
        1. Identifiera den Kubernetes Server-version som är installerad på enheten. I enhetens lokala användar gränssnitt går du till sidan **program uppdateringar** . Notera **Kubernetes Server-versionen** på den här sidan.
        1. Ladda ned en klient som inte skiljer sig mer än en mindre version från huvudversionen. Klient versionen men kan leda till att huvud servern får en lägre version. Till exempel bör en v 1.3-huvud fungera med v 1.1-, v 1.2-och v 1.3-noder och bör fungera med v 1.2-, v 1.3-och v 1.4-klienter. Mer information om Kubernetes-klientens version finns i [support policy för Kubernetes version och versions förvrängning](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Du kan också [Installera klient verktyg för att distribuera och hantera Azure Arc-aktiverade data tjänster](../azure-arc/data/install-client-tools.md). Dessa verktyg är inte obligatoriska men rekommenderas.  
1. Se till att det finns tillräckligt med resurser på enheten för att etablera en datakontrollant och en SQL-hanterad instans. För datakontrollant och en SQL-hanterad instans behöver du minst 16 GB RAM-minne och 4 processor kärnor. Detaljerad vägledning finns i [minimi krav för Azure Arc-aktiverade data Services-distribution](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Konfigurera Kubernetes external service IP-adresser

1. Gå till det lokala webb gränssnittet för enheten och gå sedan till **Compute**.
1. Välj det nätverk som är aktiverat för beräkning. 

    ![Compute-sidan i lokalt användar gränssnitt 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Se till att du tillhandahåller tre ytterligare Kubernetes externa tjänst-IP: er (utöver de IP-adresser som du redan har konfigurerat för andra externa tjänster eller behållare). Data styrenheten använder två tjänst-IP: er och den tredje IP-adressen används när du skapar en SQL-hanterad instans. Du behöver en IP-adress för varje ytterligare data tjänst som du ska distribuera. 

    ![Compute-sidan i lokalt användar gränssnitt 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Tillämpa inställningarna och dessa nya IP-adresser börjar gälla omedelbart på ett redan befintligt Kubernetes-kluster. 


## <a name="deploy-azure-arc-data-controller"></a>Distribuera Azure Arc data Controller

Innan du distribuerar en datakontrollant måste du skapa ett namn område.

### <a name="create-namespace"></a>Skapa namnområde 

Skapa ett nytt, dedikerat namn område där du kommer att distribuera data styrenheten. Du skapar också en användare och beviljar användaren åtkomst till namn området som du skapade. 

> [!NOTE]
> För både namn områdes-och användar namn gäller [konventionerna för namngivning av DNS-underdomäner](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

1. [Anslut till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Skapa ett namnområde. Ange:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Skapa en användare. Ange: 

    `New-HcsKubernetesUser -UserName <User name>`

1. En konfigurations fil visas som oformaterad text. Kopiera den här filen och spara den som en *konfigurations* fil. 

    > [!IMPORTANT]
    > Spara inte config-filen som *. txt* -fil, spara filen utan fil namns tillägg.

1. Konfigurations filen ska finnas i `.kube` mappen i din användar profil på den lokala datorn. Kopiera filen till mappen i din användar profil.

    ![Plats för konfigurations filen på klienten](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Ge användaren åtkomst till namn området som du skapade. Ange: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Här är ett exempel på utdata från föregående kommandon. I det här exemplet skapar vi ett `myadstest` namn område, en `myadsuser` användare och beviljat användaren åtkomst till namn området.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Lägg till en DNS-post i värd filen på systemet. 

    1. Kör anteckningar som administratör och öppna `hosts` filen som finns på `C:\windows\system32\drivers\etc\hosts` . 
    2. Använd den information som du sparade från **enhets** sidan i det lokala användar gränssnittet (krav) för att skapa posten i hosts-filen. 

        Kopiera till exempel den här slut punkten `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` för att skapa följande post med IP-adressen för enheten och DNS-domänen: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Du kan kontrol lera att du kan ansluta till Kubernetes-poddar genom att starta en cmd-prompt eller en PowerShell-session. Ange:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Nu kan du distribuera data styrenheten och data tjänst programmen i namn området och sedan Visa programmen och deras loggar.

### <a name="create-data-controller"></a>Skapa datakontrollant

Datakontrollanten är en samling poddar som distribueras till ditt Kubernetes-kluster för att tillhandahålla ett API, styrenhets tjänsten, start program och övervaknings databaser och instrument paneler. Följ de här stegen för att skapa en datakontrollant i Kubernetes-klustret som finns på din Azure Stack Edge-enhet i namn området som du skapade tidigare.   

1. Samla in följande information som du behöver för att skapa en datakontrollant:

    
    |Column1  |Column2  |
    |---------|---------|
    |Data enhets namn     |Ett beskrivande namn för data kontrollen. Till exempel `arctestdatacontroller`.         |
    |Användar namn för datakontrollant     |Användar namn för administratörs användaren för datakontrollanten. Användar namn och lösen ord för datakontrollanten används för att autentisera till datakontrollantens API för att utföra administrativa funktioner.          |
    |Lösen ord för datakontrollant     |Ett lösen ord för administratörs användaren för data kontrollanten. Välj ett säkert lösen ord och dela det med bara de som behöver ha kluster administratörs behörighet.         |
    |Namn på ditt Kubernetes-namnområde     |Namnet på Kubernetes-namnområdet som du vill skapa data styrenheten i.         |
    |ID för Azure-prenumeration     |GUID för Azure-prenumerationen där du vill att data styrenhets resursen i Azure ska skapas.         |
    |Azure-resursgruppsnamn     |Namnet på den resurs grupp där du vill att data styrenhets resursen i Azure ska skapas.         |
    |Azure-plats     |Azure-platsen där data styrenhetens resurs-metadata ska lagras i Azure. En lista över tillgängliga regioner finns i Azure Global Infrastructure/products by region.|


1. Anslut till PowerShell-gränssnittet. Om du vill skapa data styrenheten skriver du: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Här är ett exempel på utdata från föregående kommandon.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Det kan ta ungefär fem minuter att slutföra distributionen.

    > [!NOTE]
    > Datakontrollanten som skapas i Kubernetes-kluster på din Azure Stack Edge Pro-enhet fungerar endast i frånkopplat läge i den aktuella versionen.

### <a name="monitor-data-creation-status"></a>Status för övervaknings data skapande

1. Öppna ett annat PowerShell-fönster.
1. Använd följande `kubectl` kommando för att övervaka skapande statusen för data styrenheten. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    När kontrollanten skapas ska statusen vara `Ready` .
    Här är ett exempel på utdata från föregående kommando:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Använd kommandot för att identifiera de IP-adresser som är tilldelade till de externa tjänsterna som körs på data styrenheten `kubectl get svc -n <namespace>` . Här är exempel på utdata:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Distribuera SQL-hanterad instans

När du har skapat data styrenheten kan du använda en mall för att distribuera en SQL-hanterad instans på data styrenheten.

### <a name="deployment-template"></a>Distributionsmall

Använd följande Distributionsmall för att distribuera en SQL-hanterad instans på data styrenheten på enheten.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Namn på metadata
    
Metadata-namnet är namnet på SQL-hanterad instans. Den associerade Pod i föregående `deployment.yaml` kommer att ha ett namn som `sqlex-n` ( `n` är antalet poddar som är associerat med programmet). 
    
#### <a name="password-and-username-data"></a>Data för lösen ord och användar namn

Användar namn och lösen ord för datakontrollanten används för att autentisera till datakontrollantens API för att utföra administrativa funktioner. Kubernetes-hemligheten för data styrenhetens användar namn och lösen ord i distributions mal len är base64-kodade strängar. 

Du kan använda ett online-verktyg för att koda ditt önskade användar namn och lösen ord, eller så kan du använda inbyggda CLI-verktyg beroende på din plattform. När du använder ett base64-kodat verktyg anger du användar namn och lösen ord (som du angav när du skapade datakontrollanten) i verktyget så skapas motsvarande base64-kodade strängar i verktyget.
    
#### <a name="service-type"></a>Typ av tjänst

Tjänst typen måste anges till `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Lagrings klass namn

Du kan identifiera lagrings klassen på Azure Stack Edge-enheten som distributionen ska använda för data, säkerhets kopior, data loggar och loggar. Använd  `kubectl get storageclass` kommandot för att hämta lagrings klassen som distribuerats på enheten.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
I föregående exempel utdata ska lagrings klassen `ase-node-local` på enheten anges i mallen.
 
#### <a name="spec"></a>Specifikationer

Om du vill skapa en SQL-hanterad instans på din Azure Stack Edge-enhet kan du ange minnes-och processor krav i avsnittet specifikation i `deployment.yaml` . Varje SQL-hanterad instans måste begära minst 2 GB minne och 1 processor kärna, vilket visas i följande exempel. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

För detaljerad storleks vägledning för datakontrollant och 1 SQL-hanterad instans granskar du [storleks informationen för SQL-hanterad instans](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Kör distributions mal len

Kör `deployment.yaml` med hjälp av följande kommando:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Här är ett exempel på utdata från följande kommando:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

`sqlex-0`Pod i exemplet visar statusen för SQL-hanterad instans.

## <a name="remove-data-controller"></a>Ta bort data styrenhet

Ta bort data kontrollen genom att ta bort det dedikerade namn området där du distribuerade det.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Nästa steg

- [Distribuera ett tillstånds lösa program på Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).