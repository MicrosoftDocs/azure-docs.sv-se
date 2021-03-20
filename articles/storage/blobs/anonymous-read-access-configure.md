---
title: Konfigurera anonym offentlig Läs behörighet för behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du tillåter eller nekar anonym åtkomst till BLOB-data för lagrings kontot. Ange offentlig åtkomst inställning för behållare för att göra behållare och blobbar tillgängliga för anonym åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533884"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurera anonym offentlig Läs behörighet för behållare och blobbar

Azure Storage stöder valfri anonym offentlig Läs behörighet för behållare och blobbar. Anonym åtkomst till dina data tillåts som standard aldrig. Om du inte uttryckligen aktiverar anonym åtkomst måste alla begär anden till en behållare och dess blobbar vara auktoriserade. När du konfigurerar en behållares inställning för offentlig åtkomst nivå för att tillåta anonym åtkomst, kan klienterna läsa data i den behållaren utan att auktorisera begäran.

> [!WARNING]
> När en behållare har kon figurer ATS för offentlig åtkomst kan alla klienter läsa data i behållaren. Offentlig åtkomst innebär en potentiell säkerhets risk, så om ditt scenario inte kräver det rekommenderar Microsoft att du inte tillåter det för lagrings kontot. Mer information finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

Den här artikeln beskriver hur du konfigurerar anonym offentlig Läs behörighet för en behållare och dess blobbar. Information om hur du kommer åt BLOB-data anonymt från ett klient program finns i [komma åt offentliga behållare och blobbar anonymt med .net](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Om anonym offentlig Läs behörighet

Offentlig åtkomst till dina data är alltid förbjuden som standard. Det finns två separata inställningar som påverkar offentlig åtkomst:

1. **Tillåt offentlig åtkomst för lagrings kontot.** Som standard ger ett lagrings konto en användare med rätt behörighet för att aktivera offentlig åtkomst till en behållare. BLOB-data är inte tillgängliga för offentlig åtkomst om inte användaren vidtar ytterligare steg för att konfigurera behållarens offentliga åtkomst inställning explicit.
1. **Konfigurera behållarens inställning för offentlig åtkomst.** Som standard är en behållares offentliga åtkomst inställning inaktive rad, vilket innebär att auktorisering krävs för varje begäran till behållaren eller dess data. En användare med rätt behörighet kan ändra en behållares offentliga åtkomst inställning för att aktivera anonym åtkomst endast om anonym åtkomst tillåts för lagrings kontot.

I följande tabell sammanfattas hur båda inställningarna påverkar den offentliga åtkomsten för en behållare.

| Offentlig åtkomst inställning | Offentlig åtkomst har inaktiverats för en behållare (standardinställning) | Offentlig åtkomst för en behållare har angetts till container | Offentlig åtkomst en behållare har angetts till BLOB |
|--|--|--|--|
| Offentlig åtkomst tillåts inte för lagrings kontot | Ingen offentlig åtkomst till någon behållare i lagrings kontot. | Ingen offentlig åtkomst till någon behållare i lagrings kontot. Inställningen för lagrings kontot åsidosätter inställningen för behållare. | Ingen offentlig åtkomst till någon behållare i lagrings kontot. Inställningen för lagrings kontot åsidosätter inställningen för behållare. |
| Offentlig åtkomst tillåts för lagrings kontot (standardinställning) | Ingen offentlig åtkomst till den här behållaren (standard konfiguration). | Offentlig åtkomst tillåts för den här behållaren och dess blobbar. | Offentlig åtkomst tillåts till blobbar i den här behållaren, men inte till själva behållaren. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Tillåt eller neka offentlig Läs behörighet för ett lagrings konto

Som standard konfigureras ett lagrings konto så att en användare med rätt behörighet kan aktivera offentlig åtkomst till en behållare. När offentlig åtkomst tillåts kan en användare med rätt behörighet ändra en behållares offentliga åtkomst inställning för att aktivera anonym offentlig åtkomst till data i den behållaren. BLOB-data är aldrig tillgängliga för offentlig åtkomst om inte användaren vidtar ytterligare steg för att konfigurera behållarens offentliga åtkomst inställning explicit.

Tänk på att offentlig åtkomst till en behållare alltid stängs av som standard och måste konfigureras uttryckligen för att tillåta anonyma begär Anden. Oavsett inställningen på lagrings kontot kommer dina data aldrig att vara tillgängliga för offentlig åtkomst, såvida inte en användare med rätt behörighet tar detta ytterligare steg för att aktivera offentlig åtkomst på behållaren.

Att neka offentlig åtkomst för lagrings kontot förhindrar anonym åtkomst till alla behållare och blobbar i det kontot. När offentlig åtkomst inte tillåts för kontot går det inte att konfigurera den offentliga åtkomst inställningen för en behållare för att tillåta anonym åtkomst. För förbättrad säkerhet rekommenderar Microsoft att du inte tillåter offentlig åtkomst för dina lagrings konton, såvida inte ditt scenario kräver att användare får åtkomst till BLOB-resurser anonymt.

> [!IMPORTANT]
> Om den offentliga åtkomsten för ett lagrings konto nekas åsidosätts de offentliga åtkomst inställningarna för alla behållare i lagrings kontot. När offentlig åtkomst inte tillåts för lagrings kontot kommer alla framtida anonyma begär anden till det kontot att Miss förväntas. Innan du ändrar den här inställningen bör du förstå hur klient program som kan komma åt data i ditt lagrings konto är anonymt. Mer information finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

Om du vill tillåta eller neka offentlig åtkomst till ett lagrings konto konfigurerar du kontots **AllowBlobPublicAccess** -egenskap. Den här egenskapen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Mer information finns i [Översikt över lagrings konto](../common/storage-account-overview.md).

Egenskapen **AllowBlobPublicAccess** har inte angetts för ett lagrings konto som standard och returnerar inget värde förrän du uttryckligen anger det. Lagrings kontot tillåter offentlig åtkomst när egenskap svärdet är antingen **Null** eller **Sant**.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Leta upp **konfigurations** inställningen under **Inställningar**.
1. Ange **offentlig åtkomst för BLOB** till **aktive rad** eller **inaktive** rad.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Skärm bild som visar hur du tillåter eller nekar den offentliga BLOB-åtkomsten för kontot":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto med PowerShell installerar du [Azure PowerShell version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) eller senare. Konfigurera sedan egenskapen **AllowBlobPublicAccess** för ett nytt eller befintligt lagrings konto.

I följande exempel skapas ett lagrings konto och egenskapen **AllowBlobPublicAccess** anges explicit till **True**. Sedan uppdateras lagrings kontot för att ange egenskapen **AllowBlobPublicAccess** till **false**. I exemplet hämtas även egenskap svärdet i varje fall. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto med Azure CLI installerar du Azure CLI version 2.9.0 eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli). Konfigurera sedan egenskapen **allowBlobPublicAccess** för ett nytt eller befintligt lagrings konto.

