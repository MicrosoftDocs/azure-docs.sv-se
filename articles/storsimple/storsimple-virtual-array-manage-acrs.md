---
title: Hantera åtkomst kontroll poster för virtuell StorSimple-matris | Microsoft Docs
description: 'Beskriver hur du hanterar åtkomst kontroll poster (ACR: er) för att avgöra vilka värdar som kan ansluta till en volym på den virtuella StorSimple-matrisen.'
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507627"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera åtkomst kontroll poster för StorSimple virtuella matris

## <a name="overview"></a>Översikt

Med åtkomst kontroll poster (ACR: er) kan du ange vilka värdar som kan ansluta till en volym på den virtuella StorSimple-matrisen (kallas även för den lokala virtuella enheten StorSimple). ACR: er anges till en särskild volym och innehåller värdernas iSCSI-kvalificerade namn (IQNs). När en värd försöker ansluta till en volym kontrollerar enheten de ACR som är kopplade till den volymen för IQN-namnet, och om det finns en matchning, upprättas anslutningen. Bladet **åtkomst kontroll poster** i **konfigurations** avsnittet i Enhetshanterarens tjänsten visar alla åtkomst kontroll poster med motsvarande IQNs av värdarna.

![Hantera åtkomst kontroll poster](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

I den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Hämta IQN
* Lägg till en åtkomst kontroll post
* Redigera en åtkomst kontroll post
* Ta bort en åtkomst kontroll post

> [!IMPORTANT]
> 
> * När du tilldelar en ACR till en volym bör du ta hänsyn till att volymen inte samtidigt används av fler än en icke-klustrad värd eftersom det kan skada volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte har åtkomst till volymen eftersom borttagningen kan resultera i ett Skriv avbrott.


## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Lägg till en ACR

Du använder bladet **åtkomst kontroll poster** i **konfigurations** avsnittet i StorSimple-Enhetshanteraren-tjänsten för att lägga till ACR: er. Normalt associerar du en ACR med en volym.

Information om hur du kopplar en ACR till en volym finns i [lägga till en volym](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> När du tilldelar en ACR till en volym bör du ta hänsyn till att volymen inte samtidigt används av fler än en icke-klustrad värd eftersom det kan skada volymen.


Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Lägga till en ACR

1. På sidan landning av tjänst väljer du din tjänst, dubbelklickar på tjänstens namn och klickar sedan på **åtkomst kontroll poster** i avsnittet **konfiguration** .
2. I bladet **åtkomst kontroll poster** klickar du på **Lägg till**.
3. Gör följande på bladet **Lägg till ACR** :
   
    1. Ange ett **namn** för din ACR.
    
    2. Under **iSCSI-initierarens namn** anger du IQN-namnet för din Windows-värd. Gör följande för att hämta IQN för Windows Server-värden:
   
    3. Starta Microsoft iSCSI-initieraren på din Windows-värd. I fönstret för iSCSI-initieraregenskaper, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
    Klistra in den här strängen i fältet **IQN** i bladet **Lägg till ACR** .
   
    6. Klicka på **Lägg** till för att lägga till ACR.  
   
        ![Lägg till åtkomst kontroll poster](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabell listan uppdateras för att avspegla detta tillägg.

## <a name="edit-an-acr"></a>Redigera en ACR

Du använder bladet **åtkomst kontroll poster** i **konfigurations** avsnittet i Enhetshanterarens tjänsten i Azure Portal för att redigera ACR: er.

> [!NOTE]
> Du bör inte ändra en ACR som används för närvarande. Om du vill redigera en ACR som är associerad med en volym som för närvarande används, bör du först koppla från volymen.


Utför följande steg för att redigera en ACR.

#### <a name="to-edit-an-acr"></a>Redigera en ACR

1. På sidan landning av tjänst väljer du din tjänst, dubbelklickar på tjänstens namn och går sedan till avsnittet **konfiguration** och **kontrollerar åtkomst kontroll poster**.
2. I bladet **åtkomst kontroll poster** , i tabell listan över åtkomst kontroll posterna, dubbelklickar du på ACR som du vill ändra.
3. Gör följande i bladet **Redigera åtkomst kontroll poster** :
   
    1. Ange IQN för ACR.
   
    2. Klicka på **Spara** överst på bladet för att spara den ändrade ACR. Följande bekräftelse meddelande visas:
   
        ![Redigera åtkomst kontroll poster](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Ta bort en åtkomst kontroll post

Du använder sidan **konfiguration** i Azure Portal för att ta bort ACR: er.

> [!NOTE]
> 
> * Du bör inte ta bort en ACR som används för närvarande. Om du vill ta bort en ACR som är associerad med en volym som för närvarande används, bör du först koppla från volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte har åtkomst till volymen eftersom borttagningen kan resultera i ett Skriv avbrott.


Utför följande steg för att ta bort en åtkomst kontroll post.

#### <a name="to-delete-an-access-control-record"></a>Ta bort en åtkomst kontroll post

1. På sidan landning av tjänst väljer du din tjänst, dubbelklickar på tjänstens namn och går sedan till avsnittet **konfiguration** och **kontrollerar åtkomst kontroll poster**.

2. I bladet **åtkomst kontroll poster** , i tabell listan över åtkomst kontroll posterna, dubbelklickar du på ACR som du vill ta bort.

3. I bladet redigera åtkomst kontroll poster klickar du på **ta bort**.
   
    ![Ta bort ACR: er](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. När du uppmanas att bekräfta klickar du på **ta bort** för att fortsätta med borttagningen. Tabell listan uppdateras för att återspegla borttagningen.
   
   ![Varnings meddelande](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att lägga till volymer och konfigurera ACR: er](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

