---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061162"
---
När du konfigurerar Azure-resurser för den här snabb starten kopieras en kort video om ett parkerings parti till den virtuella Linux-datorn i Azure som används som IoT Edge enhet. Den här video filen används för att simulera en Live-ström för den här självstudien.

Öppna ett program som [VLC Media Player](https://www.videolan.org/vlc/), Välj CTRL + N och klistra in [den här länken](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) till parkerings sidans video för att starta uppspelningen. Vid ungefär 5 sekunders märket flyttas en vit bil genom parkerings partiet.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Utför följande steg för att använda video analys i real tid för IoT Edge för att identifiera rörelsen i bilen och spela in ett videoklipp som börjar runt det 5-andra märket.
