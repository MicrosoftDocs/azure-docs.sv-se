---
title: Administration av virtuell matris i Microsoft Azure StorSimple Manager | Microsoft Docs
description: Lär dig hur du hanterar din StorSimple lokala virtuella matris med hjälp av StorSimple Enhetshanteraren-tjänsten i Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: cb5b6b1a2d890fe49d78da17d549c5676557b971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654259"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Använd tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple virtuella matris
![konfigurations process flöde](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Översikt
I den här artikeln beskrivs StorSimple-Enhetshanteraren tjänst gränssnittet, inklusive hur du ansluter till det och de olika tillgängliga alternativen och innehåller länkar till de olika arbets flöden som kan utföras via det här användar gränssnittet.

När du har läst den här artikeln får du veta hur du:

* Ansluta till StorSimple-Enhetshanteraren tjänsten
* Navigera i StorSimple Enhetshanteraren UI
* Administrera din virtuella StorSimple-matris via tjänsten StorSimple Enhetshanteraren

> [!NOTE]
> Om du vill visa de hanterings alternativ som är tillgängliga för enheten StorSimple 8000-serien går du till [använda StorSimple Manager-tjänsten för att administrera StorSimple-enheten](./storsimple-8000-manager-service-administration.md).

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ansluta till StorSimple-Enhetshanteraren tjänsten
Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör så här för att ansluta till StorSimple-Enhetshanteraren tjänsten:

#### <a name="to-connect-to-the-service"></a>Så här ansluter du till tjänsten
1. Gå till [https://portal.azure.com](https://portal.azure.com).
2. Logga in på Microsoft Azure-portalen (som finns längst upp till höger i fönstret) med dina Microsoft-konto autentiseringsuppgifter.
3. Navigera till Browse--> ' filter ' på StorSimple Device Managers om du vill visa alla enhets hanterare i en specifik prenumeration.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Använd tjänsten StorSimple Enhetshanteraren för att utföra hanterings uppgifter
I följande tabell visas en översikt över alla vanliga hanterings uppgifter och komplexa arbets flöden som kan utföras på bladet StorSimple Enhetshanteraren tjänst Sammanfattning. De här uppgifterna är organiserade baserat på de blad där de initieras.

Klicka på lämplig procedur i tabellen om du vill ha mer information om varje arbets flöde.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Enhetshanteraren arbets flöden
| Om du vill göra det här... | Använd den här proceduren |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa tjänst registrerings nyckeln |[Distribuera tjänsten StorSimple Enhetshanteraren](storsimple-virtual-array-manage-service.md) |
| Visa aktivitets loggarna |[Använda StorSimple-tjänstens Sammanfattning](storsimple-virtual-array-service-summary.md) |
| Inaktivera en virtuell matris</br>Ta bort en virtuell matris |[Inaktivera eller ta bort en virtuell matris](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Haveri beredskap och enhets växling vid fel</br>Krav för redundans</br>Katastrof återställning av affärs kontinuitet (BCDR)</br>Fel vid katastrof återställning |[Haveri beredskap och redundanstest för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| Säkerhetskopiera resurser och volymer</br>Gör en manuell säkerhets kopiering</br>Ändra schema för säkerhets kopiering</br>Visa befintliga säkerhets kopior |[Säkerhetskopiera din virtuella StorSimple-matris](storsimple-virtual-array-backup.md) |
| Klona resurser från en uppsättning säkerhetskopior</br>Klona volymer från en uppsättning säkerhetskopior</br>Återställning på objekt nivå (endast fil Server) |[Klona från en säkerhets kopia av din virtuella StorSimple-matris](storsimple-virtual-array-clone.md) |
| Om lagrings konton</br>Lägg till ett lagrings konto</br>Redigera ett lagrings konto</br>Ta bort ett lagringskonto |[Hantera lagrings konton för den virtuella StorSimple-matrisen](storsimple-virtual-array-manage-storage-accounts.md) |
| Om åtkomst kontroll poster</br>Lägga till eller ändra en åtkomst kontroll post </br>Ta bort en åtkomst kontroll post |[Hantera åtkomst kontroll poster för den virtuella StorSimple-matrisen](storsimple-virtual-array-manage-acrs.md) |
| Visa jobbinformation |[Hantera StorSimple virtuella mat ris jobb](storsimple-virtual-array-manage-jobs.md) |
| Konfigurera aviseringsinställningar</br>Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för den virtuella StorSimple-matrisen](storsimple-virtual-array-manage-alerts.md) |
| Ändra enhetens administratörs lösen ord |[Ändra administratörs lösen ordet för virtuell StorSimple-enhet](storsimple-virtual-array-change-device-admin-password.md) |
| Installera program uppdateringar |[Uppdatera din virtuella matris](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Du måste använda det [lokala webb gränssnittet](storsimple-ova-web-ui-admin.md) för följande uppgifter:
> 
> * [Hämta krypterings nyckeln för tjänst data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Skapa ett support paket](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Stoppa och starta om en virtuell matris](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Nästa steg
Information om webb gränssnittet och hur du använder det finns i [använda StorSimple-WEBBgränssnittet för att administrera din StorSimple-virtuella matris](storsimple-ova-web-ui-admin.md).