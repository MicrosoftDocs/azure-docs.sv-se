---
title: Flytta ett Azure Storage till en annan region | Microsoft Docs
description: Visar hur du flyttar ett Azure Storage till en annan region.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 1900326bf03c6a32f25c7a019d8bd1e460735bd6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505606"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Flytta ett Azure Storage till en annan region

Om du vill flytta ett lagringskonto skapar du en kopia av lagringskontot i en annan region. Flytta sedan dina data till det kontot med hjälp av AzCopy eller något annat verktyg.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> 
> * Exportera en mall.
> * Ändra mallen genom att lägga till målregionen och lagringskontots namn.
> * Distribuera mallen för att skapa det nya lagringskontot.
> * Konfigurera det nya lagringskontot.
> * Flytta data till det nya lagringskontot.
> * Ta bort resurserna i källregionen.

## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att tjänsterna och funktionerna som kontot använder stöds i målregionen.

- För förhandsgranskningsfunktioner ser du till att din prenumeration är tillåten för målregionen.

<a id="prepare"></a>

## <a name="prepare"></a>Förbereda

Kom igång genom att exportera och sedan ändra en Resource Manager mall. 

### <a name="export-a-template"></a>Exportera en mall

Den här mallen innehåller inställningar som beskriver ditt lagringskonto. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här exporterar du en mall med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla resurser** och välj sedan ditt lagringskonto.

3. Välj > **Automation Exportera**  >  **mall**.

4. Välj **Ladda** ned på **bladet Exportera** mall.

5. Leta upp ZIP-filen som du laddade ned från portalen och packa upp filen till valfri mapp.

   Zip-filen innehåller de .json-filer som utgör mallen och skripten för att distribuera mallen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Så här exporterar du en mall med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration [med kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen till prenumerationen på det lagringskonto som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för ditt källlagringskonto. De här kommandona sparar en json-mall till din aktuella katalog.

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

Så här distribuerar du mallen med hjälp Azure Portal:

1. I Azure Portal väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution och** trycker sedan på **RETUR.**

3. Välj **Malldistribution**.

    ![Azure Resource Manager-mallbibliotek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Välj **Skapa**.

5. Välj **Skapa en egen mall i redigeraren**.

6. Välj **Läs in** fil och följ sedan anvisningarna för att läsa intemplate.js **fil** som du laddade ned i det senaste avsnittet.

7. I filen **template.jsnamnet** på mållagringskontot genom att ange standardvärdet för lagringskontots namn. I det här exemplet anges standardvärdet för lagringskontots namn till `mytargetaccount` .
    
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
    Information om hur du hämtar regionkoder finns [i Azure-platser.](https://azure.microsoft.com/global-infrastructure/locations/)  Koden för en region är regionnamnet utan blanksteg, USA, **centrala**  =  **USA, centrala.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Så här distribuerar du mallen med hjälp av PowerShell:

1. I filen **template.jsnamnet** på mållagringskontot genom att ange standardvärdet för lagringskontots namn. I det här exemplet anges standardvärdet för lagringskontots namn till `mytargetaccount` .
    
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

2. Redigera **platsegenskapen** **itemplate.jsfil** till målregionen. I det här exemplet anges målregionen till `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Du kan hämta regionkoder genom att köra [kommandot Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett nytt lagringskonto i målregionen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Spara **template.jsfilen.**

2. Ange eller välj egenskapsvärdena:

- **Prenumeration:** Välj en Azure-prenumeration.

- **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.

- **Plats:** Välj en Azure-plats.

3. Klicka på **kryssrutan Jag godkänner villkoren som anges** ovan och klicka sedan på knappen **Välj** köp.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hämta prenumerations-ID:t där du vill distribuera den offentliga [mål-IP-adressen med Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Använd de här kommandona för att distribuera mallen:

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
| **Principer för livscykelhantering** | [Hantera Azure Blob Storage-livscykeln](../blobs/storage-lifecycle-management-concepts.md) |
| **Statiska webbplatser** | [Vara värd för en statisk webbplats i Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Prenumerationer på händelser** | [Reagera på Blob Storage-händelser](../blobs/storage-blob-event-overview.md) |
| **Aviseringar** | [Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Innehållsleverantörsnätverk (CDN)** | [Använd Azure CDN åtkomst till blobar med anpassade domäner över HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Om du ställer in ett CDN för källlagringskontot ändrar du bara ursprunget för ditt befintliga CDN till slutpunkten för den primära blobtjänsten (eller slutpunkten för den primära statiska webbplatsen) för ditt nya konto. 

### <a name="move-data-to-the-new-storage-account"></a>Flytta data till det nya lagringskontot

AzCopy är det bästa verktyget för att flytta över dina data. Den är optimerad för prestanda.  En sak som gör den snabbare är att data kopieras direkt mellan lagringsservrar, och därför använder AzCopy inte datorns nätverksbandbredd. Använd AzCopy på kommandoraden eller som en del av ett anpassat skript. Se [Kom igång med AzCopy.](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Du kan också använda Azure Data Factory för att flytta över dina data. Det ger ett intuitivt användargränssnitt. Om du Azure Data Factory kan du gå till någon av dessa länkar:. 

  - [Kopiera data till och från Azure Blob Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiera data till och från Azure File Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Kopiera data till och från Azure Table Storage med hjälp av Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Ta bort eller rensa

Om du vill börja om efter distributionen kan du ta bort mållagringskontot [](#move) och upprepa stegen som beskrivs i [avsnitten Förbered](#prepare) och Flytta i den här artikeln.

Ta bort källagringskontot för att genomföra ändringarna och slutföra flyttningen av ett lagringskonto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort ett lagringskonto med Azure-portalen:

1. I Azure Portal expanderar du menyn till vänster för att öppna tjänstemenyn och väljer **Lagringskonton** för att visa listan över dina lagringskonton.

2. Leta upp mållagringskontot som du  vill ta bort och högerklicka på knappen Mer (**...**) till höger om listan.

3. Välj **Ta** bort och bekräfta.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resursgruppen och dess associerade resurser, inklusive det nya lagringskontot, använder du kommandot [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat ett Azure Storage-konto från en region till en annan och rensat källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../../site-recovery/azure-to-azure-tutorial-migrate.md)