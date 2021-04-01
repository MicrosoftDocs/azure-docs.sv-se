---
title: Skapa ett support ärende eller fall för StorSimple 8000-serien
description: Lär dig hur du loggar support förfrågan och startar en support-session på din StorSimple 8000-serie enhet.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017210"
---
# <a name="contact-microsoft-support"></a>Kontakta Microsoft Support

StorSimple-Enhetshanteraren ger möjlighet att **Logga en ny supportbegäran** på bladet för tjänst Sammanfattning. Om du stöter på problem med din StorSimple-lösning kan du skapa en tjänstbegäran för teknisk support. I en online-session med support teknikern kan du också behöva starta en support-session på din StorSimple-enhet. Den här artikeln vägleder dig genom:

* Så här skapar du en support förfrågan.
* Så här hanterar du en livs cykel för support begär Anden från portalen.
* Starta en support-session i Windows PowerShell-gränssnittet på din StorSimple-enhet.

Granska [StorSimple 8000-serien support service avtal och information](./storsimple-8000-support-options.md) innan du skapar en supportbegäran.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Beroende på ditt [support](https://azure.microsoft.com/support/plans/)avtal kan du skapa support biljetter för ett problem på din StorSimple-enhet direkt från bladet StorSimple Enhetshanteraren service Summary. Utför följande steg för att skapa en support förfrågan:

#### <a name="to-create-a-support-request"></a>Så här skapar du en support förfrågan

1. Gå till StorSimple Device Manager-tjänsten. I bladet tjänst Sammanfattning går du till avsnittet **support + fel sökning** och klickar sedan på **ny supportbegäran**.
     
    ![Kontakta MS support via ny portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. I bladet **nytt support ärende** väljer du **grundläggande**. Utför följande steg på bladet **grundläggande** :
   1. I list rutan **ärende typ** väljer du **teknisk**.
   2. Den aktuella **prenumerationen**, **tjänst** typen och **resursen** (StorSimple Enhetshanteraren-tjänsten) väljs automatiskt. 
   3. Välj en **Support plan** i list rutan om du har flera planer som är kopplade till din prenumeration. Du behöver ett betalt support avtal för att aktivera teknisk support.
   4. Klicka på **Nästa**.

       ![Kontakta MS support via ny Portal 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. På bladet **ny support ärende** väljer du **steg 2 problem**. Utför följande steg på bladet **problem** :
    
    1. Välj **allvarlighets grad**.
    2. Ange om problemet är relaterat till enheten eller tjänsten StorSimple Enhetshanteraren.
    3. Välj en **kategori** för det här problemet och ange mer **information** om problemet.
    4. Ange start datum och start tid för problemet.
    5. Klicka på mappikonen i **fil överföringen** för att bläddra till ditt support paket.
    6. Kontrol lera **delning av diagnostikinformation**.
    7. Klicka på **Nästa**.

       ![Kontakta MS support via ny portal 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Klicka på **steg 3 kontakt information** i bladet **nytt support ärende** . Utför följande steg på bladet **kontakt information** :

   1. I **kontakt alternativen** anger du önskad kontakt metod (telefon eller e-post) och språket. Svars tiden väljs automatiskt baserat på prenumerations planen.
   2. I kontakt uppgifterna anger du ditt namn, din e-postadress, valfri kontakt, land/region. Markera kryss rutan **Spara kontakt ändringar för framtida support begär Anden** .
   3. Klicka på **Skapa**.
   
       ![Kontakta MS support via ny portal 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Support kommer att använda den här informationen för att kontakta dig för ytterligare information, diagnos och lösning.
      När du har skickat in din begäran kontaktar en support tekniker så snart som möjligt för att fortsätta med din begäran.

## <a name="manage-a-support-request"></a>Hantera en support förfrågan

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina support ärenden

1. Öppna sidan hjälp och support genom att gå till **bläddra > hjälp + support**.

    ![Hantera support förfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. En lista över alla support förfrågningar visas i bladet **Hjälp + Support** .

    ![Hantera support förfrågningar 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Välj och klicka på en support förfrågan. Du kan visa status och information för den här begäran. Klicka på **+ nytt meddelande** om du vill följa upp den här begäran.

    ![Hantera support förfrågningar 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starta en support-session i Windows PowerShell för StorSimple

Om du vill felsöka eventuella problem som kan uppstå med StorSimple-enheten måste du delta i Microsoft Support-teamet. Microsoft Support kan behöva använda en support-session för att logga in på enheten.

Utför följande steg för att starta en support-session:

#### <a name="to-start-a-support-session"></a>Starta en support-session

1. Få åtkomst till enheten direkt med hjälp av serie konsolen eller via en Telnet-session från en fjärran sluten dator. Det gör du genom att följa stegen i [använda SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I sessionen som öppnas trycker du på **RETUR** -tangenten för att hämta en kommando tolk.
3. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**.
4. Skriv följande lösen ord i prompten:
   
    `Password1`
5. Skriv följande kommando i prompten:
   
    `Enable-HcsSupportAccess`
6. En krypterad sträng visas för dig. Kopiera strängen till en text redigerare, till exempel Anteckningar.
7. Spara den här strängen och skicka den i ett e-postmeddelande till Microsoft Support.

> [!IMPORTANT]
> Du kan inaktivera support åtkomst genom att köra `Disable-HcsSupportAccess` . StorSimple-enheten kommer också att försöka inaktivera support åtkomst 8 timmar efter att sessionen initierades. Vi rekommenderar att du ändrar dina autentiseringsuppgifter för StorSimple-enheten när du har initierat en support-session.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [diagnostiserar och löser problem relaterade till din StorSimple 8000-serie enhet](storsimple-8000-troubleshoot-deployment.md)