---
title: Få åtkomst till Azure Blob Storage med Azure Databricks och Azure Key Vault
description: I den här självstudien lär du dig att komma åt Azure Blob Storage från Azure Databricks med hjälp av en hemlighet som lagras i Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: be1b0da23841b5a63ec044e04a5465e29345f9d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772207"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Självstudie: Komma Azure Blob Storage med Azure Databricks och Azure Key Vault

I den här självstudien lär du dig att komma åt Azure Blob Storage från Azure Databricks en hemlighet som lagras i Azure Key Vault. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto och en blobcontainer med Azure CLI
> * Skapa en Key Vault och ange en hemlighet
> * Skapa en Azure Databricks arbetsyta och lägga Key Vault ett hemligt omfång
> * Få åtkomst till din blobcontainer Azure Databricks arbetsyta

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Innan du börjar med den här självstudien installerar [du Azure CLI](/cli/azure/install-azure-cli-windows).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Skapa ett lagringskonto och en blobcontainer med Azure CLI

Du måste först skapa ett allmänt lagringskonto för att kunna använda blobar. Om du inte har någon [resursgrupp skapar](/cli/azure/group#az_group_create)du en innan du kör kommandot. Följande kommando skapar och visar metadata för lagringscontainern. Kopiera **ID:t**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Konsolens utdata från kommandot ovan. ID är markerat i grönt för slutanvändaren att se.](../media/databricks-command-output-1.png)

Innan du kan skapa en container att ladda upp bloben till måste du tilldela rollen [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. I det här exemplet tilldelas rollen till det lagringskonto som du skapade tidigare.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Nu när du har tilldelar rollen till lagringskontot kan du skapa en container för din blob.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

När containern har skapats kan du ladda upp en blob (valfri fil) till den containern. I det här exemplet laddas en .txt-fil med helloworld upp.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Visa en lista över blobarna i containern för att verifiera att containern har den.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Konsolens utdata från kommandot ovan. Den visar filen som just lagrades i containern.](../media/databricks-command-output-2.png)

Hämta **värdet key1** för lagringscontainern med hjälp av följande kommando. Kopiera värdet nedåt.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Konsolens utdata från kommandot ovan. Värdet för key1 är markerat i en grön ruta.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Skapa en Key Vault och ange en hemlighet

Du skapar en Key Vault med följande kommando. Det här kommandot visar metadata för Key Vault också. Kopiera ID och **vaultUri** . 

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![](../media/databricks-command-output-4.png)
 ![ Avbildningskonsolens utdata från kommandot ovan. ID och vaultUri är båda markerade i grönt för användaren att se.](../media/databricks-command-output-5.png)

Använd följande kommando för att skapa hemligheten. Ange värdet för hemligheten till **värdet key1** från ditt lagringskonto.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Skapa en Azure Databricks och lägga till Key Vault hemlighetsomfång

Det här avsnittet kan inte slutföras via kommandoraden. Följ den här [guiden](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Du måste komma åt följande [Azure Portal:](https://ms.portal.azure.com/#home)

1. Skapa din Azure Databricks resurs
1. Starta din arbetsyta
1. Skapa ett Key Vault hemlighetsomfång som stöds

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Få åtkomst till din blobcontainer Azure Databricks arbetsyta

Det här avsnittet kan inte slutföras via kommandoraden. Följ den här [guiden](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Du måste använda arbetsytan för Azure Databricks för att:

1. Skapa ett **nytt kluster**
1. Skapa en **ny notebook-dator**
1. Fyll i motsvarande fält i Python-skriptet
1. Köra Python-skriptet

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Nästa steg

Kontrollera att Key Vault kan återställas:
> [!div class="nextstepaction"]
> [Rensa dina resurser](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)