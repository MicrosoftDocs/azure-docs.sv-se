---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94574273"
---
> [!IMPORTANT]
> Cosmos DB-instansen skapas i en Microsoft-hanterad resurs grupp i __din prenumeration__, tillsammans med de resurser som krävs. Det innebär att du debiteras för den här Cosmos DB-instansen. Den hanterade resurs gruppen har namnet i formatet `<AML Workspace Resource Group Name><GUID>` . Om din Azure Machine Learning-arbetsyta använder en privat slut punkt skapas även ett virtuellt nätverk för Cosmos DB-instansen. Det här virtuella nätverket används för att skydda kommunikationen mellan Cosmos DB och Azure Machine Learning.
> 
> * __Ta inte bort resurs gruppen__ som innehåller den här Cosmos DB-instansen eller någon av de resurser som skapas automatiskt i den här gruppen. Om du behöver ta bort resurs gruppen, Cosmos DB instans osv., måste du ta bort arbets ytan Azure Machine Learning som använder den. Resurs gruppen, Cosmos DB instansen och andra automatiskt skapade resurser tas bort när den associerade arbets ytan tas bort.
> * [__Standardenheterna för programbegäran__](../articles/cosmos-db/request-units.md) för Cosmos DB-kontot anges till __8000__.
> * Du __kan inte ange ditt eget VNet för användning med Cosmos DB-instansen__ som skapas. Du __kan inte heller ändra det virtuella nätverket__. Du kan till exempel inte ändra det IP-adressintervall som används.