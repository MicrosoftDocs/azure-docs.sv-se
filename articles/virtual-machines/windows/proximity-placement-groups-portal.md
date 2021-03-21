---
title: Skapa en närhetsplaceringsgrupp med hjälp av portalen
description: Lär dig hur du skapar en närhets placerings grupp med hjälp av Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: daf844870670c14db5208f45fbd9c9adf46be985
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504634"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Skapa en närhets placerings grupp med hjälp av Azure Portal

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](../co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.

> [!NOTE]
> Placerings grupper för närhet kan inte användas med dedikerade värdar.
>
> Om du vill använda tillgänglighets zoner tillsammans med placerings grupper måste du se till att de virtuella datorerna i placerings gruppen också finns i samma tillgänglighets zon.
>

## <a name="create-the-proximity-placement-group"></a>Skapa placerings gruppen närhet

1. Skriv **närhets placerings grupp** i sökningen.
1. Under **tjänster** i Sök resultaten väljer du **närhets placerings grupper**.
1. På sidan **närhets placerings grupper** väljer du **Lägg till**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad.
1. I **resurs grupp** väljer du antingen **Skapa ny** för att skapa en ny grupp eller väljer en tom resurs grupp som redan finns, från List rutan. 
1. I **region** väljer du den plats där du vill att placerings gruppen för närhet ska skapas.
1. I **närhet placerings grupp namn** skriver du ett namn och väljer sedan **Granska och skapa**.
1. När verifieringen är klar väljer du **skapa** för att skapa närhets gruppen.

    ![Skärm bild där du skapar en närhets placerings grupp](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Skapa en virtuell dator

1. När du skapar en virtuell dator i portalen går du till fliken **Avancerat** . 
1. I området **närhets placering** väljer du rätt placerings grupp. 

    ![Skärm bild av avsnittet närhets placerings grupp när du skapar en ny virtuell dator i portalen](./media/ppg/vm-ppg.png)

1. När du har gjort alla andra obligatoriska val väljer du **Granska + skapa**.
1. När verifieringen har godkänts väljer du **skapa** för att distribuera den virtuella datorn i placerings gruppen.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Lägg till virtuella datorer i en tillgänglighets uppsättning till en närhets placerings grupp

Om den virtuella datorn är en del av tillgänglighets uppsättningen måste du lägga till tillgänglighets uppsättningen i placerings gruppen innan du lägger till de virtuella datorerna.

1. I [portalen](https://portal.azure.com) söker du efter *tillgänglighets uppsättningar* och väljer din tillgänglighets uppsättning från resultaten.
1. Stop\deallocate varje virtuell dator i tillgänglighets uppsättningen genom att välja den virtuella datorn och sedan välja **stoppa** på sidan för den virtuella datorn och välj sedan **OK** för att stoppa den virtuella datorn.
1. Kontrol lera att alla virtuella datorer har **statusen** **stoppad (Frigjord)** på sidan för din tillgänglighets uppsättning.
1. I den vänstra menyn väljer du **Konfiguration**.
1. Välj en placerings grupp i list rutan under **närhets placerings grupp** och välj sedan **Spara**.
1. Välj **Översikt** på den vänstra menyn för att visa listan över virtuella datorer igen. 
1. Välj varje virtuell dator i tillgänglighets uppsättningen och välj sedan **Starta** på sidan för varje virtuell dator. 


## <a name="add-existing-vm-to-placement-group"></a>Lägg till befintlig virtuell dator till placerings grupp 


1. På sidan för den virtuella datorn väljer du **stoppa**.
1. När statusen för den virtuella datorn visas som **stoppad (Frigjord)** väljer du **konfiguration** på den vänstra menyn.
1. Välj en placerings grupp i list rutan under **närhets placerings grupp** och välj sedan **Spara**.
1. Välj **Översikt** i den vänstra menyn och välj **Starta** för att starta om den virtuella datorn.

 

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure PowerShell](proximity-placement-groups.md) för att skapa närhets grupper.