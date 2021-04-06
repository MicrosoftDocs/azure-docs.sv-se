---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582666"
---
Följande varningar gäller för data när de flyttas till Azure.

- Vi rekommenderar att fler än en enhet inte skriver till samma behållare.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB eller en fil) i molnet med samma namn som det objekt som kopieras, kommer enheten att skriva över filen i molnet.
- En tom katalogpartition (utan några filer) som skapas under delade mappar överförs inte till BLOB-behållarna.
- Du kan kopiera data med hjälp av dra och släpp med Utforskaren eller via kommando raden. Om den sammanställda storleken på filer som kopieras är större än 10 GB rekommenderar vi att du använder ett Mass kopierings program som `Robocopy` eller `rsync` . Verktyget för Mass kopiering gör om kopierings åtgärden för återkommande fel och ger ytterligare återhämtnings kapacitet.
- Om resursen som är kopplad till Azure Storage-behållaren överför blobbar som inte matchar den typ av blobbar som definierats för resursen vid tidpunkten för skapandet, uppdateras inte sådana blobbar. Om du till exempel skapar en Block-Blob-resurs på enheten, associerar resursen med en befintlig moln behållare som har Page blobbar, uppdaterar den resursen för att ladda ned filerna och ändrar sedan några av de uppdaterade filerna som redan är lagrade som Page blobbar i molnet, så visas uppladdnings felen.
- När en fil har skapats i resurserna stöds inte namnbytet för filen.
- När en fil tas bort från en resurs försvinner inte posten i lagringskontot.
- Om du använder `rsync` för att kopiera data stöds inte alternativet `rsync -a`.