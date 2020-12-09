---
title: Bygg, testa och distribuera behållare till Azure Kubernetes-tjänsten med hjälp av GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera din behållare till Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 716cf4f4bfaed31dcbd756ae9494e1ddc8e475ad
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929888"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub-åtgärder för att distribuera till Kubernetes-tjänsten

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett arbets flöde för automatiserad livs cykel för program utveckling. Du kan använda flera Kubernetes-åtgärder för att distribuera till behållare från Azure Container Registry till Azure Kubernetes-tjänsten med GitHub-åtgärder. 

## <a name="prerequisites"></a>Krav 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  
- Ett fungerande Kubernetes-kluster
    - [Självstudie: förbereda ett program för Azure Kubernetes service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

För ett arbets flöde som riktar sig till AKS har filen tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | Logga in på ett privat container Registry (ACR) |
|**Skapa** | Bygg & push-överför behållar avbildningen  |
|**Distribuera** | 1. Ange mål AKS-klustret |
| |2. skapa en allmän/Docker-register hemlighet i Kubernetes-kluster  |
||3. distribuera till Kubernetes-klustret|

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

I kommandot ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs grupp. Utdata är de autentiseringsuppgifter för roll tilldelning som ger åtkomst till din resurs. Kommandot ska generera ett JSON-objekt liknande detta.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub hemligheter

Följ stegen för att konfigurera hemligheterna:

1. I [GitHub](https://github.com/), bläddra till din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

    ![Skärm bild som visar Lägg till en ny hemlig länk för en lagrings plats.](media/kubernetes-action/secrets.png)

2. Klistra in innehållet i ovanstående `az cli` kommando som värde för den hemliga variabeln. Exempelvis `AZURE_CREDENTIALS`.

3. På samma sätt definierar du följande ytterligare hemligheter för autentiseringsuppgifterna för behållar registret och anger dem i Docker login login-åtgärd. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Du ser de hemligheter som visas nedan när den har definierats.

    ![Skärm bild som visar befintliga hemligheter för en lagrings plats.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Bygg en behållar avbildning och distribuera till Azure Kubernetes service-kluster

Att bygga och skicka behållar avbildningar görs med hjälp av `Azure/docker-login@v1` åtgärden. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Distribuera till Azure Kubernetes service-kluster

Om du vill distribuera en behållar avbildning till AKS måste du använda `Azure/k8s-deploy@v1` åtgärden. Den här åtgärden har fem parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **namn område** | Valfritt Välj mål namn området Kubernetes. Om namn området inte anges körs kommandona i standard namn området | 
| **manifest** |  Kunna Sökväg till manifest-filerna som ska användas för distribution |
| **avbildningar** | Valfritt Fullständigt resurs-URL för den eller de avbildningar som ska användas för ersättningar på manifest filerna |
| **imagepullsecrets** | Valfritt Namnet på en Docker-register hemlighet som redan har kon figurer ATS i klustret. Vart och ett av dessa hemliga namn läggs till i fältet imagePullSecrets för de arbets belastningar som finns i manifest filen för indatafiler |
| **kubectl-version** | Valfritt Installerar en speciell version av kubectl Binary |


Innan du kan distribuera till AKS måste du ange mål namn område för Kubernetes och skapa en bild-pull-hemlighet. Mer information om hur hämtnings avbildningar fungerar finns i [Hämta bilder från ett Azure Container Registry till ett Kubernetes-kluster](../container-registry/container-registry-auth-kubernetes.md). 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Slutför distributionen med `k8s-deploy` åtgärden. Ersätt miljövariablerna med värden för ditt program. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Rensa resurser

När Kubernetes-klustret, container Registry och lagrings platsen inte längre behövs, rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen och din GitHub-lagringsplats. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Kubernetes-tjänsten](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Fler Kubernetes GitHub-åtgärder

* [Installations program för Kubectl Tool](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): installerar en angiven version av Kubectl på löpare.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): Ange mål Kubernetes kluster kontexten som ska användas av andra åtgärder eller kör eventuella kubectl-kommandon.
* [AKS set context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): Ange mål kluster kontexten för Azure Kubernetes-tjänsten.
* [Kubernetes Create Secret](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): skapa en allmän hemlighet eller Docker-register hemlighet i Kubernetes-klustret.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): bageri-och distributions manifest till Kubernetes-kluster.
* [Setup Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): installera en angiven version av Helm Binary i löpare.
* [Kubernetes bageri](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): den manifest fil som ska användas för distributioner med helm2, kustomize eller kompose.
* [Kubernetes luddfri](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): validera/luddfri dina MANIFEST filer.
