---
title: Reparera ett brutet Azure Automanage konto
description: Om du nyligen har flyttat en prenumeration som innehåller ett automanagekonto till en ny klientorganisation måste du konfigurera om den. I den här artikeln får du lära dig hur.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834200"
---
# <a name="repair-an-automanage-account"></a>Reparera ett automanagekonto
Ditt [Azure Automanage konto är](./automanage-virtual-machines.md#automanage-account) den säkerhetskontext eller identitet som de automatiserade åtgärderna utförs under. Om du nyligen har flyttat en prenumeration som innehåller ett automanagekonto till en ny klientorganisation måste du konfigurera om kontot. Om du vill konfigurera om den måste du återställa identitetstypen och tilldela lämpliga roller för kontot.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Steg 1: Återställ identitetstypen För automatiskt konto
Återställ identitetstypen automanagekonto med hjälp av följande Azure Resource Manager (ARM). Spara filen lokalt som armdeploy.jspå eller ett liknande namn. Anteckna namnet och platsen för ditt automanagekonto eftersom de är obligatoriska parametrar i ARM-mallen.

1. Skapa en Resource Manager-distribution med hjälp av följande mall. Använd `identityType = None`.
    * Du kan skapa distributionen i Azure CLI med hjälp av `az deployment sub create` . Mer information finns i [az deployment sub](/cli/azure/deployment/sub).
    * Du kan skapa distributionen i PowerShell med hjälp av `New-AzDeployment` modulen . Mer information finns i [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Kör samma ARM-mall igen med `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Steg 2: Tilldela lämpliga roller för det automatiska administratörskontot
Kontot för automatisk hantering kräver rollerna Deltagare och Resursprincipdeltagare i prenumerationen som innehåller de virtuella datorer som automanage hanterar. Du kan tilldela dessa roller med hjälp Azure Portal, ARM-mallar eller Azure CLI.

Om du använder en ARM-mall eller Azure CLI behöver du huvudkonto-ID:t (även kallat objekt-ID) för ditt automanagekonto. (Du behöver inte ID:t om du använder Azure Portal.) Du hittar det här ID:t med hjälp av följande metoder:

- [Azure CLI:](/cli/azure/ad/sp)Använd kommandot `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Gå till **Azure Active Directory** och sök efter ditt automanagekonto efter namn. Under **Företagsprogram** väljer du namnet på Det automatiska kontot när det visas.

### <a name="azure-portal"></a>Azure Portal
1. Under **Prenumerationer** går du till den prenumeration som innehåller dina automatisktanterade virtuella datorer.
1. Gå till **Åtkomstkontroll (IAM).**
1. Välj **Lägg till rolltilldelningar.**
1. Välj rollen **Deltagare** och ange namnet på ditt automanagekonto.
1. Välj **Spara**.
1. Upprepa steg 3 till 5, den här gången med rollen **Resursprincipdeltagare.**

### <a name="arm-template"></a>ARM-mall
Kör följande ARM-mall. Du behöver huvudkonto-ID:t för ditt automanagekonto. Stegen för att hämta det är i början av det här avsnittet. Ange ID:t när du uppmanas att göra det.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Kör dessa kommandon:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Nästa steg
[Läs mer om Azure Automanage](./automanage-virtual-machines.md)
