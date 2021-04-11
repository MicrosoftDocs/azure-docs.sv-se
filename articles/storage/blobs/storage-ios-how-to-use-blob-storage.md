---
title: Använda objekt lagring (BLOB) från iOS – Azure | Microsoft Docs
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
author: twooley
ms.author: twooley
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 1b0653c997930c60950da335e79b0388edd43897
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278362"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Använda Blob Storage från iOS

Den här artikeln visar hur du utför vanliga scenarier med Microsoft Azure Blob Storage. Exemplen skrivs i mål-C och använder [Azure Storage klient bibliotek för iOS](https://github.com/Azure/azure-storage-ios). I scenarierna ingår överföring, registrering, hämtning och borttagning av blobbar. Mer information om blobbar finns i avsnittet [Nästa steg](#next-steps) . Du kan också hämta [exempel appen](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) för att snabbt se användningen av Azure Storage i ett iOS-program.

Mer information om Blob Storage finns i [Introduktion till Azure Blob Storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importera Azure Storage iOS-biblioteket till ditt program

Du kan importera Azure Storage iOS-biblioteket till ditt program antingen med hjälp av [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) eller genom att importera **Ramverks** filen. CocoaPod är det rekommenderade sättet eftersom det gör det enklare att integrera biblioteket, men att importera från Framework-filen är mindre påträngande för ditt befintliga projekt.

Om du vill använda det här biblioteket behöver du följande:

- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Om du inte redan har gjort det [installerar du CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) på datorn genom att öppna ett terminalfönster och köra följande kommando

    ```shell
    sudo gem install cocoapods
    ```

2. Därefter skapar du en ny fil med namnet _Podfile_(inga fil namns tillägg) i projekt katalogen (katalogen som innehåller din. XCODEPROJ-fil). Lägg till följande i _Podfile_ och spara.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. I terminalfönstret navigerar du till projekt katalogen och kör följande kommando

    ```shell
    pod install
    ```

4. Om din. XCODEPROJ är öppen i Xcode, Stäng den. Öppna den nya projekt filen i projekt katalogen som har fil namns tillägget. xcworkspace. Det här är den fil som du kommer att arbeta från för tillfället.

## <a name="framework"></a>Ramverk

Det andra sättet att använda biblioteket är att bygga ramverket manuellt:

1. Börja med att hämta eller klona [Azure-Storage-iOS-lagrings platsen](https://github.com/azure/azure-storage-ios).
2. Gå till *Azure-Storage – iOS-*  ->  *lib*  ->  *Azure Storage klient bibliotek* och öppna `AZSClient.xcodeproj` i Xcode.
3. Ändra det aktiva schemat från "Azure Storage klient bibliotek" till "Framework" längst upp till vänster i Xcode.
4. Bygg projektet (⌘ + B). Då skapas en `AZSClient.framework` fil på Skriv bordet.

Du kan sedan importera Ramverks filen till ditt program genom att göra följande:

1. Skapa ett nytt projekt eller öppna det befintliga projektet i Xcode.
2. Dra och släpp i `AZSClient.framework` Xcode-projekt navigeringen.
3. Välj *Kopiera objekt om det behövs* och klicka på *Slutför*.
4. Klicka på ditt projekt i det vänstra navigerings fältet och klicka på fliken *Allmänt* längst upp i projekt redigeraren.
5. Under avsnittet *länkade ramverk och bibliotek* klickar du på knappen Lägg till (+).
6. I listan över bibliotek som redan har angetts kan du söka efter `libxml2.2.tbd` och lägga till det i projektet.

## <a name="import-the-library"></a>Importera biblioteket

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Om du använder Swift måste du skapa ett bryggnings huvud och importera \<AZSClient/AZSClient.h> där:

1. Skapa en rubrik fil `Bridging-Header.h` och Lägg till import instruktionen ovan.
2. Gå till fliken *versions inställningar* och Sök efter *mål-C-bryggnings huvud*.
3. Dubbelklicka på fältet för *mål-C-bryggnings rubriken* och Lägg till sökvägen till huvud filen: `ProjectName/Bridging-Header.h`
4. Skapa projektet (⌘ + B) för att kontrol lera att bryggnings huvudet hämtades av Xcode.
5. Börja använda biblioteket direkt i en SWIFT-fil, det finns inget behov av import-instruktioner.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynkrona åtgärder

> [!NOTE]
> Alla metoder som utför en begäran mot tjänsten är asynkrona åtgärder. I kod exemplen kommer du att se att dessa metoder har en slut för ande hanterare. Koden i slut för ande hanteraren kommer att köras **när** begäran har slutförts. Koden efter att slut för ande hanteraren körs **medan** begäran görs.

## <a name="create-a-container"></a>Skapa en container

Varje BLOB i Azure Storage måste finnas i en behållare. I följande exempel visas hur du skapar en behållare, som kallas *newcontainer*, i ditt lagrings konto om det inte redan finns. När du väljer ett namn för din behållare ska du vara mindful av de namngivnings regler som anges ovan.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Du kan bekräfta att detta fungerar genom att titta på [Microsoft Azure Storage Explorer](https://storageexplorer.com) och kontrol lera att *newcontainer* finns i listan över behållare för ditt lagrings konto.

## <a name="set-container-permissions"></a>Ange behållar behörigheter

En behållares behörigheter har kon figurer ATS för **privat** åtkomst som standard. Behållare innehåller dock några olika alternativ för behållar åtkomst:

- **Privat**: endast behållare och BLOB-data kan läsas av konto ägaren.
- **BLOB**: BLOB-data i den här behållaren kan läsas via en anonym begäran, men behållar data är inte tillgängliga. Klienter kan inte räkna upp blobar i behållaren via anonym begäran.
- **Container**: behållare och BLOB-data kan läsas via anonym begäran. Klienter kan räkna upp blobar i behållaren via anonym begäran, men kan inte räkna upp behållare i lagrings kontot.

I följande exempel visas hur du skapar en behållare med åtkomst behörigheter för **behållaren** , vilket ger offentlig, skrivskyddad åtkomst för alla användare på Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container

Som nämnts i avsnittet Blob Service-begrepp erbjuder Blob Storage tre olika typer av blobbar: block-blobar, bifoga blobbar och sid-blobar. Azure Storage iOS-biblioteket stöder alla tre typer av blobbar. I de flesta fall är blockblob den rekommenderade typen.

I följande exempel visas hur du laddar upp en Block-Blob från en NSString. Om det redan finns en blob med samma namn i den här behållaren kommer innehållet i denna BLOB att skrivas över.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Du kan bekräfta att detta fungerar genom att titta på [Microsoft Azure Storage Explorer](https://storageexplorer.com) och kontrol lera att behållaren, *containerpublic*, innehåller blobben, *sampleblob*. I det här exemplet använde vi en offentlig behållare så att du kan kontrol lera att det här programmet fungerade genom att gå till BLOB-URI: n:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Förutom att ladda upp en Block-Blob från en NSString, finns det liknande metoder för NSData, NSInputStream eller en lokal fil.

## <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

I följande exempel visas hur du visar en lista över alla blobbar i en behållare. När du utför den här åtgärden måste du vara mindful av följande parametrar:

- **continuationToken** – den fortsättnings-token representerar där registrerings åtgärden ska starta. Om ingen token anges, visar den en lista över blobar från början. Ett valfritt antal blobbar kan anges, från noll till en högsta mängd. Även om den här metoden returnerar noll resultat, om `results.continuationToken` inte är Nil, kan det finnas fler blobbar på tjänsten som inte har listats.
- **prefix** – du kan ange det prefix som ska användas för BLOB-listan. Endast blobbar som börjar med det här prefixet visas i listan.
- **useFlatBlobListing** – som nämnts i avsnittet [namnge och referera till behållare och blobbar](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , även om BLOB service är ett plant lagrings schema, kan du skapa en virtuell hierarki genom att namnge blobbar med Sök vägs information. Icke-flata listor stöds inte för närvarande. Den här funktionen kommer snart. För tillfället ska det här värdet vara **Ja**.
- **blobListingDetails** – du kan ange vilka objekt som ska inkluderas vid registrering av blobbar
  - _AZSBlobListingDetailsNone_: lista endast allokerade blobbar och returnera inte BLOB-metadata.
  - _AZSBlobListingDetailsSnapshots_: lista dedikerade blobbar och blob-ögonblicksbilder.
  - _AZSBlobListingDetailsMetadata_: hämtar BLOB-metadata för varje blob som returneras i listan.
  - _AZSBlobListingDetailsUncommittedBlobs_: visar lista över allokerade och avallokerade blobbar.
  - _AZSBlobListingDetailsCopy_: inkludera Kopiera egenskaper i listan.
  - _AZSBlobListingDetailsAll_: Visa en lista över alla tillgängliga allokerade blobbar, avallokerade blobbar och ögonblicks bilder och returnera alla metadata och kopierings status för dessa blobbar.
- **maxResults** – det maximala antalet resultat som ska returneras för den här åtgärden. Använd-1 om du inte vill ange en gräns.
- **completionHandler** – kod blocket som ska köras med resultaten från List åtgärden.

I det här exemplet används en hjälp metod för att rekursivt anropa List BLOB-metoden varje gång som en fortsättnings-token returneras.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Ladda ned en blob

I följande exempel visas hur du laddar ned en blob till ett NSString-objekt.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Ta bort en blob

I följande exempel visas hur du tar bort en blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Ta bort en BLOB-behållare

I följande exempel visas hur du tar bort en behållare.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Blob Storage från iOS kan du följa dessa länkar om du vill veta mer om iOS-biblioteket och lagrings tjänsten.

- [Azure Storage klient bibliotek för iOS](https://github.com/azure/azure-storage-ios)
- [Dokumentation om Azure Storage iOS-referens](https://azure.github.io/azure-storage-ios/)
- [REST-API för Azure Storage Services](/rest/api/storageservices/)
- [Azure Storage teamets blogg](/archive/blogs/windowsazurestorage/)

Om du har frågor om det här biblioteket är du välkommen att publicera till vår [Microsoft Q-&en fråge sida](/answers/topics/azure-blob-storage.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Om du har funktions förslag för Azure Storage kan du skicka vidare till [Azure Storage feedback](https://feedback.azure.com/forums/217298-storage/).