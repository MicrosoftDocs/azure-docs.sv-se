---
title: Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs
description: Identifiera och Felsök fel för att ta bort filer från Azure-filresursen.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629690"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs

Det kan finnas flera symptom på att det inte går att ta bort filer från Azure-filresurs:

**Symptom 1:**

Det gick inte att ta bort en fil i Azure-filresursen på grund av ett av de två problemen nedan:

* Filen som marker ATS för borttagning
* Den angivna resursen kanske används av en SMB-klient

**Symptom 2:**

Det finns inte tillräckligt med kvot utrymme för att bearbeta det här kommandot

## <a name="cause"></a>Orsak

Fel 1816 uppstår när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil, på den dator där fil resursen monteras. Mer information finns i [Check lista för Azure Storage prestanda och skalbarhet](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser.

## <a name="prerequisite"></a>Förutsättning

### <a name="install-the-latest-azure-powershell-module"></a>Installera den senaste Azure PowerShell modulen

* [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Anslut till Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Välj prenumerationen för mål lagrings kontot:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Skapa en kontext för mål lagrings kontot:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Hämta de aktuella öppna handtagen för fil resursen:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Exempel på resultat:

|HandleId|Sökväg|ClientIp|ClientPort|Opentime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Ny mapp/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Stäng en öppen referens:

Om du vill stänga en öppen referens använder du följande kommando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)