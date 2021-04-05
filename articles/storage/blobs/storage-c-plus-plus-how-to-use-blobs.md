---
title: Använda objekt lagring (BLOB) från C++ – Azure | Microsoft Docs
description: Lär dig hur du lagrar ostrukturerade data (blobbar) i molnet med Azure Blob-lagring (objekt lagring) med hjälp av C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 64069292ea0059216d06bfc41316c2aed7484dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011106"
---
# <a name="how-to-use-blob-storage-from-c"></a>Använda Blob Storage från C++

Den här guiden visar hur du utför vanliga scenarier med Azure Blob Storage. I exemplen visas hur du överför, listar, laddar ned och tar bort blobbar. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
Mer information om Blob Storage finns i [Introduktion till Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Microsoft rekommenderar att du använder den senaste versionen av lagrings klient biblioteket för C++, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden ska du använda lagrings funktioner som kan köras i ett C++-program.

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

- **Linux:** Följ anvisningarna i avsnittet [Azure Storage klient bibliotek för C++ viktigt: komma igång på Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) -sidan.
- **Windows:** Använd [vcpkg](https://github.com/microsoft/vcpkg) som beroende hanterare i Windows. Starta vcpkg genom att följa [snabb](https://github.com/microsoft/vcpkg#quick-start) starten. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du hittar en guide för hur du skapar käll koden och exporterar till NuGet i [README](https://github.com/Azure/azure-storage-cpp#download--install) -filen.

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurera ditt program för att få åtkomst till Blob Storage
Lägg till följande include-instruktioner överst i C++-filen där du vill använda Azure Storage-API: er för att få åtkomst till blobbar:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klient program måste du ange lagrings anslutnings strängen i följande format, med namnet på ditt lagrings konto och lagrings åtkomst nyckeln för det lagrings konto som anges i [Azure Portal](https://portal.azure.com) för värdena *AccountName* och *AccountKey* . Information om lagrings konton och åtkomst nycklar finns i [om Azure Storage-konton](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa programmet på den lokala Windows-datorn kan du använda [Azurite Storage-emulatorn](../common/storage-use-azurite.md). Azurite är ett verktyg som simulerar blob-och Queue Services som är tillgängliga i Azure på din lokala utvecklings dator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Information om hur du startar Azurite finns i [använda Azurite-emulatorn för lokal Azure Storage utveckling](../common/storage-use-azurite.md).

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="retrieve-your-storage-account"></a>Hämta ditt lagrings konto
Du kan använda **cloud_storage_account** -klassen för att representera din lagrings konto information. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Därefter får du en referens till en **cloud_blob_client** -klass som du kan använda för att hämta objekt som representerar behållare och blobbar lagrade i Blob Storage. Följande kod skapar ett **cloud_blob_client** -objekt med det lagrings konto objekt som vi hämtade ovan:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Gör så här: skapa en behållare
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Det här exemplet visas hur du skapar en container om den inte redan finns:

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Som standard är den nya behållaren privat och du måste ange din lagrings åtkomst nyckel för att ladda ned blobbar från den här behållaren. Om du vill skapa filer (blobbar) i behållaren som är tillgängliga för alla kan du ange att behållaren ska vara offentlig med följande kod:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Alla på Internet kan se blobbar i en offentlig behållare, men du kan bara ändra eller ta bort dem om du har rätt åtkomst nyckel.

## <a name="how-to-upload-a-blob-into-a-container"></a>Gör så här: Ladda upp en BLOB i en behållare
Azure Blob Storage stöder block-blobbar och Page blobbar. I de flesta fall är blockblobbar den rekommenderade typen.

Om du vill ladda upp en fil till en blockblob hämtar du en referens för containern och använder den för att hämta en referens för blockbloben. När du har en BLOB-referens kan du ladda upp data strömmen till den genom att anropa metoden **upload_from_stream** . Med den här åtgärden skapas blobben om den inte fanns tidigare, eller skrivs över om den redan fanns. Följande exempel visar hur du laddar upp en blob till en container och förutsätter att containern redan hade skapats.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Du kan också använda metoden **upload_from_file** för att överföra en fil till en Block-Blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Gör så här: Visa en lista över blobarna i en behållare
Om du vill visa blobar i en container börjar du med att hämta en referens för containern. Du kan sedan använda behållarens **list_blobs** Metod för att hämta blobbar och/eller kataloger i den. För att få åtkomst till den omfattande uppsättningen med egenskaper och metoder för en returnerad **list_blob_item**, måste du anropa metoden **list_blob_item. as _blob** för att hämta ett  **cloud_blob** -objekt, eller **list_blob. as _directory** -metoden för att hämta ett cloud_blob_directory-objekt. Följande kod visar hur du hämtar och matar ut URI för varje objekt i behållaren **My-Sample-container** :

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Mer information om List åtgärder finns i [lista Azure Storage resurser i C++](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Så här gör du: Ladda ned blobbar
Hämta blobar genom att först hämta en BLOB-referens och sedan anropa **download_to_stream** -metoden. I följande exempel används metoden **download_to_stream** för att överföra BLOB-innehållet till ett Stream-objekt som du sedan kan behålla till en lokal fil.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Du kan också använda metoden **download_to_file** för att ladda ned innehållet i en blob till en fil.
Dessutom kan du också använda metoden **download_text** för att ladda ned innehållet i en blob som en text sträng.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Så här gör du: ta bort blobbar
Om du vill ta bort en BLOB måste du först skaffa en BLOB-referens och sedan anropa metoden **delete_blob** på den.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Blob Storage kan du följa dessa länkar om du vill veta mer om Azure Storage.

- [Så använder du Queue Storage från C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Visa Azure Storage resurser i C++](../common/storage-c-plus-plus-enumeration.md)
- [Lagrings klient bibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
- [Azure Storage dokumentation](https://azure.microsoft.com/documentation/services/storage/)
- [Överföra data med kommando rads verktyget AzCopy](../common/storage-use-azcopy-v10.md)