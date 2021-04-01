---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553180"
---
1. Logga in på Data Box-enheten. Se till att den är upplåst.

    ![Skärm bilden visar din instrument panel med den enhet som visas som olåst.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Gå till **Ställ in nätverksgränssnitt**. Anteckna enhetens IP-adress för nätverksgränssnittet som används för att ansluta till klienten.

    ![Skärm bild som visar nätverks inställningarna där du kan se i P-adressen.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Gå till **Anslut och kopiera** och klicka på **rest**.

    ![Skärm bild som visar fönstret Anslut och kopiera där du kan välja REST som en åtkomst inställning.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Från dialogrutan **Öppna lagringskonto och ladda upp data** kopierar du **Blob Service-slutpunkt**.

    ![Skärm bild som visar dialog rutan åtkomst till lagrings konto och ladda upp data där du kan kopiera BLOB-tjänstens slut punkt.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starta **Anteckningar** som administratör och öppna sedan **värdens** fil på `C:\Windows\System32\Drivers\etc`.
6. Lägg till följande post för **värdens** fil: `<device IP address> <Blob service endpoint>`
7. Använd följande bild som referens. Spara **värdens** fil.

    ![Skärm bild som visar ett Anteckningar-dokument med den I P-adressen och blob-tjänstens slut punkt tillagd.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
