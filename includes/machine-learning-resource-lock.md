---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204478"
---
Med Azure kan du placera _Lås_ på resurser, så att de inte kan tas bort eller är skrivskyddade. __Att låsa en resurs kan leda till oväntade resultat.__ Vissa åtgärder som inte verkar ändra resursen behöver inte ha åtgärder som blockeras av låset. 

Om du till exempel använder ett borttagnings lås till resurs gruppen för din arbets yta kommer du att förhindra skalnings åtgärder för Azure ML-beräknings kluster.

Mer information om hur du låser resurser finns i [låsa resurser för att förhindra oväntade ändringar](../articles/azure-resource-manager/management/lock-resources.md).