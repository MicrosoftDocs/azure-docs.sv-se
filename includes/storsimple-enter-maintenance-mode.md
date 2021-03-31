---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187370"
---
#### <a name="to-enter-maintenance-mode"></a>Ange underhålls läge
1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**.
2. Ange lösen ordet. Standard lösen ordet är **Password1**.
3. Skriv i kommando tolken
   
     `Enter-HcsMaintenanceMode`
4. Ett varnings meddelande visas som talar om att underhålls läget avbryter alla I/O-begäranden och ger anslutningen till den klassiska Azure-portalen, och du uppmanas att bekräfta. Skriv **Y** för att ange underhålls läge.
   
    Båda styrenheterna kommer att startas om. När omstarten är klar visas ett nytt meddelande som anger att enheten är i underhålls läge.

