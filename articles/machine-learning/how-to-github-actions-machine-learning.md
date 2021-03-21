---
title: GitHub Actions för CI/CD
titleSuffix: Azure Machine Learning
description: Lär dig mer om hur du skapar ett GitHub-åtgärds arbets flöde för att träna en modell på Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218289"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Använda GitHub-åtgärder med Azure Machine Learning

Kom igång med [GitHub-åtgärder](https://docs.github.com/en/actions) för att träna en modell på Azure Machine Learning. 

> [!NOTE]
> GitHub-åtgärder för Azure Machine Learning tillhandahålls i befintligt skick och stöds inte fullt ut av Microsoft. Om du stöter på problem med en speciell åtgärd öppnar du ett ärende i databasen för åtgärden. Om du till exempel stöter på problem med åtgärden AML-Deploy rapporterar du problemet i [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) lagrings platsen.

## <a name="prerequisites"></a>Förutsättningar 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  

## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

Filen har fyra avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. |
|**Anslut** | 1. Anslut till Machine Learning-arbetsytan. <br /> 2. Anslut till ett beräknings mål. |
|**Kör** | 1. skicka en utbildnings körning. |
|**Distribuera** | 1. registrera modell i Azure Machine Learning registret. 1. Distribuera modellen. |

## <a name="create-repository"></a>Skapa lagrings plats

Skapa en ny lagrings plats av [ml-Ops med GitHub-åtgärder och Azure Machine Learning mall](https://github.com/machine-learning-apps/ml-template-azure). 

1. Öppna [mallen](https://github.com/machine-learning-apps/ml-template-azure) på GitHub. 
2. Välj **Använd den här mallen**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Välj Använd den här mallen":::
3. Skapa en ny lagrings plats från mallen. Ange ett namn på lagrings platsen `ml-learning` eller valfritt namn. 


## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID, resurs grupp namn och app-namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare.

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

1. I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

2. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten namnet `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Anslut till arbets ytan

Använd [Azure Machine Learning-arbetsyta åtgärden](https://github.com/marketplace/actions/azure-machine-learning-workspace) för att ansluta till Azure Machine Learning-arbetsytan. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Som standard förväntar sig åtgärden en `workspace.json` fil. Om JSON-filen har ett annat namn kan du ange den med `parameters_file` indataparametern. Om det inte finns en fil skapas en ny med namnet på databasen.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Åtgärden skriver arbets ytans Azure Resource Manager (ARM) egenskaper till en konfigurations fil som kommer att plockas av alla framtida Azure Machine Learning GitHub åtgärder. Filen sparas i `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Anslut till ett beräknings mål i Azure Machine Learning

Använd [Azure Machine Learning beräknings åtgärd](https://github.com/Azure/aml-compute) för att ansluta till ett beräknings mål i Azure Machine Learning.  Om beräknings målet finns ansluter åtgärden till den. Annars kommer åtgärden att skapa ett nytt beräknings mål. [Compute-åtgärden AML](https://github.com/Azure/aml-compute) stöder endast Azure ml Compute-kluster och Azure Kubernetes service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Skicka träningskörning

Använd [Azure Machine Learning utbildnings åtgärd](https://github.com/Azure/aml-run) för att skicka en ScriptRun, en uppskattare eller en pipeline till Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrera modell i registret

Använd [åtgärden Azure Machine Learning registrera modell](https://github.com/Azure/aml-registermodel) för att registrera en modell för Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Distribuera modellen till Azure Machine Learning till ACI

Använd [åtgärden Azure Machine Learning distribution](https://github.com/Azure/aml-deploy) för att distribuera en modell och skapa en slut punkt för modellen. Du kan också använda Azure Machine Learning Deploy för att distribuera till Azure Kubernetes-tjänsten. Se [det här exempel arbets flödet](https://github.com/Azure-Samples/mlops-enterprise-template) för en modell som distribuerar till Azure Kubernetes-tjänsten.

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

Träna din modell och distribuera den till Azure Machine Learning. 

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

När din resurs grupp och lagrings plats inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)