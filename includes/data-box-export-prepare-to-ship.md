---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 97491000c63c966c3d8159b9361fcb60e062aa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86210453"
---
Det sista steget är att förbereda enheten för att skickas. I det här steget frånkopplas alla enhetsresurser. Det går inte att komma åt resurserna när du har startat den här processen. Du kan också välja att rensa enheten genom att ta bort lokala data på enheten permanent. När steget har slutförts visas etiketten returnera leverans i din e-Penn visning.

> [!IMPORTANT]
> - Du kan välja att radera data på enheten permanent i det här steget och rensa enheten. Data i ditt Azure Storages konto kommer att stanna kvar och debiteras. Vi rekommenderar att du bara tar bort dessa data när du har verifierat att data kopiering till en lokal data server har slutförts.

1. Gå till **Förbered för att skicka** och välj **Starta förberedelse**. 
   
    ![Förbered för att skicka 1](media/data-box-export-prepare-to-ship/prepare-to-ship1.png)

 
2. Förbered för att skicka startas och enhetsresurserna kopplas från. Som standard utförs rensningen av enheten och Data Box-enhet raderar data på diskarna. 


    ![Förbered för att skicka 2](media/data-box-export-prepare-to-ship/prepare-to-ship2.png)

    Du kan välja mellan rensnings proceduren genom att avmarkera kombinations rutan. I det här fallet raderas enhets data senare i data centret.

    ![Förbered för att skicka 3](media/data-box-export-prepare-to-ship/prepare-to-ship3.png)


3. När **Förbered för att skicka** startar visas ett förlopps meddelande med en förlopps indikator.

    ![Förbered för att skicka 4](media/data-box-export-prepare-to-ship/prepare-to-ship4.png)

4. När du har valt att leverera och enhets rensningen (om det är markerat) är klar visas även E-Penn visningen på enheten. 

    Du kan också se ett slut för ande referens nummer som används för olika saker beroende på vilket land du befinner dig i. Du kan också visa instruktioner för retur leverans. Beroende på vilken typ av leverans du har valt, hanteras Microsoft eller styrs av själv. 
        
    ![Förbered för att skicka 5](media/data-box-export-prepare-to-ship/prepare-to-ship5.png)


5. Stäng av enheten. Leta reda på strömknappen på enhetens frontpanel. Stäng av enheten.

    ![Strömknappen på Data Box](media/data-box-export-prepare-to-ship/data-box-powered-door-open.png)

6. Ta bort kablarna. Nästa steg är att skicka enheten till Microsoft.
