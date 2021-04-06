---
title: Hantera volym behållare för enheter med StorSimple 8000-serien
description: Förklarar hur du kan använda sidan StorSimple Enhetshanteraren service Volume containers för att lägga till, ändra eller ta bort en volym behållare.
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
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545393"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Hantera StorSimple-volymcontainrar med StorSimple Device Manager-tjänsten

## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du använder tjänsten StorSimple Enhetshanteraren för att skapa och hantera StorSimple-volym behållare.

En volym behållare i en Microsoft Azure StorSimple-enhet innehåller en eller flera volymer som delar inställningar för lagrings konto, kryptering och bandbredds användning. En enhet kan ha flera volym behållare för alla dess volymer. 

En volym behållare har följande attribut:

* **Volymer** – de skiktade eller lokalt fästa StorSimple-volymer som finns i volym containern. 
* **Kryptering** – en krypterings nyckel som kan definieras för varje volym behållare. Den här nyckeln används för att kryptera data som skickas från din StorSimple-enhet till molnet. En enklassad AES-256-bitars nyckel används med den användare som anges. För att skydda dina data rekommenderar vi att du alltid aktiverar kryptering av moln lagring.
* **Lagrings konto** – det Azure Storage-konto som används för att lagra data. Alla volymer som finns i en volym behållare delar det här lagrings kontot. Du kan välja ett lagrings konto från en befintlig lista eller skapa ett nytt konto när du skapar volym containern och sedan ange autentiseringsuppgifter för det kontot.
* **Moln bandbredd** – bandbredden som används av enheten när data från enheten skickas till molnet. Du kan framtvinga en bandbredds kontroll genom att ange ett värde mellan 1 Mbit/s och 1 000 Mbit/s när du skapar den här behållaren. Om du vill att enheten ska förbruka all tillgänglig bandbredd ställer du in det här fältet på **obegränsat**. Du kan också skapa och använda en bandbredds mall för att allokera bandbredd baserat på schema.

Följande procedurer beskriver hur du använder bladet StorSimple **Volume containers** för att utföra följande vanliga åtgärder:

* Lägg till en volym behållare
* Ändra en volym behållare
* Ta bort en volym behållare

## <a name="add-a-volume-container"></a>Lägg till en volym behållare
Utför följande steg för att lägga till en volym behållare.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Ändra en volym behållare
Utför följande steg för att ändra en volym behållare.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Ta bort en volym behållare
Det finns volymer i en volym behållare. Den kan bara tas bort om alla volymer som finns i den först tas bort. Utför följande steg för att ta bort en volym behållare.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md). 
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

