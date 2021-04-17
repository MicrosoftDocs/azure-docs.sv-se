---
title: Distribuera en Azure Arc datastyrenhet på din AZURE STACK EDGE PRO GPU-enhet| Microsoft Docs
description: Beskriver hur du distribuerar en Azure Arc Data Controller och Azure Data Services på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568615"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Distribuera Azure Data Services på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du skapar en Azure Arc datakontrollant och sedan distribuerar Azure Data Services på din Azure Stack Edge Pro GPU-enhet. 

Azure Arc Data Controller är det lokala kontrollplanet som möjliggör Azure-Data Services i kund-hanterade miljöer. När du har skapat Azure Arc-datakontrollanten på Kubernetes-klustret som körs på din Azure Stack Edge Pro GPU-enhet kan du distribuera Azure Data Services, till exempel SQL Managed Instance (förhandsversion) på den datastyrenheten.

Proceduren för att skapa en datakontrollant och sedan distribuera en SQL Managed Instance omfattar användning av PowerShell och – ett inbyggt verktyg som ger kommandoradsåtkomst till `kubectl` Kubernetes-klustret på enheten.


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en Azure Stack Edge Pro GPU-enhet och du har aktiverat enheten enligt beskrivningen i [Aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Du har aktiverat beräkningsrollen på enheten. Ett Kubernetes-kluster skapades också på enheten när du konfigurerade beräkning på enheten enligt anvisningarna i Konfigurera beräkning på din [Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Du har Kubernetes API-slutpunkten från **enhetssidan** för ditt lokala webbgränssnitt. Mer information finns i anvisningarna i Hämta [Kubernetes API-slutpunkt.](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)

1. Du har åtkomst till en klient som ska ansluta till enheten. 
    1. Den här artikeln använder ett Windows-klientsystem som kör PowerShell 5.0 eller senare för att få åtkomst till enheten. Du kan använda vilken annan klient som helst med [ett operativsystem som stöds.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Installera `kubectl` på klienten. För klientversionen:
        1. Identifiera vilken Kubernetes-serverversion som är installerad på enheten. I enhetens lokala användargränssnitt går du till **sidan Programuppdateringar.** Observera **Kubernetes-serverversionen på** den här sidan.
        1. Ladda ned en klient som inte skiljer sig mer än en mindre version från huvudversionen. Klientversionen men kan leda huvudversionen av upp till en delversion. Till exempel ska en v1.3-huvudnod fungera med noderna v1.1, v1.2 och v1.3 och fungera med v1.2-, v1.3- och v1.4-klienter. Mer information om Kubernetes-klientversionen finns i [Stödprincip för Kubernetes-version och versionsskevningar.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)
    
1. Du kan också [installera klientverktyg för att distribuera och hantera Azure Arc-aktiverade datatjänster](../azure-arc/data/install-client-tools.md). De här verktygen krävs inte men rekommenderas.  
1. Kontrollera att du har tillräckligt med resurser tillgängliga på enheten för att etablera en datakontrollant och en SQL Managed Instance. För datastyrenheten och en SQL Managed Instance behöver du minst 16 GB RAM-minne och 4 CPU-kärnor. Detaljerad information finns i Minimikrav [för Azure Arc-aktiverade datatjänster distribution.](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)


## <a name="configure-kubernetes-external-service-ips"></a>Konfigurera kubernetes externa tjänst-IP-adresser

1. Gå till enhetens lokala webbgränssnitt och gå sedan till **Compute**.
1. Välj det nätverk som är aktiverat för beräkning. 

    ![Beräkningssida i lokalt användargränssnitt 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Se till att du anger ytterligare tre externa Kubernetes-tjänst-IP-adresser (utöver de IP-adresser som du redan har konfigurerat för andra externa tjänster eller containrar). Datakontrollanten använder två tjänst-IP-adresser och den tredje IP-adressen används när du skapar en SQL Managed Instance. Du behöver en IP-adress för varje ytterligare datatjänst som du ska distribuera. 

    ![Beräkningssida i lokalt användargränssnitt 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Tillämpa inställningarna så börjar dessa nya IP-adresser omedelbart gälla för ett redan befintligt Kubernetes-kluster. 


## <a name="deploy-azure-arc-data-controller"></a>Distribuera Azure Arc datakontrollant

Innan du distribuerar en datakontrollant måste du skapa ett namnområde.

### <a name="create-namespace"></a>Skapa namnområde 

Skapa en ny, dedikerad namnrymd där du ska distribuera datakontrollanten. Du skapar också en användare och ger sedan användaren åtkomst till det namnområde som du skapade. 

> [!NOTE]
> Namngivningskonventionerna för DNS-underdomäner gäller för [både namnområde och](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) användarnamn.

1. [Anslut till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Skapa ett namnområde. Ange:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Skapa en användare. Ange: 

    `New-HcsKubernetesUser -UserName <User name>`

1. En konfigurationsfil visas i oformaterad text. Kopiera den här filen och spara den som *en konfigurationsfil.* 

    > [!IMPORTANT]
    > Spara inte konfigurationsfilen som *.txt-fil,* spara filen utan filnamnstillägg.

1. Konfigurationsfilen ska finnas i mappen `.kube` för din användarprofil på den lokala datorn. Kopiera filen till den mappen i din användarprofil.

    ![Plats för konfigurationsfilen på klienten](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Ge användaren åtkomst till det namnområde som du skapade. Ange: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Här är ett exempel på utdata från föregående kommandon. I det här exemplet skapar vi `myadstest` ett namnområde, en `myadsuser` användare och ger användaren åtkomst till namnområdet.
    
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
1. Lägg till en DNS-post i värdfilen på datorn. 

    1. Kör Anteckningar som administratör och öppna filen `hosts` som finns på `C:\windows\system32\drivers\etc\hosts` . 
    2. Använd den information som du sparade från **sidan Enhet** i det lokala användargränssnittet (krav) för att skapa posten i värdfilen. 

        Kopiera till exempel den här slutpunkten `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` för att skapa följande post med enhetens IP-adress och DNS-domän: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Kontrollera att du kan ansluta till Kubernetes-poddarna genom att starta en kommandotolk eller en PowerShell-session. Ange:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Nu kan du distribuera dina datastyrenhets- och datatjänstprogram i namnområdet och sedan visa programmen och deras loggar.

### <a name="create-data-controller"></a>Skapa datakontrollant

Datastyrenheten är en samling poddar som distribueras till kubernetes-klustret för att tillhandahålla ett API, kontrollanttjänsten, bootstrapper samt övervakningsdatabaser och instrumentpaneler. Följ dessa steg för att skapa en datakontrollant i Kubernetes-klustret som finns på Azure Stack Edge i namnområdet som du skapade tidigare.   

1. Samla in följande information som du behöver för att skapa en datakontrollant:

    
    |Column1  |Column2  |
    |---------|---------|
    |Namn på datakontrollant     |Ett beskrivande namn för datakontrollanten. Till exempel `arctestdatacontroller`.         |
    |Användarnamn för datastyrenhet     |Alla användarnamn för datakontrollantens administratörsanvändare. Användarnamnet och lösenordet för datakontrollanten används för att autentisera till API:et för datakontrollant för att utföra administrativa funktioner.          |
    |Lösenord för datastyrenhet     |Ett lösenord för datakontrollantens administratörsanvändare. Välj ett säkert lösenord och dela det med endast de som behöver ha administratörsbehörighet för klustret.         |
    |Namnet på kubernetes-namnområdet     |Namnet på det Kubernetes-namnområde som du vill skapa datakontrollanten i.         |
    |Azure-prenumerations-ID     |GUID för Azure-prenumerationen där du vill att datakontrollantresursen i Azure ska skapas.         |
    |Azure-resursgruppsnamn     |Namnet på resursgruppen där du vill att datakontrollantresursen i Azure ska skapas.         |
    |Azure-plats     |Azure-platsen där datakontrollantens resursmetadata kommer att lagras i Azure. En lista över tillgängliga regioner finns i Global Azure-infrastruktur/Produkter efter region.|


1. Anslut till PowerShell-gränssnittet. Skapa datakontrollanten genom att skriva: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Här är ett exempel på utdata från föregående kommandon.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Distributionen kan ta cirka 5 minuter att slutföra.

    > [!NOTE]
    > Den datakontrollant som skapats i Kubernetes-klustret Azure Stack Edge Pro GPU-enheten fungerar bara i frånkopplat läge i den aktuella versionen. Det frånkopplade läget är för datastyrenheten och inte för din enhet.

### <a name="monitor-data-creation-status"></a>Övervaka status för dataskapande

1. Öppna ett annat PowerShell-fönster.
1. Använd följande kommando `kubectl` för att övervaka datastyrenhetens skapandestatus. 

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
1. Om du vill identifiera IP-adresser som tilldelats till externa tjänster som körs på datakontrollanten använder du `kubectl get svc -n <namespace>` kommandot . Här är exempel på utdata:

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

## <a name="deploy-sql-managed-instance"></a>Distribuera sql-hanterad instans

När du har skapat datakontrollanten kan du använda en mall för att distribuera SQL Managed Instance på datakontrollanten.

### <a name="deployment-template"></a>Distributionsmall

Använd följande distributionsmall för att distribuera SQL Managed Instance på datastyrenheten på enheten.

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


#### <a name="metadata-name"></a>Metadatanamn
    
Metadatanamnet är namnet på SQL Managed Instance. Den associerade podden i föregående `deployment.yaml` är namn som ( är antalet `sqlex-n` `n` poddar som är associerade med programmet). 
    
#### <a name="password-and-username-data"></a>Lösenords- och användarnamnsdata

Användarnamnet och lösenordet för datastyrenheten används för att autentisera till API:et för datakontrollant för att utföra administrativa funktioner. Kubernetes-hemligheten för datakontrollantens användarnamn och lösenord i distributionsmallen är base64-kodade strängar. 

Du kan använda ett onlineverktyg för att base64-koda önskat användarnamn och lösenord eller så kan du använda inbyggda CLI-verktyg beroende på din plattform. När du använder ett Base64-kodverktyg online anger du användarnamnet och lösenordssträngarna (som du angav när du skapade datakontrollanten) i verktyget så genererar verktyget motsvarande Base64-kodade strängar.
    
#### <a name="service-type"></a>Typ av tjänst

Tjänsttypen ska vara inställd på `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Lagringsklassnamn

Du kan identifiera lagringsklassen på din Azure Stack Edge som distributionen kommer att använda för data, säkerhetskopior, dataloggar och loggar. Använd kommandot  `kubectl get storageclass` för att distribuera lagringsklassen på enheten.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
I föregående exempelutdata ska `ase-node-local` lagringsklassen på enheten anges i mallen.
 
#### <a name="spec"></a>Spec

Om du vill SQL Managed Instance på Azure Stack Edge enhet kan du ange dina minne- och cpu-krav i specifikationsavsnittet i `deployment.yaml` . Varje hanterad SQL-instans måste begära minst 2 GB minne och 1 PROCESSORkärna, som du ser i följande exempel. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Detaljerad storleksvägledning för datakontrollant och 1 SQL Managed Instance finns i [storleksinformationen för SQL Managed Instance.](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)

### <a name="run-deployment-template"></a>Kör distributionsmallen

Kör med `deployment.yaml` följande kommando:

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

Podden `sqlex-0` i exempelutdata visar status för SQL Managed Instance.

## <a name="remove-data-controller"></a>Ta bort datakontrollant

Ta bort datakontrollanten genom att ta bort det dedikerade namnområdet där du distribuerade den.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Nästa steg

- [Distribuera ett tillståndslöst program på din Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
