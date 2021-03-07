---
title: Aktivera Azure Arc på Kubernetes på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du aktiverar Azure Arc på ett befintligt Kubernetes-kluster på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 4d75986880075f6eb07aa31b9322bdae15535802
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437612"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivera Azure Arc på Kubernetes-kluster på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln visar hur du aktiverar Azure Arc på ett befintligt Kubernetes-kluster på din Azure Stack Edge Pro-enhet. 

Den här proceduren är avsedd för de som har granskat [Kubernetes-arbetsbelastningar på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-workload-management.md) och som är bekanta med begreppen [Vad är Azure Arc Enabled Kubernetes (för hands version)?](../azure-arc/kubernetes/overview.md).


## <a name="prerequisites"></a>Förutsättningar

Innan du kan aktivera Azure Arc på Kubernetes-kluster måste du kontrol lera att du har slutfört följande krav för din Azure Stack Edge Pro-enhet och klienten som du ska använda för att få åtkomst till enheten:

### <a name="for-device"></a>För enheten

1. Du har inloggnings uppgifter till en 1-nod Azure Stack Edge Pro-enhet.
    1. Enheten är aktive rad. Se [Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
    1. Enheten har den beräknings roll som kon figurer ATS via Azure Portal och har ett Kubernetes-kluster. Se [Konfigurera Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Du har ägar åtkomst till prenumerationen. Du behöver den här åtkomsten under steget roll tilldelning för tjänstens huvud namn.
 

### <a name="for-client-accessing-the-device"></a>För klient åtkomst till enheten

1. Du har ett Windows-klientsystem som ska användas för att få åtkomst till Azure Stack Edge Pro-enheten.
  
    - Klienten kör Windows PowerShell 5,0 eller senare. Om du vill hämta den senaste versionen av Windows PowerShell går du till [Installera Windows PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Du kan också ha andra klienter med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Den här artikeln beskriver proceduren när du använder en Windows-klient. 
    
1. Du har slutfört proceduren som beskrivs i [komma åt Kubernetes-klustret på Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md). Du har:
    
    - Installerat `kubectl` på klienten.    
    - Kontrol lera att `kubectl` klient versionen inte är mer än en version från den Kubernetes huvud version som körs på din Azure Stack Edge Pro-enhet. 
      - Används `kubectl version` för att kontrol lera vilken version av kubectl som körs på klienten. Anteckna den fullständiga versionen.
      - I det lokala användar gränssnittet för din Azure Stack Edge Pro-enhet går du till **program uppdatering** och noterar Kubernetes-serverns versions nummer. 
    
        ![Verifiera Kubernetes-serverns versions nummer](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Kontrol lera att dessa två versioner är kompatibla. 


## <a name="register-kubernetes-resource-providers"></a>Registrera Kubernetes-resurs leverantörer

Innan du aktiverar Azure Arc i Kubernetes-klustret måste du aktivera och registrera `Microsoft.Kubernetes` och `Microsoft.KubernetesConfiguration` mot din prenumeration. 

1. Om du vill aktivera en resurs leverantör går du till den prenumeration som du planerar att använda för distributionen i Azure Portal. Gå till **Resource providers**. 
1. Sök efter de providrar som du vill lägga till i den högra rutan. I det här exemplet `Microsoft.Kubernetes` och `Microsoft.KubernetesConfiguration` .

    ![Registrera Kubernetes-resurs leverantörer](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Välj en resurs leverantör och överst i kommando fältet väljer du **Registrera**. Registreringen tar flera minuter. 

    ![Registrera Kubernetes Resource providers 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Uppdatera användar gränssnittet tills du ser att resurs leverantören är registrerad. Upprepa processen för båda resurs leverantörerna.
    
    ![Registrera Kubernetes Resource providers 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Du kan också registrera resurs leverantörer via `az cli` . Mer information finns i [registrera två providers för Azure Arc-aktiverade Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Skapa tjänstens huvud namn, tilldela roll

1. Kontrol lera att du har `Subscription ID` och namnet på den resurs grupp som du använde för resurs distributionen för din Azure Stack Edge-tjänst. Hämta prenumerations-ID genom att gå till din Azure Stack Edge-resurs i Azure Portal. Gå till **översikt > Essentials**.

    ![Hämta prenumerations-ID](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Hämta resurs gruppens namn genom att gå till **Egenskaper**.

    ![Hämta resurs grupp namn](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Om du vill skapa ett huvud namn för tjänsten använder du följande kommando via `az cli` .

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    För information om hur du loggar in på `az cli` [startar du Cloud Shell i Azure Portal](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Här är ett exempel. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Anteckna `appID` ,, `name` `password` och `tenantID` som du kommer att använda den som inmatare i nästa kommando.

1. När du har skapat det nya huvud namnet för tjänsten tilldelar du `Kubernetes Cluster - Azure Arc Onboarding` rollen till det nyligen skapade huvud kontot. Det här är en inbyggd Azure-roll (Använd roll-ID: t i kommandot) med begränsad behörighet. Ange följande kommando:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Här är ett exempel.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Mer information om hur du skapar tjänstens huvud namn och utför roll tilldelningen finns i stegen i [skapa en Azure Arc-aktiverad onboarding-tjänstens huvud namn](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Aktivera Arc på Kubernetes-kluster

Följ dessa steg för att konfigurera Kubernetes-klustret för hantering av Azure-båge:

1. [Anslut till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) på enheten.

1. Ange:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Om du vill distribuera Azure-bågen på enheten kontrollerar du att du använder en [region som stöds för Azure-bågen](../azure-arc/kubernetes/overview.md#supported-regions). 
    > - Använd `az account list-locations` kommandot för att ta reda på det exakta plats namnet som ska skickas i- `Set-HcsKubernetesAzureArcAgent` cmdleten. Plats namn är vanligt vis formaterade utan blank steg.
    
    Här är ett exempel:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    I Azure Portal ska en resurs skapas med det namn som du angav i föregående kommando.

    ![Gå till Azure Arc-resurs](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Kontrol lera att Azure-bågen har Aktiver ATS genom att köra följande kommando från PowerShell-gränssnittet:

    `kubectl get deployments -n azure-arc`

    Det här kommandot hittar alla program som har distribuerats i `azure-arc` namn området som motsvarar Azure-bågen.

    Här är ett exempel på utdata som visar de Azure Arc-agenter som har distribuerats på ditt Kubernetes-kluster i `azure-arc` namn området. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Du kan också hämta en lista över poddar som körs på ditt Kubernetes-kluster i `azure-arc` namn området. En pod är en program behållare eller process som körs på ditt Kubernetes-kluster. 

    Ange följande kommando:
    
    `kubectl get pods -n azure-arc`
    
    Här är ett exempel på utdata.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


När föregående utdata visas, består Azure Arc-aktiverade Kubernetes av några agenter (operatörer) som körs i ditt kluster och som har distribuerats till `azure-arc` namn området.

- `config-agent`: bevakar det anslutna klustret för käll kontroll konfigurations resurser som tillämpas på klustret och uppdaterar kompatibilitetstillstånd
- `controller-manager`: är en operatör av operatörer och dirigerar interaktioner mellan Azure båg-komponenter
- `metrics-agent`: samlar in Mät värden för andra Arc-agenter för att säkerställa att dessa agenter visar optimala prestanda
- `cluster-metadata-operator`: samlar in kluster-metadata-kluster version, antal noder och Azure Arc agent-version
- `resource-sync-agent`: synkroniserar ovanstående klustrade metadata till Azure
- `clusteridentityoperator`: Azure Arc-aktiverad Kubernetes har för närvarande stöd för systemtilldelad identitet. clusteridentityoperator upprätthåller det hanterade tjänst identitets certifikatet (MSI) som används av andra agenter för kommunikation med Azure.
- `flux-logs-agent`: samlar in loggar från flödes operatörer som distribuerats som en del av käll kontroll konfigurationen.
- `connect-agent`: pratar med Azure Arc-resursen.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Ta bort bågen från Kubernetes-klustret

Följ dessa steg om du vill ta bort hanteringen av Azure-bågen:

1. 1. [Anslut till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) på enheten.
2. Ange:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> När resursen `yamls` tas bort från git-lagringsplatsen tas som standard inte de motsvarande resurserna bort från Kubernetes-klustret. Du måste ställa in `--sync-garbage-collection`  i Arc-OperatorParams för att tillåta borttagning av resurser när de tas bort från git-lagringsplatsen. Mer information finns i [ta bort en konfiguration](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>Nästa steg

Information om hur du kör en Azure Arc-distribution finns i [distribuera ett tillstånds löst php- `Guestbook` program med Redis via GitOps på en Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
