---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187374"
---
| **Gräns för identifierare** | **Gränserna** | **Kommentarer** |
| --- | --- | --- |
| Total kapacitet (inklusive moln) |Upp till 64 TB per virtuell enhet |Du kan redundansväxla en fullständig virtuell StorSimple-matris till en annan tom matris. Om du försöker återställa till samma enhet måste du kontrol lera att det finns tillräckligt med utrymme på enheten för att slutföra den här åtgärden. När du har överskridit 32 TB kan du inte återställa till samma enhet. |
| Maximalt antal autentiseringsuppgifter per enhet för lagrings konto |1 | |
| Maximalt antal volymer/resurser |16 | |
| Minsta storlek på en nivå resurs |500 GB | |
| Minsta storlek på en nivå volym |500 GB | |
| Maximal storlek för en nivå resurs |20 TB | |
| Maximal storlek på en nivå volym |5 TB | |
| Minsta storlek på en lokalt fäst resurs |50 GB | |
| Minsta storlek på en lokalt fäst volym |50 GB | |
| Maximal storlek på en lokalt fäst resurs |2 TB | |
| Maximal storlek på en lokalt fäst volym |200 GB | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomst kontroll poster per enhet |64 | |
| Maximalt antal säkerhets kopior som behålls av den virtuella enheten i *. backups* -mappen för fil Server |5 |Detta omfattar det senaste schemalagda (som genereras av standard säkerhets kopierings principen) och manuella säkerhets kopieringar. |
| Maximalt antal schemalagda säkerhets kopieringar som behålls av enheten |55 |30 dagliga säkerhets kopieringar<br>12 månatliga säkerhets kopieringar<br>13 års säkerhets kopieringar |
| Maximalt antal manuella säkerhets kopior som behålls av enheten |45 | |
| Maximalt antal filer per resurs för en fil Server |1 miljon |När du utför en återställning av enheten är återställnings tiden proportionell till antalet filer över alla resurser på enheten. |
| Maximalt antal filer per volym för en iSCSI-server |1 miljon | |
| Maximalt antal filer per virtuell matris |4 000 000 | |
| Återställa återställnings tid |Snabb återställning |Återställningen baseras på värme kartan och är beroende av volymens storlek.<br>Säkerhets kopierings åtgärder kan utföras medan en återställnings åtgärd pågår. |

