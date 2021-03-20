---
title: Installera uppdatering 4 på StorSimple 8000-serien het | Microsoft Docs
description: Förklarar hur du installerar StorSimple 8000-serien uppdatering 4 på din StorSimple 8000-serie enhet.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514313"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Installera uppdatering 4 på din StorSimple-enhet

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du installerar uppdatering 4 på en StorSimple-enhet som kör en tidigare program varu version via Azure Portal och använder snabb korrigerings metoden. Snabb korrigerings metoden används när en gateway har kon figurer ATS på ett annat nätverks gränssnitt än DATA 0 på StorSimple-enheten och du försöker uppdatera från en program varu version som är för uppdatering 1.

Uppdatering 4 innehåller enhets program vara, USM POLICYEGENSKAPER inbyggd program vara, LSI driv rutin och inbyggd program vara, Storport och Spaceport, OS-säkerhetsuppdateringar och en värd för andra OS-uppdateringar.  Enhetens program vara, USM POLICYEGENSKAPER-programvara, Spaceport, Storport och andra OS-uppdateringar är icke-störande uppdateringar. Icke-störande eller regelbundna uppdateringar kan tillämpas via Azure Portal eller via snabb korrigerings metoden. Diskens inbyggda program uppdateringar är störande uppdateringar och kan bara tillämpas via snabb korrigerings metoden med hjälp av Windows PowerShell-gränssnittet på enheten.

> [!IMPORTANT]
> * En uppsättning manuella och automatiska för kontroller görs före installationen för att fastställa enhetens hälso status vad gäller maskin varu tillstånd och nätverks anslutning. Dessa för kontroller utförs endast om du tillämpar uppdateringarna från Azure Portal.
> * Vi rekommenderar att du installerar program varan och andra regelbundna uppdateringar via Azure Portal. Du bör endast gå till Windows PowerShell-gränssnittet för enheten (för att installera uppdateringar) om kontrollen för för uppdateringen av gatewayen Miss lyckas i portalen. Beroende på vilken version du uppdaterar från kan uppdateringarna ta 4 timmar (eller senare) att installeras. Uppdateringar av underhålls läge måste också installeras via Windows PowerShell-gränssnittet på enheten. När uppdateringar av underhålls läge är störande uppdateringar, kommer dessa att leda till en tids fördröjning för enheten.
> * Om du kör den valfria StorSimple Snapshot Manager se till att du har uppgraderat Snapshot Manager-versionen till uppdatering 4 innan du uppdaterar enheten.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Installera uppdatering 4 via Azure Portal
Utför följande steg för att uppdatera enheten till [uppdatering 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft hämtar ytterligare diagnostikinformation från enheten. Det innebär att när vårt drift team identifierar enheter som har problem är vi bättre utrustade med att samla in information från enheten och diagnostisera problem. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Kontrol lera att enheten kör **StorSimple 8000-serien uppdatering 4 (6.3.9600.17820)**. **Datumet för senaste uppdatering** bör också ändras.

* Nu ser du att underhålls läges uppdateringar är tillgängliga (det här meddelandet kan visas i upp till 24 timmar efter att du har installerat uppdateringarna). Uppdateringar av underhålls läge är avbrotts bara uppdateringar som leder till enhets stillestånd och kan endast tillämpas via Windows PowerShell-gränssnittet på enheten.

* Hämta uppdateringar för underhålls läge genom att följa stegen i [Hämta snabb korrigeringar](#to-download-hotfixes) för att söka efter och ladda ned KB4011837, som installerar diskens inbyggda uppdateringar (de andra uppdateringarna bör redan installeras av nu). Följ stegen i [Installera och verifiera snabb korrigeringar för underhålls läge](#to-install-and-verify-maintenance-mode-hotfixes) för att installera uppdatering av underhålls läge.

## <a name="install-update-4-as-a-hotfix"></a>Installera uppdatering 4 som en snabb korrigering
Den rekommenderade metoden för att installera uppdatering 4 är via Azure Portal.

Använd den här proceduren om du inte felsöker gatewayen vid försök att installera uppdateringarna via Azure Portal. Kontrollen Miss lyckas eftersom du har en gateway som har tilldelats ett nätverks gränssnitt som inte är av DATA 0 och enheten kör en program varu version före uppdatering 1.

De program varu versioner som kan uppgraderas med hjälp av snabb korrigerings metoden är:

* Uppdatering 0,1, 0,2, 0,3
* Uppdatering 1, 1,1, 1,2
* Uppdatering 2, 2,1, 2,2
* Uppdatering 3, 3,1


Snabb korrigerings metoden omfattar följande tre steg:

1. Hämta snabb korrigeringarna från Microsoft Update katalogen.
2. Installera och verifiera snabb korrigeringarna i vanlig läge.
3. Installera och verifiera snabb korrigeringen för underhålls läge.

#### <a name="download-updates-for-your-device"></a>Hämta uppdateringar för din enhet

Du måste ladda ned och installera följande snabb korrigeringar i den angivna ordningen och de föreslagna mapparna:

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid |Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Program uppdatering |Återkommande <br></br>Icke-störande |~ 25 minuter |FirstOrderUpdate|
| blankett. |KB4011841 <br> KB4011842 |Uppdatering av LSI-drivrutin och inbyggd program vara <br> Uppdatering av USM POLICYEGENSKAPER inbyggd program vara (version 3,38) |Återkommande <br></br>Icke-störande |~ 3 timmar <br> (inkluderar 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2b. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Paket för säkerhets uppdateringar av operativ system <br> Hämta Windows Server 2012 R2 |Återkommande <br></br>Icke-störande |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Paket för operativ Systems uppdatering <br> Hämta Windows Server 2012 R2 |Återkommande <br></br>Icke-störande |- |SecondOrderUpdate|

Du kan också behöva installera uppdateringar av inbyggd disk på alla uppdateringar som visas i föregående tabeller. Du kan kontrol lera om du behöver diskens inbyggda program varu uppdateringar genom att köra `Get-HcsFirmwareVersion` cmdleten. Om du kör dessa versioner av inbyggd program vara:,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N002` `0106` , behöver du inte installera dessa uppdateringar.

| Beställning | KB | Beskrivning | Uppdateringstyp | Installationstid | Installera i mapp|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Inbyggd disk |Underhåll <br></br>Störande |~ 30 minuter | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Den här proceduren behöver bara utföras en gång för att installera uppdatering 4. Du kan använda Azure Portal för att tillämpa efterföljande uppdateringar.
> * Om du uppdaterar från Update 3 eller 3,1 är den totala installations tiden nära 4 timmar.
> * Innan du använder den här proceduren för att tillämpa uppdateringen måste du kontrol lera att både enhetens styrenheter är online och att alla maskin varu komponenter är felfria.

Utför följande steg för att ladda ned och installera snabb korrigeringarna.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [uppdatering 4-versionen](storsimple-update4-release-notes.md).

