---
title: Självstudie för att skicka Azure Data Box tillbaka i export ordning | Microsoft Docs
description: Lär dig hur du levererar Azure Data Box till Microsoft när export ordningen är klar
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 4b5bbe19ddeda7c6b313186b7e24532a294f7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680836"
---
# <a name="tutorial-return-azure-data-box"></a>Självstudie: returnera Azure Data Box

I den här självstudien beskrivs hur du returnerar Azure Data Box och data raderas när enheten tas emot på Azure-data.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
>
> * Krav
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Radera data från Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

* Du har slutfört [självstudien: kopiera data från Azure Data Box](data-box-deploy-export-copy-data.md).
* Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Nästa steg beror på var du returnerar enheten.

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrol lera att data kopieringen från enheten är klar och att **Förbered för att skicka** körningen har slutförts. Proceduren skiljer sig från den region där du levererar enheten.

## <a name="us-canada-europe"></a>[USA, Kanada, Europa](#tab/in-us-canada-europe)

Utför följande steg om du returnerar enheten i USA, Kanada eller Europa.

1. Kontrollera att enheten är avstängd och att kablarna har tagits bort. 
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Se till att leverans etiketten visas på E-Penn visningen och Schemalägg en upphämtning med operatören. Om etiketten är skadad eller förlorad eller inte visas i visningen av E-pennan kontaktar du Microsoft Support. Om supporten föreslår det kan du gå till **Översikt > Ladda ned adressetikett** i Azure-portalen. Hämta skeppnings etiketten och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger du en upphämtning:

   - Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
   - Uppge spårningsnumret för returförsändelsen som visas på skärmen E-ink (E-bläck) eller på den utskrivna etiketten. Om du inte citerar spårnings numret krävs en extra kostnad vid hämtningen.
   - Om några problem uppstår när du schemalägger en upphämtning, eller om du ombeds att betala ytterligare avgifter, kan du kontakta Azure Data Box åtgärder. Skicka ett e-postmeddelande till [adbops@microsoft.com](mailto:adbops@microsoft.com).

    I stället för att schemalägga upphämtningen kan du även lämna in Data Box-enheten på närmaste inlämningsställe.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

## <a name="australia"></a>[Australien](#tab/in-australia)

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid leveranser i Australien.

1. Behåll den ursprungliga rutan som används för att leverera enheten för retur leverans.
2. Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades.
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
5. Boka en upphämtning online på [DHL-länken](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="japan"></a>[Japan](#tab/in-japan)

1. Behåll den ursprungliga rutan som används för att leverera enheten för retur leverans.
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

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Behåll den ursprungliga rutan som används för att leverera enheten för retur leverans.
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
   >
   > * Före 15:00 sker upphämtningen nästa arbetsdag mellan 09:00 och 13:00.
   > * Efter 15:00 sker upphämtningen nästa arbetsdag mellan 14:00 och 18:00.  

## <a name="south-africa"></a>[Sydafrika](#tab/in-sa)

1. Behåll den ursprungliga rutan som används för att paketera enheten för retur leverans.
2. Anteckna referens numret (frakt sedels numret) som visas i enhetens lokala webb gränssnitt. Antalet visas när den **Förbered för att skicka körningen** har slutförts.
3. Hämta och skriv ut etikett för leverans som finns på enhetens lokala webb gränssnitt och anbringa den i försändelse paketet.
4. Om du vill boka en upphämtning med DHL väljer du något av följande alternativ:

    * Kontakta kund tjänst kontakt centret innan 2:00 på **+ 27 (0) 11 9213600**, Välj alternativ 1 och ange frakt sedels numret.
    * Skicka ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) baserat på följande mall:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * I stället kan du ta bort paketet på den närmaste DHL-platsen.

5. Om du stöter på problem, e-post [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) med information om de problem som du påträffat och anger frakt sedels numret i raden Ämne:. Du kan också ringa +27 (0) 119 21 39 02.

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Placera enheten i den ursprungliga förpackningen.
2. Anteckna referens numret (spårnings numret för omvänd försändelse) som visas i enhetens lokala webb gränssnitt. Antalet visas när den **Förbered för att skicka körningen** har slutförts.
3. Hämta och skriv ut etikett för leverans som finns på enhetens lokala webb gränssnitt och anbringa den i försändelse paketet.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
5. Ring **Quantium Solutions** direktnummer på **(852) 23 18 12 13** under kontorstid (måndag till fredag mellan kl. 09.00 och 18.00).  
6. Ange att det gäller en Microsoft Azure-upphämtning och uppge referens- och spårningsnumret (ovanför streckkoden) på fraktsedeln för att boka upphämtningen.
7. Du får en muntlig bekräftelse för upphämtningsschemat. Om courier inte tar emot insamlingen kan du ringa Quantium-lösningar, Hotline för alternativa arrangemang.
8. När du har bokat en upphämtning med Quantium skickar du bekräftelsen till [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) genom att använda följande mall:

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

Om du kommer över alla problem, e-Data Box-enhet åtgärder för Asien som [adbo@microsoft.com](mailto:adbo@microsoft.com) tillhandahåller jobb namnet i ämnes rubriken och problemet påträffades.

## <a name="self-managed"></a>[Egenhanterad](#tab/in-selfmanaged)

Om du använder Data Box i Japan, Singapore, Korea, Indien, Sydafrika eller Västeuropa och valde alternativet för egenhanterad frakt när ordern skapades, följer du dessa anvisningar.

1. Skriv ned auktoriseringskoden på sidan Förbereda för att leverera i det lokala webbgränssnittet för Data Box efter att det här steget har slutförts.
2. Stäng av enheten och avlägsna kablarna. Rulla ihop strömkabeln som levererades med enheten och sätt fast kabeln ordentligt på baksidan av enheten.
3. Skicka ett e-postmeddelande till Azure Data Box Operations-teamet med hjälp av nedanstående mall när du är redo att returnera enheten.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box

När enheten når Azure-datacenter raderar Data Box-enhet data på sina diskar enligt [rikt linjerna i NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Radera data från Data Box

Fortsätt till nästa artikel och lär dig hur du hanterar din Data Box-enhet.

> [!div class="nextstepaction"]
> [Hantera Data Box-enhet via Azure Portal](./data-box-portal-admin.md)


