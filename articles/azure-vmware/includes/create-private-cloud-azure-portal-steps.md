---
title: Skapa ett privat moln för Azure VMware-lösningen
description: Steg för att skapa ett privat moln för Azure VMware-lösningar med hjälp av Azure Portal.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: da79881e609f982960468a8f26c98178f972ad43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725412"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **skapa en ny resurs**. I text rutan **Sök i Marketplace** `Azure VMware Solution` och välj **Azure VMware-lösning** i listan. I fönstret **Azure VMware-lösning** väljer du **skapa**.

1. Ange värden för fälten på fliken **grundläggande** . I följande tabell visas egenskaperna för fälten.

   | Fält   | Värde  |
   | ---| --- |
   | **Prenumeration** | Prenumerationen som du planerar att använda för distributionen.|
   | **Resursgrupp** | Resurs gruppen för dina privata moln resurser. |
   | **Plats** | Välj en plats, t. ex. **USA, östra**.|
   | **Resurs namn** | Namnet på ditt privata moln i Azure VMware-lösningen. |
   | **SKU** | Välj följande SKU-värde: AV36 |
   | **Värdar** | Antalet värdar som ska läggas till i det privata moln klustret. Standardvärdet är 3, som kan höjas eller sänkas efter distributionen.  |
   | **Adress block** | Ange ett IP-adressblock för CIDR-nätverket för det privata molnet, till exempel 10.175.0.0/22. |
   | **Virtual Network** | Välj en Virtual Network eller skapa en ny för det privata molnet Azure VMware-lösning.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Ange värden för fälten på fliken grundläggande." border="true":::

1. När du är färdig väljer du **Granska + skapa**. Kontrol lera att informationen har angetts på nästa skärm. Om informationen är korrekt väljer du **skapa**.

   > [!NOTE]
   > Det här steget tar ungefär två timmar. 

1. Kontrol lera att distributionen har slutförts. Navigera till resurs gruppen som du skapade och välj ditt privata moln.  Du ser statusen **slutförd** när distributionen har slutförts. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Kontrol lera att distributionen har slutförts." border="true":::