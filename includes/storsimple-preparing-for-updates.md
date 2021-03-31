---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050209"
---
## <a name="preparing-for-updates"></a>Förbereder för uppdateringar
Du måste utföra följande steg innan du genomsöker och tillämpar uppdateringen:

1. Ta en moln ögonblicks bild av enhets data.
2. Se till att styrenhetens fasta IP-adresser är flyttbara och kan ansluta till Internet. Dessa fasta IP-adresser kommer att användas för att betjäna uppdateringar av enheten. Du kan testa detta genom att köra följande cmdlet på varje styrenhet från Windows PowerShell-gränssnittet på enheten:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Exempel på utdata för Test-Connection när fasta IP-adresser kan ansluta till Internet**

    ```output
    Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

    Source      Destination     IPV4Address      IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200

    Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

    Source      Destination       IPV4Address    IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    ```

När du har slutfört de manuella för kontrollerna kan du fortsätta med att söka efter och installera uppdateringarna.

