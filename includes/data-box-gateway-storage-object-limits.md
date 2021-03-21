---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582639"
---
Här följer storleken på de Azure-objekt som kan skrivas. Se till att alla filer som överförs följer dessa gränser.

| Objekt typ för Azure | Uppladdnings gräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockera BLOB        | ~ 4,75 TB                                                 |
| Sid-BLOB         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |
| Azure Files         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |

> [!IMPORTANT]
> Att skapa filer (oberoende av lagrings typen) tillåts upp till 5 TB. Men om du skapar en fil vars storlek är större än den uppladdnings gräns som anges i tabellen ovan överförs inte filen. Du måste ta bort filen manuellt för att frigöra utrymme.