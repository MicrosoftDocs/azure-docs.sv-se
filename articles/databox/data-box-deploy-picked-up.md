---
title: Självstudie för att skicka Azure Data Box | Microsoft Docs
description: I den här självstudien lär du dig hur du returnerar Azure Data Box. Du lär dig bland annat hur du förbereder Data Box-enheten för leverans, hur du skickar den, hur du verifierar datauppladdningen och hur du raderar data från enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 577c184f78c5784b29db7562f3c33a3cb7518f0c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142051"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Skicka tillbaka Data Box och verifiera datauppladdningen till Azure

::: zone-end

::: zone target="docs"

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
>
> * Krav
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

* Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md).
* Kopieringsjobben har slutförts och det finns inga fel på sidan **Anslut och kopiera**. Förbered för att skicka kan inte köras om kopieringsjobb pågår eller om det finns fel på sidan **Anslut och kopiera**.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

När datakopieringen är klar förbereder du och skickar enheten. När enheten kommer fram till Azures datacenter laddas dina data automatiskt upp till Azure.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Innan du förbereder leveransen kontrollerar du att kopieringsjobben har slutförts.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten. 
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten. 

Nästa steg beror på var du returnerar enheten.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades. Stegen skiljer sig beroende på i vilken region du skickar enheten.

::: zone-end

## <a name="us-canada-europe"></a>[USA, Kanada, Europa](#tab/in-us-canada-europe)

Utför följande steg om du returnerar enheten i USA, Kanada eller Europa.

1. Kontrollera att enheten är avstängd och att kablarna har tagits bort. 
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om supporten föreslår det kan du gå till **Översikt > Ladda ned adressetikett** på Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger du en upphämtning:

    * Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    * Uppge spårningsnumret för returförsändelsen som visas på skärmen E-ink (E-bläck) eller på den utskrivna etiketten.
    * Om du inte uppger spårningsnumret kräver UPS att du betalar en extra avgift vid upphämtningen.

    I stället för att schemalägga upphämtningen kan du även lämna in Data Box-enheten på närmaste inlämningsställe.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="australia"></a>[Australien](#tab/in-australia)

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid leveranser i Australien.

1. Returnera enheten i originalkartongen.
2. Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades.
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
5. Boka en upphämtning online på [DHL-länken](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="japan"></a>[Japan](#tab/in-japan)

1. Returnera enheten i originalkartongen.
2. Stäng av enheten och avlägsna kablarna.
3. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
4. Skriv företagets namn och adressuppgifter på fraktsedeln som din avsändarinformation.
5. Skicka ett e-postmeddelande till Quantium Solutions baserat på följande e-postmall.

    * Om Chakubarai-fraktsedeln från Japan Post inte inkluderades eller saknas, nämner du det i e-postmeddelandet. Quantium Solutions Japan ber Japan Post att ta med fraktsedeln vid upphämtningen.
    * Om du har flera beställningar som ska hämtas upp separat meddelar du detta via e-post.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body:
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Se till att du får en e-postbekräftelse från Quantium Solutions när du har bokat en upphämtning. E-postbekräftelsen innehåller även information om Chakubarai-fraktsedeln.

Vid behov kan du kontakta Quantium Solutions support (på japanska) med följande information: 

* E-postadress: Customerservice.JP@quantiumsolutions.com 
* Telefon：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Returnera enheten i originalkartongen.
2. Skriv ned spårningsnumret (visas som referensnummer på sidan Förbereda för att skicka i det lokala webbgränssnittet för Data Box). Detta är tillgängligt när steget Förbered för att skicka har slutförts. Hämta frakthandlingen på den här sidan och klistra fast den på förpackningen.
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten. 
5. Skicka ett e-postmeddelande till SingPosts kundtjänst med hjälp av följande e-postmall med spårningsnumret.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > För bokningsbegäranden som tas emot under en arbetsdag:
   > * Före 15:00 sker upphämtningen nästa arbetsdag mellan 09:00 och 13:00.
   > * Efter 15:00 sker upphämtningen nästa arbetsdag mellan 14:00 och 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="south-africa"></a>[Sydafrika](#tab/in-sa)

1. Placera enheten i den ursprungliga förpackningen.
2. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Så här bokar du en upphämtning med DHL:

    * Kontakta kundtjänsten på +27 (0) 119 21 36 00. Välj alternativ 1 och uppge numret på fraktsedeln.
    * Du måste ringa före 14:00 för att schemalägga en upphämtning.
    * Skicka ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) baserat på följande mall:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Du kan också lämna in paketet på närmaste DHL-servicepunkt.

4. Om du stöter på problem skickar du ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com). Beskriv problemet och ange numret på fraktsedeln på ämnesraden. Du kan också ringa +27 (0) 119 21 39 02.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Placera enheten i den ursprungliga förpackningen.
2. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Ring **Quantium Solutions** direktnummer på **(852) 23 18 12 13** under kontorstid (måndag till fredag mellan kl. 09.00 och 18.00).  
4. Ange att det gäller en Microsoft Azure-upphämtning och uppge referens- och spårningsnumret (ovanför streckkoden) på fraktsedeln för att boka upphämtningen.
5. Du får en muntlig bekräftelse för upphämtningsschemat. Om fraktföretaget inte anländer för upphämtning ringer du direktnumret till Quantium Solutions International för alternativa lösningar.
6. När du har bokat en upphämtning med Quantium skickar du bekräftelsen till [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) genom att använda följande mall:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Om du stöter på problem kontaktar du Data Box Operations Asia via e-post på [adbo@microsoft.com](mailto:adbo@microsoft.com). Beskriv problemet och ange jobbets namn på ämnesraden.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Egenhanterad](#tab/in-selfmanaged)

Om du använder Data Box i Japan, Singapore, Korea, Indien, Sydafrika eller Västeuropa och valde alternativet för egenhanterad frakt när ordern skapades, följer du dessa anvisningar. 

1. Skriv ned auktoriseringskoden på sidan Förbereda för att leverera i det lokala webbgränssnittet för Data Box efter att det här steget har slutförts.
2. Stäng av enheten och avlägsna kablarna. Rulla ihop strömkabeln som levererades med enheten och sätt fast kabeln ordentligt på baksidan av enheten.
3. Skicka ett e-postmeddelande till Azure Data Box-driftsteamet med hjälp av mallen nedan när du är redo att returnera enheten.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

---

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

