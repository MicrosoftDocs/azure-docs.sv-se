---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88655042"
---
Här följer storleken på de Azure-objekt som kan skrivas. Se till att alla filer som överförs följer dessa gränser.

| Objekt typ för Azure | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |
| Azure Files        | 1 TiB                                                      |
| Hanterade diskar     | 4 TiB <br> Mer information om storlek och begränsningar finns i: <li>[Skalbarhets mål för standard-SSD](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Skalbarhets mål för Premium-SSD](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Skalbarhets mål för standard hård diskar](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Priser och fakturering av hanterade diskar](../articles/virtual-machines/disks-types.md#billing)</li>  
