---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "89505912"
---
| Port nr.| In eller ut | Port omfång| Obligatorisk| Kommentarer |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|I|LAN|Ja|Den här porten används för att ansluta till Data Box-enhet Blob Storage REST-API: er via HTTP. Om du inte ansluter till REST API: er omdirigeras detta automatiskt till lokalt webb gränssnitt över 8443. |
| TCP 443 (HTTPS)|I|LAN|Ja|Den här porten används för att ansluta till Data Box-enhet Blob Storage REST-API: er över HTTPS. Om du inte ansluter till REST API: er omdirigeras detta automatiskt till lokalt webb gränssnitt över 8443. |
| TCP 8443 (HTTPS-Alt)|I|LAN|Ja|Detta är en alternativ port för HTTPS och används vid anslutning till lokalt webb gränssnitt för enhets hantering. |
| TCP 445 (SMB)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via SMB. |
| TCP 2049 (NFS)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via NFS. |
| TCP 111 (NFS)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten används för rpcbind/port-mappning och krävs endast om du ansluter via NFS.  |
