---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185551"
---
1. Gå till det lokala webb gränssnittet på enheten och logga in på enheten. Kontrol lera att enheten är upplåst.

2. Gå till sidan **nätverks inställningar** . Anteckna enhetens IP-adress för nätverksgränssnittet som används för att ansluta till klienten.

3. Om du arbetar med en fjärran sluten Windows-klient startar du **anteckningar** som administratör och öppnar sedan hosts-filen som finns på `C:\Windows\System32\Drivers\etc` .

4. Lägg till följande post för värdens fil: `<Device IP address> <Blob service endpoint>`

    Du fick BLOB service-slutpunkten från Edge Storage-kontot som skapades i Azure Portal. Du kommer bara att använda suffixet för BLOB-tjänstens slut punkt.

    Använd följande bild som referens. Spara filen `hosts`.

    ![Ändra hosts-filen på Windows-klienten](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)