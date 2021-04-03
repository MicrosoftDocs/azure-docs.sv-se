---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553201"
---
Anslut till lagringskontot och verifiera anslutningen på följande sätt.

1. Välj **Anslut till Azure Storage** i Storage Explorer. Välj **Använd lagringskontonamn och nyckel** i dialogrutan **Anslut till Azure Storage**.

    ![Skärm bild som visar dialog rutan Anslut till Azure Storage där du kan använda ett lagrings konto namn och en nyckel vald.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Klistra in ditt **kontonamn** och din **kontonyckel** (nyckel 1-värdet från sidan **Anslut och kopiera** i det lokala webbanvändargränssnittet). Välj **Annan (ange nedan)** som domän för lagringsslutpunkter och ange den blobtjänstslutpunkt som visas nedan. Markera alternativet **Använd HTTP** endast om du över via *http*. Lämna alternativet omarkerat om du använder *https*. Välj **Nästa**.

    ![Skärm bild som visar dialog rutan Anslut med namn och nyckel med angivna värden.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Kontrollera informationen i dialogrutan **Anslutningssammanfattning**. Välj **Anslut**.

    ![Skärm bild som visar dialog rutan anslutnings Sammanfattning med Anslut valt.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Det konto som du har lagt till visas i den vänstra rutan i Storage Explorer med (extern, övrigt) i slutet av namnet. Visa containern genom att klicka på **Blobcontainer**.

    ![Skärm bild som visar Explorer-menyn med BLOB-behållare markerade.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
