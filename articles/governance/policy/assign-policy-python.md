---
title: 'Snabbstart: Ny principtilldelning med Python'
description: I den här snabbstarten använder du Python för att skapa Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c9a0d6bb00d82748505304264aeaefa409c4b06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379410"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser med hjälp av Python

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar identifierar du virtuella datorer som inte _är kompatibla._

Python-biblioteket används för att hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur du använder Python-biblioteket för att skapa en principtilldelning.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Lägga till principbiblioteket

Om du vill aktivera Python för Azure Policy måste du lägga till biblioteket. Det här biblioteket fungerar överallt där Python kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrollera att den senaste Python-versionen är installerad (minst **3.8**). Om det inte har installerats än laddar du ned det på [Python.org](https://www.python.org/downloads/).

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om det inte har installerats än kan du [gå till Installera Azure CLI.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Azure CLI krävs för att Python ska kunna använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i Autentisera [med hjälp av Azure-hanteringsbibliotek för Python.](/azure/developer/python/azure-sdk-authenticate)

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I valfri Python-miljö installerar du de bibliotek som krävs för Azure Policy:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Om Python är installerat för alla användare måste dessa kommandon köras från en upphöjd konsol.

1. Kontrollera att biblioteken har installerats. `azure-mgmt-policyinsights` bör vara **0.5.0** eller högre, ska vara `azure-mgmt-resource` **9.0.0** eller högre och ska vara `azure-cli-core` **2.5.0** eller högre.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen Granska virtuella datorer som inte använder **hanterade** diskar ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kod för att skapa en ny principtilldelning:

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

Tilldelningsinformation:
- **display_name** – Visningsnamn för principtilldelningen. I det här fallet använder du Tilldelningen _Granska virtuella datorer utan hanterade diskar._
- **policy_definition_id** – Principdefinitionssökvägen som du använder för att skapa tilldelningen. I det här fallet är det ID:t för principdefinitionen _Granska virtuella datorer som inte använder hanterade diskar_. I det här exemplet är principdefinitionen inbyggd och sökvägen innehåller inte information om hanteringsgrupp eller prenumeration.
- **scope** – Ett omfång avgör vilka resurser eller gruppering av resurser som principtilldelningen tillämpas på. Det kan vara allt från en hanteringsgrupp till en enskild resurs. Se till att `{scope}` ersätta med något av följande mönster:
  - Hanteringsgrupp: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Prenumeration: `/subscriptions/{subscriptionId}`
  - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Resurs: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **beskrivning** – En djupare förklaring av vad principen gör eller varför den har tilldelats till det här omfånget.

Skapa tilldelning:

- Omfång – Det här omfånget avgör var principtilldelningen sparas. Det omfång som anges i tilldelningsinformationen måste finnas i det här omfånget.
- Namn – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.
- Principtilldelning – Python **PolicyAssignment-objektet** som skapades i föregående steg.

Nu är du redo att identifiera icke-kompatibla resurser för att förstå kompatibilitetstillståndet för din miljö.

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

Ersätt `{subscriptionId}` med den prenumeration som du vill visa kompatibilitetsresultaten för den här principtilldelningen. En lista över andra omfång och sätt att sammanfatta data finns i [Metoder för principtillstånd.](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)

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

Resultatet matchar det som visas på fliken **Resursefterlevnad** för en principtilldelning i Azure Portal vyn.

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

Ersätt `{scope}` med samma omfång som du använde för att skapa principtilldelningen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Mer information om hur du tilldelar principdefinitioner för att verifiera att nya resurser är kompatibla finns i självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)
