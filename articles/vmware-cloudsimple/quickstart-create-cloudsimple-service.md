---
title: 'Snabb start: skapa VMware CloudSimple-tjänst'
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig hur du skapar CloudSimple-tjänsten, Purchase Nodes och Reserve Nodes
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86507598"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Snabb start – skapa en Azure VMware-lösning av CloudSimple-tjänsten

Kom igång genom att skapa Azure VMware-lösningen genom CloudSimple i Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-lösning av CloudSimple-service – översikt

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple.  Genom att skapa tjänsten kan du etablera noder, reservera noder och skapa privata moln.  Du lägger till CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple.  Det här Edge-nätverket används för tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina privata moln.

Om du vill lägga till CloudSimple-tjänsten måste du skapa ett Gateway-undernät. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något av dina lokala nätverks adress utrymmen eller adress utrymmet för det virtuella Azure-nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernät.  Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för Azure VMware-lösningar från CloudSimple Edge-nätverkstjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön.  De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk.

Granska [nätverks krav](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänst**.

    ![Sök CloudSimple-tjänst](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.

    ![Lägg till CloudSimple-tjänst](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="provision-nodes"></a>Etablera noder

Om du vill ställa in betala per användning-kapacitet för en CloudSimple privat moln miljö ska du först etablera noder i Azure Portal.

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

* [Skapa ett privat moln och konfigurera miljön](quickstart-create-private-cloud.md)
* Läs mer om [CloudSimple-tjänsten](./cloudsimple-service.md)
