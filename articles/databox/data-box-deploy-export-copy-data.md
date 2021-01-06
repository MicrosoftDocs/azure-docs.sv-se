---
title: Självstudie för att kopiera data via SMB från din Azure Data Box | Microsoft Docs
description: Lär dig hur du kopierar data till Azure Data Box via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 495b4427fb7e456c60a489b9ce3d19c2a44bd918
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680886"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb"></a>Självstudier: Kopiera data från Azure Data Box via SMB

Den här självstudien beskriver hur du ansluter till och kopierar data från din Data Box-enhet till en lokal server med hjälp av det lokala webbgränssnittet. Data Box-enheten innehåller de data som exporterades från ditt Azure Storage-konto.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har beställt Azure Data Box.
    - Information om importbeställningar finns i [Självstudie: Beställ Azure Data Box](data-box-deploy-ordered.md).
    - Information om exportbeställningar finns i [Självstudie: Beställ Azure Data Box](data-box-deploy-export-ordered.md).
2. Du har fått din Data Box-enhet och orderstatusen i portalen är **Levererad**.
3. Du har en värddator som du vill kopiera data från Data Box-enhet till. Värddatorn måste
   * Köra ett [operativsystem som stöds](data-box-system-requirements.md).
   * Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas.

## <a name="connect-to-data-box"></a>Ansluta till Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Välj **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto. 

    ![Hämta resursautentiseringsuppgifter](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Välj **OK**.
    
    ![Hämta autentiseringsuppgifter för en resurs, få åtkomst till resursen och kopiera data](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. För att komma åt resurser som är associerade med ditt lagringskonto (*exportbvtdataset2* i följande exempel) från värddatorn öppnar du ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Beroende på ditt dataformat är resursssökvägarna följande:
    - Azure-blockblob – `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure-sidblob – `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure Files – `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Öppna Utforskaren genom att välja **OK**.
    
    ![Ansluta till en resurs via Utforskaren genom att ange enhetens IP](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Du bör nu se resurserna som mappar.
    
    ![Ansluta till en resurs via Utforskaren för att se resurser](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Om du använder en Linux-klient använder du följande kommando för att montera SMB-resursen. Parametern ”vers” nedan är den version av SMB din Linux-värd stödjer. Inför lämplig version i kommandot nedan. Versioner av SMB som Data Box har stöd för finns i avsnittet om [filsystem som stöds för Linux-klienter](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Kopiera data från Data Box-enhet

När du är ansluten till Data Box-resurser är nästa steg att kopiera data.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 När du har anslutit till SMB-resursen kan du påbörja en datakopiering. Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. 


Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

När kopieringen är klar går du till **Instrumentpanel** och kontrollerar använt och ledigt utrymme på enheten.

Nu kan du gå vidare och leverera Data Box-enheten till Microsoft.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-export-picked-up.md)