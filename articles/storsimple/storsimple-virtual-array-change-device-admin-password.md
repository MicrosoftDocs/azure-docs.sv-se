---
title: Ändra StorSimple Virtual Array Device Admin Password | Microsoft Docs
description: Beskriver hur du ändrar lösen ordet för enhets administratören genom att använda antingen Azure Portal eller StorSimple virtuella matrisens webb gränssnitt.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513616"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Ändra StorSimple för den virtuella matris enheten via StorSimple Enhetshanteraren

## <a name="overview"></a>Översikt

När du använder Windows PowerShell-gränssnittet för att få åtkomst till den virtuella StorSimple-matrisen måste du ange ett administratörs lösen ord för enheten. När StorSimple-enheten först är etablerad och startas, är standard lösen ordet *Password1*. För att skydda dina data, förfaller standard lösen ordet första gången du loggar in och du måste ändra det här lösen ordet.

Du kan också använda antingen det lokala webb gränssnittet eller Azure Portal för att ändra lösen ordet för enhets administratör när som helst efter att enheten har distribuerats i produktions miljön. Var och en av dessa procedurer beskrivs i den här artikeln.

 ![bladet enheter](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Använd Azure Portal för att ändra lösen ordet

Utför följande steg för att ändra enhetens administratörs lösen ord via Azure Portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Ändra enhetens administratörs lösen ord via Azure Portal

1. På sidan landning av tjänst väljer du din tjänst, dubbelklickar på tjänstens namn och klickar sedan på **enheter** i avsnittet **hantering** . Då öppnas bladet **enheter** som visar en lista över alla dina StorSimple virtuella mat ris enheter.

2. I bladet **enheter** dubbelklickar du på den enhet som kräver en ändring av lösen ordet.

3. I **inställnings** bladet för enheten klickar du på **säkerhet**.

4. Gör följande på bladet **säkerhets inställningar** :
   
   1. Rulla ned till avsnittet **enhets administratörs lösen ord** . Ange ett administratörs lösen ord som innehåller mellan 8 och 15 tecken.
   2. Bekräfta lösenordet.
   3. Klicka på **Spara** överst på bladet.

Enhetens administratörs lösen ord har nu uppdaterats. Du kan använda det här ändrade lösen ordet för att komma åt enheten lokalt.

![Bladet säkerhets inställningar](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Använd det lokala webb gränssnittet för att ändra lösen ordet

Utför följande steg för att ändra enhetens administratörs lösen ord via det lokala webb gränssnittet.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Ändra enhetens administratörs lösen ord via det lokala webb gränssnittet

1. I det lokala webb gränssnittet klickar du på **underhålls**  >  **ändring av lösen ord** för enheten.
   
    ![ändra password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Ange det **aktuella lösen ordet**.
3. Ange ett **nytt lösen ord**. Lösen ordet måste vara minst 8 tecken långt. Det måste innehålla 3 av 4 av följande: versaler, gemener, siffror och specialtecken.
   
    Observera att ditt lösen ord inte kan vara detsamma som de senaste 24 lösen orden.
4. Ange lösenordet igen för att bekräfta det.
   
    ![ändra password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Klicka på **Använd** längst ned på sidan. Det nya lösen ordet används nu. Om lösen ords ändringen inte lyckas visas följande fel meddelande:
   
    ![lösen ords fel](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    När lösen ordet har uppdaterats får du ett meddelande. Du kan sedan använda det här ändrade lösen ordet för att komma åt enheten lokalt.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du [administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

