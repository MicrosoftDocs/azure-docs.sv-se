---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401077"
---
Följande varningar gäller för data när de flyttas till Azure.

- Vi rekommenderar att fler än en enhet inte skriver till samma behållare.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB eller en fil) i molnet med samma namn som det objekt som ska kopieras, kommer enheten att skriva över filen i molnet.
- En tom katalogpartition (utan några filer) som skapas under delade mappar överförs inte till BLOB-behållarna.
- Du kan kopiera data genom att dra och släppa i Utforskaren eller via kommandoraden. Om den sammanlagda storleken på de filer som kopieras är större än 10 GB rekommenderar vi att du använder ett masskopieringsprogram som Robocopy eller rsync. Verktygen för masskopiering gör om kopieringsåtgärden vid tillfälliga fel och har fler återhämtningsfunktioner.
- Om resursen som är kopplad till Azure Storage-behållaren överför blobbar som inte matchar den typ av blobbar som definierats för resursen vid tidpunkten för skapandet, uppdateras inte sådana blobbar. Du kan till exempel skapa en blockblobresurs på enheten. Associera resursen med en befintlig molncontainer som innehåller sidblobar. Uppdatera den här resursen för att ladda ned filerna. Ändra några av de uppdaterade filerna som redan lagras som sidblobar i molnet. Då uppstår uppladdningsfel.
- När en fil har skapats i resurserna stöds inte namnbytet för filen.
- När en fil tas bort från en resurs försvinner inte posten i lagringskontot.
- Om du använder rsync för att kopiera data, `rsync -a` stöds inte alternativet.

