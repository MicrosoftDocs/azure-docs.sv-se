---
title: 'Snabb start: ny princip tilldelning med python'
description: I den här snabb starten använder du python för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: e600f97dafdd1040c22b6e4d9e333f638334b663
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742355"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med python

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar kommer du att identifiera virtuella datorer som _inte är kompatibla_.

Python-biblioteket används för att hantera Azure-resurser från kommando raden eller i skript. Den här guiden förklarar hur du använder python-biblioteket för att skapa en princip tilldelning.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Lägg till princip biblioteket

Om du vill att python ska fungera med Azure Policy måste biblioteket läggas till. Det här biblioteket fungerar där python kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrol lera att den senaste python-versionen är installerad (minst **3,8**). Om den inte har installerats än kan du ladda ned den på [python.org](https://www.python.org/downloads/).

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om den inte har installerats än kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI krävs för att tillåta python att använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i [autentisera med hjälp av Azures hanterings bibliotek för python](/azure/developer/python/azure-sdk-authenticate).

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I din python-miljö väljer du de bibliotek som krävs för Azure Policy:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Om python installeras för alla användare måste dessa kommandon köras från en upphöjd konsol.

1. Verifiera att biblioteken har installerats. `azure-mgmt-policyinsights` bör vara **0.5.0** eller högre, `azure-mgmt-resource` vara **9.0.0** eller högre, och `azure-cli-core` bör vara **2.5.0** eller högre.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kod för att skapa en ny princip tilldelning:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Föregående kommandon använder följande information:

Tilldelnings information:
- **display_name** -visnings namn för princip tilldelningen. I det här fallet använder du _granskning av virtuella datorer utan Managed disks tilldelning_.
- **policy_definition_id** – princip definitions Sök vägen, baserat på vilken du använder för att skapa tilldelningen. I det här fallet är det ID: t för granskning av princip definition för _virtuella datorer som inte använder hanterade diskar_. I det här exemplet är princip definitionen en inbyggd och sökvägen innehåller inte hanterings grupps-eller prenumerations information.
- **omfång** – en omfattning avgör vilka resurser eller grupper av resurser som princip tilldelningen tillämpas på. Det kan vara ett intervall från en hanterings grupp till en enskild resurs. Se till att ersätta `{scope}` med något av följande mönster:
  - Hanterings grupp: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Prenumeration: `/subscriptions/{subscriptionId}`
  - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Klusterresursen `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **Beskrivning** – en djupare förklaring av vad principen gör eller varför den är tilldelad till det här omfånget.

Skapa tilldelning:

- Omfattning – det här omfånget avgör var princip tilldelningen sparas. Omfånget som anges i tilldelnings informationen måste finnas i det här omfånget.
- Namn – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.
- Princip tilldelning – python **PolicyAssignment** -objektet som skapades i föregående steg.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Använd följande information för att identifiera resurser som inte är kompatibla med principtilldelningen som du skapade. Kör följande kod:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Ersätt `{subscriptionId}` med den prenumeration som du vill se resultatet av efterlevnaden för den här princip tilldelningen. En lista över andra omfattningar och metoder för att sammanfatta data finns i [principer för princip tillstånd](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Ditt resultat liknar följande exempel:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Resultaten matchar det du ser på fliken **Resource Compliance (resurs krav** ) i en princip tilldelning i vyn Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Ersätt `{scope}` med samma omfång som du använde för att skapa princip tilldelningen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill veta mer om att tilldela princip definitioner för att verifiera att nya resurser är kompatibla fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)
