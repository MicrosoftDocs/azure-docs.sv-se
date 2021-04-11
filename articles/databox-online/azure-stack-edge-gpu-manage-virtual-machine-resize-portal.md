---
title: Ändra storlek på virtuella datorer på Azure Stack Edge Pro GPU, Pro R, Mini R via Azure Portal
description: Lär dig hur du ändrar storlek på virtuella datorer (VM) som körs på Azure Stack Edge Pro GPU, Azure Stack Edge Pro R, Azure Stack Edge Mini R via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080518"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Använd Azure Portal för att ändra storlek på de virtuella datorerna på Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln förklarar hur du ändrar storlek på de virtuella datorer som distribueras på Azure Stack Edge Pro GPU-enhet.

       
## <a name="about-vm-sizing"></a>Om storlek på virtuell dator

Storleken på den virtuella datorn avgör hur mycket data bearbetnings resurser (t. ex. processor, GPU och minne) som görs tillgängliga för den virtuella datorn. Du bör skapa virtuella datorer med hjälp av en storlek på den virtuella datorn som är lämplig för din program arbets belastning. 

Även om alla datorer ska köras på samma maskin vara, har dator storlekarna olika begränsningar för disk åtkomst. Detta kan hjälpa dig att hantera den totala disk åtkomsten över dina virtuella datorer. Om en arbets belastning ökar kan du också ändra storlek på en befintlig virtuell dator.

Mer information finns i [VM-storlekar som stöds för enheten](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Förutsättningar

Innan du ändrar storlek på en virtuell dator som körs på enheten via Azure Portal, se till att:

1. Du har minst en virtuell dator som har distribuerats på enheten. Om du vill skapa den här virtuella datorn går du till anvisningarna i [distribuera VM på Azure Stack Edge Pro via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Den virtuella datorn ska vara i **stoppat** tillstånd. Stoppa den virtuella datorn genom att gå till **virtual machines > Overview** och välj den virtuella dator som du vill stoppa. På sidan Översikt väljer du **stoppa** och väljer sedan **Ja** när du uppmanas att bekräfta. Innan du ändrar storlek på den virtuella datorn måste du stoppa den virtuella datorn.

    ![Stoppa översikts sidan för virtuell dator](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

Följ dessa steg om du vill ändra storlek på en virtuell dator som har distribuerats på enheten. 

1. Gå till den virtuella datorn som du har stoppat och gå sedan till sidan **Översikt** . Välj **storlek på virtuell dator (ändra)**.
    
    ![Välj ändring av virtuell dator storlek på översikts Sidan](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. På bladet **ändra VM-storlek** väljer du den **virtuella dator storleken** i kommando fältet och väljer sedan **ändra**.

    ![Välj ny VM-storlek](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Ett meddelande visas om att den virtuella datorn håller på att uppdateras. När den virtuella datorn har uppdaterats uppdateras sidan **Översikt** så att den ändrade virtuella datorn visas.

    ![Ändra storlek på virtuell dator ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar virtuella datorer på din Azure Stack Edge Pro-enhet finns i [distribuera virtuella datorer via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
