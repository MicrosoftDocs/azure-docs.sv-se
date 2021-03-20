---
title: Etablera noder för VMware-lösning av CloudSimple – Azure
description: Lär dig hur du lägger till noder i VMWare med CloudSimple-distribution i Azure Portal. Du kan ställa in betala per användning-kapacitet för din privata moln miljö.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140742"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Etablera noder för Azure VMware-lösningen av CloudSimple

Etablera noder i Azure Portal. Sedan kan du konfigurera kapaciteten betala per användning för din CloudSimple privata moln miljö.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Lägg till en nod i ditt CloudSimple privata moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-noder**.

   ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple-noder.
6. Välj resurs grupp för noderna. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj platsen för nodens resurser.
9. Välj den dedikerade plats som ska vara värd för nodens resurser.
10. Välj [nodtyp](cloudsimple-node.md).
11. Välj antalet noder som ska etableras.
12. Välj **Granska + skapa**.
13. Granska inställningarna. Klicka på **föregående** om du vill ändra inställningarna.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privat moln](create-private-cloud.md)
