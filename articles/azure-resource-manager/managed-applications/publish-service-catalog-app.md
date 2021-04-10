---
title: Publicera hanterad app för tjänst katalog
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093638"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Snabbstart: Skapa och publicera en definition för det hanterade programmet

Den här snabb starten ger en introduktion till att arbeta med [Azure Managed Applications](overview.md). Du kan skapa och publicera ett hanterat program som är avsett för medlemmar i din organisation.

Om du vill publicera ett hanterat program i tjänst katalogen måste du:

* Skapa en mall som definierar resurserna som ska distribueras med det hanterade programmet.
* Definiera användargränssnittselementen för portalen när du distribuerar det hanterade programmet.
* Skapa ett _zip_ -paket som innehåller de mallfiler som krävs.
* Bestämma vilken användare, vilken grupp eller vilket program som behöver åtkomst till resursgruppen i användarens prenumeration.
* Skapa definitionen för det hanterade programmet som pekar på _. zip_ -paketet och begär åtkomst till identiteten.

## <a name="create-the-arm-template"></a>Skapa ARM-mallen

Alla definitioner för hanterade program innehåller en fil med namnet _mainTemplate.json_. I den filen definierar du Azure-resurserna som ska distribueras. Mallen är inte annorlunda än en normal ARM-mall.

Skapa en fil med namnet _mainTemplate.json_. Namnet är skiftlägeskänsligt.

Lägg till följande JSON i filen. Den definierar parametrarna för att skapa ett lagringskonto och anger egenskaperna för lagringskontot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Spara _mainTemplate.jspå_ filen.

## <a name="define-your-create-experience"></a>Definiera din skapa upplevelse

Som utgivare definierar du Portal upplevelsen för att skapa det hanterade programmet. _createUiDefinition.jspå_ filen genererar Portal gränssnittet. Du definierar hur användare anger indatatyper för varje parameter med [kontroll element](create-uidefinition-elements.md) , inklusive List rutor, text rutor och lösen ord.

Skapa en fil med namnet _createUiDefinition.jspå_ (detta namn är Skift läges känsligt)

Lägg till följande start-JSON i filen och spara den.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

