---
title: Azure Data Box Disk gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: e003d0121721838bd5ae038a3a8b4d1b8cd9d1eb
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573198"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk gränser


Tänk på följande begränsningar när du distribuerar och använder Microsoft Azure Data Box Disk-lösningen.

## <a name="data-box-service-limits"></a>Data Box-enhet tjänst gränser

 - Data Box-enhets tjänsten är tillgänglig i Azure-regionerna som anges i [regions tillgänglighet](data-box-disk-overview.md#region-availability).
 - Ett enda lagrings konto stöds med Data Box Disk.

## <a name="data-box-disk-performance"></a>Data Box Disk prestanda

Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

## <a name="azure-storage-limits"></a>Azure Storage-gränser

I det här avsnittet beskrivs gränserna för Azure Storage tjänst och de namngivnings konventioner som krävs för Azure Files, Azure block-blobbar och Azure Page-blobbar, som är tillämpliga för Data Box-enhet-tjänsten. Granska lagrings gränserna noggrant och följ alla rekommendationer.

Den senaste informationen om begränsningar för Azure Storage-tjänsten och metod tips för namngivning av resurser, behållare och filer finns på:

- [Namnge och referera till behållare](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och Page BLOB-konventioner](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstens gränser eller inte uppfyller Azure Files/BLOB-namngivnings konventionerna, matas dessa filer eller kataloger inte in i Azure Storage via Data Box-enhet-tjänsten.

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

- Kopiera inte data direkt till diskarna. Kopiera data till i förväg skapade *BlockBlob*-,*PageBlob*-och *AzureFile* -mappar.
- En mapp under *BlockBlob* och *PageBlob* är en behållare. Till exempel skapas behållare som *BlockBlob/container* och *PageBlob/container*.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB) i molnet med samma namn som det objekt som ska kopieras, kommer Data Box Disk byta namn på filen till filen (1) i molnet.
- Varje fil som skrivs till *BlockBlob* -och *PageBlob* -resurser laddas upp som en Block-Blob och Page blob.
- En tom katalogpartition (utan några filer) som skapats under *BlockBlob* och *PageBlob* mappar laddas inte upp.
- Om det uppstår några fel när data överförs till Azure skapas en fellogg på mål lagrings kontot. Sökvägen till den här fel loggen är tillgänglig i portalen när överföringen är klar och du kan granska loggen för att vidta lämpliga åtgärder. Ta inte bort data från källan utan att verifiera överförda data.
- Fil-metadata och NTFS-behörigheter bevaras inte när data överförs till Azure Files. Till exempel kommer det *senast ändrade* attributet för filerna inte att sparas när data kopieras.
- Om du har angett hanterade diskar i ordningen bör du granska följande ytterligare överväganden:

    - En hanterad disk i en resursgrupp måste ha ett unikt namn i de förskapade mapparna och på hela Data Box Disk. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp. Om de virtuella hårddiskarna har samma namn, konverteras bara en virtuell hårddisk till den hanterade disken med det namnet. De andra virtuella hårddiskarna laddas upp som sidblobar till mellanlagringskontot.
    - Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar de virtuella hårddiskarna utanför dessa mappar eller i en mapp som du har skapat, laddas de virtuella hårddiskarna upp till Azure Storage-kontot som sidblobar i stället för som hanterade diskar.
    - Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. Dynamiska virtuella hårddiskar och differentierande VHD- eller VHDX-filer stöds inte.
    - Icke-VHD-filer som kopieras till förskapade hanterade diskpartitioner kommer inte att konverteras till en hanterad disk.

## <a name="azure-storage-account-size-limits"></a>Storleks gränser för Azure Storage-kontot

Här följer gränserna för storleken på de data som kan kopieras till ett lagrings konto. Kontrol lera att de data som du överför följer dessa gränser. 

| Typ av data             | Standardgräns          |
|--------------------------|------------------------|
| blockera BLOB, Page BLOB    | Aktuell information om dessa begränsningar finns i [skalnings mål för Azure Blob Storage](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Azure standard Storage Scale-mål](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)och [Azure Files skala mål](../storage/files/storage-files-scale-targets.md#file-share-and-file-scale-targets). <br /><br /> Gränserna innehåller data från alla källor, inklusive Data Box Disk.|


## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

Här följer storleken på de Azure-objekt som kan skrivas. Se till att alla filer som överförs följer dessa gränser.

| Objekt typ för Azure | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockera BLOB        | ~ 4,75 TiB                                                 |
| Sid-BLOB         | 8 TiB <br> (Alla filer som laddats upp i sid-BLOB-format måste vara 512 byte justerade, annars Miss lyckas överföringen. <br> Både VHD och VHDX är 512 byte-justerade.) |
|Azure Files        | 1 TiB <br> Max. resurs storleken är 5 TiB     |
| Hanterade diskar     |4 TiB <br> Mer information om storlek och begränsningar finns i: <li>[Skalbarhets mål för Managed disks](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Konventioner för Azure Block Blob, Page blob och namngivning av filer

| Entitet                                       | Konventioner                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Behållar namn för Block-Blob och Page BLOB <br> Fileshare-namn för Azure Files | Måste vara ett giltigt DNS-namn som är mellan 3 och 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Får bara innehålla gemena bokstäver, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad tillåts inte i namn. |
| Katalog-och fil namn för Azure Files     |<li> Skift läges känsligt, SKIFT läges okänsligt och får inte överskrida 255 tecken. </li><li> Kan inte sluta med snedstreck (/). </li><li>Om den anges tas den bort automatiskt. </li><li> Följande tecken är inte tillåtna: <code>" \\ / : \| < > * ?</code></li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Otillåtna Sök vägs tecken för URL är inte tillåtna. Kod punkter som \\ uE000 är inte giltiga Unicode-tecken. Vissa ASCII-eller Unicode-tecken, t. ex. styr tecken (0x00 till 0x1F, \\ u0081 osv.), är inte heller tillåtna. För regler som styr Unicode-strängar i HTTP/1.1 se RFC 2616, avsnitt 2,2: Basic rules och RFC 3987. </li><li> Följande fil namn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, klock $, punkt tecken (.) och två punkt tecken (..).</li>|
| Blobnamn för blockblobar och sidblobar      | Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. <br> Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. <br> Reserverade URL-tecken måste undantas korrekt. <br>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog. |

## <a name="managed-disk-naming-conventions"></a>Namn konventioner för hanterade diskar

| Entitet | Konventioner                                             |
|-------------------|-----------------------------------------------------------|
| Hanterade disk namn       | <li> Namnet måste vara mellan 1 och 80 tecken långt. </li><li> Namnet måste börja med en bokstav eller en siffra, sluta med en bokstav, en siffra eller ett under streck. </li><li> Namnet får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. </li><li>   Namnet får inte innehålla blank steg eller `/` .                                              |

## <a name="next-steps"></a>Nästa steg

- Granska [data Box disk system krav](data-box-disk-system-requirements.md)