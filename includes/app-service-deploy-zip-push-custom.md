---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018465"
---
## <a name="deployment-customization"></a>Distributions anpassning

Distributions processen förutsätter att. zip-filen som du push-överför innehåller en app som är klar att köra. Som standard körs inga anpassningar. Om du vill aktivera samma Bygg processer som du får med kontinuerlig integrering lägger du till följande i dina program inställningar:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

När du använder. zip push-distribution är den här inställningen **falskt** som standard. Standardvärdet är **True** för kontinuerlig integrerings distributioner. När värdet är **True** används dina distributions inställningar under distributionen. Du kan konfigurera dessa inställningar antingen som appinställningar eller i en. distributions konfigurations fil som finns i roten av din. zip-fil. Mer information finns i avsnittet [om inställningar för lagring och distribution](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) i distributions referensen.