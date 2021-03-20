---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187730"
---
1. På bladet Inställningar för den resurs, resursgrupp eller prenumeration som du vill låsa upp väljer du **Lås**.
   
      ![Välj Lås](./media/resource-manager-lock-resources/select-lock.png)
2. Om du vill lägga till ett lås väljer du **Lägg till**. Om du vill skapa ett lås på en överordnad nivå väljer du överordnad. Den markerade resursen ärver låset från det överordnade objektet. Du kan till exempel låsa resurs gruppen om du vill använda ett lås till alla dess resurser.
   
      ![Lägg till lås](./media/resource-manager-lock-resources/add-lock.png) 
3. Ge låset ett namn och en Lås nivå. Om du vill kan du lägga till anteckningar som beskriver låset.
   
      ![Ange lås](./media/resource-manager-lock-resources/set-lock.png) 
4. Om du vill ta bort låset väljer du ellips och **ta bort** från de tillgängliga alternativen.
   
      ![ta bort lås](./media/resource-manager-lock-resources/delete-lock.png) 

