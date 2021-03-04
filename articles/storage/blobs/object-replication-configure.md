---
title: Konfigurera objektreplikering
titleSuffix: Azure Storage
description: Lär dig hur du konfigurerar objekt replikering till asynkron kopiering av block-blobbar från en behållare i ett lagrings konto till ett annat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2b6855d72b644a3fe1fa46c883eb7414383a1a57
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031709"
---
# <a name="configure-object-replication-for-block-blobs"></a>Konfigurera objekt replikering för block-blobar

Objekt replikeringen kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Mer information om objekt replikering finns i [objekt replikering](object-replication-overview.md).

När du konfigurerar objekt replikering skapar du en replikeringsprincip som anger käll lagrings kontot och mål kontot. En replikeringsprincip innehåller en eller flera regler som anger en käll behållare och en mål behållare och anger vilka block-blobar som ska replikeras i käll behållaren.

Den här artikeln beskriver hur du konfigurerar objekt replikering för ditt lagrings konto med hjälp av Azure Portal, PowerShell eller Azure CLI. Du kan också använda en av klient biblioteken för Azure Storage Resource Provider för att konfigurera objekt replikering.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Skapa en replikeringsprincip och regler

Innan du konfigurerar objekt replikering skapar du käll-och mål lagrings konton om de inte redan finns. Båda kontona måste vara generella-syfte v2-lagrings konton. Mer information finns i [skapa ett Azure Storage-konto](../common/storage-account-create.md).

Objekt replikering kräver att BLOB-versioner aktive ras för både käll-och mål kontot och att BLOB Change-feeden är aktiverat för käll kontot. Mer information om BLOB-versioner finns i [BLOB-versioner](versioning-overview.md). Mer information om ändrings flöden finns i [ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md). Tänk på att aktivering av dessa funktioner kan leda till ytterligare kostnader.

Ett lagrings konto kan fungera som käll konto för upp till två mål konton. Käll-och mål kontona kan finnas i samma region eller i olika regioner. De kan också finnas i olika prenumerationer och i olika Azure Active Directory-klienter (Azure AD). Det går bara att skapa en replikeringsprincip för varje konto par.

När du konfigurerar objekt replikering skapar du en replikeringsprincip på mål kontot via Azure Storage resurs leverantör. När replikeringsprincipen har skapats tilldelar Azure Storage den ett princip-ID. Du måste sedan associera den replikeringsprincipen med käll kontot med hjälp av princip-ID: t. Princip-ID: t på käll-och mål kontona måste vara desamma för att replikeringen ska äga rum.

Om du vill konfigurera en princip för objekt replikering för ett lagrings konto måste du ha tilldelats rollen som Azure Resource Manager **Contributor** , som är begränsad till lagrings kontots nivå eller högre. Mer information finns i [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) i Azures rollbaserade åtkomst kontroll (Azure RBAC)-dokumentation.

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Konfigurera objekt replikering när du har åtkomst till båda lagrings kontona

Om du har åtkomst till både käll-och mål lagrings kontona kan du konfigurera principen för objekt replikering på båda kontona.

Innan du konfigurerar objekt replikering i Azure Portal skapar du käll-och mål behållare i deras respektive lagrings konton, om de inte redan finns. Aktivera också BLOB-versioner och ändra feed på käll kontot och aktivera BLOB-versioner på mål kontot.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Azure Portal skapar automatiskt principen på käll kontot när du har konfigurerat det för mål kontot.

Följ dessa steg om du vill skapa en replikeringsprincip i Azure Portal:

1. Navigera till käll lagrings kontot i Azure Portal.
1. Under **BLOB service** väljer du **objekt replikering**.
1. Välj **Konfigurera regler för replikering**.
1. Välj mål prenumeration och lagrings konto.
1. I avsnittet **behållar par** väljer du en käll behållare från käll kontot och en mål behållare från mål kontot. Du kan skapa upp till 10 container par per replikeringsprincip.

    Följande bild visar en uppsättning regler för replikering.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Skärm bild som visar regler för replikering i Azure Portal":::

1. Om du vill kan du ange ett eller flera filter för att endast kopiera blobbar som matchar ett prefix. Om du till exempel anger ett prefix `b` replikeras endast blobbar vars namn börjar med den bokstaven. Du kan ange en virtuell katalog som en del av prefixet. Prefixlängden stöder inte jokertecken.

    Följande bild visar filter som begränsar vilka blobbar som kopieras som en del av en replikeringsprincip.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Skärm bild som visar filter för en replikeringsprincip":::

