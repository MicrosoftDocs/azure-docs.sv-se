---
title: Azure Cosmos DB bilagor
description: Den här artikeln innehåller en översikt över Azure Cosmos DB bifogade filer.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 455fdc7615e0909189f311755571d02a9acca24e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501999"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB bilagor
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB bilagor är särskilda objekt som innehåller referenser till en associerad metadata med en extern BLOB-eller mediafil.

Azure Cosmos DB stöder två typer av bilagor:

* **Ohanterade bilagor** är en omslutning runt en URI-referens till en blob som lagras i en extern tjänst (till exempel Azure Storage, OneDrive osv.). Den här metoden liknar att lagra en URI-egenskap i ett standard Azure Cosmos DBs objekt.
* **Hanterade bilagor** är blobbar som hanteras och lagras internt av Azure Cosmos DB och exponeras via en systemgenererad mediaLink.


> [!NOTE]
> Bilagan är en äldre funktion. Supporten är begränsad till att erbjuda fortsatta funktioner om du redan använder den här funktionen.
> 
> I stället för att använda bilagor rekommenderar vi att du använder Azure Blob Storage som ett syfte att skapa BLOB-data med hjälp av Azure. Du kan fortsätta att lagra metadata som är relaterade till blobbar, tillsammans med referens-URI-länkar i Azure Cosmos DB som objekt egenskaper. Att lagra dessa data i Azure Cosmos DB ger möjlighet att fråga metadata och länkar till blobbar som lagras i Azure Blob Storage.
> 
> Microsoft strävar efter att tillhandahålla minst 36 månads meddelande innan bilagor tas bort helt och hållet.

## <a name="known-limitations"></a>Kända begränsningar

Azure Cosmos DBs hanterade bifogade filer skiljer sig från dess stöd för standard objekt – och ger obegränsad skalbarhet, global distribution och integrering med andra Azure-tjänster.

- Bilagor stöds inte i alla versioner av Azure Cosmos DB SDK: er.
- Hanterade bilagor är begränsade till 2 GB lagrings utrymme per databas konto.
- Hanterade bilagor är inte kompatibla med Azure Cosmos DB global distribution och de replikeras inte över flera regioner.

> [!NOTE]
> Azure Cosmos DB-API för MongoDB-version 3,2 använder hanterade bilagor för GridFS, vilket innebär att samma begränsningar används som Azure Cosmos DBs hanterade bifogade filer.
>
> Vi rekommenderar utvecklare som använder funktionen MongoDB GridFS att uppgradera till Azure Cosmos DB API för MongoDB version 3,6 eller högre, som är frikopplad från bilagor och ger en starkare och stabilare upplevelse. Utvecklare som använder funktions uppsättningen MongoDB GridFS bör också överväga att använda Azure Blob Storage – vilket är syftet med att lagra BLOB-innehåll och erbjuder utökade funktioner till lägre kostnad jämfört med GridFS.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrera bilagor till Azure Blob Storage

Vi rekommenderar att du migrerar Azure Cosmos DB bifogade filer till Azure Blob Storage genom att följa dessa steg:

1. Kopiera bilagors data från din käll Azure Cosmos DB behållare till din Azure Blob Storage-behållare.
2. Verifiera överförda BLOB-data i Azures Blob Storage-behållare.
3. Lägg till URI-referenser till blobarna i Azure Blob Storage som sträng egenskaper i Azure Cosmos DB data uppsättningen, om så är tillämpligt.
4. Återskapar din program kod för att läsa och skriva blobbar från den nya Azure Blob Storage-behållaren.

Följande kod exempel för dotNET visar hur du kopierar bilagor från Azure Cosmos DB till Azure Blob Storage som en del av ett migreringsjobb med hjälp av Azure Cosmos DBs .NET SDK v2 och Azure Blob Storage .NET SDK-V12. Se till att ersätta `<placeholder values>` käll Azure Cosmos DBS konto och Azure Blob Storage-behållare.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Nästa steg

- Kom igång med [Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Hämta referenser för att använda bilagor via [Azure Cosmos dB .NET SDK v2](/dotnet/api/microsoft.azure.documents.attachment)
- Hämta referenser för att använda bilagor via [Azure Cosmos DB Java SDK v2](/java/api/com.microsoft.azure.documentdb.attachment)
- Hämta referenser för att använda bilagor via [Azure Cosmos DB REST API](/rest/api/cosmos-db/attachments)
