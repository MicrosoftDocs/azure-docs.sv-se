---
title: Använd Key Vault när du distribuerar den hanterade appen
description: Visar hur du använder åtkomst hemligheter i Azure Key Vault när du distribuerar hanterade program
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81458291"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Åtkomst Key Vault hemlighet när du distribuerar Azure Managed Applications

När du behöver skicka ett säkert värde (till exempel ett lösen ord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../../key-vault/general/overview.md). Om du vill komma åt Key Vault när du distribuerar hanterade program måste du bevilja åtkomst till tjänstens huvud namn för enhets **resursen** . Tjänsten Managed Applications använder den här identiteten för att köra åtgärder. För att kunna hämta ett värde från en Key Vault under distributionen måste tjänstens huvud namn kunna komma åt Key Vault.

I den här artikeln beskrivs hur du konfigurerar Key Vault att arbeta med hanterade program.

## <a name="enable-template-deployment"></a>Aktivera mall distribution

1. I portalen väljer du din Key Vault.

1. Välj **Åtkomstprinciper**.   

   ![Välj åtkomst principer](./media/key-vault-access/select-access-policies.png)

1. Välj **Click to show advanced access policies** (Klicka för att visa avancerade åtkomstprinciper).

   ![Visa avancerade åtkomst principer](./media/key-vault-access/advanced.png)

1. Välj **Aktivera åtkomst till Azure Resource Manager för mall distribution**. Välj sedan **Spara**.

   ![Aktivera mall distribution](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Lägg till tjänst som deltagare

1. Välj **Åtkomstkontroll (IAM)** .

   ![Välj åtkomst kontroll](./media/key-vault-access/access-control.png)

1. Välj **Lägg till rolltilldelning**.

   ![Välj Lägg till](./media/key-vault-access/add-access-control.png)

1. Välj **deltagare** för rollen. Sök efter installations **resurs leverantören** och välj den från de tillgängliga alternativen.

   ![Sök efter Provider](./media/key-vault-access/search-provider.png)

1. Välj **Spara**.

## <a name="reference-key-vault-secret"></a>Referens Key Vault hemlighet

Om du vill skicka en hemlighet från en Key Vault till en mall i det hanterade programmet måste du använda en [länkad eller kapslad mall](../templates/linked-templates.md) och referera till Key Vault i parametrarna för den länkade eller kapslade mallen. Ange resurs-ID för Key Vault och namnet på hemligheten.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Nästa steg

Du har konfigurerat dina Key Vault så att de är tillgängliga under distributionen av ett hanterat program.

* Information om hur du skickar ett värde från en Key Vault som en mallparameter finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../templates/key-vault-parameter.md).
* Exempel på hanterade program finns i [exempel projekt för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.
