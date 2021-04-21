---
title: Konfigurera anonym offentlig läsåtkomst för containrar och blobar
titleSuffix: Azure Storage
description: Lär dig hur du tillåter eller tillåter anonym åtkomst till blobdata för lagringskontot. Ange inställningen för offentlig åtkomst för containern för att göra containrar och blobar tillgängliga för anonym åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 31812a7b2dddad474ab5cd422a15f6e5368dba5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774637"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurera anonym offentlig läsåtkomst för containrar och blobar

Azure Storage har stöd för valfri anonym offentlig läsåtkomst för containrar och blobar. Som standard tillåts aldrig anonym åtkomst till dina data. Om du inte uttryckligen aktiverar anonym åtkomst måste alla begäranden till en container och dess blobar godkännas. När du konfigurerar en containers inställning för offentlig åtkomstnivå för att tillåta anonym åtkomst kan klienter läsa data i containern utan att auktorisera begäran.

> [!WARNING]
> När en container har konfigurerats för offentlig åtkomst kan alla klienter läsa data i containern. Offentlig åtkomst utgör en potentiell säkerhetsrisk, så om ditt scenario inte kräver det rekommenderar Microsoft att du inte tillåter det för lagringskontot. Mer information finns i Förhindra [anonym offentlig läsåtkomst till containrar och blobar.](anonymous-read-access-prevent.md)

