---
title: Självstudie – anpassade åtgärder & resurser
description: I den här självstudien beskrivs hur du skapar ett Azure-hanterat program med en anpassad Azure-Provider.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 228d1a8bcd97daebae873de4c69cbec238e0c995
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312543"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Självstudie: skapa ett hanterat program med anpassade åtgärder och resurser

I den här självstudien skapar du ett eget hanterat program med anpassade åtgärder och resurser. Det hanterade programmet kommer att innehålla en anpassad åtgärd på `Overview` sidan, en anpassad resurs typ som visas som ett separat meny alternativ i `Table of Content` och en anpassad kontext åtgärd på sidan för en anpassad resurs.

Den här självstudien innehåller följande steg:

> [!div class="checklist"]
> * Redigera definitions fil för användar gränssnitt för att skapa en hanterad program instans
> * Skapa distributions mal len med [Azure anpassad Provider](../custom-providers/overview.md), Azure Storage konto och Azure Function
> * Redigera definitions artefakt för bild med anpassade åtgärder och resurser
> * Distribuera en definition för hanterade program
> * Distribuera en instans av ett hanterat program
> * Utföra anpassade åtgärder och skapa anpassade resurser

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du känna till följande:

* [Skapa och publicera en definition för ett hanterat program](publish-service-catalog-app.md).
* Så här [distribuerar du en tjänst katalog app via Azure Portal](deploy-service-catalog-quickstart.md).
* Så här [skapar du Azure Portal användar gränssnitt för ditt hanterade program](create-uidefinition-overview.md).
* [Visa definitions artefakt](concepts-view-definition.md) funktioner.
* Funktioner i [Azure anpassad Provider](../custom-providers/overview.md) .

## <a name="user-interface-definition"></a>Definition av användar gränssnitt

I den här självstudien skapar du ett hanterat program och dess hanterade resurs grupp kommer att innehålla anpassad Provider-instans, lagrings konto och funktion. Azure-funktionen som används i det här exemplet implementerar ett API som hanterar anpassade Provider-åtgärder för åtgärder och resurser. Azure Storage kontot används som grundläggande lagring för dina anpassade Provider-resurser.

Definitionen av användar gränssnittet för att skapa en hanterad program instans innehåller `funcname` och `storagename` inmatade element. Lagrings kontots namn och funktions namn måste vara globalt unikt. Som standard kommer filerna att distribueras från [exempel funktions paketet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), men du kan ändra det genom att lägga till ett inmatat element för en paket länk i *createUiDefinition.jspå*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