1. Som standard är kopierings omfånget inställt på att bara kopiera nya objekt. Om du vill kopiera alla objekt i behållaren eller kopiera objekt från ett anpassat datum och en anpassad tid, väljer du länken **ändra** och konfigurerar kopierings omfånget för behållar paret.

    Följande bild visar en anpassad kopierings omfattning som kopierar objekt från ett angivet datum och tid.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Skärm bild som visar en anpassad kopierings omfattning för objekt replikering":::

1. Välj **Spara och tillämpa** för att skapa replikeringsprincipen och påbörja replikering av data.

När du har konfigurerat objekt replikering visar Azure Portal replikeringsprincipen och reglerna, som du ser i följande bild.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Skärm bild som visar princip för objekt replikering i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa en replikeringsprincip med PowerShell installerar du först version [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) eller senare av AZ. Storage PowerShell-modulen. Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

I följande exempel visas hur du skapar en replikeringsprincip på käll-och mål kontona. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa en replikeringsprincip med Azure CLI måste du först installera Azure CLI version 2.11.1 eller senare. Mer information finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

Aktivera sedan BLOB-versioner på käll-och mål lagrings kontona och aktivera ändra feed på käll kontot genom att anropa kommandot [AZ Storage Account BLOB-service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) . Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Skapa käll-och mål behållare i deras respektive lagrings konton.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Skapa en ny replikeringsprincip och en associerad regel på mål kontot genom att anropa [AZ lagrings konto eller-princip skapa](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Azure Storage anger princip-ID: t för den nya principen när den skapas. Om du vill lägga till ytterligare regler i principen anropar du [AZ lagrings konto eller-princip regel Lägg till](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) och ange princip-ID: t.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Skapa sedan principen på käll kontot med hjälp av princip-ID: t.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Konfigurera objekt replikering när du har åtkomst till mål kontot

Om du inte har behörighet till käll lagrings kontot kan du konfigurera objekt replikering på mål kontot och ange en JSON-fil som innehåller princip definitionen till en annan användare för att skapa samma princip på käll kontot. Om käll kontot till exempel finns i en annan Azure AD-klient från mål kontot kan du använda den här metoden för att konfigurera objekt replikering.

Tänk på att du måste tilldelas rollen som Azure Resource Managers **deltagare** som är begränsad till nivån för mål lagrings kontot eller högre för att kunna skapa principen. Mer information finns i [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) i Azures rollbaserade åtkomst kontroll (Azure RBAC)-dokumentation.

I följande tabell sammanfattas vilka värden som ska användas för princip-ID och regel-ID: n i JSON-filen i varje scenario.

| När du skapar JSON-filen för det här kontot... | Ange princip-ID: t till det här värdet | Ange regel-ID: n till det här värdet |
|-|-|-|
| Mål konto | Standardvärdet för sträng *värde.* Azure Storage skapar ett princip-ID-värde åt dig. | En tom sträng. Azure Storage skapar regel-ID-värdena åt dig. |
| Käll konto | Värdet för princip-ID: t som returneras när du laddar ned principen som definierats på mål kontot som en JSON-fil. | Värdena för de regel-ID: n som returneras när du laddar ned principen som definierats på mål kontot som en JSON-fil. |

I följande exempel definieras en replikeringsprincip på mål kontot med en enda regel som matchar prefixet *b* och anger den minsta skapande tiden för blobbar som ska replikeras. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera objekt replikering på mål kontot med en JSON-fil i Azure Portal:

1. Skapa en lokal JSON-fil som definierar replikeringsprincipen på mål kontot. Ange **policyId** -fältet som *standard* så att Azure Storage definierar princip-ID: t.

    Ett enkelt sätt att skapa en JSON-fil som definierar en replikeringsprincip är att först skapa en princip för redundanstest mellan två lagrings konton i Azure Portal. Du kan sedan hämta reglerna för replikering och ändra JSON-filen efter behov.

1. Navigera till inställningarna för **objekt replikering** för mål kontot i Azure Portal.
1. Välj **Ladda upp regler för uppladdning av replikering**.
1. Ladda upp JSON-filen. Azure Portal visar principen och reglerna som kommer att skapas, som du ser i följande bild.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Skärm bild som visar hur du laddar upp en JSON-fil för att definiera en replikeringsprincip":::

1. Välj **överför** för att skapa replikeringsprincipen på mål kontot.

Du kan sedan hämta en JSON-fil som innehåller princip definitionen som du kan ge till en annan användare att konfigurera käll kontot. Följ dessa steg om du vill ladda ned denna JSON-fil:

1. Navigera till inställningarna för **objekt replikering** för mål kontot i Azure Portal.
1. Välj knappen **mer** bredvid den princip som du vill ladda ned och välj sedan **Hämta regler**, som du ser i följande bild.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Skärm bild som visar hur du laddar ned regler för replikering till en JSON-fil":::

1. Spara JSON-filen på den lokala datorn om du vill dela den med en annan användare för att konfigurera principen på käll kontot.

Den nedladdade JSON-filen innehåller princip-ID: t som Azure Storage skapats för principen på mål kontot. Du måste använda samma princip-ID om du vill konfigurera objekt replikering på käll kontot.

Tänk på att överföring av en JSON-fil för att skapa en replikeringsprincip för mål kontot via Azure Portal inte automatiskt skapar samma princip i käll kontot. En annan användare måste skapa principen på käll kontot innan Azure Storage börjar replikera objekt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill hämta en JSON-fil som innehåller replikeringsprincipen för mål kontot från PowerShell anropar du kommandot [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) för att returnera principen. Konvertera sedan principen till JSON och spara den som en lokal fil, som du ser i följande exempel. Kom ihåg att ersätta värden inom vinkelparenteser och fil Sök vägen med dina egna värden:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Om du vill använda JSON-filen för att definiera replikeringsprincipen på käll kontot med PowerShell hämtar du den lokala filen och konverterar från JSON till ett objekt. Anropa sedan kommandot [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) för att konfigurera principen för käll kontot, som du ser i följande exempel. Kom ihåg att ersätta värden inom vinkelparenteser och fil Sök vägen med dina egna värden:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skriva replikeringsprincipen för mål kontot till en JSON-fil från Azure CLI anropar du kommandot [AZ Storage Account eller-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) och utdata till en fil.

I följande exempel skrivs princip definitionen till en JSON-fil med namnet *policy.jspå*. Kom ihåg att ersätta värden inom vinkelparenteser och fil Sök vägen med dina egna värden:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Om du vill använda JSON-filen för att konfigurera replikeringsprincipen på käll kontot med Azure CLI anropar du kommandot [AZ Storage Account eller-policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) och refererar till *policy.jsi* filen. Kom ihåg att ersätta värden inom vinkelparenteser och fil Sök vägen med dina egna värden:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Kontrol lera replikeringsstatus för en BLOB

Du kan kontrol lera replikeringsstatus för en BLOB i käll kontot med hjälp av Azure Portal, PowerShell eller Azure CLI. Egenskaperna för objektet Replication har inte fyllts i förrän replikeringen har slutförts eller misslyckats.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill kontrol lera replikeringsstatus för en BLOB i käll kontot i Azure Portal:

1. Navigera till käll kontot i Azure Portal.
1. Leta upp den behållare som innehåller käll-bloben.
1. Välj bloben för att visa dess egenskaper. Om blobben har repliker ATS visas i avsnittet **objekt replikering** att status är *slutförd*. Replikeringsprinciper-ID och ID för regeln som styr objekt replikeringen för den här behållaren visas också.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Skärm bild som visar replikeringsstatus för en BLOB i käll kontot":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera replikeringsstatus för en BLOB i käll kontot med PowerShell hämtar du värdet för **ReplicationStatus** -egenskapen för objekt replikering, som du ser i följande exempel. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera replikeringsstatus för en BLOB i käll kontot med Azure CLI hämtar du värdet **för egenskapen objekt replikeringsstatus,** som visas i följande exempel:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Om replikeringsstatus för en BLOB i käll kontot indikerar fel, kan du undersöka följande möjliga orsaker:

- Se till att principen för objekt replikering är konfigurerad på mål kontot.
- Kontrol lera att mål behållaren fortfarande finns.
- Om käll-bloben har krypterats med en kundanged nyckel som en del av en Skriv åtgärd, kommer objekt replikeringen att Miss Miss sen. Mer information om kundspecifika nycklar finns i [Ange en krypterings nyckel på en begäran till Blob Storage](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Ta bort en replikeringsprincip

Om du vill ta bort en replikeringsprincip och dess associerade regler använder du Azure Portal, PowerShell eller CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill ta bort en replikeringsprincip i Azure Portal:

1. Navigera till käll lagrings kontot i Azure Portal.
1. Under **Inställningar** väljer du **objekt replikering**.
1. Klicka på knappen **mer** bredvid princip namnet.
1. Välj **ta bort regler**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ta bort en replikeringsprincip genom att ta bort principen från både käll kontot och mål kontot. Om du tar bort principen raderas även eventuella regler som är kopplade till den.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta bort en replikeringsprincip genom att ta bort principen från både käll kontot och mål kontot. Om du tar bort principen raderas även eventuella regler som är kopplade till den.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nästa steg

- [Översikt över objekt replikering](object-replication-overview.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
- [Bearbeta ändrings flöde i Azure Blob Storage](storage-blob-change-feed-how-to.md)
