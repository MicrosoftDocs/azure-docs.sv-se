---
title: Självstudie för att skicka Azure Data Box Disk| Microsoft Docs
description: I den här självstudien lär du dig hur du returnerar Azure Data Box Disk. Instruktionerna för upphämtning beror på var du returnerar enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 542eed34f2be05cce3829baab2f07a5c58413ba0
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526304"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Självstudier: Returnera Azure Data Box Disk

I den här självstudien beskrivs hur du returnerar din Azure Data Box Disk. Instruktionerna för upphämtning beror på var du returnerar enheten.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
>
> * Skicka Data Box-disk till Microsoft

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrol lera att du har slutfört [självstudien: kopiera data till Azure Data Box disk och verifiera](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Returnera Azure Data Box Disk

::: zone-end

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du förpackar diskarna i ordentligt fäst cellplast.
    - Packa tätt så att den förpackade enheten inte rör sig i kartongen.

Nästa steg beror på var du returnerar enheten. Instruktionerna skiljer sig för USA/Kanada, EU, Australien eller länder/regioner i Asien.

### <a name="us-or-canada"></a>[USA eller Kanada](#tab/in-us-or-canada)

Utför följande steg om du returnerar enheten i USA eller Kanada.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och ladda ned en returetikett.
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Schemalägg en upphämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    - Uppge spårningsnumret för returförsändelsen som visas på den utskrivna etiketten.
    - Om spårnings numret inte är citerat, kräver UPS att du betalar ytterligare en kostnad under upphämtningen.
    - I stället för att schemalägga upphämtningen kan du även lämna in Data Box Disk-enheten på närmaste inlämningsställe.


### <a name="europe-or-uk"></a>[Europa eller Storbritannien](#tab/in-europe-or-uk)

Utför följande steg om du returnerar enheten i Europa eller Storbritannien.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och ladda ned en returetikett.
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Gå till DHL Express-webbplatsen för aktuellt land/region och välj **Schemalägg en upphämtning**. Under **Behöver du en frakthandling** väljer du **Nej** > **Jag har ett DHL-fraktsedelsnummer**.
4. Ange fraktsedelsnumret och ordna hämtningen genom att klicka på **Boka upphämtning**.

### <a name="australia"></a>[Australien](#tab/in-australia)

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid upphämtning i Australien.

1. Använd den tillhandahållna returetiketten och kontrollera att TAU-koden (referensnummer) är skriven på den. Om den tillhandahållna adressetiketten saknas eller om du har något annat problem skickar du ett e-postmeddelande till [Data Box Asia Operations](mailto:adbo@microsoft.com). Ange beställnings namnet i ämnes rubriken och information om problemet.
2. Fäst etiketten på kartongen.
3. Boka en upphämtning online på länken https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference.

### <a name="japan"></a>[Japan](#tab/in-japan)

1. Skriv företagets namn och adressuppgifter på fraktsedeln som din avsändarinformation.
2. Skicka ett e-postmeddelande till Quantium Solutions baserat på följande e-postmall.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Om du hämtar upp i Osaka** ändrar du ämnet i e-postmallen till: `Pickup request for Microsoft Azure OSA`.
    - Om Chakubarai-fraktsedeln från Japan Post inte fanns med eller saknas nämner du det i e-postmeddelandet. Quantium Solutions Japan ber Japan Post att ta med fraktsedeln vid upphämtningen.
    - Om du har flera beställningar som ska hämtas upp separat meddelar du detta via e-post.

3. Se till att du får en e-postbekräftelse från Quantium Solutions när du har bokat en upphämtning. E-postbekräftelsen innehåller även information om Chakubarai-fraktsedeln.

Vid behov kan du kontakta Quantium Solutions support (på japanska) med följande information: 

- E-post：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefon：03-5755-0150 

### <a name="korea"></a>[Korea](#tab/in-korea)

1. Se till att inkludera returfraktsedeln.
2. Så här begär du upphämtning när returfraktsedeln finns:
    1. Ring journumret för *Quantium Solutions International* på 070-8231-1418 under kontorstider (10–17, måndag till fredag). Ange *Microsoft Azure pickup* och numret för tjänstbegäran för att anordna en upphämtning.  
    2. Om Hotline är upptagen, e-post [microsoft@rocketparcel.com](mailto:microsoft@rocketparcel.com) med e-postmeddelandets ämnesrad *Microsoft Azure upphämtningen* och service förfrågnings numret för referens.
    3. Om courier inte tar emot insamling kan du anropa *Quantium Solutions International* Hotline för alternativa arrangemang.
    4. Du får en bekräftelse via e-post för upphämtningsschemat.
3. Utför bara det här steget om frakt sedeln inte finns. Så här begär du upphämtning:
    1. Ring journumret för *Quantium Solutions International* på 070-8231-1418 under kontorstider (10–17, måndag till fredag). Ange *Microsoft Azure pickup* och numret för tjänstbegäran för att anordna en upphämtning. Ange att du behöver en ny fraktsedel för att anordna en upphämtning. Ange avsändare (kund), information om mottagare (Azure-datacenter) och referensnummer (nummer för tjänstbegäran).
    2. Om Hotline är upptagen, e-post [microsoft@rocketparcel.com](mailto:microsoft@rocketparcel.com) med e-postmeddelandets ämnesrad *Microsoft Azure upphämtningen* och service förfrågnings numret som referens.
    3. Om courier inte tar emot insamling kan du anropa *Quantium Solutions International* Hotline för alternativa arrangemang.
    4. Du får en muntlig bekräftelse om begäran görs via telefon.

### <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Skriv ut adressetiketten och fäst den på kartongen. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och hämta en returetikett.
    - Fäst etiketten på enheten. Se till att etiketten syns tydligt.

2. För att begära upphämtning skickar du e-post till SingPost-kundtjänsten med följande mall och spårningsnumret (spårningsnumret kan hittas på returetiketten som tillhandahålls i det levererade paketet).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > För bokningsbegäranden som tas emot under en arbetsdag:
   >
   > * Före 15:00 sker upphämtningen nästa arbetsdag mellan 09:00 och 13:00.
   > * Efter 15:00 sker upphämtningen nästa arbetsdag mellan 14:00 och 18:00.

   Om du kommer över eventuella problem kontaktar du Data Box-enhet åtgärder Asien på [adbo@microsoft.com](mailto:adbo@microsoft.com) . I ämnesrubriken anger du jobbnamnet och det problem som påträffats.

3. Lämna över till fraktföretaget.

### <a name="south-africa"></a>[Sydafrika](#tab/in-sa)

Gör så här om du returnerar enheten i Sydafrika.

1. Fäst den medföljande leveransetiketten på kartongen. Den här handlingen innehåller spårningsnumret. Om frakthandlingen saknas kan du ladda ned en ny från **Översikt > Ladda ned frakthandling**.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.

3. Begär en returkod från Azure Data Box Operations. En returkod krävs för att skicka tillbaka paketet till datacentret. Skicka ett e-postmeddelande till [adbops@microsoft.com](mailto:adbops@microsoft.com). Skriv ned den här koden på frakthandlingen bredvid returadressen så att den syns tydligt.

4. Boka en upphämtning med DHL på något av följande sätt:
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

5. Om du får problem skickar du ett e-postmeddelande till [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) med information om problemet och anger fraktsedelsnumret på ämnesraden. Du kan också ringa +27 (0) 119 21 39 02.

### <a name="china"></a>[Kina](#tab/in-china)

Gör så här om du returnerar enheten i Kina.

1. Fäst den medföljande frakthandlingen och klistra fast den på förpackningen. Den här handlingen innehåller spårningsnumret. Om frakthandlingen saknas kan du ladda ned en ny från **Översikt > Ladda ned frakthandling**.

2. Skicka e-post till premiumkundtjänsten på FedEx och ange spårningsnumret (leveransens referensnummer) för att organisera upphämtningen med hjälp av följande e-postmall:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Du får en e-postbekräftelse från FedEx när upphämtningen har bokats.  

4. Om du stöter på några problem, e-post [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) med information om problemen och Lägg beställnings namnet i raden Ämne:.

#### <a name="premier-customer-care-contact-information"></a>Kontaktuppgifter till premiumkundsupporten

<ins>Primär kontakt</ins>

| Kontaktinformation | Information |
|---|---|
|Namn:       | Bao Ying|
|Beteckning | Senior ansvarig för OneCall |
|Telefon:      | 400-889-6066 ankn. 3693 |
|E-post:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Säkerhetskopiering</ins>

| Kontaktinformation | Information |
|---|---|
|Namn:       | He Xun|
|Beteckning | Ansvarig för OneCall |
|Telefon:      | 400-889-6066 ankn. 3603 |
|E-post:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[Egenhanterad](#tab/in-selfmanaged)

Om du använder Data Box Disk i amerikanska myndigheter, Japan, Singapore, Korea, Storbritannien, Västeuropa, Australien, Sydafrika eller Indien och har valt alternativet för självhanterad leverans under skapande av order, följer du dessa anvisningar.

1. Gå till bladet **Översikt** för din beställning i Azure Portal. Gå igenom de instruktioner som visas när du väljer **Schemalägg hämtning**. Du bör se en auktoriseringskod som används vid tidpunkten för avlämning av ordern.

2. Skicka ett e-postmeddelande till Azure Data Box Operations-teamet med hjälp av följande mall när du är redo att returnera enheten.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop-off.
    ```
3. Azure Data Box Operations-teamet kommer att arbeta med dig för att ordna List rutan till Azure-datacentret.

---

::: zone target="docs"

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
>
> * Skicka Data Box-disk till Microsoft

Gå vidare till nästa anvisningar för att lära dig hur du verifierar data uppladdning från Data Box Disk till Azure Storage-kontot.

> [!div class="nextstepaction"]
> [Verifiera datauppladdning från Azure Data Box Disk](./data-box-disk-deploy-upload-verify.md)

::: zone-end
