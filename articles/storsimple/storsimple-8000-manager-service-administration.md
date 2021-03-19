---
title: StorSimple Enhetshanteraren tjänst administration | Microsoft Docs
description: Lär dig hur du hanterar din StorSimple-enhet med hjälp av tjänsten StorSimple Enhetshanteraren i Azure Portal.
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
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656829"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Använd tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet

## <a name="overview"></a>Översikt

I den här artikeln beskrivs StorSimple-Enhetshanteraren tjänst gränssnittet, inklusive hur du ansluter till det, de olika alternativen är tillgängliga och länkar till de olika arbets flöden som kan utföras via det här användar gränssnittet. Den här vägledningen gäller för båda. den fysiska StorSimple-enheten och moln installationen.

När du har läst den här artikeln får du lära dig att:

* Ansluta till StorSimple Enhetshanteraren-tjänsten
* Administrera din StorSimple-enhet via tjänsten StorSimple Enhetshanteraren

## <a name="connect-to-storsimple-device-manager-service"></a>Ansluta till StorSimple Enhetshanteraren-tjänsten

Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör så här för att ansluta till StorSimple-Enhetshanteraren tjänsten:

#### <a name="to-connect-to-the-service"></a>Så här ansluter du till tjänsten
1. Navigera till [https://portal.azure.com/](https://portal.azure.com/) .
2. Logga in på Microsoft Azure-portalen (som finns längst upp till höger i fönstret) med dina Microsoft-konto autentiseringsuppgifter.
3. Bläddra nedåt i det vänstra navigerings fönstret för att få åtkomst till StorSimple Enhetshanteraren-tjänsten.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrera StorSimple-enheten med StorSimple Enhetshanteraren-tjänsten

I följande tabell visas en översikt över alla vanliga hanterings uppgifter och komplexa arbets flöden som kan utföras i StorSimple-Enhetshanteraren tjänstens användar gränssnitt. De här uppgifterna organiseras baserat på de GRÄNSSNITTs blad som de initieras på.

Klicka på lämplig procedur i tabellen om du vill ha mer information om varje arbets flöde.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Enhetshanteraren arbets flöden

| Om du vill göra det här... | Använd den här proceduren. |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta tjänst registrerings nyckel</br>Återskapa tjänst registrerings nyckel |[Distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-8000-manage-service.md) |
| Visa aktivitets loggarna |[Använda StorSimple för Enhetshanteraren tjänsten](storsimple-8000-service-dashboard.md) |
| Ändra krypterings nyckeln för tjänst data</br>Visa åtgärds loggarna |[Använd instrument panelen för StorSimple-Enhetshanteraren-tjänsten](storsimple-8000-service-dashboard.md) |
| Inaktivera en enhet</br>Ta bort en enhet |[Inaktivera eller ta bort en enhet](storsimple-8000-deactivate-and-delete-device.md) |
| Läs mer om haveri beredskap och enhets växling vid fel</br>Redundans till en fysisk enhet</br>Redundans till en virtuell enhet</br>Katastrof återställning av affärs kontinuitet (BCDR) |[Redundansväxling och haveri beredskap för din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista säkerhets kopior för en volym</br>Välj en säkerhets kopierings uppsättning</br>Ta bort en säkerhets kopia |[Hantera säkerhetskopior](storsimple-8000-manage-backup-catalog.md) |
| Klona en volym |[Klona en volym](storsimple-8000-clone-volume-u2.md) |
| Återställa en säkerhets kopia |[Återställa en säkerhets kopia](storsimple-8000-restore-from-backup-set-u2.md) |
| Om lagrings konton</br>Lägg till ett lagrings konto</br>Redigera ett lagrings konto</br>Ta bort ett lagringskonto</br>Nyckel rotation för lagrings konton |[Hantera lagringskonton](storsimple-8000-manage-storage-accounts.md) |
| Om bandbredds-mallar</br>Lägg till en bandbredds mall</br>Redigera en bandbredds mall</br>Ta bort en bandbredds mall</br>Använd en mall för standard bandbredd</br>Skapa en mall för bandbredd som startar vid en viss tidpunkt |[Hantera bandbreddsmallar](storsimple-8000-manage-bandwidth-templates.md) |
| Om åtkomst kontroll poster</br>Skapa en åtkomst kontroll post</br>Redigera en åtkomst kontroll post</br>Ta bort en åtkomst kontroll post |[Hantera åtkomst kontroll poster](storsimple-8000-manage-acrs.md) |
| Visa jobbinformation</br>Avbryta ett jobb |[Hantera jobb](storsimple-8000-manage-jobs-u2.md) |
| Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera StorSimple-aviseringar](storsimple-8000-manage-alerts.md) |
| Skapa övervaknings diagram |[Övervaka din StorSimple-enhet](./storsimple-8000-monitor-device.md) |
| Lägg till en volym behållare</br>Ändra en volym behållare</br>Ta bort en volym behållare |[Hantera volymcontainrar](storsimple-8000-manage-volume-containers.md) |
| Lägg till en volym</br>Ändra en volym</br>Koppla från en volym</br>Ta bort en volym</br>Övervaka en volym |[Hantera volymer](storsimple-8000-manage-volumes-u2.md) |
| Ändra enhets inställningar</br>Ändra tids inställningar</br>Ändra inställningar för DNS.md</br>Konfigurera nätverks gränssnitt |[Ändra enhets konfigurationen för din StorSimple-enhet](storsimple-8000-modify-device-config.md) |
| Visa webbproxy-inställningar |[Konfigurera webbproxy för din enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra enhetens administratörs lösen ord</br>Ändra StorSimple Snapshot Manager-lösenord |[Ändra StorSimple-lösenord](storsimple-8000-change-passwords.md) |
| Konfigurera fjärrhantering |[Fjärrans luta till din StorSimple-enhet](storsimple-8000-remote-connect.md) |
| Konfigurera aviseringsinställningar |[Visa och hantera StorSimple-aviseringar](storsimple-8000-manage-alerts.md) |
| Konfigurera CHAP för din StorSimple-enhet |[Konfigurera CHAP för din StorSimple-enhet](./storsimple-8000-configure-chap.md) |
| Lägga till en säkerhetskopieringspolicy</br>Lägg till eller ändra ett schema</br>Ta bort en princip för säkerhets kopiering</br>Gör en manuell säkerhets kopiering</br>Skapa en anpassad säkerhets kopierings princip med flera volymer och scheman |[Hantera säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md) |
| Stoppa enhets styrenheter</br>Starta om enhets styrenheter</br>Stänga av enhets styrenheter</br>Återställa enheten till fabriks inställningarna</br>(Ovan gäller endast för lokala enheter) |[Hantera StorSimple enhets styrenhet](storsimple-8000-manage-device-controller.md) |
| Lär dig mer om StorSimple maskin varu komponenter</br>Övervaka maskinvarustatus</br>(Ovan gäller endast för lokala enheter) |[Övervaka maskin varu komponenter](storsimple-8000-monitor-hardware-status.md) |
| Skapa ett support paket |[Skapa och hantera ett support paket](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Installera program uppdateringar |[Uppdatera enheten](storsimple-update-device.md) |

## <a name="next-steps"></a>Nästa steg

Om du får problem med den dagliga driften av din StorSimple-enhet eller med någon av dess maskin varu komponenter, se:

* [Felsöka med hjälp av diagnostik-verktyget](storsimple-8000-diagnostics.md)
* [Använda indikatorer för StorSimple-övervakning](storsimple-monitoring-indicators.md)

Om du inte kan lösa problemen och behöver skapa en tjänstbegäran, se [kontakt Microsoft Support](storsimple-8000-contact-microsoft-support.md).