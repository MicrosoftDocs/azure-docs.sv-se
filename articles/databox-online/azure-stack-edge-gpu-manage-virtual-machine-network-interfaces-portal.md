---
title: Hantera virtuella datorers nätverks gränssnitt i Azure Stack Edge Pro via Azure Portal
description: Lär dig hur du hanterar nätverks gränssnitt på virtuella datorer som distribueras på Azure Stack Edge Pro GPU via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027740"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Använd Azure Portal för att hantera nätverks gränssnitt på de virtuella datorerna på din Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Du kan skapa och hantera virtuella datorer på en Azure Stack Edge-enhet med Azure Portal, mallar, Azure PowerShell-cmdletar och via Azure CLI/Python-skript. Den här artikeln beskriver hur du hanterar nätverks gränssnitten på en virtuell dator som körs på din Azure Stack Edge-enhet med hjälp av Azure Portal. 

När du skapar en virtuell dator anger du ett virtuellt nätverks gränssnitt som ska skapas. Du kanske vill lägga till ett eller flera nätverks gränssnitt till den virtuella datorn när den har skapats. Du kanske också vill ändra standardinställningarna för nätverks gränssnittet för ett befintligt nätverks gränssnitt.

Den här artikeln förklarar hur du lägger till ett nätverks gränssnitt till en befintlig virtuell dator, ändrar befintliga inställningar, till exempel IP-typ (statisk eller dynamisk) och slutligen tar bort eller kopplar från ett befintligt gränssnitt. 

        
## <a name="about-network-interfaces-on-vms"></a>Om nätverks gränssnitt på virtuella datorer

Ett nätverks gränssnitt gör det möjligt för en virtuell dator (VM) som körs på din Azure Stack Edge Pro-enhet att kommunicera med Azure och lokala resurser. När du aktiverar en port för Compute Network på enheten, skapas en virtuell växel på nätverks gränssnittet. Den här virtuella växeln används sedan för att distribuera beräknings arbets belastningar som virtuella datorer eller behållare program på din enhet. 

Enheten stöder bara en virtuell växel men flera virtuella nätverks gränssnitt. Varje nätverks gränssnitt på den virtuella datorn har en statisk eller dynamisk IP-adress tilldelad till sig. Med IP-adresser tilldelade till flera nätverks gränssnitt på den virtuella datorn är vissa funktioner aktiverade på den virtuella datorn. Din virtuella dator kan till exempel vara värd för flera webbplatser eller tjänster med olika IP-adresser och SSL-certifikat på en enskild server. En virtuell dator på enheten kan fungera som en virtuell nätverks installation, till exempel en brand vägg eller en belastningsutjämnare. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar hantera virtuella datorer på enheten via Azure Portal måste du kontrol lera att:

1. Du har aktiverat ett nätverks gränssnitt för beräkning på enheten. Den här åtgärden skapar en virtuell växel på det nätverks gränssnittet på den virtuella datorn. 
    1. I enhetens lokala användar gränssnitt går du till **Compute**. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel.

        > [!IMPORTANT] 
        > Du kan bara konfigurera en port för beräkning.

    1. Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge Pro GPU skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet.