Mer information finns i [Kom igång med CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Paketera filerna

Lägg till de två filerna i en _. zip_ -fil med namnet _app.zip_. De två filerna måste finnas på rot nivån i _. zip_ -filen. Om du lägger dem i en mapp får du ett felmeddelande när du skapar definitionen för det hanterade programmet, som anger att de nödvändiga filerna saknas.

Ladda upp paketet till en tillgänglig plats som det kan användas från. Du måste ange ett unikt namn för lagrings kontot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Skapa en Azure Active Directory-användargrupp eller ett Azure Active Directory-program

Nästa steg är att välja en användar grupp, användare eller ett program för att hantera resurser för kunden. Den här identiteten har behörigheter på den hanterade resursgruppen enligt den roll som är tilldelad. Rollen kan vara valfri inbyggd Azure-roll som ägare eller deltagare. Information om hur du skapar en ny Active Directory-användargrupp finns i [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Du behöver objekt-ID:t för den användargrupp som du vill använda för att hantera resurser.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Hämta rolldefinitions-ID:t

Sedan behöver du roll Definitions-ID: t för den inbyggda Azure-rollen som du vill bevilja åtkomst till användaren, användar gruppen eller programmet. Normalt använder du rollen Ägare, Deltagare eller Läsare. Följande kommando visar hur du hämtar rolldefinitions-ID:t för ägarrollen:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

Om du inte redan har en resursgrupp där det hanterade programmets definition kan lagras skapar du en nu:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Skapa nu definitionsresursen för det hanterade programmet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

När kommandot har slutförts har du en definition för det hanterade programmet i resursgruppen.

Några av de parametrar som användes i det föregående exemplet är:

* **resurs grupp**: namnet på resurs gruppen där definitionen för det hanterade programmet skapas.
* **Lås nivå**: typen av lås som placerats i den hanterade resurs gruppen. Det förhindrar kunden från att utföra oönskade åtgärder på den här resursgruppen. ReadOnly stöds för närvarande endast på låsnivå. När ReadOnly har angivits kan kunden endast läsa resurser som finns i den hanterade resursgruppen. Utgivaridentiteter som beviljas åtkomst till den hanterade resursgruppen är undantagna från låset.
* **auktorisering**: Beskriver huvudkontots ID och rolldefinitions-ID som används för att ge behörigheter till den hanterade resursgruppen. Det anges i formatet `<principalId>:<roleDefinitionId>`. Om det krävs fler än ett värde anger du dem i formatet `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Värdena avgränsas med kommatecken.
* **paket filens URI**: platsen för ett _. zip_ -paket som innehåller de filer som krävs.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Ta med din egen lagring för definitionen av det hanterade programmet

Du kan välja att lagra definitionen för det hanterade programmet i ett lagrings konto som du har angett under skapandet, så att dess plats och åtkomst kan hanteras helt av dig enligt dina krav.

> [!NOTE]
> Det finns bara stöd för att ta med din egen lagring med ARM-mall eller REST API distributioner av definitionen för hanterade program.

### <a name="select-your-storage-account"></a>Välj ditt lagringskonto

Du måste [skapa ett lagrings konto](../../storage/common/storage-account-create.md) som innehåller din definition av hanterade program för användning med tjänst katalogen.

Kopiera lagrings kontots resurs-ID. Den kommer att användas senare när du distribuerar definitionen.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Ange roll tilldelningen för "utrustnings resurs leverantör" i ditt lagrings konto

Innan din definition av hanterade program kan distribueras till ditt lagrings konto måste du ge deltagar behörighet till enhets **resurs leverantörs** rollen så att den kan skriva definitions filerna till lagrings kontots behållare.

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com).
1. Välj **åtkomst kontroll (IAM)** om du vill visa inställningarna för åtkomst kontroll för lagrings kontot. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
1. I fönstret **Lägg till roll tilldelning** väljer du rollen **deltagare** .
1. I fältet **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**.
1. Under **Välj**, söker du efter enhets **resurs leverantörs** rollen och markerar den.
1. Spara roll tilldelningen.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Distribuera den hanterade program definitionen med en ARM-mall

Använd följande ARM-mall för att distribuera ditt paketerade hanterade program som en ny definition för hanterade program i tjänst katalogen vars definitionsfiler lagras och bevaras i ditt eget lagrings konto:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

Vi har lagt till en ny egenskap med namnet `storageAccountId` på dina `applicationDefinitions` egenskaper och ange det lagrings konto-ID som du vill lagra definitionen i som dess värde:

Du kan kontrol lera att programdefinitions-filerna sparas i det angivna lagrings kontot i en behållare med rubriken `applicationDefinitions` .

> [!NOTE]
> För ökad säkerhet kan du skapa en definition för hanterade program i en [Azure Storage-konto-BLOB där kryptering är aktiverat](../../storage/common/storage-service-encryption.md). Definitions innehållet krypteras via lagrings kontots krypterings alternativ. Endast användare med behörighet till filen kan se definitionen i tjänst katalogen.

## <a name="make-sure-users-can-see-your-definition"></a>Kontrollera att användare kan se din definition

Du har åtkomst till definitionen för hanterade program, men du vill kontrollera att andra användare i din organisation kan komma åt den. Ge dem minst rollen Läsare på definitionen. De kan ha ärvt den här åtkomstnivån från prenumerationen eller resursgruppen. Om du vill kontrol lera vem som har åtkomst till definitionen och lägga till användare eller grupper, se [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

Du har publicerat definitionen av det hanterade programmet. Nu ska du lära dig hur du distribuerar en instans av den definitionen.

> [!div class="nextstepaction"]
> [Snabbstart: Distribuera tjänstkatalogapp](deploy-service-catalog-quickstart.md)
