---
title: Självstudie för att skicka Azure Data Box | Microsoft Docs
description: I den här självstudien lär du dig hur du returnerar Azure Data Box. Du lär dig bland annat hur du förbereder Data Box-enheten för leverans, hur du skickar den, hur du verifierar datauppladdningen och hur du raderar data från enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 267094ea6a7295a65b93fb7700c97e4280da341d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539119"
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
* Kopieringsjobben har slutförts och det finns inga fel på sidan **Anslut och kopiera**. **Förbered för att skicka** kan inte köras om kopieringsjobb pågår eller om det finns fel på sidan **Anslut och kopiera**.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

När datakopieringen är klar förbereder du och skickar enheten. När enheten kommer fram till Azures datacenter laddas dina data automatiskt upp till Azure.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Innan du förbereder leveransen kontrollerar du att kopieringsjobben har slutförts.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten.
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten.

De efterföljande stegen beror på vart du returnerar enheten.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrollera att datakopieringen till enheten har slutförts och att **Förbered för att skicka** kördes utan problem. Stegen skiljer sig beroende på i vilken region du skickar enheten.

::: zone-end

## <a name="us-canada-europe"></a>[USA, Kanada, Europa](#tab/in-us-canada-europe)

Utför följande steg om du returnerar enheten i USA, Kanada eller Europa.

1. Kontrollera att enheten är avstängd och att kablarna har tagits bort.
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om supporten föreslår det kan du gå till **Översikt > Ladda ned adressetikett** på Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger du en upphämtning:

    * Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    * Uppge spårningsnumret för returförsändelsen som visas på skärmen E-ink (E-bläck) eller på den utskrivna etiketten. Om du inte uppger spårningsnumret kommer UPS att ta ut en extra avgift vid upphämtningen.
    * Om du får problem när du schemalägger en upphämtning, eller om du ombeds betala ytterligare avgifter, kontaktar du Azure Data Box Operations. Skicka ett e-postmeddelande till [adbops@microsoft.com](mailto:adbops@microsoft.com).

    I stället för att schemalägga upphämtningen kan du även lämna in Data Box-enheten på närmaste inlämningsställe.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

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

6. Kontrollera att du får en e-postbekräftelse från Quantium Solutions när du har bokat en upphämtning. E-postbekräftelsen innehåller även information om Chakubarai-fraktsedeln.

Om det behövs kan du kontakta Quantium Solutions support (på japanska):

* E-postadress: Customerservice.JP@quantiumsolutions.com 
* Telefon：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Returnera enheten i originalkartongen.
2. Skriv ner spårningsnumret (visas som referensnumret på sidan **Förbered för att skicka** i det lokala webbgränssnittet för Data Box-enheten). Spårningsnumret är tillgängligt när steget **Förbered för att skicka** har utförts. Hämta frakthandlingen på den här sidan och klistra fast den på förpackningen.
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

## <a name="south-africa"></a>[Sydafrika](#tab/in-sa)

1. Placera enheten i den ursprungliga förpackningen.
2. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Skriv ned spårningsnumret (visas som referensnummer på sidan **Förbered för att skicka** i det lokala webbgränssnittet för Data Box). Spårningsnumret är tillgängligt när steget ”Förbered för att skicka” har utförts. Hämta frakthandlingen på den här sidan och klistra fast den på förpackningen.
4. Begär en returkod från Azure Data Box Operations. En returkod krävs för att skicka tillbaka paketet till datacentret. Skicka ett e-postmeddelande till [adbops@microsoft.com](mailto:adbops@microsoft.com). Skriv den här koden på fraktsedeln bredvid returadressen så att den syns tydligt.
5. Boka en upphämtning med DHL på något av följande sätt:
 
   * Boka en upphämtning online genom att gå till [DHL Express South Africa, **Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) (Schemalägg en upphämtning).
   * Skicka ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) baserat på följande mall:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Du kan också lämna in paketet hos närmaste DHL-servicepunkt.

6. Om du får problem skickar du ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) med information om problemet och anger fraktsedelsnumret på ämnesraden. Du kan också ringa +27 (0) 119 21 39 02.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Placera enheten i den ursprungliga förpackningen.
2. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Ring **Quantium Solutions** direktnummer på **(852) 2318 1213** under kontorstid (måndag till fredag mellan kl. 09.00 och 18.00).  
4. Ange att det gäller en Microsoft Azure-upphämtning och uppge referens- och spårningsnumret (ovanför streckkoden) på fraktsedeln för att boka upphämtningen.
5. Du får en muntlig bekräftelse på upphämtningsschemat. Om fraktföretaget inte kommer för upphämtning ringer du direktnumret till Quantium Solutions International för alternativa lösningar.
6. När du har bokat en upphämtning med Quantium Solutions skickar du bekräftelsen till [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) genom att använda följande mall:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Om du får problem skickar du ett e-postmeddelande till Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) med information om problemet och anger jobbnamnet på ämnesraden.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 

::: zone-end

## <a name="united-arab-emirates"></a>[Förenade Arabemiraten](#tab/in-uae)

1. Returnera enheten i originalkartongen.
2. Kontrol lera att data kopieringen till enheten är klar och att **Förbered för att skicka** steget har slutförts.
3. Notera referens numret på sidan **Förbered för att skicka** i det lokala webb gränssnittet för enheten.
4. Stäng av enheten och ta bort kablarna. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
6. Placera enheten i den ursprungliga förpackningen.
7. E- [Azure Data Box åtgärder](mailto:adbops@microsoft.com) för att hämta ett ID som ska användas för att identifiera paketet när det går tillbaka till data centret.
8. Skriv ned det här ID: t på etiketten utskrift, bredvid retur adressen så att den visas tydligt.  
9. Boka en upphämtning online genom att gå till [DHL Express Förenade Arabemiraten](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab)  >  **Schemalägg en hämtning**.
   - Ange referens numret på sidan **Förbered för att skicka** i det lokala webb gränssnittet för enheten i fältet frakt sedel nummer.
   - Bokningar godkänns från 9:00 AM – 2:00 PM sex dagar i veckan (exklusive fre och offentliga helgdagar).
   - Upphämtnings begär Anden ska placeras minst 90 minuter innan kundens stängnings tid.
10. Om du stöter på ett problem med DHL-boknings verktyget kan du kontakta DHL med någon av följande metoder:
    - Ring 04-2924545.
    - E-postmeddelande [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) med information om problemen och Lägg frakt sedels numret i raden subject:.
    - Ring DHL kund support på 600 567567.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
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

Om du använder Data Box-enhet i USA, Japan, Singapore, Korea, Indien, Sydafrika, Storbritannien, Västeuropa eller Australien, och du har valt självhanterad leverans när du skapade din beställning, följer du dessa anvisningar.

1. Skriv ner auktoriseringskoden som visas på sidan **Förbered för att skicka** i det lokala webbgränssnittet för Data Box-enheten när steget har utförts.
2. Stäng av enheten och avlägsna kablarna. Rulla ihop strömkabeln som levererades med enheten och sätt fast kabeln ordentligt på baksidan av enheten.
3. Skicka ett e-postmeddelande till Azure Data Box Operations-teamet med hjälp av mallen nedan när du är redo att returnera enheten.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

