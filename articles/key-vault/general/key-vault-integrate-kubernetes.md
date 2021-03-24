---
title: Integrera Azure Key Vault med Kubernetes
description: I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av CSI-drivrutinen (hemligheter Store container Storage Interface) för att montera i Kubernetes poddar.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b83905280faf4486d2febfbd81514e1d85273ad5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950764"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Självstudie: Konfigurera och kör Azure Key Vault-providern för hemligheter Store CSI-drivrutinen på Kubernetes

> [!IMPORTANT]
> Hemligheter för att lagra CSI är ett projekt med öppen källkod som inte stöds av teknisk support för Azure. Rapportera all feedback och problem som rör CSI-drivrutinen Key Vault-integrering på CSI-drivrutinen [GitHub](https://github.com/kubernetes-sigs/secrets-store-csi-driver). Det här verktyget tillhandahålls för användare att själv installera i kluster och samla in feedback från vår community.

I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av CSI-drivrutinen (hemligheter Store container Storage Interface) för att montera hemligheterna i Kubernetes poddar som en volym.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd hanterade identiteter.
> * Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI.
> * Installera Helm, hemligheter lagrar CSI-drivrutinen och Azure Key Vault leverantören för CSI-drivrutinen.
> * Skapa ett Azure Key Vault och Ställ in dina hemligheter.
> * Skapa ett eget SecretProviderClass-objekt.
> * Distribuera din POD med monterade hemligheter från ditt nyckel valv.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Innan du börjar den här självstudien installerar du [Azure CLI](/cli/azure/install-azure-cli-windows).

I den här självstudien förutsätter vi att du kör Azure Kubernetes service på Linux-noder.

## <a name="use-managed-identities"></a>Använda hanterade identiteter

Det här diagrammet illustrerar AKS-Key Vault-integrations flödet för hanterad identitet:

![Diagram som visar AKS – Key Vault integrations flöde för hanterad identitet](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI

Du behöver inte använda Azure Cloud Shell. Kommando tolken (Terminal) med Azure CLI är tillräckligt. 

Slutför avsnitten "skapa en resurs grupp," skapa AKS-kluster "och" Anslut till klustret "i [distribuera ett Azure Kubernetes service-kluster med hjälp av Azure CLI](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Om du planerar att använda Pod identitet, är det rekommenderade nätverks-plugin-programmet `azure` . Mer information finns i [doc](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) . Skapa Kubernetes-klustret, som du ser i följande kommando:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Ange miljövariabeln PATH](https://www.java.com/en/download/help/path.xml) till den *kubectl.exe* -fil som du laddade ned.
2. Kontrol lera Kubernetes-versionen med hjälp av följande kommando, som visar klienten och Server versionen. Klient versionen är den *kubectl.exe* -fil som du har installerat och Server versionen är Azure Kubernetes Services (AKS) som klustret körs på.
    ```azurecli
    kubectl version
    ```
3. Se till att din Kubernetes-version är 1.16.0 eller senare. För Windows-kluster ser du till att din Kubernetes-version är 1.18.0 eller senare. Följande kommando uppgraderar både Kubernetes-klustret och Node-poolen. Det kan ta några minuter att köra kommandot. I det här exemplet är resurs gruppen *contosoResourceGroup* och Kubernetes-klustret är *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Använd följande kommando för att visa metadata för det AKS-kluster som du har skapat. Kopiera **principalId**, **clientId**, **subscriptionId** och **nodeResourceGroup** för senare användning. Om AKS-klustret inte skapades med hanterade identiteter aktiverade, kommer **principalId** och **clientId** att vara null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Utdata visar båda parametrarna markerade:
    
    ![Skärm bild av Azure CLI med principalId-och clientId-värden markerad ](../media/kubernetes-key-vault-2.png) ![ skärm bild av Azure CLI med subscriptionId-och nodeResourceGroup-värden markerade](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installera Helm och nyckeln för hemligheter Store CSI
> [!NOTE]
> Under installationen fungerar bara på AKS i Linux. Mer information om hemligheter för att lagra CSI-drivrutiner finns i [Azure Key Vault Provider för hemligheter Store CSI-drivrutin](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

Om du vill installera CSI-drivrutinen för hemligheter och Azure Key Vault providern måste du först installera [Helm](https://helm.sh/docs/intro/install/).

Med [hemligheterna för CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/) -drivrutinen kan du hämta de hemligheter som lagras i Azure Key Vault-instansen och sedan använda driv rutins gränssnittet för att montera hemligt innehåll i Kubernetes poddar.

1. Kontrol lera att Helm-versionen är v3 eller senare:
    ```azurecli
    helm version
    ```
1. Installera driv rutinen för hemligheter Store CSI och Azure Key Vault leverantören för driv rutinen:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Om du planerar att använda hemligheter för att lagra CSI-drivrutinen och Azure Key Vault providern på Windows-noder, aktiverar du driv rutinen och providern på Windows-noder med hjälp av [konfigurations värden för Helm](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration)

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Skapa ett Azure Key Vault och Ställ in dina hemligheter

Om du vill skapa ett eget nyckel valv och ange dina hemligheter följer du anvisningarna i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Du behöver inte använda Azure Cloud Shell eller skapa en ny resurs grupp. Du kan använda den resurs grupp som du skapade tidigare för Kubernetes-klustret.

## <a name="create-your-own-secretproviderclass-object"></a>Skapa ett eget SecretProviderClass-objekt

[Använd den här mallen](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml)om du vill skapa ett eget anpassat SecretProviderClass-objekt med providerspecifika parametrar för hemligheter Store CSI-drivrutinen. Det här objektet ger identitets åtkomst till ditt nyckel valv.

Fyll i de saknade parametrarna i filen sample SecretProviderClass YAML. Följande parametrar måste anges:

* **keyvaultName**: namnet på ditt nyckel valv
* **objekt**: lista över allt hemligt innehåll som du vill montera
    * **objectName**: namnet på det hemliga innehållet
    * **objectType**: objekt typ (hemlighet, nyckel, certifikat)
* **tenantID**: klient-ID eller katalog-ID för nyckel valvet

Dokumentation av alla obligatoriska fält och konfigurationer som stöds finns här: [länk](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Den uppdaterade mallen visas i följande kod. Ladda ned den som en YAML-fil och fyll i de obligatoriska fälten. I det här exemplet är nyckel valvet **contosoKeyVault5**. Det har två hemligheter, **secret1** och **secret2**.

> [!NOTE] 
> Om du använder Pod identiteter ställer du in **usePodIdentity** -värdet som *Sant* och anger **userAssignedIdentityID** -värdet som ett par med citat tecken (**""**). Om du använder hanterade identiteter ställer du in värdet **useVMManagedIdentity** som *Sant* och anger **userAssignedIdentityID** -värdet som clientID för användarens identitet.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
Följande bild visar konsolens utdata för **AZ-contosoKeyVault5 show--name** med relevanta markerade metadata:

![Skärm bild som visar konsolens utdata för "AZ-valv show--name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Installera Azure Active Directory (Azure AD) Pod-identitet

1. Tilldela vissa roller till det AKS-kluster som du har skapat. 

    Dokumentation för alla nödvändiga roll tilldelningar med Azure Active Directory (Azure AD) Pod-identitet finns här: [länk](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Installera Azure Active Directory (Azure AD)-identiteten i AKS.

    > [!NOTE] 
    > Om du använder ett AKS-kluster med Kubernetes Network-plugin-programmet läser du det här [dokumentet](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) om hur du distribuerar Pod-identitet i klustret.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Skapa en User-Assigned hanterad identitet. I utdata kopierar du **clientId** för senare användning.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Ge identiteten behörighet att hämta hemligheter från ditt nyckel valv. Använd **clientId** från den användare-tilldelade hanterade identiteten.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Distribuera din POD med monterade hemligheter från ditt nyckel valv

Kör följande kommando för att konfigurera SecretProviderClass-objektet:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Använda hanterade identiteter

Om du använder Pod identiteter skapar du en *AzureIdentity* i klustret som refererar till den identitet som du skapade tidigare. Skapa sedan en *AzureIdentityBinding* som refererar till AzureIdentity som du skapade. Fyll i parametrarna i följande mall och spara den som *podIdentityAndBinding. yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Kör följande kommando för att konfigurera bindningen:

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
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
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
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Kör följande kommando för att visa innehållet i en speciell hemlighet:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Kontrol lera att innehållet i hemligheten visas.

## <a name="next-steps"></a>Nästa steg

Information om hur du kontrollerar att nyckel valvet kan återskapas finns i:
> [!div class="nextstepaction"]
> [Aktivera mjuk borttagning](./key-vault-recovery.md)
