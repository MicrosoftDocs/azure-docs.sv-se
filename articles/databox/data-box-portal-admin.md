---
title: Hantera Azure Data Box Azure Data Box Heavy via Azure Portal | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att administrera Azure Data Box och Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678642"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Använd Azure Portal för att administrera Azure Data Box och Azure Data Box Heavy

Den här artikeln gäller både Azure Data Box och Azure Data Box Heavy. I den här artikeln beskrivs några komplexa arbets flöden och hanterings uppgifter som kan utföras på den Azure Data Box enheten. Du kan hantera Data Box-enhet-enheten via Azure Portal eller via det lokala webb gränssnittet.

Den här artikeln fokuserar på de uppgifter som du kan utföra på Azure Portal. Använd Azure Portal för att hantera order, hantera Data Box-enhets enhet och spåra status för beställningen när den fortsätter att slutföras.

## <a name="cancel-an-order"></a>Annullera en beställning

Du kan behöva avbryta en order av olika orsaker efter att du har placerat den.

För både import-och export order kan du bara avbryta beställningen innan den bearbetas. När din beställning har bearbetats och den Data Box-enhet enheten är för beredd kan du inte annullera ordningen.

Följ stegen nedan om du vill annullera en beställning.

1.  Gå till **Översikt > Avbryt**.

    ![Kommandot Avbryt på fliken Översikt för en order](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Fyll i en orsak som beskriver varför du vill annullera beställningen.  

    ![Dialog rutan Avbryt beställning](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  När beställningen har annullerats uppdateras statusen för ordern på portalen och visas som **Avbruten**.

## <a name="clone-an-order"></a>Klona en order

Kloning är användbart i vissa situationer. Du har till exempel använt Data Box-enhet för att överföra vissa data. När mer data genereras måste du använda en annan Data Box-enhet enhet för att överföra dessa data till Azure. I det här fallet kan du bara klona samma ordning.

Utför följande steg för att klona en import ordning.

1.  Gå till **Översikt > Klona**. 

    ![Klona kommando på fliken Översikt för en order](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  All information för ordern förblir densamma. Namnet på beställningen är det ursprungliga ordernamnet, med tillägget *-Klon*. Markera kryssrutan för att bekräfta att du har läst sekretessinformationen. Klicka på **Skapa**.

Klonen skapas på några minuter och portalen uppdateras och visar den nya ordern.


## <a name="delete-order"></a>Ta bort en order

Du kanske vill ta bort en order när den har verkställts. Ordern innehåller din personliga information, till exempel namn, adress och kontaktuppgifter. Den här personliga informationen tas bort när ordern tas bort.

Du kan bara ta bort order som har slutförts eller avbrutits. Följ steg nedan om du vill ta bort en order.

1. Gå till **Alla resurser**. Sök efter din order.

2. Klicka på den order som du vill ta bort och gå till **Översikt**. Klicka på **Ta bort** i kommandofältet.

    ![Kommandot Ta bort på fliken Översikt för en order](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Ange namnet på ordern när du uppmanas att bekräfta borttagningen av ordern. Klicka på **Ta bort**.

## <a name="download-shipping-label"></a>Ladda ned adressetikett

Du kan behöva hämta skeppnings etiketten om E-Penn visningen av din Data Box-enhet inte fungerar och inte visar etiketten retur leverans. Det finns inget E-Penn visning på Data Box Heavy, så det här arbets flödet gäller inte för Data Box Heavy.

Följ stegen nedan om du behöver ladda ned en fraktsedel.

1.  Öppna **Översikt > Ladda ned adressetikett**. Det här alternativet är endast tillgängligt när enheten har skickats. 

    ![Ladda ned adressetikett](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Med det här alternativet laddas följande returfraktsedel ned. Spara etiketten och skriv ut den. Vik och infoga etiketten i ta bort fläns på enheten. Se till att etiketten syns tydligt. Ta bort eventuella klistermärken som finns på enheten från föregående leverans.

    ![Exempel på adressetikett](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Redigera leveransadress

Du kan behöva ändra leveransadressen när beställningen har gjorts. Det här alternativet är endast tillgängligt innan enheten skickas. När enheten har skickats är det här alternativet inte längre tillgängligt.

Följ stegen nedan om du vill redigera ordern.

1. Gå till **Orderdetaljer > Redigera leveransadress**.

    ![Kommandot Redigera leverans adress i Orderspecifikationer](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Redigera och verifiera leveransadressen och spara sedan ändringarna.

    ![Dialog rutan Redigera leverans adress](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Redigera meddelandeinformation

Du kan behöva ändra de användare som tar emot e-post för order status. Exempelvis ska en användare kanske meddelas när enheten levereras eller hämtas. En annan användare kan behöva informeras när data kopieringen är klar så att de kan verifiera att data finns i Azure Storage-kontot innan de tas bort från källan. I dessa fall kan du redigera meddelandeinformationen.

Följ stegen nedan om du vill redigera meddelandeinformationen.

1. Gå till **Orderdetaljer > Redigera meddelandeinformation**.

    ![Redigera meddelande information kommandot i order Detaljer](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Nu kan du redigera meddelandeinformationen och spara ändringarna.
 
    ![Dialog rutan Redigera meddelande information](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Ladda ned beställningshistorik

När Data Box-beställningen är klar raderas data på enheten. När enhetsrensningen är klar kan du ladda ned beställningshistoriken i Azure-portalen.

Ladda ned beställningshistoriken med hjälp av följande steg.

1. I Data Box-beställningen går du till **Översikt**. Kontrollera att beställningen är komplett. Om beställningen är komplett enhetsrensningen har slutförts går du till **Beställningsinformation**. Alternativet **Ladda ned beställningshistorik** är tillgängligt.

    ![Ladda ned beställningshistorik](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Klicka på **Ladda ned beställningshistorik**. Den hämtade historiken innehåller en post med spårnings loggar för bärvåg. Det finns två uppsättningar med logg som motsvarar de två noderna på en Data Box Heavy enhet. Om du rullar ned till slutet av loggen visas följande länkar:
    
   - **Kopierings loggar** – innehåller en lista över filer som fel uppstod under data kopieringen från data Box-enhet till ditt Azure Storage-konto (import ordning) eller från ditt lagrings konto till data Box-enhet (export order).
   - **Gransknings loggar** – innehåller information om hur du kan sätta på data Box-enhet och få åtkomst till resurser på den när data Box-enhet är utanför Azure-datacentret.
   - **Strukturlisteartiklar i import ordning** – innehåller en lista med filer (även kallat fil manifestet) som du kan ladda ned under **Förbered för att skicka** och har fil namn, fil storlekar och fil kontroll summor.
   - **Utförliga loggar i export ordning** – innehåller en lista över filer med fil namn, fil storlekar och beräkning av kontroll Summa när data kopierades från Azure Storage konton till data Box-enhet.

   Här är ett exempel på en order historik från en import order.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Du kan sedan gå till lagringskontot och visa kopieringsloggarna.

   ![Kopierings loggarna för ett lagrings konto](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Du kan också Visa kedjan med vårdnads loggar, inklusive gransknings loggar och BOM-filer.

   ![Kedja av vårdnads loggar för ett lagrings konto](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Visa orderstatus

När enhetens status ändras i portalen meddelas du via ett e-postmeddelande.

### <a name="statuses-for-import-order"></a>Status för import ordning

Här är status för en import ordning.

|Beställningsstatus |Beskrivning |
|---------|---------|
|Beställt     | En beställning har gjorts. <br>Om enheten är tillgänglig identifierar Microsoft en enhet för leverans och förbereder enheten. <br> Om enheten inte är tillgänglig direkt kommer ordern att bearbetas när enheten blir tillgänglig. Ordern kan ta flera dagar till några månader att behandla. Om ordern inte kan uppfyllas i 90 dagar annulleras ordern och du meddelas.         |
|Bearbetad     | Orderbearbetningen har slutförts. Enligt din order förbereds enheten för leverans i datacentret.         |
|Skickat     | Beställningen har skickats. Använd det spårnings-ID som visas i din order i portalen för att spåra leveransen.        |
|Levererade     | Försändelsen har levererats till den adress som angetts i ordern.        |
|Hämtat     |Din returleverans har hämtats och genomsökts av transportören.         |
|Mottaget     | Enheten tas emot och genomsöks vid Azure-datacentret. <br> När leveransen kontrolleras startar enhetsöverföringen.      |
|Datakopiering     | Data kopieras. Spåra kopieringsprocessen för din order i Azure-portalen. <br> Vänta tills datakopieringen är klar. |
|Slutförd       |Ordern har slutförts.<br> Kontrollera att dina data finns i Azure innan du tar bort lokala data från servrarna.         |
|Slutfört med fel| Datakopieringen slutfördes men fel inträffade under kopieringen. <br> Granska kopieringsloggarna med hjälp av sökvägen som anges i Azure-portalen. Se [exempel på kopierings loggar när överföringen är klar med fel](./data-box-logs.md#upload-completed-with-errors).   |
|Slutfört med varningar| Data kopieringen slutfördes men dina data ändrades. Data innehöll icke-kritiska BLOB-eller fil namns fel som korrigerades genom att ändra fil-eller BLOB-namn. <br> Granska kopieringsloggarna med hjälp av sökvägen som anges i Azure-portalen. Anteckna ändringarna i dina data. Se [exempel på kopierings loggar när överföringen är klar med varningar](./data-box-logs.md#upload-completed-with-warnings).   |
|Avbrutna            |Ordern har avbrutits. <br> Antingen avbröt du ordern eller så avbröts tjänsten när ett fel uppstod. Om ordern inte kan uppfyllas i 90 dagar avbryts även ordern och du får ett meddelande.     |
|Rensa | Data på enhetsdiskarna raderas. Enhetsrensningen anses slutförd när beställningshistoriken är tillgänglig för nedladdning i Azure-portalen.|

### <a name="statuses-for-export-order"></a>Status för export ordning

Här är status för en export ordning.

|Beställningsstatus |Beskrivning |
|---------|---------|
|Beställt     | En export order har placerats. <br>Om enheten är tillgänglig identifierar Microsoft en enhet för leverans och förbereder enheten. <br> Om enheten inte är tillgänglig omedelbart bearbetas ordern när enheten blir tillgänglig. Ordern kan ta flera dagar till några månader att behandla. Om ordern inte kan uppfyllas inom 90 dagar annulleras den och du får ett meddelande.         |
|Avbrutna            |Ordern har avbrutits. <br> Antingen avbröt du ordern (du kan bara avbryta innan ordern bearbetas) eller så påträffades ett fel och tjänsten avbröt ordern. Om ordern inte kan uppfyllas inom 90 dagar annulleras den också och du får ett meddelande.     |
|Bearbetad     | Orderbearbetningen har slutförts. Enligt din beställning förbereds enheten för data kopiering i data centret. Enhets resurser skapas.         |
|Data kopiering pågår     | Data kopieringen från de angivna Azure Storage-kontona till enheten pågår. Spåra kopieringsprocessen för din order i Azure-portalen. <br> Vänta tills datakopieringen är klar. |
|Kopiering slutförd     | Data kopieringen från de angivna Azure Storage-kontona till enheten har slutförts. En utförlig loggfil (om alternativet har Aktiver ATS i ordningen) och en kopierings logg skapas i ditt lagrings konto. Utförlig logg innehåller information om alla filer (namn, sökväg, beräknings kontroll summa) som kopieras till enheten. Kopierings loggen innehåller en sammanfattning av kopierings processen, inklusive en lista över filer som inte kunde kopieras på grund av eventuella fel. <br> Lagrings kontots data är oförändrade. |
|Kopieringen slutfördes med fel| Datakopieringen slutfördes men fel inträffade under kopieringen. <br> Granska kopierings loggarna i Azure Storage kontot med hjälp av sökvägen som anges i Azure Portal. Se [exempel på kopierings loggar när hämtningen är klar med fel](./data-box-logs.md#upload-completed-with-errors).   |
|Kopieringen slutfördes med varningar| Data kopieringen från Azure Storage kontot slutfördes, men data innehöll icke-kritiska fel. <br> Granska kopieringsloggarna med hjälp av sökvägen som anges i Azure-portalen. Anteckna de icke-kritiska felen. Se [exempel på kopierings loggar när hämtningen är klar med varningar](./data-box-logs.md#upload-completed-with-warnings).   |
|Kopieringen misslyckades med fel| Det gick inte att kopiera data från Azure Storage konto, och ordningen avslutas. Ingen enhet skickas. <br> Granska kopierings loggarna i Azure Storage kontot med hjälp av sökvägen som anges i Azure Portal. Se [exempel på kopierings loggar när hämtningen misslyckades med fel](./data-box-logs.md#upload-completed-with-errors).   |
|Skickat     |Beställningen har skickats. Använd det spårnings-ID som visas i din order i portalen för att spåra leveransen.        |
|Levererade     |Försändelsen har levererats till den adress som angetts i ordern.        |
|Hämtat     |Din returleverans har hämtats och genomsökts av transportören.         |
|Mottaget     | Enheten tas emot och genomsöks vid Azure-datacentret. <br> Leveransen har inspekterats.      |
|Slutförd           |Ordern har slutförts.     |
|Rensa | Data på enhetsdiskarna raderas. Enhetsrensningen anses slutförd när beställningshistoriken är tillgänglig för nedladdning i Azure-portalen.|

> [!NOTE]
> Om kopierings jobbet för att exportera data från Azure Storage-konton till Data Box-enhet slutförs med fel eller varningar, levereras enheten fortfarande. I händelse av ett kopierings haveri avslutas ordningen och enheten levereras inte.


Om du använder självhanterad leverans efter att kopieringen har slutförts och innan du får enheten, ser du följande tillstånd (i stället för de som nämns i tabellen ovan):

|Beställningsstatus |Beskrivning |
|---------|---------|
|Redo för hämtning i Azure Data Center      |Enheten är redo att hämtas i Azure-datacentret.        |
|Hämtat    |Du har valt enheten.         |
|Redo att ta emot i Azure Data Center     |Enheten är redo att tas emot i Azure-datacentret.        |
|Mottaget     |Enheten har mottagits i Azure-datacentret.      |





## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker data Box-enhet och data Box Heavy problem](data-box-troubleshoot.md).