Den här artikeln beskriver hur du konfigurerar anonym offentlig läsåtkomst för en container och dess blobar. Information om hur du kommer åt blobdata anonymt från ett klientprogram finns i Få åtkomst till offentliga containrar och [blobar anonymt med .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Om anonym offentlig läsbehörighet

Offentlig åtkomst till dina data är alltid förbjuden som standard. Det finns två separata inställningar som påverkar offentlig åtkomst:

1. **Tillåt offentlig åtkomst för lagringskontot.** Som standard tillåter ett lagringskonto en användare med rätt behörighet för att aktivera offentlig åtkomst till en container. Blobdata är inte tillgängliga för offentlig åtkomst såvida inte användaren vidtar ytterligare åtgärder för att uttryckligen konfigurera containerns inställning för offentlig åtkomst.
1. **Konfigurera containerns inställning för offentlig åtkomst.** Som standard är inställningen för offentlig åtkomst för en container inaktiverad, vilket innebär att auktorisering krävs för varje begäran till containern eller dess data. En användare med rätt behörighet kan ändra en containers inställning för offentlig åtkomst för att endast aktivera anonym åtkomst om anonym åtkomst tillåts för lagringskontot.

I följande tabell sammanfattas hur båda inställningarna tillsammans påverkar offentlig åtkomst för en container.

| Inställning för offentlig åtkomst | Offentlig åtkomst är inaktiverat för en container (standardinställning) | Offentlig åtkomst för en container är inställd på Container | Offentlig åtkomst till en container är inställd på Blob |
|--|--|--|--|
| Offentlig åtkomst tillåts inte för lagringskontot | Ingen offentlig åtkomst till någon container i lagringskontot. | Ingen offentlig åtkomst till någon container i lagringskontot. Inställningen för lagringskontot åsidosätter containerinställningen. | Ingen offentlig åtkomst till någon container i lagringskontot. Inställningen för lagringskontot åsidosätter containerinställningen. |
| Offentlig åtkomst tillåts för lagringskontot (standardinställning) | Ingen offentlig åtkomst till den här containern (standardkonfiguration). | Offentlig åtkomst tillåts till den här containern och dess blobar. | Offentlig åtkomst tillåts för blobar i den här containern, men inte för själva containern. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Tillåt eller inte tillåta offentlig läsbehörighet för ett lagringskonto

Som standard konfigureras ett lagringskonto för att tillåta en användare med rätt behörighet att aktivera offentlig åtkomst till en container. När offentlig åtkomst tillåts kan en användare med rätt behörighet ändra en containers inställning för offentlig åtkomst för att aktivera anonym offentlig åtkomst till data i containern. Blobdata är aldrig tillgängliga för offentlig åtkomst såvida inte användaren vidtar ytterligare steg för att uttryckligen konfigurera containerns inställning för offentlig åtkomst.

Tänk på att offentlig åtkomst till en container alltid är inaktiverad som standard och måste uttryckligen konfigureras för att tillåta anonyma begäranden. Oavsett inställningen för lagringskontot är dina data aldrig tillgängliga för offentlig åtkomst såvida inte en användare med rätt behörighet vidtar det här ytterligare steget för att aktivera offentlig åtkomst för containern.

Att inte tillåta offentlig åtkomst för lagringskontot förhindrar anonym åtkomst till alla containrar och blobar i det kontot. När offentlig åtkomst inte tillåts för kontot går det inte att konfigurera inställningen för offentlig åtkomst för en container för att tillåta anonym åtkomst. För bättre säkerhet rekommenderar Microsoft att du inte tillåter offentlig åtkomst för dina lagringskonton, såvida inte ditt scenario kräver att användarna får anonym åtkomst till blobresurser.

> [!IMPORTANT]
> Om du inte ger offentlig åtkomst för ett lagringskonto åsidosätts inställningarna för offentlig åtkomst för alla containrar i det lagringskontot. Om offentlig åtkomst inte tillåts för lagringskontot misslyckas eventuella framtida anonyma begäranden till det kontot. Innan du ändrar den här inställningen måste du se till att förstå effekten på klientprogram som kan komma åt data i ditt lagringskonto anonymt. Mer information finns i Förhindra [anonym offentlig läsåtkomst till containrar och blobar.](anonymous-read-access-prevent.md)

Om du vill tillåta eller inte tillåta offentlig åtkomst för ett lagringskonto konfigurerar du kontots **allowBlobPublicAccess-egenskap.** Den här egenskapen är tillgänglig för alla lagringskonton som skapas med Azure Resource Manager distributionsmodellen. Mer information finns i Översikt [över lagringskonto.](../common/storage-account-overview.md)

Egenskapen **AllowBlobPublicAccess** har inte angetts för ett lagringskonto som standard och returnerar inte något värde förrän du uttryckligen anger det. Lagringskontot tillåter offentlig åtkomst när egenskapsvärdet antingen är **null** eller **sant**.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Om du vill tillåta eller inte tillåta offentlig åtkomst för ett lagringskonto i Azure Portal gör du följande:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Leta upp **konfigurationsinställningen** under **Inställningar**.
1. Ange **Offentlig blobåtkomst till** Aktiverad **eller** **Inaktiverad.**

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Skärmbild som visar hur du tillåter eller tillåter offentlig blobåtkomst för kontot":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill tillåta eller inte tillåta offentlig åtkomst för ett lagringskonto med PowerShell installerar du [Azure PowerShell version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) eller senare. Konfigurera sedan egenskapen **AllowBlobPublicAccess för** ett nytt eller befintligt lagringskonto.

I följande exempel skapas ett lagringskonto och egenskapen **AllowBlobPublicAccess anges** uttryckligen till **true**. Sedan uppdateras lagringskontot så att egenskapen **AllowBlobPublicAccess** anges till **false.** I exemplet hämtas även egenskapsvärdet i varje fall. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill tillåta eller inte tillåta offentlig åtkomst för ett lagringskonto med Azure CLI installerar du Azure CLI version 2.9.0 eller senare. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli) Konfigurera sedan egenskapen **allowBlobPublicAccess för** ett nytt eller befintligt lagringskonto.

