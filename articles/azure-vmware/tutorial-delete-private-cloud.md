---
title: Självstudie – ta bort ett privat moln för Azure VMware-lösningen
description: Lär dig hur du tar bort ett privat moln i Azure VMware-lösningen som du inte längre behöver.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462107"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Självstudie: ta bort ett privat moln för Azure VMware-lösningen

Om du har ett privat moln i Azure VMware-lösningen som du inte längre behöver kan du ta bort det. Det privata molnet innehåller en isolerad nätverks domän, ett eller fler etablerade vSphere-kluster på dedikerade server värdar och flera virtuella datorer (VM). När du tar bort ett privat moln tas alla virtuella datorer, deras data och kluster bort. De dedikerade Azure VMware-lösningarna rensas på ett säkert sätt och returneras till den kostnads fria poolen. Det tillhandahållna nätverks adress utrymmet tas också bort.  

> [!CAUTION]
> Borttagning av det privata molnet är en åtgärd som inte kan ångras. När det privata molnet har tagits bort går det inte att återställa data eftersom de avslutar alla aktiva arbets belastningar och komponenter och förstör alla privata moln data och konfigurations inställningar, inklusive offentliga IP-adresser.

## <a name="prerequisites"></a>Förutsättningar

Om du behöver de virtuella datorerna och deras data senare, se till att säkerhetskopiera data innan du tar bort det privata molnet.  Det finns inget sätt att återställa de virtuella datorerna och deras data.


## <a name="delete-the-private-cloud"></a>Ta bort det privata molnet

1. Öppna Azure VMware-lösningar-konsolen i [Azure Portal](https://portal.azure.com).

2. Välj det privata moln som ska tas bort.
 
3. Ange namnet på det privata molnet och välj **Ja**. 

>[!NOTE]
>Borttagnings processen tar några timmar att slutföra.  
