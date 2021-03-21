---
title: Hantera förfallo datum för Azure Blob Storage
titleSuffix: Azure Content Delivery Network
description: Lär dig mer om alternativen för att kontrol lera Time-to-Live för blobbar i Azure CDN caching.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778711"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Hantera förfallo datum för Azure Blob Storage i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Blob Storage-tjänsten](../storage/common/storage-introduction.md#blob-storage) i Azure Storage är en av flera Azure-baserade ursprung som är integrerade med Azure Content Delivery Network (CDN). Ett offentligt tillgängligt BLOB-innehåll kan cachelagras i Azure CDN tills dess TTL-värde (Time to Live) förflutit. TTL-värdet bestäms av `Cache-Control` huvudet i HTTP-svaret från ursprungs servern. I den här artikeln beskrivs flera olika sätt som du kan ställa in `Cache-Control` sidhuvudet på en BLOB i Azure Storage.

Du kan också styra cacheinställningar från Azure Portal genom att ange regler för CDN-cachelagring. Om du skapar en regel för cachelagring och ställer in dess cachelagring för att **åsidosätta** eller **kringgå cache**, ignoreras de cacheinställningar som beskrivs i den här artikeln. Information om allmänna cachelagring av koncept finns i [så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att ange inget TTL-värde för en blob. I det här fallet använder Azure CDN automatiskt en standard-TTL på sju dagar, om du inte har konfigurerat regler för cachelagring i Azure Portal. Detta standard-TTL gäller endast för allmänna webb leverans optimeringar. För stora fil optimeringar är standard-TTL en dag och för optimering av medie direkt uppspelning är standard-TTL ett år.
> 
> Mer information om hur Azure CDN arbetar för att påskynda åtkomst till blobbar och andra filer finns i [Översikt över Azure-Content Delivery Network](cdn-overview.md).
> 
> Mer information om Azure Blob Storage finns i [Introduktion till Blob Storage](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange Cache-Control rubriker med hjälp av regler för CDN-cachelagring
Den bästa metoden för att ställa in en BLOB- `Cache-Control` rubrik är att använda regler för cachelagring i Azure Portal. Mer information om regler för CDN-cachelagring finns i [styra Azure CDN cachelagring med regler för cachelagring](cdn-caching-rules.md).

> [!NOTE] 
> Reglerna för cachelagring är bara tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler. För **Azure CDN Premium från Verizon** -profiler måste du använda [Azure CDN-regel motorn](./cdn-verizon-premium-rules-engine.md) i **hanterings** portalen för liknande funktioner.

**Så här navigerar du till sidan regler för CDN-cachelagring**:

1. I Azure Portal väljer du en CDN-profil och väljer sedan slut punkten för blobben.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN caching rules](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Sidan CDN-cachelagring](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Ange Cache-Control huvuden i Blob Storage-tjänsten med globala regler för cachelagring:**

1. Under **globala regler för cachelagring**, **ställer du in** **beteende för cachelagring av frågesträngar** för att **Ignorera frågesträngar** och ställa in **beteende för cachelagring**
      
2. Vid **förfallo tid för cache** anger du 3600 i rutan **sekunder** eller 1 i rutan **timmar** . 

   ![Exempel på globala regler för CDN-cachelagring](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Den här globala regeln för cachelagring anger en cache-varaktighet på en timme och påverkar alla begär anden till slut punkten. Den åsidosätter alla `Cache-Control` eller `Expires` http-huvuden som skickas av ursprungs servern som anges av slut punkten.   

3. Välj **Spara**.
 
**Så här anger du en BLOB-fils Cache-Control huvuden med anpassade regler för cachelagring:**

1. Skapa två matchnings villkor under **anpassade regler för cachelagring**:

     A. För det första matchnings villkoret anger du **matcha villkor** till **sökväg** och anger `/blobcontainer1/*` för **matchnings värde**. Ange **beteende för cachelagring** för att **åsidosätta** och ange 4 i rutan **timmar** .

    B. För det andra matchnings villkoret anger du **matchnings villkor** till **sökväg** och anger `/blobcontainer1/blob1.txt` för **matchnings värde**. Ange **beteende för cachelagring** för att **åsidosätta** och ange 2 i rutan **timmar** .

    ![Exempel på anpassade caching-regler för CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Den första anpassade regeln för cachelagring anger en cache-varaktighet på fyra timmar för alla BLOB-filer i `/blobcontainer1` mappen på ursprungs servern som anges av slut punkten. Den andra regeln åsidosätter bara den första regeln för `blob1.txt` BLOB-filen och anger varaktigheten två timmar för cachen.

2. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Ange Cache-Control rubriker med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) är ett av de snabbaste och mest kraftfulla sätten att administrera dina Azure-tjänster. Använd `Get-AzStorageBlob` cmdleten för att hämta en referens till bloben och ange sedan `.ICloudBlob.Properties.CacheControl` egenskapen. 

Exempel:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Du kan också använda PowerShell för att [Hantera dina CDN-profiler och slut punkter](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Ange Cache-Control rubriker med .NET
Om du vill ange en BLOB `Cache-Control` -rubrik med hjälp av .NET-kod använder du [Azure Storage klient biblioteket för .net](../storage/blobs/storage-quickstart-blobs-dotnet.md) för att ange egenskapen [CloudBlob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) .

Exempel:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Det finns fler .NET-kod exempel som är tillgängliga i [Azure Blob Storage-exempel för .net](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Ange Cache-Control rubriker med hjälp av andra metoder

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Med [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)kan du Visa och redigera dina Blob Storage-resurser, inklusive egenskaper som egenskapen *CacheControl* . 

Så här uppdaterar du egenskapen *CacheControl* för en blob med Azure Storage Explorer:
   1. Välj en blob och välj sedan **Egenskaper** på snabb menyn. 
   2. Rulla ned till egenskapen *CacheControl* .
   3. Ange ett värde och välj sedan **Spara**.


![Azure Storage Explorer egenskaper](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet
Med [azure Command-Line Interface](/cli/azure) (CLI) kan du hantera Azure Blob-resurser från kommando raden. Om du vill ange Cache-Control-huvudet när du laddar upp en blob med Azure CLI ställer du in egenskapen *cacheControl* med hjälp av `-p` växeln. I följande exempel visas hur du ställer in TTL till en timme (3600 sekunder):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure Storage-tjänster REST API
Du kan använda [Azure Storage-tjänster REST API](/rest/api/storageservices/) för att explicit ange egenskapen *x-MS-BLOB-Cache-Control* genom att använda följande åtgärder på en begäran:
  
   - [Placera blob](/rest/api/storageservices/Put-Blob)
   - [Lista över blockerade](/rest/api/storageservices/Put-Block-List)
   - [Ange BLOB-egenskaper](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control huvud
Du kan enkelt verifiera TTL-inställningarna för dina blobbar. Testa att din BLOB innehåller svars huvudet med webbläsarens [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) `Cache-Control` . Du kan också använda ett verktyg som [wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/)eller [Fiddler](https://www.telerik.com/fiddler) för att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du hanterar förfallo datum för moln tjänst innehåll i Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Lär dig mer om cachelagring av koncept](cdn-how-caching-works.md)