I följande exempel skapas ett lagringskonto och egenskapen **allowBlobPublicAccess anges** uttryckligen till **true**. Sedan uppdateras lagringskontot så att egenskapen **allowBlobPublicAccess** anges till **false.** I exemplet hämtas även egenskapsvärdet i varje fall. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill tillåta eller inte tillåta offentlig åtkomst för ett lagringskonto med en mall skapar du en mall med egenskapen **AllowBlobPublicAccess** inställd på **sant** eller **falskt.** Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal du Skapa **en resurs**.
1. I **Sök på Marketplace** skriver du **malldistribution och** trycker sedan på **RETUR.**
1. Välj Malldistribution (distribuera med anpassade **mallar) (förhandsversion)**, välj **Skapa** och välj sedan **Skapa en egen mall i redigeraren**.
1. I mallredigeraren klistrar du in följande JSON för att skapa ett nytt konto och anger egenskapen **AllowBlobPublicAccess** till **sant** eller **falskt.** Kom ihåg att ersätta platshållarna inom vinkelparenteser med dina egna värden.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Spara mallen.
1. Ange resursgruppsparametern och välj sedan knappen Granska **+** skapa för att distribuera mallen och skapa ett lagringskonto med **egenskapen allowBlobPublicAccess** konfigurerad.

---

> [!NOTE]
> Att inte tillåta offentlig åtkomst för ett lagringskonto påverkar inte statiska webbplatser som finns i det lagringskontot. Container$ **$web** alltid offentligt tillgänglig.
>
> När du har uppdaterat inställningen för offentlig åtkomst för lagringskontot kan det ta upp till 30 sekunder innan ändringen har spridits fullständigt.

För att tillåta eller inte tillåta offentlig blobåtkomst krävs version 2019-04-01 eller senare av Azure Storage-resursprovidern. Mer information finns i [Azure Storage resursprovider REST API](/rest/api/storagerp/).

