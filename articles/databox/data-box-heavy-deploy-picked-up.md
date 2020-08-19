---
title: Självstudie för att skicka Azure Data Box Heavy | Microsoft Docs
description: I den här självstudien lär du dig hur du returnerar Azure Data Box Heavy. Du lär dig bland annat hur du förbereder Data Box Heavy-enheten för leverans, hur du skickar den, hur du verifierar datauppladdningen och hur du raderar data från enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fa4df7119763e835cf62e67512105b748c7e2265
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921018"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box Heavy och verifiera datauppladdning till Azure

::: zone-end

::: zone target = "chromeless"

## <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Skicka tillbaka Azure Data Box Heavy och verifiera datauppladdning till Azure

::: zone-end

::: zone target = "docs"

Den här självstudien beskriver hur du returnerar Azure Data Box Heavy och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Krav
> * Förbereda för att skicka
> * Skicka Data Box Heavy till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box Heavy

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-heavy-deploy-copy-data.md).
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.


## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Innan du förbereder leveransen kontrollerar du att kopieringsjobben har slutförts.

1. Gå till sidan Förbered för att skicka i det lokala webbgränssnittet och börja förbereda leveransen.
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten.

Nu kan du skicka tillbaka enheten.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Skicka tillbaka Data Box Heavy

1. Kontrollera att enheten är avstängd och att alla kablarna har avlägsnats. Rulla ihop och placera de fyra nätkablarna på ett säkert sätt i facket som du kommer åt från baksidan av enheten.
2. Enheten levereras med LTL-frakt via FedEx i USA och DHL i EU

    1. Kontakta [Data Box Operations](mailto:DataBoxOps@microsoft.com) för att informera om upphämtningen och för att få returfraktsedeln.
    2. Ring ditt lokala transportföretag för att schemalägga upphämtningen.
    3. Kontrollera att fraktsedeln visas på en framträdande plats på utsidan av kartongen.
    4. Kontrollera att de gamla fraktsedlarna från den tidigare leveransen har tagits bort från enheten.
3. När Data Box Heavy hämtas och skannas av transportföretaget uppdateras orderstatusen på portalen till **Hämtad**. Du får också ett spårnings-ID.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När kontrollen har slutförts ansluts Data Box Heavy till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Försäkra dig om att dina data har överförts till Azure innan du tar bort dem från källan. Dina data kan finnas i:

- Ditt Azure Storage-konto. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Dina resursgrupper för hanterade diskar. När du skapar hanterade diskar laddas de virtuella hårddiskarna upp som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna ansluts till de resursgrupper som anges när ordern skapas. 

    - Om kopieringen till hanterade diskar i Azure lyckades kan du gå till **Beställningsinformation** på Azure-portalen och kontrollera vilka resursgrupper som har angetts för hanterade diskar.

        ![Identifiera resursgrupper för hanterade diskar](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Gå till resursgruppen och leta upp dina hanterade diskar.

        ![Hanterad disk ansluten till resursgrupper](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Om du har kopierat en VHDX, eller en dynamisk/differentierad virtuell hårddisk, överförs VHDX/VHD till mellanlagringskontot som en sidblob, men konverteringen av de virtuella hårddiskarna till hanterade diskar misslyckas. Gå till ditt **mellanlagringskonto > Blobar** och välj sedan lämplig container – Standard SSD, Standard HDD eller Premium SSD. De virtuella hårddiskarna laddas upp som sidblobar i mellanlagringskontot.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Data Box Heavy har anslutits till nätverket på Azures datacenter startar datauppladdningen till Azure automatiskt. Data Box-tjänsten meddelar dig via Azure-portalen när datakopieringen är klar.

- Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
- Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Radera data från Data Box Heavy
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). När raderingen har slutförts kan du [ladda ned orderhistoriken](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Krav
> * Förbereda för att skicka
> * Skicka Data Box Heavy till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box Heavy

Gå vidare till följande artikeln om hur du hanterar Data Box Heavy via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


