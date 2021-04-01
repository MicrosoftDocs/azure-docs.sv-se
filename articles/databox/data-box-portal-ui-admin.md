---
title: Administratörshandbok för Azure Data Box Disk-portalen | Microsoft Docs
description: Lär dig hur du hanterar Data Box Disk med hjälp av Azure Portal. Hantera order, hantera diskar och spåra statusen för en order när den fortskrider.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 538a650c6063422f89c8ed3d1753981a293693b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94338177"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>Använda Azure-portalen för att administrera din Data Box Disk

Självstudierna i den här artikeln gäller för förhandsversionen av Microsoft Azure Data Box Disk. Den här artikeln beskriver några av de komplexa arbetsflöden och de administrativa uppgifter som du kan utföra med Data Box Disk. 

Du kan hantera Data Box Disk via Azure Portal. Den här artikeln fokuserar på de uppgifter som du kan utföra på Azure Portal. Använd Azure Portal för att hantera beställningar, hantera diskar och spåra orderstatusen från början till slut.

## <a name="cancel-an-order"></a>Annullera en beställning

Ibland kan du behöva annullera en beställning som du gjort. Du kan bara annullera en beställning innan diskförberedelsen påbörjas. När arbetet med diskarna och orderbehandlingen har påbörjats går det inte längre att annullera beställningen. 

Följ stegen nedan om du vill annullera en beställning.

1.  Gå till **Översikt > Avbryt**. 

    ![Kommandot Avbryt på fliken Översikt för en order](media/data-box-portal-ui-admin/portal-ui-admin-cancel-command.png)

2.  Fyll i en orsak som beskriver varför du vill annullera beställningen.  

    ![Orsak till annullering av en order](media/data-box-portal-ui-admin/portal-ui-admin-cancel-order-reason.png)

3.  När beställningen har annullerats uppdateras statusen för ordern på portalen och visas som **Avbruten**.

    ![Annullerad ordning](media/data-box-portal-ui-admin/portal-ui-admin-canceled-order.png)

Du får inget e-postmeddelande när beställningen har annullerats.

## <a name="clone-an-order"></a>Klona en order

Kloning är användbart i vissa situationer. En användare kanske exempelvis har använt Data Box Disk för att överföra vissa data. Allteftersom mer data genereras finns behov av fler diskar för att överföra dessa data till Azure. I så fall behöver du bara klona samma order.

Följ stegen nedan om du vill klona en order.

1.  Gå till **Översikt > Klona**. 

    ![Klona kommando på fliken Översikt för en order](media/data-box-portal-ui-admin/portal-ui-admin-clone-command.png)

2.  All information för ordern förblir densamma. Namnet på beställningen är det ursprungliga ordernamnet, med tillägget *-Klon*. Markera kryssrutan för att bekräfta att du har läst sekretessinformationen. Klicka på **Skapa**.    

Klonen skapas på några minuter och portalen uppdateras och visar den nya ordern.