1. Du har minst en virtuell dator som har distribuerats på enheten. Om du vill skapa den här virtuella datorn går du till anvisningarna i [distribuera VM på Azure Stack Edge Pro via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Den virtuella datorn ska vara i **stoppat** tillstånd. Stoppa den virtuella datorn genom att gå till **virtual machines > Overview** och välj den virtuella dator som du vill stoppa. På sidan Egenskaper för virtuell dator väljer du **stoppa** och väljer sedan **Ja** när du uppmanas att bekräfta. Innan du lägger till, redigerar eller tar bort nätverks gränssnitt måste du stoppa den virtuella datorn.

    ![Stoppa virtuell dator från sidan Egenskaper för virtuell dator](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Lägg till ett nätverks gränssnitt

Följ dessa steg om du vill lägga till ett nätverks gränssnitt till en virtuell dator som har distribuerats på enheten. 

1. Gå till den virtuella datorn som du har stoppat och gå sedan till sidan **Egenskaper för virtuell dator** . Välj **Nätverk**.
    
    ![Sidan Välj nätverk på Egenskaper för virtuell dator](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. I bladet **nätverk** väljer du **+ Lägg till nätverks gränssnitt** i kommando fältet.

    ![Välj Lägg till nätverks gränssnitt](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. I bladet **Lägg till nätverks gränssnitt** anger du följande parametrar:

    
    |Column1  |Column2  |
    |---------|---------|
    |Name     | Ett unikt namn inom resurs gruppen. Namnet kan inte ändras efter att nätverks gränssnittet har skapats. Använd de förslag som anges i [namngivnings konventionerna](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)för att enkelt hantera flera nätverks gränssnitt.     |
    |Virtuellt nätverk| Det virtuella nätverk som är associerat med den virtuella växeln som skapats på enheten när du aktiverade beräkning i nätverks gränssnittet. Det finns bara ett virtuellt nätverk som är kopplat till din enhet.         |         
    |Undernät     | Ett undernät i det valda virtuella nätverket. Det här fältet fylls i automatiskt med det undernät som är associerat med det nätverks gränssnitt som du har aktiverat Compute.         |       
    |IP-tilldelning   | En statisk eller dynamisk IP-adress för ditt nätverks gränssnitt. Den statiska IP-adressen ska vara en tillgänglig, kostnads fri IP-adress från det angivna under nätet. Välj dynamisk om det finns en DHCP-server i miljön.        | 

    ![Lägg till ett nätverks gränssnitts blad](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Ett meddelande visas om att nätverks gränssnittet skapas.

    ![Meddelande när nätverks gränssnittet skapas](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  När nätverks gränssnittet har skapats uppdateras listan över nätverks gränssnitt för att visa det nyligen skapade gränssnittet.

    ![Uppdaterad lista över nätverks gränssnitt](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Redigera ett nätverks gränssnitt

Följ dessa steg om du vill redigera ett nätverks gränssnitt som är associerat med en virtuell dator som har distribuerats på enheten.

1. Gå till den virtuella datorn som du har stoppat och gå till sidan **Egenskaper för virtuell dator** . Välj **Nätverk**.

1. I listan över nätverks gränssnitt väljer du det gränssnitt som du vill redigera. Längst till höger om det valda nätverks gränssnittet väljer du redigerings ikonen (blyertspenna).  

    ![Välj ett nätverks gränssnitt som ska redige ras](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. På bladet **Redigera nätverks gränssnitt** kan du bara ändra IP-tilldelningen för nätverks gränssnittet. Det går inte att ändra namnet, det virtuella nätverket och under nätet som är kopplade till nätverks gränssnittet när det har skapats. Ändra **IP-tilldelningen** till statisk och spara ändringarna.

    ![Ändra IP-tilldelning för nätverks gränssnittet](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. Listan över nätverks gränssnitt uppdateras för att visa det uppdaterade nätverks gränssnittet.


## <a name="detach-a-network-interface"></a>Koppla från ett nätverks gränssnitt

Följ dessa steg om du vill koppla bort eller ta bort ett nätverks gränssnitt som är associerat med en virtuell dator som har distribuerats på enheten.

1. Gå till den virtuella datorn som du har stoppat och gå till sidan **Egenskaper för virtuell dator** . Välj **Nätverk**.

1. I listan över nätverks gränssnitt väljer du det gränssnitt som du vill redigera. Längst till höger om det valda nätverks gränssnittet väljer du koppla från-ikonen (koppla från).  

    ![Välj ett nätverks gränssnitt för att koppla från](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. När gränssnittet är helt frånkopplat uppdateras listan över nätverks gränssnitt för att visa de återstående gränssnitten.

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar virtuella datorer på din Azure Stack Edge Pro-enhet finns i [distribuera virtuella datorer via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
