---
title: Hantera roller i din arbets yta
titleSuffix: Azure Machine Learning
description: Lär dig hur du kommer åt en Azure Machine Learning-arbetsyta med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 01/20/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 8420aecbc160fa6df2640d2ba0ae8a8b77702b67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624548"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Hantera åtkomst till en Azure Machine Learning-arbetsyta

I den här artikeln får du lära dig hur du hanterar åtkomst (auktorisering) till en Azure Machine Learning-arbetsyta. [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) används för att hantera åtkomst till Azure-resurser, till exempel möjligheten att skapa nya resurser eller använda befintliga. Användare i din Azure Active Directory (Azure AD) tilldelas särskilda roller som ger åtkomst till resurser. Azure tillhandahåller både inbyggda roller och möjligheten att skapa anpassade roller.

> [!TIP]
> Den här artikeln fokuserar på Azure Machine Learning, enskilda tjänster som Azure ML använder för att tillhandahålla egna RBAC-inställningar. Med hjälp av informationen i den här artikeln kan du till exempel konfigurera vem som kan skicka Poäng begär anden till en modell som distribueras som en webb tjänst i Azure Kubernetes-tjänsten. Men Azure Kubernetes-tjänsten tillhandahåller en egen uppsättning Azure-roller. Information om tjänstspecifika RBAC-information som kan vara användbar med Azure Machine Learning finns i följande länkar:
>
> * [Styr åtkomsten till Azure Kubernetes-kluster resurser](../aks/azure-ad-rbac.md)
> * [Använd Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)
> * [Använda Azure RBAC för åtkomst till BLOB-data](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Att använda vissa roller kan begränsa GRÄNSSNITTs funktionerna i Azure Machine Learning Studio för andra användare. Om en användares roll till exempel inte kan skapa en beräknings instans, är alternativet för att skapa en beräknings instans inte tillgängligt i Studio. Det här beteendet förväntas och hindrar användaren från att försöka utföra åtgärder som returnerar ett nekat åtkomst fel.

## <a name="default-roles"></a>Standard roller

En Azure Machine Learning-arbetsyta är en Azure-resurs. När en ny Azure Machine Learning-arbetsyta skapas levereras den med tre standardroller precis som andra Azure-resurser. Du kan lägga till användare i arbets ytan och tilldela dem till någon av dessa inbyggda roller.

| Roll | Åtkomstnivå |
| --- | --- |
| **Läsare** | Skrivskyddade åtgärder på arbets ytan. Läsarna kan visa och Visa till gångar, inklusive autentiseringsuppgifter för [data lagret](how-to-access-data.md) , i en arbets yta. Läsarna kan inte skapa eller uppdatera dessa till gångar. |
| **Deltagare** | Visa, skapa, redigera eller ta bort (om det är tillämpligt) till gångar i en arbets yta. Deltagare kan till exempel skapa ett experiment, skapa eller ansluta ett beräkningskluster, skicka in en körning och distribuera en webbtjänst. |
| **Ägare** | Fullständig åtkomst till arbets ytan, inklusive möjligheten att visa, skapa, redigera eller ta bort (om tillämpligt) till gångar i en arbets yta. Dessutom kan du ändra rolltilldelningar. |
| **Anpassad roll** | Gör att du kan anpassa åtkomsten till vissa kontroller eller data Plans åtgärder inom en arbets yta. Du kan till exempel skicka en körning, skapa en beräkning, distribuera en modell eller registrera en data uppsättning. |

> [!IMPORTANT]
> Roll åtkomsten kan begränsas till flera nivåer i Azure. Till exempel kanske någon med ägar åtkomst till en arbets yta saknar ägar åtkomst till den resurs grupp som innehåller arbets ytan. Mer information finns i [så här fungerar Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-works).

Det finns för närvarande inga ytterligare inbyggda roller som är speciella för Azure Machine Learning. Mer information om inbyggda roller finns i [inbyggda roller i Azure](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Hantera åtkomst till arbets ytan

Om du är ägare till en arbets yta kan du lägga till och ta bort roller för arbets ytan. Du kan också tilldela roller till användare. Använd följande länkar för att identifiera hur du hanterar åtkomst:
- [Azure Portal användar gränssnitt](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST-API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager-mallar](../role-based-access-control/role-assignments-template.md)

Om du har installerat [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)kan du använda CLI-kommandon för att tilldela roller till användare:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`Fältet är e-postadressen till en befintlig användare i instansen av Azure Active Directory där arbets ytans överordnade prenumeration finns. Här är ett exempel på hur du använder det här kommandot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> kommandot "AZ ml-arbetsytans resurs" fungerar inte för federerat konto med Azure Active Directory B2B. Använd Azures GRÄNSSNITTs portal i stället för kommandot.

## <a name="create-custom-role"></a>Skapa anpassad roll

Om de inbyggda rollerna är otillräckliga kan du skapa anpassade roller. Anpassade roller kan ha behörigheterna läsa, skriva, ta bort och beräkna resurs på arbets ytan. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå.

> [!NOTE]
> Du måste vara ägare till resursen på den nivån för att skapa anpassade roller i resursen.

Skapa en anpassad roll genom att skapa en JSON-fil för roll definition som anger behörigheten och omfånget för rollen. I följande exempel definieras en anpassad roll med namnet "data expert Custom" som omfattas av en speciell arbets ytans nivå:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Du kan ändra `AssignableScopes` fältet för att ange omfånget för den här anpassade rollen på prenumerations nivå, resurs grupp nivå eller en speciell arbets ytans nivå.
> Den anpassade rollen ovan är bara ett exempel, se vissa föreslagna [anpassade roller för tjänsten Azure Machine Learning](#customroles).

Den här anpassade rollen kan göra allt på arbets ytan, förutom följande åtgärder:

- Det går inte att skapa eller uppdatera en beräknings resurs.
- Det går inte att ta bort en beräknings resurs.
- Det går inte att lägga till, ta bort eller ändra roll tilldelningar.
- Det går inte att ta bort arbets ytan.

Använd följande Azure CLI-kommando för att distribuera den här anpassade rollen:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Efter distributionen blir den här rollen tillgänglig på den angivna arbets ytan. Nu kan du lägga till och tilldela den här rollen i Azure Portal. Du kan också tilldela rollen till en användare med hjälp av CLI- `az ml workspace share` kommandot:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Mer information om anpassade roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning åtgärder

Mer information om åtgärder (åtgärder och inte åtgärder) kan användas med anpassade roller i [Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). Du kan också använda följande Azure CLI-kommando för att Visa åtgärder:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Lista anpassade roller

Kör följande kommando i Azure CLI:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Använd följande Azure CLI-kommando om du vill visa roll definitionen för en angiven anpassad roll. `<role-name>`Ska ha samma format som returneras av kommandot ovan:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Kör följande kommando i Azure CLI:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Du måste ha behörighet för hela omfattningen av din nya roll definition. Om den nya rollen till exempel har ett omfång över tre prenumerationer måste du ha behörighet för alla tre prenumerationerna. 

> [!NOTE]
> Roll uppdateringar kan ta 15 minuter till en timme att tillämpa på alla roll tilldelningar i det omfånget.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Använda Azure Resource Manager mallar för repeterbarhet

Om du tror att du kommer att behöva återskapa komplexa roll tilldelningar kan en Azure Resource Manager mall vara en stor hjälp. [Mallen 201-Machine-Learning--beroenden-roll tilldelning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) visar hur roll tilldelningar kan anges i käll koden för åter användning. 

## <a name="common-scenarios"></a>Vanliga scenarier

Följande tabell är en sammanfattning av Azure Machine Learning aktiviteter och de behörigheter som krävs för att utföra dem med minsta möjliga omfattning. Om en aktivitet till exempel kan utföras med ett definitions område för arbets ytor (kolumn 4), kommer alla högre omfång med den behörigheten också att fungera automatiskt:

> [!IMPORTANT]
> Alla sökvägar i den här tabellen som börjar med `/` är **relativa sökvägar** till `Microsoft.MachineLearningServices/` :

| Aktivitet | Omfång på prenumerations nivå | Omfång på resurs grupps nivå | Omfång på arbets ytans nivå |
| ----- | ----- | ----- | ----- |
| Skapa ny arbets yta | Krävs inte | Ägare eller deltagare | Ej tillämpligt (blir ägare eller ärver högre omfattnings roll efter att det har skapats) |
| Begär Amlcompute kvot för prenumerations nivå eller Ange gräns för arbets ytans kvot | Ägare, eller deltagare eller anpassad roll </br>tillåts `/locations/updateQuotas/action`</br> vid prenumerations omfång | Inte auktoriserad | Inte auktoriserad |
| Skapa nytt beräknings kluster | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `/workspaces/computes/write` |
| Skapa en ny beräknings instans | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `/workspaces/computes/write` |
| Sändning av vilken typ av körning som helst | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publicera pipeliner och slut punkter | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Distribuera en registrerad modell på en AKS/ACI-resurs | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Poäng till en distribuerad AKS-slutpunkt | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (när du inte använder Azure Active Directory auth) eller `"/workspaces/read"` (när du använder token auth) |
| Åtkomst till lagring med interaktiva antecknings böcker | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Skapa ny anpassad roll | Ägare, deltagare eller anpassad roll som tillåter `Microsoft.Authorization/roleDefinitions/write` | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter: `/workspaces/computes/write` |

> [!TIP]
> Om du får ett fel när du försöker skapa en arbets yta för första gången ska du kontrol lera att din roll tillåter `Microsoft.MachineLearningServices/register/action` . Med den här åtgärden kan du registrera Azure Machine Learning Resource Provider med din Azure-prenumeration.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Användare som tilldelats hanterad identitet med Azure ML Compute-kluster

Om du vill tilldela en tilldelad identitet till ett Azure Machine Learning beräknings kluster måste du ha Skriv behörighet för att skapa rollen Compute och [hanterad identitets operatör](../role-based-access-control/built-in-roles.md#managed-identity-operator). Mer information om Azure RBAC med hanterade identiteter finns i [hantera användarens tilldelade identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>MLflow-åtgärder

Om du vill utföra MLflow åtgärder med din Azure Machine Learning-arbetsyta använder du följande omfång din anpassade roll:

| MLflow-åtgärd | Omfång |
| --- | --- |
| Lista alla experiment i butiken för arbets ytans spårning, få ett experiment med ID, få ett experiment efter namn | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Skapa ett experiment med ett namn, ange en tagg för ett experiment, Återställ ett experiment som marker ATS för borttagning| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Ta bort ett experiment | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Hämta en körning och relaterade data och metadata, hämta en lista över alla värden för angivet mått för en given körnings List artefakter för en körning | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Skapa en ny körning inom ett experiment, ta bort körningar, återställa borttagna körningar, logg mått under den aktuella körningen, ange taggar på en körnings-, ta bort-taggar för en körning, logg-param (nyckel/värde-par) som används för körning, logga en batch med mått, parametrar och taggar för körning, logga en batch med mått, params | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Hämta registrerad modell efter namn, hämta en lista över alla registrerade modeller i registret, Sök efter registrerade modeller, senaste versions modeller för varje begär Ande steg, hämta en registrerad modells version, Sök modell versioner, Hämta URI där en modell versions artefakter lagras, Sök efter körningar efter experiment-ID: n | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Skapa en ny registrerad modell, uppdatera en registrerad modells namn/beskrivning, Byt namn på befintlig registrerad modell, skapa ny version av modellen, uppdatera en modell versions beskrivning, gå över en registrerad modell till någon av stegen | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Ta bort en registrerad modell tillsammans med alla dess version, ta bort vissa versioner av en registrerad modell | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Exempel på anpassade roller

### <a name="data-scientist"></a>Dataexpert

Gör att en data expert kan utföra alla åtgärder inom en arbets yta **förutom**:

* Skapa beräkning
* Distribuera modeller till ett AKS-kluster för produktion
* Distribuera en pipeline-slutpunkt i produktion

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Data expert begränsad

En mer begränsad roll definition utan jokertecken i tillåtna åtgärder. Den kan utföra alla åtgärder inom en arbets yta **förutom**:

* Skapa beräkning
* Distribuera modeller till ett AKS-kluster för produktion
* Distribuera en pipeline-slutpunkt i produktion

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow data expert

Gör att en data expert kan utföra alla MLflow AzureML-åtgärder som stöds, **förutom**:

* Skapa beräkning
* Distribuera modeller till ett AKS-kluster för produktion
* Distribuera en pipeline-slutpunkt i produktion

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Gör att du kan tilldela en roll till ett huvud namn för tjänsten och använda det för att automatisera dina MLOps-pipeliner. Till exempel för att skicka körningar till en redan publicerad pipeline:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Arbets ytans administratör

Gör att du kan utföra alla åtgärder inom en arbets ytans omfång, **förutom**:

* Skapa en ny arbets yta
* Tilldelar kvoter för prenumerations-eller arbets ytans nivå

Administratören för arbets ytan kan inte heller skapa en ny roll. Den kan bara tilldela befintliga inbyggda eller anpassade roller inom omfånget för deras arbets yta:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Data Labeler

Gör att du kan definiera en roll som är begränsad till att etikettera data:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Felsökning

Här är några saker som du bör känna till när du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC):

- När du skapar en resurs i Azure, t. ex. en arbets yta, är du inte direkt resursens ägare. Rollen ärvs från den högsta omfattnings rollen som du har behörighet för i den prenumerationen. Som exempel om du är nätverks administratör och har behörighet att skapa en Machine Learning arbets yta, skulle du ha rollen som nätverks administratör mot den arbets ytan och inte till ägar rollen.

- Om du vill utföra kvot åtgärder på en arbets yta måste du ha behörigheter på prenumerations nivå. Det innebär att det bara går att ange kvoten på prenumerations nivå eller på arbets ytans kvot för dina hanterade beräknings resurser om du har Skriv behörighet i prenumerations omfånget.

- Om det finns två roll tilldelningar för samma Azure Active Directory användare med motstridiga avsnitt i åtgärder/NotActions, kanske dina åtgärder som anges i NotActions från en roll inte börjar gälla om de också visas som åtgärder i en annan roll. Om du vill veta mer om hur Azure kan parsa roll tilldelningar läser du [hur Azure RBAC avgör om en användare har åtkomst till en resurs](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- Om du vill distribuera dina beräknings resurser i ett VNet måste du uttryckligen ha behörighet för följande åtgärder:
    - `Microsoft.Network/virtualNetworks/join/action` på VNet-resursen.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` på under näts resursen.
    
    För mer information om Azure RBAC med nätverk, se de [inbyggda nätverks rollerna](../role-based-access-control/built-in-roles.md#networking).

- Det kan ibland ta upp till en timme innan dina nya roll tilldelningar börjar gälla för cachelagrade behörigheter i stacken.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Enterprise Security](concept-enterprise-security.md)
- [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)
- [Självstudie: träna modeller](tutorial-train-models-with-aml.md)
- [Åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
