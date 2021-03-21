---
title: Ändra dina StorSimple-lösenord | Microsoft Docs
description: Beskriver hur du använder tjänsten StorSimple Enhetshanteraren för att ändra lösen ord för StorSimple Snapshot Manager och enhets administratör.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023762"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Använd tjänsten StorSimple Enhetshanteraren för att ändra dina StorSimple-lösenord

## <a name="overview"></a>Översikt
Alternativet Azure Portal **enhets inställningar** innehåller alla enhets parametrar som du kan konfigurera på en StorSimple-enhet som hanteras av en StorSimple Enhetshanteraren-tjänst. I den här självstudien beskrivs hur du kan använda **säkerhets** alternativet under **enhets inställningar** för att ändra enhets administratören eller StorSimple Snapshot Manager lösen ord.

## <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
När du använder Windows PowerShell-gränssnittet för att få åtkomst till StorSimple-enheten måste du ange ett lösen ord för enhets administratören. När den första StorSimple-enheten registreras med en tjänst är standard lösen ordet för det här gränssnittet *Password1*. För att skydda dina data måste du ändra lösen ordet i slutet av registrerings processen. Du kan inte avsluta registrerings processen utan att ändra lösen ordet. Mer information finns i [steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Det lösen ord som först angavs via Windows PowerShell-gränssnittet under registreringen kan ändras senare via Azure Portal. Utför följande steg för att ändra enhetens administratörs lösen ord.

#### <a name="to-change-the-device-administrator-password"></a>Ändra enhetens administratörs lösen ord
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Markera och klicka på enheten vars lösen ord du vill ändra i list rutan med enheter.

    ![Skärm bild som visar StorSimple Enhetshanteraren-tjänsten. Under hantering, väljs enheter. En enhet har valts i enhets listan.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. I bladet **Inställningar** går du till **enhets inställningar > säkerhet**.

    ![Skärm bild som visar bladet inställningar i Enhetshanterarens tjänsten. Under enhets inställningar väljer du säkerhet.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I bladet **säkerhets inställningar** klickar du på **lösen ord** för att ändra enhetens administratörs lösen ord.

    ![Skärm bild som visar bladet säkerhets inställningar. Knappen lösen ord är markerad.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. På bladet **lösen ord** anger du ett administratörs lösen ord som innehåller mellan 8 och 15 tecken. Lösen ordet måste vara en kombination av 3 eller flera versaler, gemener, siffror och specialtecken.

6. Bekräfta lösenordet.

    ![Skärm bild som visar bladet lösen ord. Under enhetens administratörs lösen ord fylls rutorna nytt lösen ord och bekräfta lösen ord i.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klicka på **Spara** och bekräfta genom att klicka på **Ja**.

    ![Skärm bild som visar bladet lösen ord. Knappen Spara är markerad.](./media/storsimple-8000-change-passwords/changepwd6.png)

Enhetens administratörs lösen ord bör nu uppdateras. Du kan använda det här ändrade lösen ordet för att få åtkomst till Windows PowerShell-gränssnittet.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ange Snapshot Manager lösen ord för StorSimple
Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

När du konfigurerar en enhet i StorSimple Snapshot Manager uppmanas du att ange enhetens IP-adress och lösen ord för att autentisera lagrings enheten.

Du kan ange eller ändra lösen ordet för StorSimple Snapshot Manager via Azure Portal. Utför följande steg för att ange eller ändra StorSimple-Snapshot Manager lösen ord.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Ange StorSimple-lösenordet Snapshot Manager
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Markera och klicka på enheten vars StorSimple Snapshot Manager lösen ord som du vill ange eller ändra i tabell listan över enheter.

     ![Skärm bild som visar StorSimple Enhetshanteraren-tjänsten. Under hantering, väljs enheter. En enhet har valts i enhets listan.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. I bladet **Inställningar** går du till **enhets inställningar > säkerhet**.

     ![Skärm bild som visar bladet inställningar i Enhetshanterarens tjänsten. Under enhets inställningar väljer du säkerhet.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I bladet **säkerhets inställningar** klickar du på **lösen ord** för att ange eller ändra StorSimple Snapshot Manager-lösenord.

     ![Skärm bild som visar bladet säkerhets inställningar. Knappen lösen ord är markerad.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. I bladet **lösen ord** anger du ett lösen ord som är 14 eller 15 tecken. Se till att lösen ordet innehåller en kombination av 3 eller flera versaler, gemener, siffror och specialtecken.

6. Bekräfta lösenordet.

     ![Skärm bild som visar bladet lösen ord. Under Snapshot Manager lösen ord fylls rutorna nytt lösen ord och bekräfta lösen ord i.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klicka på **Spara** och bekräfta genom att klicka på **Ja**.

     ![Skärm bild som visar bladet lösen ord. Knappen Spara är markerad.](./media/storsimple-8000-change-passwords/changepwd6.png)

StorSimple Snapshot Manager-lösenordet bör nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om hur [du ändrar enhets konfigurationen](storsimple-8000-modify-device-config.md).
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

