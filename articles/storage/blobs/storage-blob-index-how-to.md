---
title: Använd BLOB-taggar för att hantera och hitta data på Azure Blob Storage
description: Se exempel på hur du använder BLOB-taggar för att kategorisera, hantera och fråga efter BLOB-objekt.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/05/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 32bb51751430dcd0208849f798d21f2b25e6b82b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430878"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Använd BLOB-Taggar (för hands version) för att hantera och hitta data på Azure Blob Storage

BLOB index-Taggar kategoriserar data i ditt lagrings konto med hjälp av attribut för nyckel värdes kod. Taggarna indexeras automatiskt och exponeras som ett sökbart flerdimensionellt index för att enkelt hitta data. Den här artikeln visar hur du ställer in, hämtar och hittar data med hjälp av BLOB-taggar.

> [!IMPORTANT]
> BLOB index-taggar finns för närvarande i för **hands version** och är tillgängliga i regionerna **Kanada**, **östra**, Australien, **centrala** och **Frankrike, södra** . Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

Mer information om den här funktionen tillsammans med kända problem och begränsningar finns i [Hantera och hitta Azure blob-data med BLOB-Taggar (för hands version)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

- En Azure-prenumeration har registrerats och godkänts för åtkomst till för hands versionen av BLOB-index
- Åtkomst till [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

När BLOB-indexet är i för hands version, släpps .NET Storage-paketet i NuGet-flödet för för hands versionen. Det här biblioteket kan ändras under för hands versions perioden.

1. Skapa ett Visual Studio-projekt för att komma igång med Azure-Blob Storage klient bibliotek V12 för .NET. Läs mer i [.net snabb start](storage-quickstart-blobs-dotnet.md)

2. I NuGet Package Manager letar du upp paketet **Azure. Storage. blobs** och installerar version **12.7.0 – Preview. 1** eller senare i projektet. Du kan också köra PowerShell-kommandot: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   Mer information finns i [hitta och installera ett paket](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Lägg till följande using-uttryck högst upp i din kod fil.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Ladda upp en ny BLOB med index-Taggar

Den här uppgiften kan utföras av en [data ägare för en lagrings-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ett säkerhets objekt som har fått behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto 

2. Navigera till alternativet **behållare** under **BLOB service** väljer du din behållare

3. Välj knappen **överför** och bläddra i det lokala fil systemet för att hitta en fil som ska laddas upp som en Block-Blob.

4. Expandera den **avancerade** List rutan och gå till avsnittet **blobb index Taggar**

5. Ange de index taggar för nyckel/värde-blob som du vill använda för dina data

6. Välj knappen **överför** för att överföra blobben

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Skärm bild av Azure Portal som visar hur du laddar upp en blob med index-taggar.":::

# <a name="net"></a>[.NET](#tab/net)

I följande exempel visas hur du skapar en tilläggs-BLOB med taggar som anges när du skapar.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Hämta, ange och uppdatera BLOB-index Taggar

Hämtning av BLOB-taggar kan utföras av en [data ägare för en lagrings-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ett säkerhets objekt som har fått behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll.

Att ange och uppdatera BLOB-index taggar kan utföras av en [data ägare för en lagrings-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ett säkerhets objekt som har fått behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto 

2. Navigera till alternativet **behållare** under **BLOB service**, välj din behållare

3. Välj din BLOB från listan över blobbar i den valda behållaren

4. På fliken Översikt över BLOB visas din blobs egenskaper inklusive eventuella **BLOB-Taggar**

5. Du kan hämta, ange, ändra eller ta bort alla nyckel-/värde index-taggar för din BLOB

6. Välj knappen **Spara** för att bekräfta eventuella uppdateringar av din BLOB

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Skärm bild av Azure Portal som visar hur du hämtar, ställer in, uppdaterar och tar bort index etiketter på blobbar.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrera och hitta data med BLOB-Taggar

Den här uppgiften kan utföras av en [data ägare för en lagrings-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ett säkerhets objekt som har fått behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal tillämpar filtret BLOB index taggar automatiskt `@container` parametern för att ange den valda behållarens omfång. Om du vill filtrera och hitta taggade data över hela lagrings kontot använder du våra REST API, SDK: er eller verktyg.

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till alternativet **behållare** under **BLOB service** väljer du din behållare

3. Välj **filter knappen BLOB index Taggar** för att filtrera i den valda behållaren

4. Ange en tagg-och tagg värde för BLOB index

5. Välj **filter knappen BLOB index Taggar** för att lägga till ytterligare tag filter (upp till 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Skärm bild av Azure Portal som visar hur du filtrerar och hittar taggade blobbar med hjälp av index Taggar":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Livs cykel hantering med tagg filter för BLOB index

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till **livs cykel hanterings** alternativet under **BLOB service**

3. Välj *Lägg till regel* och fyll sedan i formulär fälten för åtgärds uppsättning

4. Välj **filter** uppsättning för att lägga till valfritt filter för prefix matchning och blob-index matchning

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Skärm bild av Azure Portal visar hur du lägger till index taggar för livs cykel hantering.":::

5. Välj **Granska + Lägg** till för att granska regel inställningarna

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Skärm bild av Azure Portal som visar en hanterings regel för livs cykel med BLOB index Taggar filter exempel":::

6. Välj **Lägg till** för att tillämpa den nya regeln i livs cykel hanterings principen

# <a name="net"></a>[.NET](#tab/net)

Policys för [livs cykel hantering](storage-lifecycle-management-concepts.md) tillämpas för varje lagrings konto på kontroll Plans nivån. För .NET installerar du [Microsoft Azure Management Storage Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) version 16.0.0 eller senare.

---

## <a name="next-steps"></a>Nästa steg

 - Lär dig mer om BLOB-taggar för BLOB, se [Hantera och hitta Azure blob-data med BLOB-Taggar (för hands version)](storage-manage-find-blobs.md )
 - Läs mer om livs cykel hantering i [Hantera Azure Blob Storage Lifecycle](storage-lifecycle-management-concepts.md)