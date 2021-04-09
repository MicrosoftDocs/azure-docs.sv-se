---
title: Ansluta virtuella nätverk med VNet-peering – självstudier – Azure Portal
description: I den här självstudien får du lära dig hur du kopplar samman virtuella nätverk med peerkoppling, med hjälp av Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 71ba82cfd5f0f4166d25983f3f80530da45bdeac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932955"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Självstudie: Koppla samman virtuella nätverk med peerkoppling med hjälp av Azure Portal

Du kan ansluta virtuella nätverk till varandra med peerkoppling. Dessa virtuella nätverk kan finnas i samma region eller andra regioner (kallas även Global VNet-peering). När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Koppla samman två virtuella nätverk med en peerkoppling
> * Distribuera en virtuell dator (VM) till varje virtuellt nätverk
> * Kommunicera mellan virtuella datorer

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-connect-virtual-networks-cli.md) eller [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha ett Azure-konto med en aktiv prenumeration. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. På fliken **grundläggande** anger eller väljer du följande information och accepterar standardinställningarna för de återstående inställningarna:

    |Inställning|Värde|
    |---|---|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och skriv *myResourceGroup*.|
    |Region| Välj **USA, östra**.|
    |Name|myVirtualNetwork1|

4. På fliken **IP-adresser** anger du 10.0.0.0/16 för fältet **adress utrymme** . Klicka på knappen **Lägg till undernät** nedan och ange *Subnet1* för **under nätets namn** och 10.0.0.0/24 för **under nätets adress intervall**.
5. Välj **Granska + skapa** och välj sedan **skapa**.
   
5. Utför steg 1–5 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork2|
    |Adressutrymme|10.1.0.0/16|
    |Resursgrupp| Välj **Använd befintlig** och välj sedan **myResourceGroup**.|
    |Namn på undernät | Subnet2|
    |Undernätsadressintervall|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

1. I rutan Sök högst upp på Azure Portal börjar du skriva *MyVirtualNetwork1*. När **myVirtualNetwork1** visas i sökresultatet väljer du det.
2. Välj **peering**, under **Inställningar** och välj sedan **Lägg till**, som du ser i följande bild:

    ![Skapa peerkoppling](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**.

    |Inställning|Värde|
    |---|---|
    |Peer-kopplingens namn från myVirtualNetwork1 till ett virtuellt dator nätverk|myVirtualNetwork1-myVirtualNetwork2 – när sidan först läses in visas frasen "fjärran sluten virtuellt nätverk" här. När du har valt det virtuella fjärrnätverket ersätts frasen "fjärrstyrt nätverk" med namnet på det virtuella fjärrnätverket.|
    |Prenumeration| Välj din prenumeration.|
    |Virtuellt nätverk|myVirtualNetwork2 – om du vill välja det virtuella *myVirtualNetwork2* -nätverket väljer du **virtuellt nätverk** och väljer sedan **myVirtualNetwork2 (myResourceGroup)**. Du kan välja ett virtuellt nätverk i samma region eller i en annan region.|
    |Peer-kopplingens namn från myVirtualNetwork2 till myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Inställningar för peerkoppling](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    **Peering-statusen** är *ansluten*, som visas på följande bild:

    ![Peering-status](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Om du inte ser status uppdaterar du webbläsaren.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men i återstående steg förutsätts att du har valt **Windows Server 2016 Datacenter**. 
3. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Resursgrupp| Välj **Använd befintlig** och välj sedan **myResourceGroup**.|
    |Name|myVm1|
    |Location| Välj **USA, östra**.|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Välj en storlek på virtuell dator för alternativet **storlek** .
5. Välj följande värden för under **nätverk**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| myVirtualNetwork1 – om det inte redan är valt väljer du **virtuellt nätverk** och väljer sedan **myVirtualNetwork1**.|
    |Undernät| Subnet1 – om det inte redan är valt väljer du **undernät** och sedan **Subnet1**.|
   
6. Välj **Nätverk**. Välj **Tillåt valda portar** för alternativet **offentliga inkommande portar** . Välj **RDP** för alternativet **Välj inkommande portar** nedan. 

7. Klicka på knappen **Granska + skapa** i det nedre vänstra hörnet för att starta distributionen av den virtuella datorn.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Utför steg 1–6 igen, med följande ändringar:

|Inställning|Värde|
|---|---|
|Namn | myVm2|
|Virtuellt nätverk | myVirtualNetwork2|

Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. I rutan *Sök* högst upp i portalen börjar du skriva *myVm1*. När **myVm1** visas i sökresultatet väljer du den.
2. Skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVm1* genom att välja **Anslut** enligt bilden nedan:

    ![Ansluta till den virtuella datorn](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut** om du uppmanas att göra det.
4. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
6. I ett senare steg används ping för att kommunicera med den virtuella datorn *myVm2* från den virtuella datorn *myVm1*. Ping använder som standard ICMP (Internet Control Message Protocol), som nekas via Windows-brandväggen. På den virtuella datorn *myVm1* aktiverar du ICMP via Windows-brandväggen, så att du kan pinga den här virtuella datorn från *myVm2* i ett senare steg, med hjälp av PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Även om ping används för att kommunicera mellan virtuella datorer i den här självstudien rekommenderar vi inte att du tillåter att ICMP kommer igenom Windows-brandväggen för produktionsdistribution.

7. Du ansluter till den virtuella datorn *myVm2* genom att ange följande kommando från en kommandotolk på den virtuella datorn *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Eftersom du har aktiverat ping på *myVm1* kan du nu pinga IP-adressen:

    ```
    ping 10.0.0.4
    ```
    
9. Koppla från RDP-sessionerna till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller: 

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om peering för virtuella nätverk](virtual-network-peering-overview.md)


