---
title: Skapa en virtuell dator i zonen med Azure Portal
description: Skapa en virtuell Windows-dator i en tillgänglighets zon med Azure Portal
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501693"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Skapa en virtuell dator i en tillgänglighets zon med hjälp av Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att skapa en virtuell dator i en Azure-tillgänglighets zon. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en tillgänglighetszon skapar du din virtuella dator i en [Azure-region som stöds](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 

3. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm). Välj en plats som USA, östra 2, som har stöd för tillgänglighets zoner. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Välj en storlek för den virtuella datorn. Välj en rekommenderad storlek eller filtrera baserat på funktioner. Bekräfta att storleken är tillgänglig i den zon som du vill använda.

    ![Välj en VM-storlek](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Under **Inställningar**  >  **hög tillgänglighet** väljer du en av de numrerade zonerna i list rutan **tillgänglighets zon** , behåller de återstående standardvärdena och klickar på **OK**.

    ![Välj en tillgänglighets zon](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. På sidan Sammanfattning klickar du på **skapa** för att starta distributionen av virtuella datorer.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bekräfta zon för hanterad disk och IP-adress

När den virtuella datorn distribueras i en tillgänglighets zon skapas en hanterad disk för den virtuella datorn i samma tillgänglighets zon. Som standard skapas även en offentlig IP-adress i den zonen.

Du kan bekräfta zon inställningarna för dessa resurser i portalen.  

1. Klicka på **resurs grupper** och sedan på namnet på resurs gruppen för den virtuella datorn, till exempel *myResourceGroup*.

2. Klicka på namnet på disk resursen. **Översikts** sidan innehåller information om resursens plats och tillgänglighets zon.

    ![Tillgänglighets zon för hanterad disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klicka på namnet på den offentliga IP-adressresursen. **Översikts** sidan innehåller information om resursens plats och tillgänglighets zon.

    ![Tillgänglighets zon för IP-adress](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Lär dig mer om [tillgänglighet](../availability.md) för virtuella Azure-datorer.