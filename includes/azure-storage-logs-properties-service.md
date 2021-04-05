---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011150"
---
| Egenskap | Beskrivning |
|:--- |:---|
|**Konto** | Namnet på lagrings kontot. Exempel: `mystorageaccount`.  |
|**requestUrl** | Den URL som begärs. |
|**userAgentHeader** | Värdet för **User-Agent-huvudet** , inom citat tecken. Exempel: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Värdet för **referent** -huvudet. Exempel: `http://contoso.com/about.html`.|
|**clientRequestId** | Huvudet **x-MS-client-Request-ID** för begäran. Exempel: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | ETag-identifieraren för det returnerade objektet, inom citat tecken. Exempel: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Den totala tiden uttryckt i millisekunder för att utföra den begärda åtgärden. Det här värdet omfattar inte nätverks svars tiden (tiden för att läsa inkommande begäran och skicka svaret till beställaren). Exempel: `22`. |
|**serviceType** | Tjänsten som är kopplad till den här begäran. Till exempel: `blob` , `table` , `files` eller `queue` . |
|**operationCount** | Antalet varje loggad åtgärd som ingår i begäran. Antalet börjar med ett index av `0` . Vissa begär Anden kräver mer än en åtgärd. De flesta förfrågningar utför bara en åtgärd. Exempel: `1`. |
|**requestHeaderSize** | Storleken på begär ande huvudet uttryckt i byte. Exempel: `578`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt. |
|**requestBodySize** | Storleken på begär ande paket, uttryckt i byte, som läses av lagrings tjänsten. <br> Exempel: `0`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt.  |
|**responseHeaderSize** | Storleken på svars huvudet uttryckt i byte. Exempel: `216`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt.  |
|**responseBodySize** | Storleken på de svars paket som skrivs av lagrings tjänsten, i byte. Om en begäran Miss lyckas kan det här värdet vara tomt. Exempel: `216`.  |
|**requestMd5** | Värdet för antingen **Content-MD5** -sidhuvudet eller **x-MS-Content-MD5-** huvudet i begäran. Det MD5-hash-värde som anges i det här fältet representerar innehållet i begäran. Exempel: `788815fd0198be0d275ad329cafd1830`. <br>Det här fältet kan vara tomt.  |
|**serverMd5** | Värdet för den MD5-hash som beräknas av lagrings tjänsten. Exempel: `3228b3cf1069a5489b298446321f8521`. <br>Det här fältet kan vara tomt.  |
|**lastModifiedTime** | Senaste ändrings tid (LMT) för det returnerade objektet.  Exempel: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Det här fältet är tomt för åtgärder som kan returnera flera objekt. |
|**conditionsUsed** | En semikolonavgränsad lista med nyckel/värde-par som representerar ett villkor. Villkoren kan vara något av följande: <li> If-Modified-sedan <li> If-Modified-sedan <li> If-Match <li> If-None-Match  <br> Exempel: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Värdet för Content-Length-huvudet för begäran som skickas till lagrings tjänsten. Om begäran lyckades är värdet lika med requestBodySize. Om en begäran Miss lyckas kan det här värdet inte vara lika med requestBodySize, eller så kan det vara tomt. |
|**tlsVersion** | TLS-versionen som används i begäran om anslutning. Exempel: `TLS 1.2`. |
|**smbTreeConnectID** | SMB- **treeConnectId** (Server Message Block) upprättades vid Tree Connect-tiden. Exempelvis: `0x3` |
|**smbPersistentHandleID** | Beständigt referens-ID från en SMB2 CREATE-begäran som överleva nätverks åter anslutning.  Refereras till i [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 som **SMB2_FILEID. Beständig**. Exempelvis: `0x6003f` |
|**smbVolatileHandleID** | Beständigt referens-ID från en SMB2 CREATE-begäran som återvinns i nätverket återansluter.  Refereras till i [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 som **SMB2_FILEID. Volatile**. Exempelvis: `0xFFFFFFFF00000065` |
|**smbMessageID** | Anslutningens relativa **messageid**. Exempelvis: `0x3b165` |
|**smbCreditsConsumed** | Ingångs-eller utgångs förbrukad av begäran, i enheter om 64 kB. Exempelvis: `0x3` |
|**smbCommandDetail** | Mer information om den här begäran snarare än allmän typ av begäran. Exempelvis: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | **FileId** som är associerad med filen eller katalogen.  Ungefär detsamma som en NTFS-FileId. Exempelvis: `0x9223442405598953` |
|**smbSessionID** | SMB2 **SessionID** upprättades vid konfigurationen av sessionen. Exempelvis: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Värde i **SMB2_HEADER.-kommandot**. För närvarande är detta ett tal mellan 0 och 18. Exempelvis: `0x6` |
|**smbCommandMinor** | Underklassen för **SmbCommandMajor**, där det är lämpligt. Exempelvis: `DirectoryCloseAndDelete` |