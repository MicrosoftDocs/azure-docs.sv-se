---
title: Azure PowerShell-skriptexempel – Beräkna den totala faktureringsstorleken på en blob-container | Microsoft Docs
description: Beräkna den totala storleken på en container i Azure Blob Storage för faktureringsändamål.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/29/2020
ms.author: fryu
ms.openlocfilehash: dfc338844e310102447e2498ee9cce8f28a79b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97809572"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Beräkna den totala faktureringsstorleken på en blob-container

Det här skriptet beräknar storleken på en container i Azure Blob Storage för att ge en uppskattning av faktureringskostnaderna. Skriptet summerar storleken på blobarna i containern.

> [!IMPORTANT]
> Exempel skriptet som anges i den här artikeln kanske inte korrekt beräknar fakturerings storleken för BLOB-ögonblicksbilder.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Den här PowerShell-skriptet beräknar storleken på en container för faktureringsändamål. Om du beräknar containerns storlek för andra ändamål hittar du ett enklare skript som ger en beräkning i [Beräkna den totala storleken på en Blob Storage-container](../scripts/storage-blobs-container-calculate-size-powershell.md).

## <a name="determine-the-size-of-the-blob-container"></a>Beräkna storleken på blob-containern

Den totala storleken på blob-containern inkluderar storleken på själva containern och storleken på alla blobar i containern.

Följande avsnitt beskriver hur lagringskapaciteten beräknas för blob-containrar och blobar. I följande avsnitt betyder Len(X) antalet tecken i strängen.

### <a name="blob-containers"></a>Blobcontainrar

Följande beräkning beskriver hur du beräknar den mängd lagringsutrymme som förbrukas per blob-container:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Här följer analysen:

* 48 byte med tillhörande information för varje container innehåller tid för senaste ändring, behörigheter, offentliga inställningar och vissa systemmetadata.

* Containerns namn lagras som Unicode, så ta antalet tecken och multiplicera med två.

* För varje block med blob-containermetadata som lagras så lagrar vi namnets längd (ASCII) samt längden på strängvärdet.

* 512 byte per signerat ID innehåller namnet på det signerade ID:t, starttiden, förfallotiden och behörigheter.

### <a name="blobs"></a>Blobar

Följande beräkningar visar hur du beräknar den mängd lagringsutrymme som förbrukas per blob.

* Blockblob (grundläggande blob eller ögonblicksbild):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Sidblob (grundläggande blob eller ögonblicksbild):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Här följer analysen:

* 124 byte med tillhörande information för blob, vilket innefattar:
    - Ändrades senast
    - Storlek
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - Behörigheter
    - Ögonblicksbildsinformation
    - Livslängd
    - Vissa systemmetadata

* Blobnamnet lagras som Unicode, så ta antalet tecken och multiplicera med två.

* För varje block med metadata som lagras lägger du till namnets längd (lagras som ASCII) samt längden på strängvärdet.

* För blockblobarna:
  * 8 byte för blocklistan.
  * Antalet block gånger block-ID-storleken i byte.
  * Storleken på data i alla incheckade och ej incheckade block.

    >[!NOTE]
    >När ögonblicksbilder används innehåller den här storleken bara unika data för den här grundläggande bloben eller ögonblicksbloben. Om de ej incheckade blocken inte används efter en vecka samlas de upp som skräp. Efter det räknas de inte mot fakturering.

* För sidblobar:
  * Antalet flera ej efterföljande sidintervall med data gånger 12 byte. Detta är antalet unika sidintervall som du ser när du anropar **GetPageRanges**-API:et.

  * Storleken på data i byte för alla lagrade sidor.

    >[!NOTE]
    >När ögonblicksbilder används omfattar den här storleken bara unika sidor för den grundläggande blob eller ögonblicksblob som räknas.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Nästa steg

- Ett enkelt skript som ger en beräkning av containerstorleken finns i [Beräkna den totala storleken på en Blob Storage-container](../scripts/storage-blobs-container-calculate-size-powershell.md).

- Mer information om Azure Storage-fakturering finns i [Förstå Windows Azure Storage-fakturering](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

- Ytterligare Storage PowerShell-skriptexempel finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).