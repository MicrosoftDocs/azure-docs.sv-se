---
title: Skapa, testa och distribuera containrar till Azure Kubernetes Service med GitHub Actions
description: Lär dig hur du använder GitHub Actions för att distribuera din container till Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3d0b6030cc63d0d7f4eac2c72c3545cf315b1fd3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832395"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions för distribution till Kubernetes-tjänsten

[GitHub Actions](https://docs.github.com/en/actions) ger dig flexibiliteten att skapa ett arbetsflöde för automatiserad programutvecklingslivscykel. Du kan använda flera Kubernetes-åtgärder för att distribuera till containrar från Azure Container Registry till Azure Kubernetes Service med GitHub Actions. 

## <a name="prerequisites"></a>Förutsättningar 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnadsfritt.](https://github.com/join)  
- Ett fungerande Kubernetes-kluster
    - [Självstudie: Förbereda ett program för Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på din lagringsplats. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

För ett arbetsflöde med AKS som mål har filen tre avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | Logga in på ett privat containerregister (ACR) |
|**Skapa** | Skapa & push-push-containeravbildningen  |
|**Distribuera** | 1. Ange AKS-målklustret |
| |2. Skapa en allmän/docker-registerhemlighet i Kubernetes-kluster  |
||3. Distribuera till Kubernetes-klustret|

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten med kommandot [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i [Azure CLI.](/cli/azure/) Du kan köra det här [kommandot Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

I kommandot ovan ersätter du platshållarna med ditt prenumerations-ID och din resursgrupp. Utdata är de autentiseringsuppgifter för rolltilldelning som ger åtkomst till din resurs. Kommandot ska mata ut ett JSON-objekt som liknar detta.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopiera det här JSON-objektet som du kan använda för att autentisera från GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub-hemligheterna

Följ stegen för att konfigurera hemligheterna:

1. I [GitHub](https://github.com/)bläddrar du till din lagringsplats och väljer **Inställningar > Hemligheter > Lägg till en ny hemlighet**.

    ![Skärmbild som visar länken Lägg till en ny hemlighet för en lagringsplats.](media/kubernetes-action/secrets.png)

2. Klistra in innehållet i kommandot `az cli` ovan som värdet för den hemliga variabeln. Till exempel `AZURE_CREDENTIALS`.

3. På samma sätt definierar du följande ytterligare hemligheter för autentiseringsuppgifterna för containerregistret och anger dem i Docker-inloggningsåtgärden. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Hemligheterna visas enligt nedan när de har definierats.

    ![Skärmbild som visar befintliga hemligheter för en lagringsplats.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Skapa en containeravbildning och distribuera till Azure Kubernetes Service kluster

Skapa och push-skicka containeravbildningarna med hjälp av `Azure/docker-login@v1` åtgärden . 


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
    - uses: actions/checkout@main
    
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

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Distribuera till Azure Kubernetes Service kluster

Om du vill distribuera en containeravbildning till AKS måste du använda `Azure/k8s-deploy@v1` åtgärden. Den här åtgärden har fem parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **Namnområde** | (Valfritt) Välj Kubernetes-målnamnområdet. Om namnområdet inte anges körs kommandona i standardnamnområdet | 
| **Manifesterar** |  (Krävs) Sökvägen till manifestfilerna som ska användas för distribution |
| **Bilder** | (Valfritt) Fullständigt kvalificerad resurs-URL för de avbildningar som ska användas för ersättning av manifestfilerna |
| **imagepullsecrets** | (Valfritt) Namnet på en docker-registry-hemlighet som redan har ställts in i klustret. Vart och ett av dessa hemliga namn läggs till under imagePullSecrets-fältet för de arbetsbelastningar som finns i indatamanifestfilerna |
| **kubectl-version** | (Valfritt) Installerar en specifik version av kubectl-binärfil |


Innan du kan distribuera till AKS måste du ange Kubernetes-målnamnrymden och skapa en pull-hemlighet för avbildningar. Mer [information om hur du hämtar avbildningar finns i](../container-registry/container-registry-auth-kubernetes.md)Hämta avbildningar från ett Azure-containerregister till ett Kubernetes-kluster. 

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
      arguments: --force true
```


Slutför distributionen med `k8s-deploy` åtgärden . Ersätt miljövariablerna med värden för ditt program. 

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
    - uses: actions/checkout@main
    
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

När kubernetes-klustret, containerregistret och lagringsplatsen inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Fler Kubernetes-GitHub Actions

* [Installationsprogram för Kubectl-verktyget](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): Installerar en specifik version av kubectl i -programmet.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( ): Ange kubernetes-målklusterkontexten som ska användas av andra åtgärder eller `azure/k8s-set-context` köra kubectl-kommandon.
* [AKS-uppsättningskontext](https://github.com/Azure/aks-set-context) `azure/aks-set-context` ( ): Ange Azure Kubernetes Service för klusterkontexten.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): Skapa en allmän hemlighet eller docker-registry-hemlighet i Kubernetes-klustret.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): Bake and deploy manifests to Kubernetes clusters (Bake and deploy manifests to Kubernetes clusters.
* [Konfigurera Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): Installera en specifik version av Helm-binärfil på -et.
* [Kubernetes bake](https://github.com/Azure/k8s-bake) ( ): Bake-manifestfil som ska användas för distributioner med `azure/k8s-bake` hjälp av helm2, kustomize eller kompose.
* [Kubernetes lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): Validera/lint dina manifestfiler.
