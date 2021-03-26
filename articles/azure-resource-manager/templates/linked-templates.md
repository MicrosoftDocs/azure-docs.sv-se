---
title: Länka mallar för distribution
description: 'Beskriver hur du använder länkade mallar i en Azure Resource Manager-mall (ARM-mall) för att skapa en modulär mall-lösning. Visar hur du skickar parameter värden, anger en parameter fil och dynamiskt skapade URL: er.'
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 981e9f10e118012911108d634fbb8bdb9524cb88
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543983"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Använda länkade och nästlade mallar vid distribution av Azure-resurser

Om du vill distribuera komplexa lösningar kan du dela din Azure Resource Manager-mall (ARM-mall) i flera relaterade mallar och sedan distribuera dem tillsammans via en huvud-mall. De relaterade mallarna kan vara separata filer eller mall-syntax som är inbäddad i huvud mal len. Den här artikeln använder den **länkade termen mall** för att referera till en separat mallfil som refereras via en länk från huvud mal len. Den använder termen **kapslad mall** för att referera till inbäddad mall-syntax i huvud mal len.

För små till medelstora lösningar är en enskild mall enklare att förstå och underhålla. Du kan se alla resurser och värden i en enda fil. I avancerade scenarier kan du använda länkade mallar till att dela upp lösningen i riktade komponenter. Du kan enkelt återanvända dessa mallar för andra scenarier.

En själv studie kurs finns i [Självstudier: Distribuera en länkad mall](./deployment-tutorial-linked-template.md).

> [!NOTE]
> För länkade eller kapslade mallar kan du bara ange distributions läget som [stegvis](deployment-modes.md). Men huvud mal len kan distribueras i fullständigt läge. Om du distribuerar huvud mal len i det fullständiga läget och den länkade eller kapslade mallen har samma resurs grupp, inkluderas de resurser som distribueras i den länkade eller kapslade mallen i utvärderingen för fullständig läges distribution. Den kombinerade samling resurser som distribueras i huvud mal len och länkade eller kapslade mallar jämförs med de befintliga resurserna i resurs gruppen. Alla resurser som inte ingår i den kombinerade samlingen tas bort.
>
> Om den länkade eller kapslade mallen är riktad mot en annan resurs grupp, använder distributionen stegvist läge.
>

## <a name="nested-template"></a>Kapslad mall

Om du vill kapsla en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I `template` egenskapen anger du mallens syntax.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

I följande exempel distribueras ett lagrings konto via en kapslad mall.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Uttrycks utvärderings omfång i kapslade mallar

