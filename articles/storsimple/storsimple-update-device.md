---
title: Uppdatera din StorSimple-enhet | Microsoft Docs
description: Förklarar hur du använder uppdaterings funktionen StorSimple för att installera uppdateringar och snabb korrigeringar för vanliga och underhålls läge.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956848"
---
# <a name="update-your-storsimple-8000-series-device"></a>Uppdatera din StorSimple 8000-serie enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](./index.yml).

## <a name="overview"></a>Översikt
Med funktionerna för StorSimple-uppdateringar kan du enkelt hålla din StorSimple-enhet uppdaterad. Beroende på uppdaterings typen kan du tillämpa uppdateringar på enheten via den klassiska Azure-portalen eller via Windows PowerShell-gränssnittet. I den här självstudien beskrivs uppdaterings typerna och hur du installerar dem.

Du kan använda två typer av enhets uppdateringar: 

* Vanliga uppdateringar (eller normalt läge)
* Uppdateringar av underhålls läge

Du kan installera regelbundna uppdateringar via den klassiska Azure-portalen eller Windows PowerShell. Du måste dock använda Windows PowerShell för att installera uppdateringar för underhålls läge. 

Varje uppdaterings typ beskrivs separat nedan.

### <a name="regular-updates"></a>Vanliga uppdateringar
Vanliga uppdateringar är icke-störande uppdateringar som kan installeras när enheten är i normalt läge. De här uppdateringarna tillämpas via den Microsoft Update webbplatsen för varje enhets styrenhet. 

> [!IMPORTANT]
> En redundansväxling kan inträffa under uppdaterings processen. Detta kommer dock inte att påverka systemets tillgänglighet eller åtgärd.
> 
> 

* Mer information om hur du installerar regelbundna uppdateringar via den klassiska Azure-portalen finns i [Installera vanliga uppdateringar via den klassiska Azure-portalen](#install-regular-updates-via-the-azure-classic-portal).
* Du kan också installera vanliga uppdateringar via Windows PowerShell för StorSimple. Mer information finns i [Installera vanliga uppdateringar via Windows PowerShell för StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Uppdateringar av underhålls läge
Uppdateringar av underhålls läge är störande uppdateringar, till exempel uppgraderingar av inbyggda CD-diskar. De här uppdateringarna kräver att enheten försätts i underhålls läge. Mer information finns i [steg 2: Ange underhålls läge](#step2). Du kan inte använda den klassiska Azure-portalen för att installera uppdateringar för underhålls läge. I stället måste du använda Windows PowerShell för StorSimple. 

Mer information om hur du installerar uppdateringar för underhålls läge finns i [installera uppdatering av underhålls läge via Windows PowerShell för StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Uppdateringar av underhålls läge måste tillämpas separat på varje kontrollant. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installera regelbundna uppdateringar via den klassiska Azure-portalen
Du kan använda den klassiska Azure-portalen för att tillämpa uppdateringar på din StorSimple-enhet.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installera regelbundna uppdateringar via Windows PowerShell för StorSimple
Du kan också använda Windows PowerShell för StorSimple för att tillämpa vanliga uppdateringar (normalt läge).

> [!IMPORTANT]
> Även om du kan installera vanliga uppdateringar med hjälp av Windows PowerShell för StorSimple rekommenderar vi starkt att du installerar regelbundna uppdateringar via den klassiska Azure-portalen. Från och med uppdatering 1 utförs för kontroller innan du installerar uppdateringar från portalen. De här för kontrollerna åsidosätter felen och säkerställer en smidigare upplevelse. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhålls läge via Windows PowerShell för StorSimple
Du använder Windows PowerShell för StorSimple för att tillämpa uppdateringar av underhålls läge på din StorSimple-enhet. Alla I/O-begäranden pausas i det här läget. Tjänster som icke-flyktigt RAM-minne (Random Access Memory) eller kluster tjänsten stoppas också. Båda styrenheterna startas om när du anger eller avslutar det här läget. När du avslutar det här läget kommer alla tjänster att återupptas och bör vara felfria. (Det kan ta några minuter.)

Om du behöver tillämpa uppdateringar av underhålls läge får du en avisering via den klassiska Azure-portalen att du har uppdateringar som måste vara installerade. Den här aviseringen innehåller instruktioner för hur du använder Windows PowerShell för StorSimple för att installera uppdateringarna. När du har uppdaterat enheten använder du samma procedur för att ändra enheten till normalt läge. Steg-för-steg-anvisningar finns i [steg 4: avsluta underhålls läge](#step4).

> [!IMPORTANT]
> * Innan du går in i underhålls läge kontrollerar du att båda styrenheterna är felfria genom att kontrol lera **maskin varu statusen** på sidan **Underhåll** i den klassiska Azure-portalen. Om kontrollanten inte är felfri kontaktar du Microsoft Support för nästa steg. Mer information finns på kontakt Microsoft Support. 
> * När du är i underhålls läge måste du först installera uppdateringen på en kontrollant och sedan på den andra styrenheten.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>Steg 1: Anslut till serie konsolen <a name="step1"></a>
Använd först ett program som till exempel SparaTillFil för att få åtkomst till serie konsolen. Följande procedur beskriver hur du använder SparaTillFil för att ansluta till serie konsolen.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>Steg 2: Ange underhålls läge <a name="step2"></a>
När du har anslutit till-konsolen kontrollerar du om det finns uppdateringar att installera och anger underhålls läget för att installera dem.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>Steg 3: installera dina uppdateringar <a name="step3"></a>
Installera sedan dina uppdateringar.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>Steg 4: avsluta underhålls läget <a name="step4"></a>
Slutligen avslutar du underhålls läge.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabb korrigeringar via Windows PowerShell för StorSimple
Till skillnad från uppdateringar för Microsoft Azure StorSimple installeras snabb korrigeringar från en delad mapp. Precis som med uppdateringar finns det två typer av snabb korrigeringar: 

* Vanliga snabb korrigeringar 
* Snabb korrigeringar för underhålls läge  

Följande procedurer beskriver hur du använder Windows PowerShell för StorSimple för att installera snabb korrigeringar för vanliga och underhålls läge.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Vad händer med uppdateringar om du utför en fabriks återställning av enheten?
Om en enhet återställs till fabriks inställningarna går alla uppdateringar förlorade. När fabriks återställnings enheten har registrerats och kon figurer ATS måste du manuellt installera uppdateringar via den klassiska Azure-portalen och/eller Windows PowerShell för StorSimple. Mer information om fabriks återställning finns i [återställa enheten till fabriks inställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [använder Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](./storsimple-8000-windows-powershell-administration.md).
* Lär dig mer om [att använda tjänsten StorSimple Manager för att administrera din StorSimple-enhet](./storsimple-8000-manager-service-administration.md).