---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: 417fbdea3f46dfb3e90ab4890cec5e88c5aa4e07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94523852"
---
| Port nr.| In eller ut | Port omfång| Obligatorisk | Kommentarer |
|---------|-----------|-----------|----------|-------|
| TCP 80 (HTTP)|Ut|WAN |Inga|Utgående port används för Internet åtkomst för att hämta uppdateringar. <br>Den utgående webbproxyn är användare konfigurerbar. |
| TCP 443 (HTTPS)|Ut|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Den utgående webbproxyn är användare konfigurerbar.|
| UDP 123 (NTP)|Ut|WAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en Internetbaserad NTP-server.  |   
| UDP 53 (DNS)|Ut|WAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en Internet-baserad DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| TCP 5985 (WinRM)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs för att ansluta till enheten via fjärr-PowerShell över HTTP.  |
| UDP 67 (DHCP)|Ut|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en lokal DHCP-server.  |
| TCP 80 (HTTP)|Ut/in|LAN|Ja|Den här porten är den inkommande porten för lokalt användar gränssnitt på enheten för lokal hantering. <br>Åtkomst till det lokala användar gränssnittet över HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|Ut/in|LAN|Ja|Den här porten är den inkommande porten för lokalt användar gränssnitt på enheten för lokal hantering. Den här porten används också för att ansluta Azure Resource Manager till lokala API: er för att ansluta Blob Storage via REST-API: er och till säkerhetstokentjänst för att autentisera via åtkomst och uppdatera tokens.|
| TCP 445 (SMB)|I|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via SMB. |
| TCP 2049 (NFS)|I|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via NFS. |


