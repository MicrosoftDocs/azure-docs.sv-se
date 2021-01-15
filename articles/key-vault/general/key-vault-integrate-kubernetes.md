---
title: Integrera Azure Key Vault med Kubernetes
description: I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av CSI-drivrutinen (hemligheter Store container Storage Interface) för att montera i Kubernetes poddar.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: f4981036ca92f6efe2d3e23ea1f507a3a1f3c70a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234264"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Självstudie: Konfigurera och kör Azure Key Vault-providern för hemligheter Store CSI-drivrutinen på Kubernetes

> [!IMPORTANT]
> CSI-drivrutinen är ett projekt med öppen källkod som inte stöds av teknisk support för Azure. Rapportera all feedback och problem som rör CSI-drivrutin Key Vault integration på github-länken [här](https://github.com/Azure/secrets-store-csi-driver-provider-azure/issues). Det här verktyget tillhandahålls för användare att själv installera i kluster och samla in feedback från vår community.


I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av CSI-drivrutinen (hemligheter Store container Storage Interface) för att montera hemligheterna i Kubernetes poddar.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Använd hanterade identiteter.
> * Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI.
> * Installera Helm och hemligheter för att lagra CSI.
> * Skapa ett Azure Key Vault och Ställ in dina hemligheter.
> * Skapa ett eget SecretProviderClass-objekt.
> * Distribuera din POD med monterade hemligheter från ditt nyckel valv.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Innan du börjar den här självstudien installerar du [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

Den här självstudien förutsätter att du köra Azure Kubernetes-tjänsten på Linux-noder.

## <a name="use-managed-identities"></a>Använda hanterade identiteter

Det här diagrammet illustrerar AKS-Key Vault-integrations flödet för hanterad identitet:

![Diagram som visar AKS – Key Vault integrations flöde för hanterad identitet](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI

Du behöver inte använda Azure Cloud Shell. Kommando tolken (Terminal) med Azure CLI är tillräckligt. 

Slutför avsnitten "skapa en resurs grupp," skapa AKS-kluster "och" Anslut till klustret "i [distribuera ett Azure Kubernetes service-kluster med hjälp av Azure CLI](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Om du planerar att använda en POD-identitet måste du aktivera den när du skapar Kubernetes-klustret, som du ser i följande kommando:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Ange miljövariabeln PATH](https://www.java.com/en/download/help/path.xml) till den *kubectl.exe* -fil som du laddade ned.
1. Kontrol lera Kubernetes-versionen med hjälp av följande kommando, som visar klienten och Server versionen. Klient versionen är den *kubectl.exe* -fil som du har installerat och Server versionen är Azure Kubernetes Services (AKS) som klustret körs på.
    ```azurecli
    kubectl version
    ```
1. Se till att din Kubernetes-version är 1.16.0 eller senare. För Windows-kluster ser du till att din Kubernetes-version är 1.18.0 eller senare. Följande kommando uppgraderar både Kubernetes-klustret och Node-poolen. Det kan ta några minuter att köra kommandot. I det här exemplet är resurs gruppen *contosoResourceGroup* och Kubernetes-klustret är *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Använd följande kommando för att visa metadata för det AKS-kluster som du har skapat. Kopiera **principalId**, **clientId**, **subscriptionId** och **nodeResourceGroup** för senare användning. Om AKS-klustret inte skapades med hanterade identiteter aktiverade, kommer **principalId** och **clientId** att vara null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Utdata visar båda parametrarna markerade:
    
    ![Skärm bild av Azure CLI med principalId-och clientId-värden markerad ](../media/kubernetes-key-vault-2.png) ![ skärm bild av Azure CLI med subscriptionId-och nodeResourceGroup-värden markerade](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installera Helm och nyckeln för hemligheter Store CSI
> [!NOTE]
> Under installationen fungerar bara på AKS i Linux. Mer information om hemligheter för att lagra CSI-drivrutiner finns i [Azure Key Vault Provider för hemligheter Store CSI-drivrutin](https://github.com/Azure/secrets-store-csi-driver-provider-azure) 

Om du vill installera CSI-drivrutinen för hemligheter-arkivet måste du först installera [Helm](https://helm.sh/docs/intro/install/).

Med [hemligheterna för CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) -drivrutinen kan du hämta de hemligheter som lagras i Azure Key Vault-instansen och sedan använda driv rutins gränssnittet för att montera hemligt innehåll i Kubernetes poddar.

1. Kontrol lera att Helm-versionen är v3 eller senare:
    ```azurecli
    helm version
    ```
1. Installera driv rutinen för hemligheter Store CSI och Azure Key Vault leverantören för driv rutinen:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Skapa ett Azure Key Vault och Ställ in dina hemligheter

Om du vill skapa ett eget nyckel valv och ange dina hemligheter följer du anvisningarna i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Du behöver inte använda Azure Cloud Shell eller skapa en ny resurs grupp. Du kan använda den resurs grupp som du skapade tidigare för Kubernetes-klustret.

## <a name="create-your-own-secretproviderclass-object"></a>Skapa ett eget SecretProviderClass-objekt

[Använd den här mallen](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml)om du vill skapa ett eget anpassat SecretProviderClass-objekt med providerspecifika parametrar för hemligheter Store CSI-drivrutinen. Det här objektet ger identitets åtkomst till ditt nyckel valv.

Fyll i de saknade parametrarna i filen sample SecretProviderClass YAML. Följande parametrar måste anges:

* **userAssignedIdentityID**: # [required] om värdet är tomt används den systemtilldelade identiteten på den virtuella datorn som standard 
* **keyvaultName**: namnet på ditt nyckel valv
* **objekt**: behållaren för allt hemligt innehåll som du vill montera
    * **objectName**: namnet på det hemliga innehållet
    * **objectType**: objekt typ (hemlighet, nyckel, certifikat)
* **resourceGroup**: namnet på resurs gruppen # [krävs för version < 0.0.4] resurs gruppen för nyckel valvet
* **subscriptionId**: PRENUMERATIONS-ID för nyckel valvet # [krävs för version < 0.0.4] PRENUMERATIONS-ID för nyckel valvet
* **tenantID**: klient-ID eller katalog-ID för nyckel valvet

Dokumentation av alla obligatoriska fält finns här: [länk](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Den uppdaterade mallen visas i följande kod. Ladda ned den som en YAML-fil och fyll i de obligatoriska fälten. I det här exemplet är nyckel valvet **contosoKeyVault5**. Det har två hemligheter, **secret1** och **secret2**.

> [!NOTE] 
> Om du använder hanterade identiteter ställer du in värdet **usePodIdentity** som *Sant* och anger **userAssignedIdentityID** -värdet som ett par citat tecken (**""**). 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED]  If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                         
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Följande bild visar konsolens utdata för **AZ-contosoKeyVault5 show--name** med relevanta markerade metadata:

![Skärm bild som visar konsolens utdata för "AZ-valv show--name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="assign-managed-identity"></a>Tilldela hanterad identitet

Tilldela vissa roller till det AKS-kluster som du har skapat. 

1. Om du vill skapa, Visa eller läsa en användardefinierad hanterad identitet måste ditt AKS-kluster tilldelas rollen [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Kontrol lera att **$clientId** är Kubernetes-klustrets clientId. För omfånget kommer den att vara under din Azure-prenumerations tjänst, särskilt den resurs grupp för noden som gjordes när AKS-klustret skapades. Det här omfånget garanterar att endast resurser inom gruppen påverkas av rollerna som tilldelas nedan. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

1. Installera Azure Active Directory (Azure AD)-identiteten i AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Skapa en Azure AD-identitet. I utdata kopierar du **clientId** och **principalId** för senare användning.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Tilldela rollen *läsare* till den Azure AD-identitet som du skapade i föregående steg för ditt nyckel valv och ge sedan identiteten behörighet att hämta hemligheter från nyckel valvet. Använd **clientId** och **PRINCIPALID** från Azure AD-identiteten.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/<SUBID>/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Distribuera din POD med monterade hemligheter från ditt nyckel valv

Kör följande kommando för att konfigurera SecretProviderClass-objektet:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Använda hanterade identiteter

Om du använder hanterade identiteter skapar du en *AzureIdentity* i klustret som refererar till den identitet som du skapade tidigare. Skapa sedan en *AzureIdentityBinding* som refererar till AzureIdentity som du skapade. Fyll i parametrarna i följande mall och spara den som *podIdentityAndBinding. yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Kör följande kommando för att köra bindningen:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Sedan distribuerar du pod. Följande kod är YAML-filen för distribution, som använder identitets bindningen Pod från föregående steg. Spara filen som *podBindingDeployment. yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Kör följande kommando för att distribuera din POD:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Kontrol lera Pod status och hemligt innehåll 

Kör följande kommando för att Visa poddar som du har distribuerat:
```azurecli
kubectl get pods
```

Kör följande kommando för att kontrol lera status för din POD:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Skärm bild av Azure CLI-utdata som visar läget "körs" för Pod och visar alla händelser som "normal" ](../media/kubernetes-key-vault-6.png)

I fönstret utdata ska den distribuerade Pod vara i *körnings* läge. I avsnittet **händelser** längst ned visas alla händelse typer som *normala*.

När du har kontrollerat att Pod körs kan du kontrol lera att Pod innehåller hemligheterna från nyckel valvet.

Om du vill visa alla hemligheter som finns i pod kör du följande kommando:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Kör följande kommando för att visa innehållet i en speciell hemlighet:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Kontrol lera att innehållet i hemligheten visas.

## <a name="next-steps"></a>Nästa steg

Information om hur du kontrollerar att nyckel valvet kan återskapas finns i:
> [!div class="nextstepaction"]
> [Aktivera mjuk borttagning](./key-vault-recovery.md)
