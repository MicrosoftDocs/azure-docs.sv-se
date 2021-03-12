---
title: Konfigurera Kundhanterade nycklar för Azure API för FHIR
description: Ta med din egen nyckel funktion som stöds i Azure API för FHIR via Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: 08b5f63f1fffc2369eff620ca1937f298c2d9ca5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020390"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurera Kundhanterade nycklar i vila

När du skapar ett nytt Azure API för FHIR-konto krypteras dina data med hjälp av Microsoft-hanterade nycklar som standard. Nu kan du lägga till ett sekundärt krypterings lager för data med hjälp av din egen nyckel som du väljer och hanterar själv.

I Azure utförs detta vanligt vis med hjälp av en krypterings nyckel i kundens Azure Key Vault. Azure SQL, Azure Storage och Cosmos DB är några exempel som tillhandahåller den här funktionen idag. Azure API för FHIR utnyttjar detta stöd från Cosmos DB. När du skapar ett konto kan du välja att ange en Azure Key Vault nyckel-URI. Den här nyckeln skickas till Cosmos DB när DB-kontot har tillhandahållits. När en FHIR-begäran görs hämtar Cosmos DB nyckeln och använder den för att kryptera/dekryptera data. För att komma igång kan du referera till följande länkar:

- [Registrera Azure Cosmos DB Resource Provider för din Azure-prenumeration](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurera Azure Key Vault-instansen](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Lägg till en åtkomst princip till Azure Key Vault-instansen](../../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generera en nyckel i Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Använda Azure Portal

När du skapar ditt Azure API för FHIR-konto på Azure Portal kan du se konfigurations alternativet "data kryptering" under "databas inställningar" på fliken "ytterligare inställningar". Som standard väljs alternativet tjänst hanterad nyckel. 

Du kan välja din nyckel från nyckel väljaren:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="-Väljaren":::

Du kan också ange din Azure Key Vault nyckel här genom att välja alternativet "kundhanterad nyckel". Du kan ange nyckel-URI: n här:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Skapa Azure API för FHIR":::

För befintliga FHIR-konton kan du Visa nyckel krypterings valet (tjänst-eller kundhanterad nyckel) i bladet "databas" enligt nedan. Konfigurations alternativet kan inte ändras när det har valts. Du kan dock ändra och uppdatera din nyckel.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Databas":::

Dessutom kan du skapa en ny version av den angivna nyckeln, efter vilken dina data kommer att krypteras med den nya versionen utan avbrott i tjänsten. Du kan också ta bort åtkomsten till nyckeln för att ta bort åtkomsten till data. När nyckeln är inaktive rad leder frågorna till ett fel. Om nyckeln har Aktiver ATS på nytt kommer frågorna att lyckas.




## <a name="using-azure-powershell"></a>Använda Azure PowerShell

Med din Azure Key Vault nyckel-URI kan du konfigurera CMK med PowerShell genom att köra PowerShell-kommandot nedan:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Använda Azure CLI

Precis som med PowerShell-metoden kan du konfigurera CMK genom att skicka din Azure Key Vault nyckel-URI under `key-vault-key-uri` parametern och köra CLI-kommandot nedan: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager mall

Med din Azure Key Vault nyckel-URI kan du konfigurera CMK genom att skicka den under egenskapen **keyVaultKeyUri** i objektet **Properties** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Och du kan distribuera mallen med följande PowerShell-skript:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar Kundhanterade nycklar i vila med hjälp av Azure Portal, PowerShell, CLI och Resource Manager-mall. Du kan titta närmare på avsnittet Azure Cosmos DB vanliga frågor och svar om du behöver: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: Konfigurera CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)