Om du använder en kapslad mall kan du ange om malluttryck ska utvärderas inom omfånget för den överordnade mallen eller den kapslade mallen. Omfånget avgör hur parametrar, variabler och funktioner som [resourceGroup](template-functions-resource.md#resourcegroup) och [prenumerationen](template-functions-resource.md#subscription) löses.

Du ställer in omfånget via `expressionEvaluationOptions` egenskapen. Som standard `expressionEvaluationOptions` är egenskapen inställd på `outer` , vilket innebär att den använder den överordnade mallens omfattning. Ställ in värdet på `inner` för att orsaka att uttryck utvärderas inom omfånget för den kapslade mallen.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> När omfånget är inställt på `outer` , kan du inte använda `reference` funktionen i avsnittet utdata i en kapslad mall för en resurs som du har distribuerat i den kapslade mallen. Om du vill returnera värdena för en distribuerad resurs i en kapslad mall, kan du antingen använda `inner` omfång eller konvertera den kapslade mallen till en länkad mall.

Följande mall visar hur mall uttryck matchas enligt omfånget. Den innehåller en variabel med namnet `exampleVar` som definieras i både den överordnade mallen och den kapslade mallen. Den returnerar variabelns värde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Värdet för `exampleVar` ändringar beroende på `scope` egenskapens värde i `expressionEvaluationOptions` . I följande tabell visas resultaten för båda omfattningarna.

| Utvärderings omfång | Utdata |
| ----- | ------ |
| innersta | från kapslad mall |
| yttre (eller standard) | från överordnad mall |

I följande exempel distribueras en SQL-Server och en nyckel valvs hemlighet som används för lösen ordet hämtas. Omfånget är inställt på `inner` eftersom det dynamiskt skapar Key Vault-ID: t (se `adminPassword.reference.keyVault` i de yttre mallarna `parameters` ) och skickar det som en parameter till den kapslade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
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
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Var försiktig när du använder säkra parameter värden i en kapslad mall. Om du anger omfånget till yttre lagras de säkra värdena som oformaterad text i distributions historiken. En användare som visar mallen i distributions historiken kan se säkra värden. Använd i stället det inre omfånget eller Lägg till i den överordnade mallen de resurser som behöver säkra värden.

Följande utdrag visar vilka värden som är säkra och som inte är säkra.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>Länkad mall

Om du vill länka en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. `templateLink`Ange URI: n för den mall som ska inkluderas i egenskapen. Följande exempel länkar till en mall som finns i ett lagrings konto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

När du refererar till en länkad mall kan värdet för `uri` inte vara en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Azure Resource Manager måste kunna komma åt mallen. Ange ett URI-värde som hämtas som HTTP eller HTTPS.

Du kan referera till mallar med parametrar som inkluderar HTTP eller HTTPS. Ett vanligt mönster är till exempel att använda `_artifactsLocation` parametern. Du kan ange den länkade mallen med ett uttryck som:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Om du länkar till en mall i GitHub använder du den råa URL: en. Länken har formatet: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Om du vill hämta den råa länken väljer du **RAW**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Välj rå URL":::

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

### <a name="parameters-for-linked-template"></a>Parametrar för länkad mall

Du kan ange parametrar för den länkade mallen antingen i en extern fil eller infogad. När du anger en extern parameter fil använder du `parametersLink` egenskapen:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Om du vill skicka parameter värden infogat använder du `parameters` egenskapen.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

Du kan inte använda både infogade parametrar och en länk till en parameter fil. Distributionen Miss lyckas med ett fel när både `parametersLink` och `parameters` har angetts.

### <a name="use-relative-path-for-linked-templates"></a>Använd relativ sökväg för länkade mallar

`relativePath`Egenskapen i `Microsoft.Resources/deployments` gör det enklare att redigera länkade mallar. Den här egenskapen kan användas för att distribuera en fjärran sluten länkad mall på en plats i förhållande till den överordnade platsen. Den här funktionen kräver att alla mallfiler är mellanlagrade och tillgängliga på en fjärr-URI, till exempel GitHub eller Azure Storage-konto. När huvud mal len anropas med hjälp av en URI från Azure PowerShell eller Azure CLI, är den underordnade distributions-URI: n en kombination av den överordnade och relativePath.

> [!NOTE]
> När du skapar en templateSpec paketeras alla mallar som egenskapen refererar till `relativePath` i templateSpec-resursen av Azure PowerShell eller Azure CLI. Det kräver inte att filerna mellanlagras. Mer information finns i [skapa en mall-specifikation med länkade mallar](./template-specs.md#create-a-template-spec-with-linked-templates).

Anta en mappstruktur så här:

![relativ sökväg till kopplad mall i Resource Manager](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

I följande mall visas hur *mainTemplate.jspå* distribuerar *nestedChild.jspå* bilden ovan.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

I följande distribution är URI: n för den länkade mallen i föregående mall **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Om du vill distribuera länkade mallar med relativa sökvägar lagrade i ett Azure Storage-konto, använder du `QueryString` / `query-string` parametern för att ange den SAS-token som ska användas med parametern TemplateUri. Den här parametern stöds endast av Azure CLI version 2,18 eller senare och Azure PowerShell version 5,4 eller senare.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

Se till att det inte finns något inledande "?" i QueryString. Distributionen lägger till en när du monterar URI: n för distributionerna.

## <a name="template-specs"></a>Mallspecifikationer

I stället för att underhålla de länkade mallarna på en tillgänglig slut punkt kan du skapa en [mall-specifikation](template-specs.md) som paketerar huvud mal len och dess länkade mallar i en enda enhet som du kan distribuera. Mallens specifikation är en resurs i din Azure-prenumeration. Det gör det enkelt att på ett säkert sätt dela mallen med användare i din organisation. Du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge åtkomst till mallen specifikation. Den här funktionen är för närvarande en för hands version.

Mer information finns i:

- [Självstudie: skapa en mall-specifikation med länkade mallar](./template-specs-create-linked.md).
- [Självstudie: Distribuera en mall specifikation som en länkad mall](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Beroenden

Precis som med andra resurs typer kan du ange beroenden mellan de länkade mallarna. Om resurserna i en länkad mall måste distribueras före resurser i en andra länkad mall, anger du den andra mallen som är beroende av den första.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

Du behöver inte ange `contentVersion` egenskapen för `templateLink` `parametersLink` egenskapen eller. Om du inte anger någon `contentVersion` , distribueras den aktuella versionen av mallen. Om du anger ett värde för innehålls version måste det matcha versionen i den länkade mallen. annars Miss lyckas distributionen med ett fel.

## <a name="using-variables-to-link-templates"></a>Använda variabler för att länka mallar

I föregående exempel visades hårdkodade URL-värden för mallens länkar. Den här metoden kan fungera för en enkel mall, men den fungerar inte bra för en stor uppsättning modulära mallar. I stället kan du skapa en statisk variabel som lagrar en bas-URL för huvudmallen och sedan dynamiskt skapa URL: er för de länkade mallarna från den bas-URL: en. Fördelen med den här metoden är att du enkelt kan flytta eller förgrena mallen eftersom du bara behöver ändra den statiska variabeln i huvud mal len. Huvud mal len skickar rätt URI: er i den sammanställda mallen.

I följande exempel visas hur du använder en bas-URL för att skapa två URL: er för länkade mallar ( `sharedTemplateUrl` och `vmTemplateUrl` ).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Du kan också använda [Deployment ()](template-functions-deployment.md#deployment) för att hämta bas-URL: en för den aktuella mallen och använda den för att hämta URL: en för andra mallar på samma plats. Den här metoden är användbar om din malls plats ändras eller om du vill undvika URL: er för hård kodning i mallfilen. `templateLink`Egenskapen returneras bara vid länkning till en fjärrmall med en URL. Om du använder en lokal mall är den egenskapen inte tillgänglig.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Slutligen använder du variabeln i `uri` egenskapen för en `templateLink` egenskap.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Använda kopiera

Om du vill skapa flera instanser av en resurs med en kapslad mall lägger du till `copy` elementet på `Microsoft.Resources/deployments` resurs nivån. Eller, om omfattningen är `inner` , kan du lägga till kopian i den kapslade mallen.

I följande exempel mall visas hur du använder `copy` med en kapslad mall.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Hämta värden från den länkade mallen

Hämta ett utdata-värde från en länkad mall genom att hämta egenskap svärdet med syntax som: `"[reference('deploymentName').outputs.propertyName.value]"` .

När du hämtar en output-egenskap från en länkad mall får egenskaps namnet inte innehålla något bindestreck.

Följande exempel visar hur du refererar till en länkad mall och hämtar ett utdata-värde. Den länkade mallen returnerar ett enkelt meddelande. Först den länkade mallen:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Huvud mal len distribuerar den länkade mallen och hämtar det returnerade värdet. Observera att den refererar till distributions resursen efter namn och använder namnet på den egenskap som returnerades av den länkade mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

I följande exempel visas en mall som distribuerar en offentlig IP-adress och returnerar resurs-ID för Azure-resursen för den offentliga IP-adressen:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Om du vill använda den offentliga IP-adressen från föregående mall när du distribuerar en belastningsutjämnare länkar du till mallen och deklarerar ett beroende på `Microsoft.Resources/deployments` resursen. Den offentliga IP-adressen för belastningsutjämnaren anges till värdet output från den länkade mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Distributions historik

Resource Manager behandlar varje mall som en separat distribution i distributions historiken. En huvud-mall med tre länkade eller kapslade mallar visas i distributions historiken som:

![Distributions historik](./media/linked-templates/deployment-history.png)

Du kan använda dessa separata poster i historiken för att hämta värden för utdata efter distributionen. Följande mall skapar en offentlig IP-adress och matar ut IP-adressen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Följande mall länkar till föregående mall. Den skapar tre offentliga IP-adresser.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Efter distributionen kan du hämta värdena för utdata med följande PowerShell-skript:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Eller Azure CLI-skript i ett bash-gränssnitt:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Skydda en extern mall

Även om den länkade mallen måste vara extern tillgänglig, behöver den inte vara allmänt tillgänglig för allmänheten. Du kan lägga till din mall till ett privat lagrings konto som endast är tillgängligt för lagrings kontots ägare. Sedan skapar du en signatur för signatur för delad åtkomst (SAS) för att aktivera åtkomst under distributionen. Du lägger till denna SAS-token i URI: n för den länkade mallen. Även om token skickas som en säker sträng, loggas URI: n för den länkade mallen, inklusive SAS-token, i distributions åtgärderna. Ange ett förfallo datum för token för att begränsa exponeringen.

Parameter filen kan också begränsas till åtkomst via en SAS-token.

För närvarande kan du inte länka till en mall i ett lagrings konto som ligger bakom en [Azure Storage brand vägg](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> I stället för att skydda din länkade mall med en SAS-token kan du skapa en [mall-specifikation](template-specs.md). I retemplate-specifikationen lagras huvud mal len och de länkade mallarna som en resurs i din Azure-prenumeration. Du använder Azure RBAC för att bevilja åtkomst till användare som behöver distribuera mallen.

I följande exempel visas hur du skickar en SAS-token vid länkning till en mall:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

I PowerShell får du en token för behållaren och distribuerar mallarna med följande kommandon. Observera att `containerSasToken` parametern definieras i mallen. Det är inte en parameter i `New-AzResourceGroupDeployment` kommandot.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

För Azure CLI i ett bash-gränssnitt får du en token för behållaren och distribuera mallarna med följande kod:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga användnings områden för länkade mallar.

|Huvud mal len  |Länkad mall |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Returnerar en sträng från den länkade mallen. |
|[Load Balancer med offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Returnerar den offentliga IP-adressen från den länkade mallen och anger värdet i belastningsutjämnaren. |
|[Flera IP-adresser](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Skapar flera offentliga IP-adresser i den länkade mallen.  |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: Distribuera en länkad mall](./deployment-tutorial-linked-template.md).
* Information om hur du definierar distributions ordningen för dina resurser finns i [definiera ordningen för distribution av resurser i arm-mallar](define-resource-dependency.md).
* Information om hur du definierar en resurs men skapar många instanser av den finns i [resurs upprepning i arm-mallar](copy-resources.md).
* Anvisningar om hur du konfigurerar en mall i ett lagrings konto och genererar en SAS-token finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md) eller [distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md).
