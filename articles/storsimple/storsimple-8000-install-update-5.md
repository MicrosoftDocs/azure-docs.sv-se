---
title: Installera uppdatering 5 på StorSimple 8000-serien het | Microsoft Docs
description: Förklarar hur du installerar StorSimple 8000-serien uppdatering 5 på din StorSimple 8000-serie enhet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513176"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installera uppdatering 5 på din StorSimple-enhet

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du installerar uppdatering 5 på en StorSimple-enhet som kör en tidigare program varu version via Azure Portal och använder snabb korrigerings metoden. Snabb korrigerings metoden används när du försöker installera uppdatering 5 på en enhet som kör för hands version 3-versioner. Snabb korrigerings metoden används också när en gateway har kon figurer ATS på ett annat nätverks gränssnitt än DATA 0 på StorSimple-enheten och du försöker uppdatera från en program varu version som är före uppdatering 1.

Uppdatering 5 innehåller enhets program vara, Storport och Spaceport, OS-säkerhetsuppdateringar och OS-uppdateringar och uppdateringar av inbyggd disk.  Enhetens program vara, Spaceport, Storport, säkerhet och andra OS-uppdateringar är icke-störande uppdateringar. Icke-störande eller regelbundna uppdateringar kan tillämpas via Azure Portal eller via snabb korrigerings metoden. Diskens inbyggda uppdateringar är störande uppdateringar och tillämpas när enheten är i underhålls läge via snabb korrigerings metoden med hjälp av Windows PowerShell-gränssnittet på enheten.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och bör installeras omedelbart. Mer information finns i [uppdatering 5 viktig](storsimple-update5-release-notes.md)information.
> * En uppsättning manuella och automatiska för kontroller görs före installationen för att fastställa enhetens hälso status vad gäller maskin varu tillstånd och nätverks anslutning. Dessa för kontroller utförs endast om du tillämpar uppdateringarna från Azure Portal.
> * Vi rekommenderar starkt att du när du uppdaterar en enhet som kör versioner före uppdatering 3, installerar uppdateringarna med hjälp av snabb korrigerings metoden. [Logga ett support ärende](storsimple-8000-contact-microsoft-support.md)om det uppstår några problem.
> * Vi rekommenderar att du installerar program varan och andra regelbundna uppdateringar via Azure Portal. Du bör endast gå till Windows PowerShell-gränssnittet för enheten (för att installera uppdateringar) om kontrollen för för uppdateringen av gatewayen Miss lyckas i portalen. Beroende på vilken version du uppdaterar från kan uppdateringarna ta 4 timmar (eller senare) att installeras. Uppdatering av underhålls läge måste installeras via Windows PowerShell-gränssnittet på enheten. Som uppdateringar av underhålls läge är störande uppdateringar, vilket leder till en tids fördröjning för enheten.
> * Om du kör det valfria StorSimple-Snapshot Manager måste du kontrol lera att du har uppgraderat Snapshot Manager versionen till uppdatering 5 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Installera uppdatering 5 via Azure Portal
Utför följande steg för att uppdatera enheten till [uppdatering 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft hämtar ytterligare diagnostikinformation från enheten. Det innebär att när vårt drift team identifierar enheter som har problem är vi bättre utrustade med att samla in information från enheten och diagnostisera problem.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Kontrol lera att enheten kör **StorSimple 8000-serien uppdatering 5 (6.3.9600.17845)**. **Datum för senaste uppdatering** ska ändras.

Nu ser du att underhålls läges uppdateringar är tillgängliga (det här meddelandet kan visas i upp till 24 timmar efter att du har installerat uppdateringarna). Stegen för att installera uppdatering av underhålls läge beskrivs i nästa avsnitt.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Installera uppdatering 5 som en snabb korrigering

De program varu versioner som kan uppgraderas med hjälp av snabb korrigerings metoden är:

* Uppdatering 0,1, 0,2, 0,3
* Uppdatering 1, 1,1, 1,2
* Uppdatering 2, 2,1, 2,2
* Uppdatering 3, 3,1
* Uppdatering 4

> [!NOTE] 
> Den rekommenderade metoden för att installera uppdatering 5 är via Azure Portal när du försöker uppdatera från Update 3 och senare. Använd den här proceduren när du uppdaterar en enhet som kör versioner före uppdatering 3. Du kan också använda den här proceduren om du inte felsöker gatewayen vid försök att installera uppdateringarna via Azure Portal. Kontrollen Miss lyckas när du har en gateway som har tilldelats ett nätverks gränssnitt som inte är av DATA 0 och enheten kör en tidigare program version än uppdatering 1.

Snabb korrigerings metoden omfattar följande tre steg:

1. Hämta snabb korrigeringarna från Microsoft Update katalogen.
2. Installera och verifiera snabb korrigeringarna i vanlig läge.
3. Installera och verifiera snabb korrigeringen för underhålls läge.

#### <a name="download-updates-for-your-device"></a>Hämta uppdateringar för din enhet

Du måste ladda ned och installera följande snabb korrigeringar i den angivna ordningen och de föreslagna mapparna:

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Program uppdatering<br> Hämta både _HcsSoftwareUpdate.exe_ och _CisMSDAgent.exe_ |Återkommande <br></br>Icke-störande |~ 25 minuter |FirstOrderUpdate|

Om du uppdaterar från en enhet som kör uppdatering 4 behöver du bara installera de kumulativa uppdateringarna för operativ systemet som andra order uppdateringar.

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| blankett. |KB4025336 |Paket för ackumulerade OS-uppdateringar <br> Hämta Windows Server 2012 R2 version |Återkommande <br></br>Icke-störande |- |SecondOrderUpdate|

Om du installerar från en enhet som kör uppdatering 3 eller tidigare installerar du följande utöver de kumulativa uppdateringarna.

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 2b. |KB4011841 <br> KB4011842 |Uppdatering av LSI-drivrutin och inbyggd program vara <br> Uppdatering av USM POLICYEGENSKAPER inbyggd program vara (version 3,38) |Återkommande <br></br>Icke-störande |~ 3 timmar <br> (inkluderar 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Paket för säkerhets uppdateringar av operativ system <br> Hämta Windows Server 2012 R2 version |Återkommande <br></br>Icke-störande |- |SecondOrderUpdate|
| -. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paket för operativ Systems uppdatering <br> Hämta Windows Server 2012 R2 version |Återkommande <br></br>Icke-störande |- |SecondOrderUpdate|


Du kan också behöva installera uppdateringar av inbyggd disk på alla uppdateringar som visas i föregående tabeller. Du kan kontrol lera om du behöver diskens inbyggda program varu uppdateringar genom att köra `Get-HcsFirmwareVersion` cmdleten. Om du kör dessa versioner av inbyggd program vara:,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N003` `0107` , behöver du inte installera dessa uppdateringar.

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Inbyggd disk |Underhåll <br></br>Störande |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Om du uppdaterar från uppdatering 4, är den totala installations tiden nära 4 timmar.
> * Innan du använder den här proceduren för att tillämpa uppdateringen måste du kontrol lera att både enhetens styrenheter är online och att alla maskin varu komponenter är felfria.

Utför följande steg för att ladda ned och installera snabb korrigeringarna.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [uppdatering 5-versionen](storsimple-update5-release-notes.md).