och utdata i *createUiDefinition.jspå*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Du hittar en fullständig *createUiDefinition.jsför* samplet i [referensen: artefakter för användar gränssnitts element](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Mall med anpassad Provider

Om du vill skapa en hanterad program instans med en anpassad Provider måste du definiera en anpassad Provider-resurs med namnet **offentlig** och skriva **Microsoft. CustomProviders/resourceProviders** i **mainTemplate.jspå**. I den resursen definierar du resurs typer och åtgärder för din tjänst. För att distribuera Azure Function-och Azure Storage konto instanser definiera resurser av typen `Microsoft.Web/sites` respektive `Microsoft.Storage/storageAccounts` .

I den här självstudien får du skapa en `users` resurs typ, en `ping` anpassad åtgärd och en `users/contextAction` anpassad åtgärd som ska utföras i en kontext för en `users` anpassad resurs. För varje resurs typ och åtgärd tillhandahåller en slut punkt som pekar på funktionen med namnet som anges i [createUiDefinition.jspå](#user-interface-definition). Ange **routingType** som `Proxy,Cache` för resurs typer och `Proxy` för åtgärder:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Du hittar den fullständiga *mainTemplate.jsför* samplet på [referensen: distributions mal len artefakt](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Visa definitionsartefakt

Om du vill definiera ett användar gränssnitt som innehåller anpassade åtgärder och anpassade resurser i det hanterade programmet måste du skapa **viewDefinition.jspå** artefakten. Mer information om Visa definitions artefakt finns [i Visa definitions artefakt i Azure Managed Applications](concepts-view-definition.md).

I den här självstudien definierar du:
* En *översikts* sida med en verktygsfälts knapp som representerar en anpassad åtgärd `TestAction` med grundläggande text ingångar.
* En *användare* -sida som representerar en anpassad resurs typ `users` .
* En anpassad resurs åtgärd `users/contextAction` på sidan *användare* som ska utföras i ett sammanhang med en anpassad resurs av typen `users` .

I följande exempel visas Visa konfiguration för en "Översikt"-sida:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

I exemplet nedan visas sidan "användare" för resurs konfiguration med anpassad resurs åtgärd:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Det går att hitta den fullständiga *viewDefinition.jsför* samplet i [referensen: Visa definitions artefakt](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definition av hanterade program

Paketera följande hanterade program artefakter till zip-arkivet och överför dem till lagring:

* createUiDefinition.jspå
* mainTemplate.jspå
* viewDefinition.jspå

Alla filer måste finnas på rotnivå. Paketet med artefakter kan lagras i vilken lagring som helst, till exempel GitHub BLOB eller Azure Storage konto-blob. Här är ett skript för att ladda upp programpaketet till lagrings kontot: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Kör Azure CLI-skriptet nedan eller följ stegen i Azure Portal för att distribuera en definition av tjänst katalog hanterade program:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du och väljer **hanterade program Center**.
2. I **Center för hanterade program** väljer du **tjänst katalog program definition** och klickar på **Lägg till**. 
    
    ![Lägg till tjänst katalog](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Ange värden för att skapa en tjänst katalog definition:

    * Ange ett unikt **namn** för tjänst katalog definitionen, **visnings namn** och *Beskrivning*(valfritt).
    * Välj den **prenumeration**, **resurs grupp** och **plats** där program definitionen ska skapas. Du kan använda samma resurs grupp som används för zip-paket eller skapa en ny resurs grupp.
    * För en **paket fil-URI** anger du sökvägen till zip-filen som du skapade i föregående steg.

    ![Ange värden](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. När du kommer till avsnittet autentisering och lås nivå väljer du **Lägg till auktorisering**.

    ![Lägg till auktorisering](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Välj en Azure Active Directory grupp för att hantera resurserna och välj **OK**.

   ![Lägg till auktoriseringsregel](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. När du har angett alla värden väljer du **skapa**.

   ![Skapa definition av hanterade program](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Hanterad program instans

När den hanterade program definitionen distribueras kör du skriptet nedan eller följer stegen i Azure Portal för att distribuera den hanterade program instansen med anpassad provider:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du och väljer **hanterade program Center**.
2. I den **hanterade program Center** väljer du **tjänst katalog program** och klickar på **Lägg till**. 

    ![Lägg till hanterat program](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. På sidan **tjänst katalog program** anger du visnings namn för tjänst katalog definition i sökrutan. Välj den definition som skapades i föregående steg och klicka på **skapa**.

    ![Välj tjänstkatalog](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Ange värden för att skapa en hanterad program instans från tjänst katalog definitionen:

    * Välj den **prenumeration**, **resurs grupp** och **plats** där program instansen ska skapas.
    * Ange ett unikt namn på Azure-Function och Azure Storage konto namn.

    ![Programinställningar](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. När verifieringen är klar klickar du på **OK** för att distribuera en instans av ett hanterat program. 
    
    ![Distribuera hanterat program](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Anpassade åtgärder och resurser

När tjänst katalogens program instans har distribuerats har du två nya resurs grupper. Den första resurs gruppen `applicationGroup` innehåller en instans av det hanterade programmet, den andra resurs gruppen innehåller `managedResourceGroup` resurserna för det hanterade programmet, inklusive **anpassad Provider**.

![Program resurs grupper](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Du kan gå till den hanterade program instansen och utföra **anpassad åtgärd** på sidan "Översikt", skapa anpassade resurser för **användare** på sidan "användare" och köra **anpassad kontext åtgärd** på en anpassad resurs.

* Gå till sidan Översikt och klicka på knappen ping-åtgärd:

![Utför anpassad åtgärd](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Gå till sidan användare och klicka på knappen Lägg till. Ange indata för att skapa en resurs och skicka formuläret:

![Skärm bild som visar knappen Lägg till som valts från användare.](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Gå till sidan användare, Välj en "användare"-resurs och klicka på "anpassad kontext åtgärd":

![Skärm bild som visar en anpassad kontext åtgärd vald.](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Söker efter hjälp

Om du har frågor om Azure Managed Applications kan du prova att använda [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managed-app) med taggen Azure-Managed-app eller [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-managed-applications.html) med taggen Azure-Managed-Application. En liknande fråga kanske redan har blivit ombeddd och besvarad, så kontrol lera först innan du publicerar. Använd respektive taggar för snabbare svar. 

## <a name="next-steps"></a>Nästa steg

Information om hur du publicerar ditt hanterade program till Azure Marketplace finns i [Azure-hanterade program på Marketplace](../../marketplace/create-new-azure-apps-offer.md).

Läs mer om [Azure-anpassade leverantörer](../custom-providers/overview.md).
