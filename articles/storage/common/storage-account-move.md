---
title: Flytta ett Azure Storage-konto till en annan region | Microsoft Docs
description: Visar hur du flyttar ett Azure Storage-konto till en annan region.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: d894ce71e0ffa5a0894a1f6b0035efe66271ded8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591472"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Flytta ett Azure Storage-konto till en annan region

Om du vill flytta ett lagringskonto skapar du en kopia av lagringskontot i en annan region. Sedan flyttar du dina data till det kontot med hjälp av AzCopy eller ett annat verktyg som du själv väljer.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> 
> * Exportera en mall.
> * Ändra mallen genom att lägga till mål regionen och lagrings kontots namn.
> * Distribuera mallen för att skapa det nya lagrings kontot.
> * Konfigurera det nya lagrings kontot.
> * Flytta data till det nya lagrings kontot.
> * Ta bort resurserna i käll regionen.

## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att tjänsterna och funktionerna som kontot använder stöds i målregionen.

- För för hands versions funktioner ser du till att din prenumeration är allowlisted för mål regionen.

<a id="prepare"></a>

## <a name="prepare"></a>Förbereda

För att komma igång, exportera och sedan ändra en Resource Manager-mall. 

### <a name="export-a-template"></a>Exportera en mall

Den här mallen innehåller inställningar som beskriver ditt lagringskonto. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här exporterar du en mall med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan ditt lagrings konto.

3. Välj   >  **Exportera mall** för > inställningar.

4. Välj **Hämta** på bladet **Exportera mall** .

5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp.

   Den här zip-filen innehåller de JSON-filer som utgör mallen och skripten för att distribuera mallen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Exportera en mall med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för ditt käll lagrings konto. De här kommandona sparar en JSON-mall i den aktuella katalogen.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Ändra mallen 

Ändra mallen genom att ändra namn och region för lagringskontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Distribuera mallen med hjälp av Azure Portal:

1. I Azure Portal väljer du **skapa en resurs**.

2. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.

3. Välj **malldistribution**.

    ![Azure Resource Manager-mallbibliotek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Välj **Skapa**.

5. Välj **Bygg en egen mall i redigeraren**.

6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.

7. I **template.js** filen namnger du mål lagrings kontot genom att ange standardvärdet för lagrings konto namnet. I det här exemplet anges standardvärdet för lagrings konto namnet till `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA**  =  **, centrala**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Distribuera mallen med hjälp av PowerShell:

1. I **template.js** filen namnger du mål lagrings kontot genom att ange standardvärdet för lagrings konto namnet. I det här exemplet anges standardvärdet för lagrings konto namnet till `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Redigera **plats** egenskapen i **template.jspå** fil till mål regionen. I det här exemplet anges mål regionen till `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Du kan hämta region koder genom att köra kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett nytt lagringskonto i målregionen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Spara **template.jspå** filen.

2. Ange eller Välj egenskaps värden:

- **Prenumeration**: Välj en Azure-prenumeration.

- **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.

- **Plats**: Välj en Azure-plats.

3. Klicka på kryss rutan **Jag accepterar villkoren som anges ovan** och klicka sedan på knappen **Välj inköp** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hämta det prenumerations-ID där du vill distribuera den offentliga mål-IP-adressen med [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Använd följande kommandon för att distribuera mallen:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurera det nya lagringskontot

Vissa funktioner kan inte exporteras till en mall, så du måste lägga till dem i det nya lagringskontot. 

I följande tabell visas dessa funktioner tillsammans med vägledning för hur du lägger till dem i ditt nya lagringskonto.

| Funktion    | Vägledning    |
|--------|-----------|
| **Principer för livs cykel hantering** | [Hantera Azure Blob Storage-livscykeln](../blobs/storage-lifecycle-management-concepts.md) |
| **Statiska webbplatser** | [Vara värd för en statisk webbplats i Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Prenumerationer på händelser** | [Reagera på Blob Storage-händelser](../blobs/storage-blob-event-overview.md) |
| **Aviseringar** | [Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Innehållsleverantörsnätverk (CDN)** | [Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Om du ställer in ett CDN för käll lagrings kontot ändrar du bara ursprunget för din befintliga CDN till den primära BLOB-tjänstens slut punkt (eller den primära statiska webbplats slut punkten) för ditt nya konto. 

### <a name="move-data-to-the-new-storage-account"></a>Flytta data till det nya lagringskontot

AzCopy är det bästa verktyget för att flytta data över. Den är optimerad för prestanda.  En sak som gör den snabbare är att data kopieras direkt mellan lagringsservrar, och därför använder AzCopy inte datorns nätverksbandbredd. Använd AzCopy på kommandoraden eller som en del av ett anpassat skript. Se [Kom igång med AZCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Du kan också använda Azure Data Factory för att flytta dina data. Det ger ett intuitivt användar gränssnitt. Om du vill använda Azure Data Factory, se någon av följande länkar:. 

  - [Kopiera data till och från Azure Blob Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiera data till och från Azure File Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Kopiera data till och från Azure Table Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Ta bort eller rensa

Om du vill börja om efter distributionen kan du ta bort mål lagrings kontot och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Ta bort källagringskontot för att genomföra ändringarna och slutföra flyttningen av ett lagringskonto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort ett lagringskonto med Azure-portalen:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna menyn med tjänster och väljer **lagrings konton** för att visa listan över dina lagrings konton.

2. Leta upp mål lagrings kontot som du vill ta bort och högerklicka på knappen **mer** (**...**) på höger sida av listan.

3. Välj **ta bort** och bekräfta.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resurs gruppen och dess kopplade resurser, inklusive det nya lagrings kontot, använder du kommandot [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat ett Azure Storage-konto från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../../site-recovery/azure-to-azure-tutorial-migrate.md)