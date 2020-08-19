---
title: Skapa ett support ärende eller fall för Azure import/export-jobbet | Microsoft Docs
description: Lär dig hur du loggar support förfrågan om problem som rör ditt import/export-jobb.
services: storsimple
author: alkohli
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ee30e9bba35ad39606fbe201c49e4eed3e656c97
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548282"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Öppna ett support ärende för ett import/export-jobb

Om du stöter på problem med import/export-tjänsten kan du skapa en tjänstbegäran för teknisk support. Den här artikeln vägleder dig genom:

* Så här skapar du en support förfrågan.
* Så här hanterar du en livs cykel för support begär Anden från portalen.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Utför följande steg för att skapa en support förfrågan:

1. Gå till ditt import/export-jobb. Gå till avsnittet **support + fel sökning** och välj sedan **ny supportbegäran**.
     
    ![Grundläggande inställningar](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. I **ny supportbegäran**väljer du **grunderna**. I **grunderna**, utför du följande steg:
    
    1. I list rutan **ärende typ** väljer du **teknisk**.
    2. Välj din **prenumeration**.
    3. Under **tjänst**kontrollerar du **Mina tjänster**. I list rutan kan du välja ett av alternativen – **lagrings konto hantering**, **BLOB**eller **fil**. 
        - Om du väljer **hantering av lagrings konto**väljer du **resurs**och **Support plan**.
            ![Välj hantering av lagrings konto](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - Om du väljer **BLOB**väljer du **resurs**, **behållar namn** (valfritt) och **Support plan**.
            ![Välj BLOB](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - Om du väljer **fil**väljer du **resurs**, **fil resurs namn** (valfritt) och **Support plan** ![ Välj fil](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. Välj **Nästa**.

3. I **New Support-begäran**väljer du **steg 2 problem**. I **problem**, utför följande steg:
    
    1. Välj **allvarlighets grad** **C-minimal påverkan**. Supporten uppdateras om det behövs.
    2. Välj **typ av problem** som **datamigrering**.
    3. Välj **kategorin** som **import-export**.
    4. Ange ett **namn** på problemet och mer **information**.
    5. Ange start datum och start tid för problemet.
    6. I **fil uppladdning**väljer du mappikonen för att bläddra bland andra filer som du vill ladda upp.
    7. Kontrol lera **delning av diagnostikinformation**.
    8. Välj **Nästa**.

       ![Problem](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. I **ny supportbegäran**väljer du **steg 3 kontakt information**. Utför följande steg i **kontakt information**:

   1. I **kontakt alternativen**anger du önskad kontakt metod (telefon eller e-post) och språket. Svars tiden väljs automatiskt baserat på prenumerations planen.
   2. I kontakt uppgifterna anger du ditt namn, din e-postadress, valfri kontakt, land/region. Markera kryss rutan **Spara kontakt ändringar för framtida support begär Anden** .
   3. Välj **Skapa**.
   
       ![Kontaktinformation](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Microsoft Support kommer att använda den här informationen för att kontakta dig för ytterligare information, diagnos och lösning.
      När du har skickat in din begäran kontaktar en support tekniker om du vill fortsätta med din begäran.

## <a name="manage-a-support-request"></a>Hantera en support förfrågan

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina support ärenden

1. Öppna sidan hjälp och support genom att gå till **bläddra > hjälp + support**.

    ![Hantera support förfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. En lista över de **senaste support förfrågningarna** visas i **Hjälp + Support**.

    ![Hantera support förfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. Välj och klicka på en support förfrågan. Du kan visa status och information för den här begäran. Välj **+ nytt meddelande** om du vill följa upp den här begäran.

    ![Hantera support förfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder Azure import/export för att överföra data till och från Azure Storage](storage-import-export-service.md).
