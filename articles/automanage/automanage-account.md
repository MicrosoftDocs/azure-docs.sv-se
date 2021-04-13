---
title: Azure Automanage konto
description: Lär dig hur ett automanagekonto fungerar och hur du skapar ett.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368744"
---
# <a name="automanage-accounts"></a>Automanage Accounts

Kontot Automanage är den identitet som används av tjänsten Automanage för att utföra automatiska åtgärder.

När du Azure Portal automatisk hantering på dina virtuella datorer finns det en avancerad listrutan på bladet Aktivera bästa praxis för virtuella **Azure-datorer** där du kan tilldela eller manuellt skapa kontot för automatisk hantering.

Automanage-kontot kommer att  beviljas  rollerna Deltagare och Resursprincipdeltagare för de prenumerationer som innehåller de maskiner som du tecknar för automatiskhantering. Du kan använda samma automanagekonto på datorer i flera prenumerationer, vilket ger den behörigheten Automanage Account **Contributor** (Kontodeltagare) och **Resource Policy Contributor** (Resursprincipdeltagare) för alla prenumerationer.

Om den virtuella datorn är ansluten till en Log Analytics-arbetsyta  i  en annan prenumeration beviljas kontot för automatiskhantering både deltagare och resursprincipdeltagare i den andra prenumerationen också.

Om du aktiverar Automanage med ett nytt automanagekonto behöver du följande behörigheter  för din **prenumeration:** Ägarroll eller Deltagare tillsammans med **administratörsroller för användaråtkomst.**

Om du aktiverar Automanage med ett befintligt automanagekonto  måste du ha rollen Deltagare för resursgruppen som innehåller dina virtuella datorer.

> [!NOTE]
> När du inaktiverar bästa praxis för automatisk manage förblir kontots behörigheter för associerade prenumerationer kvar. Ta bort behörigheterna manuellt genom att gå till prenumerationens IAM-sida eller ta bort automanage-kontot. Det går inte att ta bort kontot för automatisk hantering om det fortfarande hanterar några datorer.

## <a name="create-an-automanage-account"></a>Skapa ett automanagekonto
Du kan skapa ett automanagekonto med hjälp av portalen eller med hjälp av en ARM-mall.

### <a name="portal"></a>Portalen
1. Gå till **bladet Automanage** (Hantera automatiskt) i portalen
1. Klicka **på Aktivera på befintlig dator**
1. Under **Avancerat** klickar du på "Skapa ett nytt konto"
1. Fyll i de obligatoriska fälten och klicka på **Skapa**

### <a name="arm-template"></a>ARM-mall
Det krävs två steg för att skapa ett automanagekonto med hjälp av en ARM-mall:
1. Skapa automanage-kontot
1. Bevilja tillräcklig behörighet till kontot så att det kan utföra åtgärder åt dig
    1. Du behöver objekt-ID för det konto som du skapade för det här steget.
        1. Anvisningar för att hitta information om ditt kontos huvudnamn för tjänsten (inklusive objekt-ID) finns [här.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)
    1. När du har hittat tjänstens huvudnamn kopierar du **objekt-ID:t**. Spara den eftersom du behöver den för att delegera behörigheter nedan.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Skapa ett automanagekonto (ger inte behörighet till det)
Om du vill skapa ett konto för automatisk hantering sparar du följande ARM-mall `azuredeploy.json` som och kör Azure CLI-kommandot nedan. När du är klar går du vidare till den andra mallen nedan för att delegera tillräcklig behörighet till kontot.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Bevilja behörigheter till det automatisktmanage-kontot
Om du vill bevilja tillräcklig behörighet till det automatisktmanage-kontot måste du göra följande:
1. Spara följande ARM-mall som `azuredeploy2.json` och kör Azure CLI-kommandot nedan.
1. När du uppmanas till det anger du objekt-ID för det automanage-konto som du skapade och sparade.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om automanage-tjänster [för Linux](./automanage-linux.md) och [Windows](./automanage-windows-server.md)