I följande exempel skapas ett lagrings konto och egenskapen **allowBlobPublicAccess** anges explicit till **True**. Sedan uppdateras lagrings kontot för att ange egenskapen **allowBlobPublicAccess** till **false**. I exemplet hämtas även egenskap svärdet i varje fall. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto med en mall skapar du en mall med egenskapen **AllowBlobPublicAccess** inställd på **True** eller **false**. Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**.
1. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
1. Välj **malldistribution (distribuera med anpassade mallar) (för hands version)**, Välj **skapa** och välj sedan **skapa en egen mall i redigeraren**.
1. I redigeraren för mallar klistrar du in följande JSON för att skapa ett nytt konto och anger egenskapen **AllowBlobPublicAccess** till **True** eller **false**. Kom ihåg att ersätta plats hållarna inom vinkelparenteser med dina egna värden.

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
1. Ange parametern resurs grupp och välj sedan knappen **Granska + skapa** för att distribuera mallen och skapa ett lagrings konto med egenskapen **allowBlobPublicAccess** konfigurerad.

---

> [!NOTE]
> Att neka offentlig åtkomst för ett lagrings konto påverkar inte några statiska webbplatser som finns på lagrings kontot. **$Web** containern alltid är offentligt tillgänglig.
>
> När du har uppdaterat inställningen för offentlig åtkomst för lagrings kontot kan det ta upp till 30 sekunder innan ändringen har spridits helt.

Att tillåta eller neka offentlig åtkomst till BLOB kräver version 2019-04-01 eller senare av Azure Storage Resource Provider. Mer information finns i [Azure Storage Resource Provider REST API](/rest/api/storagerp/).

