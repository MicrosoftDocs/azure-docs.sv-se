---
title: Utveckla för Azure Files med C++ | Microsoft Docs
description: Lär dig hur du utvecklar C++-program och-tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb74ab16e51fbb3a157757353d5743e889f993dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629707"
---
# <a name="develop-for-azure-files-with-c"></a>Utveckla för Azure Files med C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Om den här självstudiekursen

I den här självstudien får du lära dig hur du utför grundläggande åtgärder på Azure Files. Genom exempel som skrivits i C++ lär du dig att skapa resurser och kataloger, ladda upp, lista och ta bort filer. Om du är nybörjare på Azure Files går du igenom begreppen i avsnitten som följer är användbara när du ska förstå exemplen.

* Skapa och ta bort Azure-filresurser
* Skapa och ta bort kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, ladda ned och ta bort en fil
* Ange kvoten (maximal storlek) för en Azure-filresurs
* Skapa en signatur för delad åtkomst (SAS-nyckel) för en fil som använder en princip för delad åtkomst som definierats för resursen.

> [!Note]  
> Eftersom Azure Files kan nås via SMB, är det möjligt att skriva enkla program som har åtkomst till Azure-filresursen med hjälp av standard-I/O-klasser och-funktioner. Den här artikeln beskriver hur du skriver program som använder Azure Storage C++ SDK, som använder [filen REST API](/rest/api/storageservices/file-service-rest-api) för att kommunicera med Azure Files.

## <a name="create-a-c-application"></a>Skapa ett C++-program

Om du vill bygga exemplen måste du installera Azure Storage klient bibliotek 2.4.0 för C++. Du bör också ha skapat ett Azure Storage-konto.

Om du vill installera Azure Storage-2.4.0 för C++ kan du använda någon av följande metoder:

* **Linux:** Följ anvisningarna i README-sidan [för Azure Storage klient bibliotek för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows:** I Visual Studio klickar du på **verktyg &gt; NuGet Package Manager &gt; Package** Manager-konsolen. Skriv följande kommando i [NuGet Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på **RETUR**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files

Lägg till följande include-instruktioner överst i C++-källfilen där du vill ändra Azure Files:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage

Om du vill använda fil lagring måste du ansluta till ditt Azure Storage-konto. Det första steget är att konfigurera en anslutnings sträng som vi använder för att ansluta till ditt lagrings konto. Nu ska vi definiera en statisk variabel.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Ansluta till ett Azure Storage-konto

Du kan använda **cloud_storage_account** -klassen för att representera din lagrings konto information. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Alla filer och kataloger i en Azure-filresurs finns i en behållare som kallas för en **resurs**. Ditt lagrings konto kan ha så många resurser som din konto kapacitet tillåter. För att få åtkomst till en resurs och dess innehåll måste du använda en Azure Files-klient.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Med hjälp av Azure Files-klienten kan du hämta en referens till en resurs.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Använd metoden **create_if_not_exists** för **cloud_file_share** -objektet för att skapa resursen.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

I det här läget innehåller **resursen** en referens till en resurs med namnet **My-Sample-Share**.

## <a name="delete-an-azure-file-share"></a>Ta bort en Azure-filresurs

Att ta bort en resurs görs genom att anropa metoden **delete_if_exists** på ett cloud_file_share-objekt. Här är exempel koden som gör det.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Skapa en katalog

Du kan organisera lagringen genom att placera filer i under kataloger i stället för att använda dem i rot katalogen. Med Azure Files kan du skapa så många kataloger som ditt konto kommer att tillåta. Koden nedan skapar en katalog med namnet **My-Sample-Directory** under rot katalogen samt en under katalog med namnet **My-Sample-** del-katalogen.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Att ta bort en katalog är en enkel uppgift, men det bör noteras att du inte kan ta bort en katalog som fortfarande innehåller filer eller andra kataloger.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs

Att hämta en lista över filer och kataloger i en resurs görs enkelt genom att anropa **list_files_and_directories** på en **cloud_file_directory** referens. För att få åtkomst till den omfattande uppsättningen med egenskaper och metoder för en returnerad **list_file_and_directory_item**, måste du anropa metoden **list_file_and_directory_item. as _file** för att hämta ett **cloud_file** -objekt, eller **list_file_and_directory_item. as _directory** -metoden för att hämta ett **cloud_file_directory** -objekt.

Följande kod visar hur du hämtar och matar ut URI för varje objekt i resursens rot Katalog.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Ladda upp en fil

En Azure-filresurs innehåller minst en rot katalog där filerna kan finnas. I det här avsnittet får du lära dig hur du laddar upp en fil från lokal lagring till rot katalogen för en resurs.

Det första steget när du laddar upp en fil är att hämta en referens till den katalog där den ska finnas. Det gör du genom att anropa metoden **get_root_directory_reference** för objektet Share.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Nu när du har en referens till resursens rot Katalog kan du ladda upp en fil till den. I det här exemplet överförs från en fil, från text och från en data ström.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Ladda ned en fil

Hämta filer genom att först hämta en fil referens och sedan anropa metoden **download_to_stream** för att överföra fil innehållet till ett Stream-objekt, som du sedan kan behålla till en lokal fil. Du kan också använda metoden **download_to_file** för att ladda ned innehållet i en fil till en lokal fil. Du kan använda metoden **download_text** för att ladda ned innehållet i en fil som en text sträng.

I följande exempel används **download_to_stream** och **download_text** metoder för att demonstrera nedladdning av filerna som skapades i föregående avsnitt.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Ta bort en fil

En annan vanlig Azure Files åtgärd är fil borttagning. Följande kod tar bort en fil med namnet My-Sample-File-3 som lagras under rot katalogen.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Ange kvoten (maximal storlek) för en Azure-filresurs

Du kan ange kvoten (eller den största storleken) för en fil resurs i gigabyte. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Genom att ange kvoten för en resurs kan du begränsa den totala storleken på filerna som lagras på resursen. Om den totala storleken på filerna som lagras på resursen överskrider kvoten som angetts för resursen kan klienterna inte öka storleken på befintliga filer eller skapa nya filer såvida inte dessa filer är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generera en signatur för delad åtkomst för en fil eller filresurs

Du kan skapa en signatur för delad åtkomst (SAS) för en fil resurs eller en enskild fil. Du kan också skapa en princip för delad åtkomst på en filresurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en princip för delad åtkomst eftersom det ger dig möjlighet att återkalla signaturen för delad åtkomst om det behövs.

I följande exempel skapar vi en princip för delad åtkomst på en resurs och använder sedan principen för att ange begränsningarna för en signatur för delad åtkomst på en fil i resursen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Nästa steg

Utforska gärna dessa resurser om du vill veta mer om Azure Storage:

* [Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)
* [Azure Storage fil tjänst exempel i C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Lagringsutforskaren](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage dokumentation](https://azure.microsoft.com/documentation/services/storage/)