Exemplen i det här avsnittet visade hur du läser egenskapen **AllowBlobPublicAccess** för lagringskontot för att avgöra om offentlig åtkomst för närvarande tillåts eller inte tillåts. Mer information om hur du kontrollerar att ett kontos inställning för offentlig åtkomst har konfigurerats för att förhindra anonym åtkomst finns i [Åtgärda anonym offentlig åtkomst.](anonymous-read-access-prevent.md#remediate-anonymous-public-access)

## <a name="set-the-public-access-level-for-a-container"></a>Ange offentlig åtkomstnivå för en container

Om du vill ge anonyma användare läsbehörighet till en container och dess blobar måste du först tillåta offentlig åtkomst för lagringskontot och sedan ange containerns offentliga åtkomstnivå. Om offentlig åtkomst nekas för lagringskontot kan du inte konfigurera offentlig åtkomst för en container.

När offentlig åtkomst tillåts för ett lagringskonto kan du konfigurera en container med följande behörigheter:

- **Ingen offentlig läsbehörighet:** Containern och dess blobar kan bara nås med en auktoriserad begäran. Det här alternativet är standard för alla nya containrar.
- **Offentlig läsbehörighet endast för blobar:** Blobar i containern kan läsas av anonym begäran, men containerdata är inte tillgängliga anonymt. Anonyma klienter kan inte räkna upp blobarna i containern.
- **Offentlig läsbehörighet för containern och dess blobar:** Container- och blobdata kan läsas av anonym begäran, förutom inställningar för containerbehörighet och containermetadata. Klienter kan räkna upp blobar i containern genom anonym begäran, men kan inte räkna upp containrar i lagringskontot.

Du kan inte ändra offentlig åtkomstnivå för en enskild blob. Offentlig åtkomstnivå anges endast på containernivå. Du kan ange containerns offentliga åtkomstnivå när du skapar containern, eller så kan du uppdatera inställningen för en befintlig container.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill uppdatera den offentliga åtkomstnivån för en eller Azure Portal befintliga containrar i Azure Portal:

1. Gå till översikten över ditt lagringskonto i Azure Portal.
1. Under **Blob Service** på menybladet väljer du **Containrar.**
1. Välj de containrar som du vill ange offentlig åtkomstnivå för.
1. Använd knappen **Ändra åtkomstnivå** för att visa inställningarna för offentlig åtkomst.
1. Välj önskad offentlig åtkomstnivå i **listrutan Offentlig åtkomstnivå** och klicka på knappen OK för att tillämpa ändringen på de valda containrarna.

    ![Skärmbild som visar hur du ställer in offentlig åtkomstnivå i portalen](./media/anonymous-read-access-configure/configure-public-access-container.png)

När offentlig åtkomst inte tillåts för lagringskontot går det inte att ange en containers offentliga åtkomstnivå. Om du försöker ange containerns offentliga åtkomstnivå ser du att inställningen är inaktiverad eftersom offentlig åtkomst inte tillåts för kontot.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Skärmbild som visar att inställningen offentlig åtkomstnivå för containern blockeras när offentlig åtkomst tillåts inte":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill uppdatera den offentliga åtkomstnivån för en eller flera containrar med PowerShell anropar du kommandot [Set-AzStorageContainerAcl.](/powershell/module/az.storage/set-azstoragecontaineracl) Auktorisera den här åtgärden genom att skicka in din kontonyckel, en anslutningssträng eller en signatur för delad åtkomst (SAS). Åtgärden [Ange container-ACL](/rest/api/storageservices/set-container-acl) som anger containerns offentliga åtkomstnivå stöder inte auktorisering med Azure AD. Mer information finns i Behörigheter [för att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

I följande exempel skapas en container med offentlig åtkomst inaktiverad och sedan uppdateras containerns inställning för offentlig åtkomst för att tillåta anonym åtkomst till containern och dess blobar. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

När offentlig åtkomst inte tillåts för lagringskontot går det inte att ange en containers offentliga åtkomstnivå. Om du försöker ange containerns offentliga åtkomstnivå returnerar Azure Storage som anger att offentlig åtkomst inte tillåts för lagringskontot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppdatera den offentliga åtkomstnivån för en eller flera containrar med Azure CLI anropar du [kommandot az storage container set permission.](/cli/azure/storage/container#az_storage_container_set_permission) Auktorisera den här åtgärden genom att skicka in din kontonyckel, en anslutningssträng eller en signatur för delad åtkomst (SAS). Åtgärden [Ange container-ACL](/rest/api/storageservices/set-container-acl) som anger containerns offentliga åtkomstnivå stöder inte auktorisering med Azure AD. Mer information finns i Behörigheter [för att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

I följande exempel skapas en container med offentlig åtkomst inaktiverad och sedan uppdateras containerns inställning för offentlig åtkomst för att tillåta anonym åtkomst till containern och dess blobar. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

När offentlig åtkomst inte tillåts för lagringskontot går det inte att ange en containers offentliga åtkomstnivå. Om du försöker ange containerns offentliga åtkomstnivå returnerar Azure Storage som anger att offentlig åtkomst inte tillåts för lagringskontot.

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Kontrollera inställningen för offentlig åtkomst för en uppsättning containrar

Du kan kontrollera vilka containrar i ett eller flera lagringskonton som har konfigurerats för offentlig åtkomst genom att visa containrarna och kontrollera inställningen för offentlig åtkomst. Den här metoden är ett praktiskt alternativ när ett lagringskonto inte innehåller ett stort antal containrar eller när du kontrollerar inställningen för ett litet antal lagringskonton. Prestanda kan dock påverkas om du försöker räkna upp ett stort antal containrar.

I följande exempel används PowerShell för att hämta inställningen för offentlig åtkomst för alla containrar i ett lagringskonto. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Nästa steg

- [Förhindra anonym offentlig läsåtkomst till containrar och blobar](anonymous-read-access-prevent.md)
- [Få åtkomst till offentliga containrar och blobar anonymt med .NET](anonymous-read-access-client.md)
- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
