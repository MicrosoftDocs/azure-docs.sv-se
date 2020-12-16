---
title: Felsöka data överföringar med loggar
titleSuffix: Azure Data Box Disk
description: Beskriver hur du använder loggarna och felsöker problem som visas när du överför data till Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605283"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Förstå loggar för att felsöka problem med data uppladdning i Azure Data Box Disk

Den här artikeln gäller för Microsoft Azure Data Box Disk och beskriver de problem som visas när du laddar upp data till Azure.

## <a name="about-upload-logs"></a>Om uppladdning av loggar

När data laddas upp till Azure på data centret `_error.xml` och `_verbose.xml` filer skapas för varje lagrings konto. Dessa loggar överförs till samma lagrings konto som användes för att ladda upp data. 

Båda loggarna har samma format och innehåller XML-beskrivningar av de händelser som uppstod när data kopierades från disken till Azure Storage kontot.

Utförlig loggen innehåller fullständig information om status för kopierings åtgärden för varje BLOB eller fil, medan fel loggen bara innehåller information om blobbar eller filer som påträffade fel under uppladdningen.

Fel loggen har samma struktur som utförlig loggen men filtrerar fram lyckade åtgärder.

## <a name="download-logs"></a>Ladda ned loggar

Utför följande steg för att hitta uppladdnings loggarna.

1. Om det uppstår några fel när du överför data till Azure, visar portalen en sökväg till den mapp där diagnostikloggar finns.

    ![Länk till loggar i portalen](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Gå till **waies**.

    ![fel-och utförliga loggar](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

I varje fall visas fel loggarna och utförliga loggar. Välj varje logg och ladda ned en lokal kopia.

## <a name="sample-upload-logs"></a>Exempel på uppladdning av loggar

Ett exempel på `_verbose.xml` visas nedan. I det här fallet har ordern slutförts utan fel.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

I samma ordning visas ett exempel på som `_error.xml` visas nedan.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Ett exempel på `_error.xml` visas nedan där ordningen har slutförts med fel. 

Fel filen i det här fallet har ett `Summary` avsnitt och ett annat avsnitt som innehåller alla fil nivå fel. 

`Summary`Innehåller `ValidationErrors` och `CopyErrors` . I det här fallet överfördes 8 filer eller mappar till Azure och det fanns inga verifierings fel. När data kopierades till Azure Storage kontot har 5 filer eller mappar laddats upp. De återstående 3 filerna eller mapparna bytte namn enligt namngivnings konventionerna för Azure Container och laddades upp till Azure.

Fil nivåns status är i `BlobStatus` som beskriver alla åtgärder som vidtagits för att ladda upp blobarna. I det här fallet byter du namn på tre behållare eftersom mappar som data har kopierats till inte stämmer överens med namngivnings konventionerna i Azure för behållare. För blobbar som laddats upp i dessa behållare, det nya container namnet, sökvägen till blobben i Azure, den ursprungliga ogiltiga fil Sök vägen och blob-storleken ingår.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Data överförings fel

Felen som genereras när data laddas upp till Azure sammanfattas i följande tabell.

| Felkod | Beskrivning                   |
|-------------|------------------------------|
|`None` |  Har slutförts.           |
|`Renamed` | Bloben har bytt namn.   |
|`CompletedWithErrors` | Överföringen slutfördes med fel. Information om felaktiga filer ingår i logg filen.  |
|`Corrupted`|CRC beräknad under data inmatningen matchar inte CRC beräknad under uppladdning.  |  
|`StorageRequestFailed` | Azure Storage-begäran misslyckades.   |     
|`LeasePresent` | Det här objektet lånas ut och används av en annan användare. |
|`StorageRequestForbidden` |Det gick inte att överföra på grund av problem med autentiseringen. |
|`ManagedDiskCreationTerminalFailure` | Det gick inte att ladda upp som hanterade diskar. Filerna är tillgängliga i mellanlagrings kontot som Page blobbar. Du kan manuellt konvertera Page blobbar till hanterade diskar.  |
|`DiskConversionNotStartedTierInfoMissing` | Eftersom VHD-filen kopierades utanför mapparna för förskapad nivå skapades ingen hanterad disk. Filen laddas upp som Page blob till det mellanlagrings konto som anges när du skapar en order. Du kan konvertera den manuellt till en hanterad disk.|
|`InvalidWorkitem` | Det gick inte att överföra data eftersom de inte följer konventionerna för namngivning och begränsning i Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Överfört som block blobbar i en behållare med prefix `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Överfört som block blobbar i en behållare med prefix `databoxdisk-invalid-af` -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Överfört som block blobbar i en behållare med prefix `databoxdisk-invalid-md` -.|
|`InvalidManagedDiskUploadAsPageBlob` |Överfört som Page blobbar i en behållare med prefix `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Överförda filer till en ny resurs eftersom den ursprungliga resurs storleken översteg Max gränsen för Azure-storlek. Det nya fil resurs namnet har det ursprungliga namnet med suffixet `-2` .   |
|`MovedToDefaultAzureShare` |Överförda filer som inte tillhör någon mapp till en standard resurs. Resurs namnet börjar med `databox-` . |
|`ContainerRenamed` |Behållaren för de här filerna stämmer inte överens med namngivnings konventionerna för Azure och får ett nytt namn. Det nya namnet börjar med `databox-` och suffixet används med SHA1-hashen för det ursprungliga namnet |
|`ShareRenamed` |Resursen för de här filerna stämmer inte överens med namngivnings konventionerna för Azure och får ett nytt namn. Det nya namnet börjar med `databox-` och suffixet används med SHA1-hashen för det ursprungliga namnet. |
|`BlobRenamed` |De här filerna stämmer inte överens med namngivnings konventionerna för Azure och bytte namn. Markera `BlobPath` fältet för det nya namnet. |
|`FileRenamed` |De här filerna stämmer inte överens med namngivnings konventionerna för Azure och bytte namn. Markera `FileStoragePath` fältet för det nya namnet. |
|`DiskRenamed` |De här filerna stämmer inte överens med namngivnings konventionerna för Azure och bytte namn. Markera `BlobPath` fältet för det nya namnet. |


## <a name="next-steps"></a>Nästa steg

- [Öppna ett support ärende för data Box disk problem](data-box-disk-contact-microsoft-support.md).