I exemplen i det här avsnittet förklaras hur du läser **AllowBlobPublicAccess** -egenskapen för lagrings kontot för att avgöra om offentlig åtkomst för närvarande är tillåten eller inte tillåts. Mer information om hur du verifierar att ett kontos offentliga åtkomst inställningar är konfigurerat för att förhindra anonym åtkomst finns i [Reparera anonym offentlig åtkomst](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Ange offentlig åtkomst nivå för en behållare

Om du vill ge anonyma användare Läs åtkomst till en behållare och dess blobbar måste du först tillåta offentlig åtkomst till lagrings kontot och sedan ange behållarens offentliga åtkomst nivå. Om offentlig åtkomst nekas för lagrings kontot kommer du inte att kunna konfigurera offentlig åtkomst för en behållare.

När offentlig åtkomst tillåts för ett lagrings konto kan du konfigurera en behållare med följande behörigheter:

- **Ingen offentlig Läs behörighet:** Behållaren och dess blobbar kan endast nås med en auktoriserad begäran. Det här alternativet är standardvärdet för alla nya behållare.
- **Offentlig Läs behörighet för blobbar:** Blobbar i behållaren kan läsas av en anonym begäran, men behållar data är inte tillgängliga anonymt. Anonyma klienter kan inte räkna upp blobar i behållaren.
- **Offentlig Läs behörighet för behållare och dess blobbar:** Container-och BLOB-data kan läsas av anonym begäran, förutom behörighets inställningar för behållare och container-metadata. Klienter kan räkna upp blobar i behållaren med anonym begäran, men kan inte räkna upp behållare i lagrings kontot.

Du kan inte ändra offentlig åtkomst nivå för en enskild blob. Offentlig åtkomst nivå anges bara på container nivå. Du kan ange behållarens offentliga åtkomst nivå när du skapar behållaren, eller så kan du uppdatera inställningen för en befintlig behållare.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill uppdatera den offentliga åtkomst nivån för en eller flera befintliga behållare i Azure Portal:

1. Gå till ditt lagrings konto – översikt i Azure Portal.
1. Under **BLOB service** på Meny bladet väljer du **behållare**.
1. Välj de behållare som du vill ange offentlig åtkomst nivå för.
1. Använd knappen **ändra åtkomst nivå** för att visa inställningarna för offentliga åtkomst.
1. Välj önskad offentlig åtkomst nivå i list rutan för **offentlig åtkomst nivå** och klicka på knappen OK för att tillämpa ändringen på de valda behållarna.

    ![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/anonymous-read-access-configure/configure-public-access-container.png)

När offentlig åtkomst inte tillåts för lagrings kontot kan inte behållarens offentliga åtkomst nivå anges. Om du försöker ange behållarens offentliga åtkomst nivå ser du att inställningen är inaktive rad eftersom offentlig åtkomst inte tillåts för kontot.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Skärm bild som visar att inställnings behållarens offentliga åtkomst nivå är blockerad när offentlig åtkomst tillåts":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill uppdatera den offentliga åtkomst nivån för en eller flera behållare med PowerShell anropar du kommandot [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) . Auktorisera den här åtgärden genom att skicka in din konto nyckel, en anslutnings sträng eller en signatur för delad åtkomst (SAS). Den [angivna ACL](/rest/api/storageservices/set-container-acl) -åtgärden för behållare som anger behållarens offentliga åtkomst nivå stöder inte auktorisering med Azure AD. Mer information finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

I följande exempel skapas en behållare med offentlig åtkomst inaktive rad och uppdaterar sedan behållarens offentliga åtkomst inställning för att tillåta anonym åtkomst till behållaren och dess blobbar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

När offentlig åtkomst inte tillåts för lagrings kontot kan inte behållarens offentliga åtkomst nivå anges. Om du försöker ange behållarens offentliga åtkomst nivå returnerar Azure Storage fel som anger att offentlig åtkomst inte tillåts på lagrings kontot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppdatera den offentliga åtkomst nivån för en eller flera behållare med Azure CLI anropar du kommandot [AZ Storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) . Auktorisera den här åtgärden genom att skicka in din konto nyckel, en anslutnings sträng eller en signatur för delad åtkomst (SAS). Den [angivna ACL](/rest/api/storageservices/set-container-acl) -åtgärden för behållare som anger behållarens offentliga åtkomst nivå stöder inte auktorisering med Azure AD. Mer information finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

I följande exempel skapas en behållare med offentlig åtkomst inaktive rad och uppdaterar sedan behållarens offentliga åtkomst inställning för att tillåta anonym åtkomst till behållaren och dess blobbar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

När offentlig åtkomst inte tillåts för lagrings kontot kan inte behållarens offentliga åtkomst nivå anges. Om du försöker ange behållarens offentliga åtkomst nivå returnerar Azure Storage fel som anger att offentlig åtkomst inte tillåts på lagrings kontot.

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Kontrol lera inställningen för offentlig åtkomst för en uppsättning behållare

Det går att kontrol lera vilka behållare i ett eller flera lagrings konton som har kon figurer ATS för offentlig åtkomst genom att lista behållarna och kontrol lera inställningen för offentlig åtkomst. Den här metoden är ett användbart alternativ när ett lagrings konto inte innehåller ett stort antal behållare, eller när du kontrollerar inställningen för ett litet antal lagrings konton. Prestanda kan dock bli lidande om du försöker räkna upp ett stort antal behållare.

I följande exempel används PowerShell för att hämta den offentliga åtkomst inställningen för alla behållare i ett lagrings konto. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Nästa steg

- [Förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md)
- [Få åtkomst till offentliga behållare och blobbar anonymt med .NET](anonymous-read-access-client.md)
- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
