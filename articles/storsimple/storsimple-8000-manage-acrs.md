---
title: Hantera åtkomst kontroll poster i StorSimple | Microsoft Docs
description: 'Beskriver hur du använder åtkomst kontroll poster (ACR: er) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple-enheten.'
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949912"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Använd StorSimple Manager-tjänsten för att hantera åtkomst kontroll poster

## <a name="overview"></a>Översikt
Med åtkomst kontroll poster (ACR: er) kan du ange vilka värdar som kan ansluta till en volym på StorSimple-enheten. ACR: er anges till en särskild volym och innehåller värdernas iSCSI-kvalificerade namn (IQNs). När en värd försöker ansluta till en volym kontrollerar enheten de ACR som är kopplade till den volymen för IQN-namnet och om det finns en matchning, upprättas anslutningen. Åtkomst kontroll posterna i **konfigurations** avsnittet i bladet StorSimple Enhetshanteraren service visar alla åtkomst kontroll poster med motsvarande IQNs för värdarna.

I den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Lägg till en åtkomst kontroll post
* Redigera en åtkomst kontroll post
* Ta bort en åtkomst kontroll post

> [!IMPORTANT]
> * När du tilldelar en ACR till en volym bör du ta hänsyn till att volymen inte samtidigt används av fler än en icke-klustrad värd eftersom det kan skada volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte har åtkomst till volymen eftersom borttagningen kan resultera i ett Skriv avbrott.

## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Lägg till en åtkomst kontroll post
Du använder **konfigurations** avsnittet i bladet StorSimple Enhetshanteraren-tjänst för att lägga till ACR: er. Normalt associerar du en ACR med en volym.

Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Lägga till en ACR

1. Gå till StorSimple Enhetshanteraren-tjänsten, dubbelklicka på tjänstens namn och klicka sedan på **åtkomst kontroll poster** i avsnittet **konfiguration** .
2. I bladet **åtkomst kontroll poster** klickar du på **+ Lägg till ACR**.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Utför följande steg på bladet **Lägg till ACR** :

    1. Ange ett namn för din ACR.
    
    2. Ange IQN-namnet för din Windows Server-värd under **iSCSI-initierarens namn (IQN)**.

    3. Klicka på **Lägg till** för att skapa ACR.

        ![Klicka på Lägg till ACR 2](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  De nyligen tillagda ACR visas i tabell listan över ACR: er.

    ![Klicka på Lägg till ACR 3](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Redigera en åtkomst kontroll post
Du använder **konfigurations** avsnittet i bladet StorSimple Enhetshanteraren tjänst för att redigera ACR: er.

> [!NOTE]
> Vi rekommenderar att du bara ändrar de ACR: er som för närvarande inte används. Om du vill redigera en ACR som är associerad med en volym som för närvarande används, måste du först koppla från volymen.

Utför följande steg för att redigera en ACR.

#### <a name="to-edit-an-access-control-record"></a>Redigera en åtkomst kontroll post
1.  Gå till StorSimple Enhetshanteraren-tjänsten, dubbelklicka på tjänstens namn och klicka sedan på **åtkomst kontroll poster** i avsnittet **konfiguration** .

    ![Gå till åtkomst kontroll poster](./media/storsimple-8000-manage-acrs/createacr1.png)

2. I list rutan med åtkomst kontroll posterna klickar du på och väljer den ACR som du vill ändra.

    ![Redigera åtkomst kontroll poster](./media/storsimple-8000-manage-acrs/editacr1.png)

3. I bladet **Redigera åtkomst kontroll post** anger du ett annat IQN som motsvarar en annan värd.

    ![Redigera åtkomst kontroll poster 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klicka på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Redigera åtkomst kontroll poster 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Du får ett meddelande när ACR uppdateras. Tabell listan uppdateras också för att avspegla ändringen.

   
## <a name="delete-an-access-control-record"></a>Ta bort en åtkomst kontroll post
Du använder **konfigurations** avsnittet i bladet StorSimple Enhetshanteraren tjänst för att ta bort ACR: er.

> [!NOTE]
> Du kan bara ta bort de ACR: er som för närvarande inte används. Om du vill ta bort en ACR som är associerad med en volym som för närvarande används, måste du först koppla från volymen.

Utför följande steg för att ta bort en åtkomst kontroll post.

#### <a name="to-delete-an-access-control-record"></a>Ta bort en åtkomst kontroll post
1.  Gå till StorSimple Enhetshanteraren-tjänsten, dubbelklicka på tjänstens namn och klicka sedan på **åtkomst kontroll poster** i avsnittet **konfiguration** .

    ![Gå till åtkomst kontroll poster 1b](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka och välj den ACR som du vill ta bort i tabell listan över åtkomst kontroll poster.

    ![Gå till åtkomst kontroll poster 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Högerklicka för att anropa snabb menyn och välj **ta bort**.

    ![Gå till åtkomst kontroll poster 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. När du uppmanas att bekräfta kontrollerar du informationen och klickar sedan på **ta bort**.

    ![Gå till åtkomst kontroll poster 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Du får ett meddelande när borttagningen är klar. Tabell listan uppdateras för att återspegla borttagningen.

    ![Gå till åtkomst kontroll poster 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig mer om [att använda tjänsten StorSimple Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

