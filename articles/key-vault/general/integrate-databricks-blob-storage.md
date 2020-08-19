---
title: Få åtkomst till Azure Blob Storage med Azure Databricks och Azure Key Vault
description: I den här självstudien får du lära dig hur du får åtkomst till Azure Blob Storage från Azure Databricks med hjälp av en hemlighet som lagras i Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: bb574bb3dd000682090c6c3f861e885761753e19
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588525"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Självstudie: få åtkomst till Azure Blob Storage med Azure Databricks och Azure Key Vault

I den här självstudien får du lära dig hur du får åtkomst till Azure Blob Storage från Azure Databricks med hjälp av en hemlighet som lagras i Azure Key Vault. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett lagrings konto och en BLOB-behållare med Azure CLI
> * Skapa en Key Vault och ange en hemlighet
> * Skapa en Azure Databricks arbets yta och Lägg till Key Vault hemligt omfång
> * Få åtkomst till din BLOB-behållare från Azure Databricks arbets yta

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Innan du börjar den här självstudien installerar du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Skapa ett lagrings konto och en BLOB-behållare med Azure CLI

Du måste först skapa ett allmänt lagrings konto för att kunna använda blobbar. Om du inte har en [resurs grupp](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)skapar du en innan du kör kommandot. Följande kommando skapar och visar lagrings behållarens metadata. Kopiera ned **ID: t**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Konsolens utdata av kommandot ovan. ID är markerat i grönt för slutanvändaren att se.](../media/databricks-command-output-1.png)

Innan du kan skapa en behållare för att överföra blobben till måste du tilldela rollen [Storage BLOB data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) till dig själv. I det här exemplet tilldelas rollen till det lagrings konto som du har gjort tidigare.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Nu när du har tilldelat rollen som lagrings konto kan du skapa en behållare för din BLOB.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

När behållaren har skapats kan du ladda upp en BLOB (valfri fil) till behållaren. I det här exemplet överförs en. txt-fil med HelloWorld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Visa en lista över blobarna i behållaren för att kontrol lera att behållaren har den.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Konsolens utdata av kommandot ovan. Den visar den fil som precis lagrades i behållaren.](../media/databricks-command-output-2.png)

Hämta **KEY1** -värdet för din lagrings behållare med hjälp av följande kommando. Kopiera värdet nedåt.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Konsolens utdata av kommandot ovan. Värdet för KEY1 är markerat i en grön ruta.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Skapa en Key Vault och ange en hemlighet

Du skapar en Key Vault med hjälp av följande kommando. Det här kommandot visar även metadata för Key Vault. Kopiera ned **ID-** och **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Avbildnings ](../media/databricks-command-output-4.png)
 ![ konsolens utdata av kommandot ovan. ID: t och vaultUri är båda markerade i grönt för att användaren ska kunna se den.](../media/databricks-command-output-5.png)

Använd följande kommando för att skapa hemligheten. Ange värdet för hemligheten till **KEY1** -värdet från ditt lagrings konto.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Skapa en Azure Databricks arbets yta och Lägg till Key Vault hemligt omfång

Det här avsnittet kan inte slutföras via kommando raden. Följ den här [guiden](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Du måste ha åtkomst till [Azure Portal](https://ms.portal.azure.com/#home) för att:

1. Skapa din Azure Databricks-resurs
1. Starta din arbets yta
1. Skapa ett omvänt hemligt omfång som Key Vault

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Få åtkomst till din BLOB-behållare från Azure Databricks arbets yta

Det här avsnittet kan inte slutföras via kommando raden. Följ den här [guiden](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Du måste använda arbets ytan Azure Databricks för att:

1. Skapa ett **nytt kluster**
1. Skapa en **ny antecknings bok**
1. Fyll i motsvarande fält i python-skriptet
1. Kör python-skriptet

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Nästa steg

Kontrol lera att Key Vault går att återskapa:
> [!div class="nextstepaction"]
> [Rensa dina resurser](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
