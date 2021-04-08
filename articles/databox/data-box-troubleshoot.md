---
title: Felsök problem på Azure Data Box Azure Data Box Heavy
description: Beskriver hur du felsöker problem som visas i Azure Data Box och Azure Data Box Heavy när du kopierar data till dessa enheter.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124687"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Felsöka problem som rör Azure Data Box och Azure Data Box Heavy

Den här artikeln innehåller information om hur du felsöker problem som kan uppstå när du använder Azure Data Box eller Azure Data Box Heavy för import order. Artikeln innehåller en lista över möjliga fel som visas när data kopieras till Data Box-enhet eller när data laddas upp från Data Box-enhet för en import ordning.

Informationen i den här artikeln gäller inte för export order som skapats för Data Box-enhet.

## <a name="error-classes"></a>Fel klasser

Felen i Data Box-enhet och Data Box Heavy sammanfattas på följande sätt:

| Fel kategori *        | Beskrivning        | Rekommenderad åtgärd    |
|----------------------------------------------|---------|--------------------------------------|
| Behållare eller resurs namn | Behållarna eller resurs namnen följer inte namngivnings reglerna för Azure.  |Hämta fel listorna. <br> Byt namn på behållarna eller resurserna. [Läs mer](#container-or-share-name-errors).  |
| Gräns för container eller resurs storlek | Den totala mängden data i behållare eller resurser överskrider Azure-gränsen.   |Hämta fel listorna. <br> Minska de övergripande data i behållaren eller resursen. [Läs mer](#container-or-share-size-limit-errors).|
| Storleks gräns för objekt eller fil | Objektet eller filerna i behållare eller resurser överskrider Azure-gränsen.|Hämta fel listorna. <br> Minska fil storleken i behållaren eller resursen. [Läs mer](#object-or-file-size-limit-errors). |    
| Data-eller filtyper | Data formatet eller filtypen stöds inte. |Hämta fel listorna. <br> För sid-blobbar eller hanterade diskar ser du till att data är 512-byte justerade och kopieras till de i förväg skapade mapparna. [Läs mer](#data-or-file-type-errors). |
| Icke-kritiska BLOB-eller filfel  | BLOB-eller fil namnen följer inte reglerna för namngivning i Azure eller också stöds inte filtypen. | Dessa BLOB-eller filer får inte kopieras eller så kan namnen ändras. [Lär dig hur du åtgärdar felen](#non-critical-blob-or-file-errors). |

\* De första fyra fel kategorierna är kritiska fel och måste åtgärdas innan du kan fortsätta med att förbereda för leverans.


## <a name="container-or-share-name-errors"></a>Fel i behållare eller resurs namn

Detta är fel som rör behållare och resurs namn.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Fel Beskrivning:** Behållaren eller resurs namnet måste innehålla mellan 3 och 63 tecken. 

**Rekommenderad lösning:** Mappen under Data Box-enhet eller Data Box Heavy resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagrings konto. 

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet för enheten laddar du ned och granskar felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under Data Box-enhet-eller Data Box Heavy-resursen för att se till att:

    - Namnet består av mellan 3 och 63 tecken.
    - Namnen får bara innehålla bokstäver, siffror och bindestreck.
    - Namnen får inte börja eller sluta med bindestreck.
    - Namnen får inte innehålla flera bindestreck.
    - Exempel på giltiga namn: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga `my-folder_1` : `my` , `--myfolder` , `myfolder--` ,, `myfolder!`

    Mer information finns i namngivnings konventionerna i Azure för [behållar namn](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resurs namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Fel Beskrivning:** Behållaren eller resurs namnet får bara bestå av bokstäver, siffror eller bindestreck.

**Rekommenderad lösning:** Mappen under Data Box-enhet eller Data Box Heavy resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagrings konto. 

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet för enheten laddar du ned och granskar felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under Data Box-enhet-eller Data Box Heavy-resursen för att se till att:

    - Namnet består av mellan 3 och 63 tecken.
    - Namnen får bara innehålla bokstäver, siffror och bindestreck.
    - Namnen får inte börja eller sluta med bindestreck.
    - Namnen får inte innehålla flera bindestreck.
    - Exempel på giltiga namn: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga `my-folder_1` : `my` , `--myfolder` , `myfolder--` ,, `myfolder!`

    Mer information finns i namngivnings konventionerna i Azure för [behållar namn](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resurs namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Fel Beskrivning:** Behållar namn och resurs namn får inte börja eller sluta med bindestreck och får inte innehålla flera bindestreck.

**Rekommenderad lösning:** Mappen under Data Box-enhet eller Data Box Heavy resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagrings konto. 

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet för enheten laddar du ned och granskar felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under Data Box-enhet-eller Data Box Heavy-resursen för att se till att:

    - Namnet består av mellan 3 och 63 tecken.
    - Namnen får bara innehålla bokstäver, siffror och bindestreck.
    - Namnen får inte börja eller sluta med bindestreck.
    - Namnen får inte innehålla flera bindestreck.
    - Exempel på giltiga namn: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga `my-folder_1` : `my` , `--myfolder` , `myfolder--` ,, `myfolder!`

    Mer information finns i namngivnings konventionerna i Azure för [behållar namn](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resurs namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Fel Beskrivning**: katalogen eller behållar namnen innehåller ogiltiga tecken.

**Rekommenderad lösning**: katalogen eller de behållar namn som du har kopierat innehåller tecken som inte stöds.

- På sidan Anslut och kopiera i det lokala webb gränssnittet laddar du ned och granskar felfilerna för att identifiera mappnamnen med problem. 
- Byt namn på katalogen eller behållare för att säkerställa att de är kompatibla med namngivnings konventionerna i Azure.

Mer information finns i namngivnings konventionerna för [kataloger](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) och [behållare](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)i Azure.

## <a name="container-or-share-size-limit-errors"></a>Gräns fel för behållare eller resurs storlek

Detta är fel som är relaterade till data som överskrider storleken på de data som tillåts i en behållare eller resurs.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Fel Beskrivning:** Azure-filresurs begränsar en resurs till 5 TiB data och stora fil resurser är inte aktiverade på lagrings kontot. Den här gränsen överskreds för vissa resurser.

**Rekommenderad lösning:** På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.

- Identifiera de mappar som har det här problemet från fel loggarna och kontrol lera att filerna i mappen är under 5 TiB.
- Gränsen på 5 TiB gäller inte för ett lagrings konto som tillåter stora fil resurser. Du måste dock ha stora fil resurser konfigurerade när du placerar din beställning. 
  - Kontakta [Microsoft Support](data-box-disk-contact-microsoft-support.md) och begär en ny leverans etikett.
  - [Aktivera stora fil resurser på lagrings kontot.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Expandera fil resurserna i lagrings kontot](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) och ange kvoten till 100 TIB.
  
  
## <a name="object-or-file-size-limit-errors"></a>Gräns fel för objekt-eller fil storlek

Detta är fel relaterade till data som överskrider den maximala storleken på objektet eller filen som tillåts i Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Fel Beskrivning:** Fil storleken överskrider den maximala fil storleken för uppladdning.

**Rekommenderad lösning:** Blobben eller fil storlekarna överskrider den högsta tillåtna gränsen för uppladdning.

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
- Kontrol lera att blob-och fil storlekarna inte överskrider storleks gränserna för Azure-objekt.

## <a name="data-or-file-type-errors"></a>Data-eller fil typs fel

Detta är fel som rör filtypen eller data typen som inte stöds i behållaren eller resursen. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Fel Beskrivning:** Blobben eller filen är felaktigt justerad.

**Rekommenderad lösning:** Sidans BLOB-resurs på Data Box-enhet eller Data Box Heavy stöder endast filer som är 512 byte-justerade (till exempel VHD/VHDX). Alla data som kopieras till sidans BLOB-resurs laddas upp till Azure som Page blobbar.

Ta bort eventuella icke-VHD/VHDX-data från sidans BLOB-resurs. Du kan använda resurser för Block-Blob eller Azure Files för allmänna data.

Mer information finns i [Översikt över Page blobbar](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Fel Beskrivning:** En filtyp som inte stöds finns i en hanterad disk resurs. Endast fasta virtuella hård diskar är tillåtna.

**Rekommenderad lösning:**

- Se till att du bara laddar upp fasta virtuella hård diskar för att skapa hanterade diskar.
- VHDX-filer eller **dynamiska** och **differentierande** virtuella hård diskar stöds inte.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Fel Beskrivning:** En katalog tillåts inte i någon av de befintliga mapparna för de hanterade diskarna. Endast fasta virtuella hård diskar tillåts i dessa mappar.

**Rekommenderad lösning:** För Managed disks i varje resurs skapas följande tre mappar som motsvarar behållare i ditt lagrings konto: Premium SSD, Standard HDD och Standard SSD. Dessa mappar motsvarar prestanda nivån för den hanterade disken.

- Se till att du kopierar dina Page BLOB-data (VHD) till någon av dessa befintliga mappar.
- En mapp eller katalog tillåts inte i dessa befintliga mappar. Ta bort alla mappar som du har skapat i befintliga mappar.

Mer information finns i [Kopiera till Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Fel Beskrivning:** Symboliska länkar är inte tillåtna i Linux. 

**Rekommenderad lösning:** Symboliska länkar är vanligt vis länkar, pipes och andra sådana filer. Ta antingen bort länkarna eller lös länkarna och kopiera data.


## <a name="non-critical-blob-or-file-errors"></a>Icke-kritiska BLOB-eller filfel

Alla icke-kritiska fel som är relaterade till namn på blobbar, filer eller behållare som visas under data kopieringen sammanfattas i följande avsnitt. Om de här felen finns, ändras namnen så att de överensstämmer med namngivnings konventionerna för Azure. Motsvarande order status för data uppladdning **slutförs med varningar**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Fel Beskrivning:** BLOB-eller fil namnen innehåller kontroll tecken som inte stöds.

**Rekommenderad lösning:** Blobarna eller filerna som du har kopierat innehåller namn med tecken som inte stöds.

På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
Ta bort eller Byt namn på filerna om du vill ta bort tecken som inte stöds.

Mer information finns i namngivnings konventionerna för Azure för [BLOB-namn](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [fil namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Fel Beskrivning:** BLOB-eller fil namnen innehåller otillåtna tecken.

**Rekommenderad lösning:** Blobarna eller filerna som du har kopierat innehåller namn med tecken som inte stöds.

På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
Ta bort eller Byt namn på filerna om du vill ta bort tecken som inte stöds.

Mer information finns i namngivnings konventionerna för Azure för [BLOB-namn](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [fil namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Fel Beskrivning:** BLOB-eller fil namnen slutar med felaktiga tecken.

**Rekommenderad lösning:** Blobarna eller filerna som du har kopierat innehåller namn med tecken som inte stöds.

På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
Ta bort eller Byt namn på filerna om du vill ta bort tecken som inte stöds.

Mer information finns i namngivnings konventionerna för Azure för [BLOB-namn](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [fil namn](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Fel Beskrivning:** Blobben eller fil namnet innehåller för många Sök vägs segment.

**Rekommenderad lösning:** Blobbar eller filer som du har kopierat överskrider det maximala antalet Sök vägs segment. Ett Sök vägs segment är strängen mellan efterföljande avgränsnings tecken, till exempel snedstreck/.

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
- Kontrol lera att [BLOB-namnen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [fil namnen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) följer namngivnings konventionerna i Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Fel Beskrivning:** Blobben eller fil namnet är för långt.

**Rekommenderad lösning:** Blobben eller fil namnen överskrider den maximala längden.

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
- BLOB-namnet får inte överskrida 1 024 tecken.
- Ta bort eller Byt namn på blobben eller filer så att namnen inte överstiger 1024 tecken.

Mer information finns i namngivnings konventionerna för Azure för BLOB-namn och fil namn.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Fel Beskrivning:** Ett av segmenten i blobben eller fil namnet är för långt.

**Rekommenderad lösning:** Ett av Sök vägs segmenten i blobben eller fil namnet överskrider det högsta antalet tecken. Ett Sök vägs segment är strängen mellan efterföljande avgränsnings tecken, till exempel snedstreck/.

- På sidan **Anslut och kopiera** i det lokala webb gränssnittet laddar du ned och granskar fel filerna.
- Kontrol lera att [BLOB-namnen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [fil namnen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) följer namngivnings konventionerna i Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Fel Beskrivning:** Felaktiga behållar namn har angetts för hanterade disk resurser.

**Rekommenderad lösning:** För Managed disks i varje resurs skapas följande mappar som motsvarar behållare i ditt lagrings konto: Premium SSD, Standard HDD och Standard SSD. Dessa mappar motsvarar prestanda nivån för den hanterade disken.

- Se till att du kopierar dina Page BLOB-data (VHD) till någon av dessa befintliga mappar. Endast data från dessa befintliga behållare laddas upp till Azure.
- Alla andra mappar som skapas på samma nivå som Premium SSD, Standard HDD och Standard SSD motsvarar inte en giltig prestanda nivå och kan inte användas.
- Ta bort filer eller mappar som skapats utanför prestanda nivåerna.

Mer information finns i [Kopiera till Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Nästa steg

- Läs mer om [system kraven för data Box-enhet Blob Storage](data-box-system-requirements-rest.md).