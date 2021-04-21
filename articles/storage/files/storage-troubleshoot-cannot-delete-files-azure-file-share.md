---
title: Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs
description: Identifiera och felsöka felet med att ta bort filer från Azure-filresursen.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: d8cc0cb7df4bb7bfff5a6b9d2f159cb674532927
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789761"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs

Det kan ha flera symptom att det inte går att ta bort filer från Azure-filresursen:

**Symptom 1:**

Det gick inte att ta bort en fil i Azure-filresursen på grund av något av följande två problem:

* Filen som markerats för borttagning
* Den angivna resursen kan användas av en SMB-klient

**Symptom 2:**

Det finns inte tillräckligt med kvot för att bearbeta det här kommandot

## <a name="cause"></a>Orsak

Fel 1816 inträffar när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på den dator där filresursen monteras. Mer information finns i checklistan [Azure Storage för prestanda och skalbarhet.](../blobs/storage-performance-checklist.md)

## <a name="resolution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser.

## <a name="prerequisite"></a>Förutsättning

### <a name="install-the-latest-azure-powershell-module"></a>Installera den senaste Azure PowerShell modulen

* [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Anslut till Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Välj prenumerationen för mållagringskontot:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Skapa kontext för mållagringskontot:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Hämta de aktuella öppna handtagen för filresursen:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Exempelresultat:

|HandleId|Sökväg|ClientIp|ClientPort|OpenTime|LastReconnectTime|Fileid|ParentId|Sessionid|
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
* [Felsöka Azure File Sync](../file-sync/file-sync-troubleshoot.md)