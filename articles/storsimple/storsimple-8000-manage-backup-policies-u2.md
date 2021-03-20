---
title: Hantera säkerhets kopierings principer för StorSimple 8000-serien | Microsoft Docs
description: Förklarar hur du kan använda tjänsten StorSimple Enhetshanteraren för att skapa och hantera manuella säkerhets kopieringar, säkerhets kopierings scheman och kvarhållning av säkerhets kopior på en StorSimple 8000-serie enhet.
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515625"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Använd tjänsten StorSimple Enhetshanteraren i Azure Portal för att hantera säkerhets kopierings principer


## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder bladet StorSimple Enhetshanteraren service **Backup policy** för att kontrol lera säkerhets kopierings processer och kvarhållning av säkerhets kopior för dina StorSimple-volymer Det beskriver också hur du utför en manuell säkerhets kopiering.

När du säkerhetskopierar en volym kan du välja att skapa en lokal ögonblicks bild eller en ögonblicks bild av molnet. Om du säkerhetskopierar en lokalt fäst volym rekommenderar vi att du anger en ögonblicks bild av molnet. Att ta ett stort antal lokala ögonblicks bilder av en lokalt fixerad volym tillsammans med en data uppsättning som har mycket omsättnings utrymme leder till en situation där du snabbt kan få slut på det lokala utrymmet. Om du väljer att ta lokala ögonblicks bilder rekommenderar vi att du tar färre dagliga ögonblicks bilder för att säkerhetskopiera det senaste läget, behålla dem för en dag och sedan ta bort dem.

När du tar en moln ögonblicks bild av en lokalt fäst volym kopierar du bara ändrade data till molnet, där den dedupliceras och komprimeras.

## <a name="the-backup-policy-blade"></a>Bladet säkerhets kopierings princip

På bladet **säkerhets kopierings princip** för din StorSimple-enhet kan du hantera säkerhets kopierings principer och schemalägga lokala och molnbaserade ögonblicks bilder. Säkerhets kopierings principer används för att konfigurera säkerhets kopierings scheman och kvarhållning av säkerhets kopior för en samling volymer. Med säkerhets kopierings principer kan du ta en ögonblicks bild av flera volymer samtidigt. Det innebär att de säkerhets kopior som skapats av en säkerhets kopierings princip är kraschbaserade kopior.

I listan säkerhets kopierings principer kan du också filtrera befintliga säkerhets kopierings principer med hjälp av ett eller flera av följande fält:

* **Princip namn** – namnet som är kopplat till principen. De olika typerna av principer är:

  * Schemalagda principer som uttryckligen skapas av användaren.
  * Importerade principer, som ursprungligen skapades i StorSimple-Snapshot Manager. De har en tagg som beskriver StorSimple-Snapshot Manager värden som principerna har importer ATS från.

  > [!NOTE]
  > Principer för automatisk eller standard säkerhets kopiering är inte längre aktiverade när volymen skapas.

* **Senaste lyckade säkerhets kopiering** – datum och tid för den senaste lyckade säkerhets kopieringen som vidtogs med den här principen.

* **Nästa säkerhets kopiering** – datum och tid för nästa schemalagda säkerhets kopiering som kommer att initieras av den här principen.

* **Volymer** – volymerna som är associerade med principen. Alla volymer som är kopplade till en säkerhets kopierings policy grupperas tillsammans när säkerhets kopior skapas.

* **Scheman** – antalet scheman som är associerade med säkerhets kopierings principen.

De ofta använda åtgärder som du kan utföra för säkerhets kopierings principer är:

* Lägga till en säkerhetskopieringspolicy
* Lägg till eller ändra ett schema
* Lägga till eller ta bort en volym
* Ta bort en princip för säkerhets kopiering
* Gör en manuell säkerhets kopiering

## <a name="add-a-backup-policy"></a>Lägga till en säkerhetskopieringspolicy

Lägg till en säkerhets kopierings princip för att automatiskt schemalägga säkerhets kopieringar. När du först skapar en volym finns det ingen standard säkerhets kopierings princip som är kopplad till volymen. Du måste lägga till och tilldela en säkerhets kopierings princip för att skydda volym data.

Utför följande steg i Azure Portal för att lägga till en princip för säkerhets kopiering för din StorSimple-enhet. När du har lagt till principen kan du definiera ett schema (se [lägga till eller ändra ett schema](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Lägg till eller ändra ett schema

Du kan lägga till eller ändra ett schema som är kopplat till en befintlig säkerhets kopierings princip på din StorSimple-enhet. Utför följande steg i Azure Portal för att lägga till eller ändra ett schema.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Lägga till eller ta bort en volym

Du kan lägga till eller ta bort en volym som har tilldelats till en säkerhets kopierings princip på din StorSimple-enhet. Utför följande steg i Azure Portal för att lägga till eller ta bort en volym.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhets kopiering

Utför följande steg i Azure Portal för att ta bort en säkerhets kopierings princip på din StorSimple-enhet.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Gör en manuell säkerhets kopiering

Utför följande steg i Azure Portal för att skapa en säkerhets kopia på begäran (manuell) för en enskild volym.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