[![Klonad ordning](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png)](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png#lightbox) 

## <a name="delete-order"></a>Ta bort en order

Du kanske vill ta bort en order när den har verkställts. Ordern innehåller din personliga information, till exempel namn, adress och kontaktuppgifter. Den här personliga informationen tas bort när ordern tas bort.

Du kan bara ta bort order som har slutförts eller avbrutits. Följ steg nedan om du vill ta bort en order.

1. Gå till **Alla resurser**. Sök efter din order.

    ![Sök efter beställningar](media/data-box-portal-ui-admin/portal-ui-admin-search-data-box-disk-orders.png)

2. Klicka på den order som du vill ta bort och gå till **Översikt**. Klicka på **Ta bort** i kommandofältet.

    ![Ta bort en order](media/data-box-portal-ui-admin/portal-ui-admin-delete-command.png)

3. Ange namnet på ordern när du uppmanas att bekräfta borttagningen av ordern. Klicka på **Ta bort**.

     ![Bekräfta borttagning av order](media/data-box-portal-ui-admin/portal-ui-admin-confirm-deletion.png)


## <a name="download-shipping-label"></a>Ladda ned adressetikett

Du kan behöva ladda ned fraktsedeln om du tappar bort returfraktsedeln som skickades med dina diskar eller om den saknas. 

Följ stegen nedan om du behöver ladda ned en fraktsedel.
1.  Öppna **Översikt > Ladda ned adressetikett**. Det här alternativet är endast tillgängligt när disken har skickats. 

    ![Ladda ned adressetikett](media/data-box-portal-ui-admin/portal-ui-admin-download-shipping-label.png)

2.  Med det här alternativet laddas följande returfraktsedel ned. Spara fraktsedeln, skriv ut den och sätt fast den på returpaketet.

    ![Exempel på adressetikett](media/data-box-portal-ui-admin/portal-ui-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Redigera leveransadress

Du kan behöva ändra leveransadressen när beställningen har gjorts. Det här alternativet är endast tillgängligt innan disken skickas. När disken har skickats är det här alternativet inte tillgänglig längre.

Följ stegen nedan om du vill redigera ordern.

1. Gå till **Orderdetaljer > Redigera leveransadress**.

    ![Kommandot Redigera leverans adress i Orderspecifikationer](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-command.png)

2. Nu kan du redigera leveransadressen och spara ändringarna.

    ![Dialog rutan Redigera leverans adress](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Redigera meddelandeinformation

Du kan behöva ändra vilka användare som ska få e-postmeddelanden om orderstatusen. Exempelvis kanske en användare ska meddelas när disken levereras eller hämtas. En annan användare kan behöva informeras när data kopieringen är klar så att de kan verifiera att data finns i Azure Storage-kontot innan de tas bort från källan. I dessa fall kan du redigera meddelandeinformationen.

Följ stegen nedan om du vill redigera meddelandeinformationen.

1. Gå till **Orderdetaljer > Redigera meddelandeinformation**.

    ![Redigera meddelande information kommandot i order Detaljer](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-command.png)

2. Nu kan du redigera meddelandeinformationen och spara ändringarna.
 
    ![Dialog rutan Redigera meddelande information](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-dbox.png)

## <a name="view-order-status"></a>Visa orderstatus

|Beställningsstatus |Beskrivning |
|---------|---------|
|Beställt     | En beställning har gjorts. <br> Om diskarna inte är tillgängliga får du ett meddelande. <br>Om diskarna är tillgängliga identifierar Microsoft en disk för leverans och förbereder diskpaketet.        |
|Bearbetad     | Orderbearbetningen har slutförts. <br> Under orderbearbetningen utförs följande åtgärder:<li>Diskarna krypteras med AES-128 BitLocker-kryptering. </li> <li>Data Box-diskarna låses för att förhindra obehörig åtkomst.</li><li>Nyckeln som låser upp diskarna genereras under den här processen.</li>        |
|Skickat     | Beställningen har skickats. Du bör få beställningen inom en till två dagar.        |
|Levererade     | Beställningen har levererats till adressen som angetts i ordern.        |
|Hämtat     |Ditt returpaket har hämtats. <br> När leveransen har tagits emot på Azure-datacentret laddas data automatiskt upp till Azure.         |
|Mottaget     | Dina diskar har tagits emot på Azures datacenter. Datakopieringen startar inom kort.        |
|Kopierade data     |Data kopieras.<br> Vänta tills datakopieringen är klar.         |
|Slutförd       |Ordern har slutförts.<br> Kontrollera att dina data finns i Azure innan du tar bort lokala data från servrarna.         |
|Slutfört med fel| Datakopieringen slutfördes men det uppstod fel. <br> Granska fel loggarna för uppladdning med hjälp av sökvägen som anges i **översikten**. Mer information finns i [Ladda ned fel loggar för uppladdning](data-box-disk-troubleshoot-upload.md#download-logs).   |
|Avbrutna            |Ordern har avbrutits. <br> Antingen annullerade du beställningen eller så påträffades ett fel som gjorde att tjänsten avbröt ordern.     |



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem med Data Box Disk](data-box-disk-troubleshoot.md).
