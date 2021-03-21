---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185574"
---
Om det här är första gången du använder Storage Explorer måste du göra följande.

1. Från det övre kommando fältet går du till **redigera > mål Azure Stack API: er**.

    ![Konfigurera Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Starta om Storage Explorer så att ändringarna träder i kraft.


Anslut till lagringskontot och verifiera anslutningen på följande sätt.

1. I Storage Explorer väljer du lagrings konton. Högerklicka och välj alternativet **Anslut till Azure Storage** . 

    ![Konfigurera Storage Explorer 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. Välj **Använd lagringskontonamn och nyckel** i dialogrutan **Anslut till Azure Storage**.

    ![Konfigurera Storage Explorer 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. Utför följande steg i dialog rutan **Anslut med namn och nyckel** :

    1. Ange ett visnings namn för ditt gräns lagrings konto. 
    2. Ange konto namnet för Edge-lagring.
    3. Klistra in åtkomst nyckeln som du fick från enhetens lokala API: er via Azure Resource Manager.
    4. Välj lagrings domän som **övrigt (ange nedan)** och ange sedan suffixet för BLOB service-slutpunkten i formatet: `<appliance name>.<DNSdomain>` . 
    5. Markera **Använd HTTP-** alternativ som överföring är över *http*. 
    6. Välj **Nästa**.

    ![Konfigurera Storage Explorer 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Kontrollera informationen i dialogrutan **Anslutningssammanfattning**. Välj **Anslut**.

    ![Konfigurera Storage Explorer 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Det konto som du har lagt till visas i den vänstra rutan i Storage Explorer med (extern, övrigt) i slutet av namnet. Välj **BLOB-behållare** för att Visa behållaren.

    ![Visa BLOB-behållare](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Nästa steg för att verifiera är att data överföringen faktiskt fungerar korrekt över den här anslutningen.

Utför följande steg för att läsa in data i ditt lagrings konto för lagring på enheten. den bör automatiskt tilldelas till det mappade Azure Storage-kontot.

1. Välj den behållare som du vill läsa in data i ditt lagrings konto för lagring. Välj **Ladda upp** och välj sedan **Ladda upp filer**.

    ![Verifiera data överföring](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. I dialog rutan **Ladda upp filer** navigerar du till och väljer de filer som du vill ladda upp. Välj **Nästa**.

    ![Verifiera data överföring 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Kontrol lera att filerna har laddats upp. De överförda filerna visas i behållaren.

    ![Verifiera data överföring 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Därefter ansluter du till det Azure Storage konto som har mappats till det här lagrings kontot. Alla data som överförs till gräns lagrings kontot bör automatiskt till Azure Storage kontot. 
    
    Om du vill hämta anslutnings strängen för Azure Storage kontot går du till **Azure Storage konto > åtkomst nycklar** och kopierar anslutnings strängen.

    ![Verifiera data överföring 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Använd anslutnings strängen för att ansluta till Azure Storage-kontot.  

    ![Verifiera data överföring 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Kontrollera informationen i dialogrutan **Anslutningssammanfattning**. Välj **Anslut**.

    ![Verifiera data överföring 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Du ser att filerna som du överförde i gräns lagrings kontot överfördes till Azure Storage kontot.

    ![Verifiera data överföring 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
