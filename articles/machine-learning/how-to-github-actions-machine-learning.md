---
title: GitHub Actions för CI/CD
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar ett GitHub Actions arbetsflöde för att träna en modell på Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780379"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Använda GitHub Actions med Azure Machine Learning

Kom igång med [GitHub Actions](https://docs.github.com/en/actions) att träna en modell på Azure Machine Learning. 

> [!NOTE]
> GitHub Actions för Azure Machine Learning tillhandahålls som de är och stöds inte fullt ut av Microsoft. Om du stöter på problem med en viss åtgärd kan du öppna ett problem på lagringsplatsen för åtgärden. Om du till exempel stöter på problem med åtgärden aml-deploy kan du rapportera problemet i [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) lagringsplatsen.

## <a name="prerequisites"></a>Förutsättningar 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnadsfritt.](https://github.com/join)  

## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på din lagringsplats. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

Filen har fyra avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten. <br /> 2. Skapa en GitHub-hemlighet. |
|**Anslut** | 1. Anslut till maskininlärningsarbetsytan. <br /> 2. Anslut till ett beräkningsmål. |
|**Kör** | 1. Skicka en träningskörning. |
|**Distribuera** | 1. Registrera modellen i Azure Machine Learning register. 1. Distribuera modellen. |

## <a name="create-repository"></a>Skapa lagringsplats

Skapa en ny lagringsplats utanför [ML Ops med GitHub Actions och Azure Machine Learning mallen](https://github.com/machine-learning-apps/ml-template-azure). 

1. Öppna mallen [på](https://github.com/machine-learning-apps/ml-template-azure) GitHub. 
2. Välj **Använd den här mallen.** 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Välj använd den här mallen":::
3. Skapa en ny lagringsplats från mallen. Ange namnet på `ml-learning` lagringsplatsen till eller ett val av namn. 


## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du platshållarna med ditt prenumerations-ID, resursgruppens namn och appnamnet. Utdata är ett JSON-objekt med autentiseringsuppgifter för rolltilldelning som ger åtkomst till din App Service som liknar nedan. Kopiera det här JSON-objektet för senare tillfälle.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I [GitHub](https://github.com/)bläddrar du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

2. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten namnet `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Ansluta till arbetsytan

Använd åtgärden [Azure Machine Learning-arbetsyta för](https://github.com/marketplace/actions/azure-machine-learning-workspace) att ansluta till din Azure Machine Learning arbetsyta. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Som standard förväntar sig åtgärden en `workspace.json` fil. Om JSON-filen har ett annat namn kan du ange den med `parameters_file` indataparametern. Om det inte finns någon fil skapas en ny med namnet på lagringsplatsen.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Åtgärden skriver arm-Azure Resource Manager (workspace Azure Resource Manager) till en konfigurationsfil som kommer att väljas av alla framtida Azure Machine Learning GitHub Actions. Filen sparas i `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Ansluta till ett beräkningsmål i Azure Machine Learning

Använd åtgärden [Azure Machine Learning Compute för](https://github.com/Azure/aml-compute) att ansluta till ett beräkningsmål i Azure Machine Learning.  Om beräkningsmålet finns ansluter åtgärden till det. Annars skapar åtgärden ett nytt beräkningsmål. [AML-beräkningsåtgärden](https://github.com/Azure/aml-compute) stöder endast Azure ML-beräkningsklustret och Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Skicka träningskörning

Använd åtgärden [Azure Machine Learning Training för](https://github.com/Azure/aml-run) att skicka en ScriptRun, en beräknare eller en pipeline för att Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrera modellen i registret

Använd åtgärden [Azure Machine Learning Registrera modell för](https://github.com/Azure/aml-registermodel) att registrera en modell för att Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Distribuera modell till Azure Machine Learning till ACI

Använd åtgärden [Azure Machine Learning Deploy för](https://github.com/Azure/aml-deploy) att distribuera en modell och skapa en slutpunkt för modellen. Du kan också använda Azure Machine Learning Distribuera för att distribuera till Azure Kubernetes Service. Se [det här exempelarbetsflödet](https://github.com/Azure-Samples/mlops-enterprise-template) för en modell som distribueras till Azure Kubernetes Service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Fullständigt exempel

Träna din modell och distribuera till Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen och lagringsplatsen inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och köra pipelines för maskininlärning med Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)