---
title: Skapa ett virtuellt nätverk – Snabbstart – Azure-portalen
titleSuffix: Azure Virtual Network
description: 'Snabb start: skapa ett virtuellt nätverk i Azure Portal. Dessa nätverk tillåter Azure-resurser, t. ex. virtuella datorer, att kommunicera på ett säkert sätt med varandra och Internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217642"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure-portalen

I den här snabb starten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Du distribuerar två virtuella datorer (VM). Därefter kommunicerar du säkert mellan virtuella datorer och ansluter till virtuella datorer från Internet. Ett virtuellt nätverk är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera på ett säkert sätt med varandra och med Internet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. På Azure Marketplace väljer du **nätverk**  >  **virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Name | Ange *myVirtualNetwork*. |
    | Plats | Välj **USA, östra**.|

1. Välj **Nästa: IP-adresser** och för **IPv4-adress utrymme** anger du *10.1.0.0/16*.

1. Välj **Lägg till undernät** och ange sedan *MyVirtualSubnet* för **under nätets namn** och *10.1.0.0/24* för **under nätets adress intervall**.

1. Välj **Lägg till** och välj sedan **Granska + skapa**. Lämna resten som standard och välj **skapa**.

1. I **Skapa virtuellt nätverk** väljer du **skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. I menyn i Azure-portalen väljer du **Skapa en resurs**.

1. På Azure Marketplace väljer du **Compute**  >  **Windows Server 2019 Data Center**. Välj **Skapa**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du har skapat den här resurs gruppen i föregående avsnitt. |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange *myVm1*. |
    | Region | Välj **USA, östra**. |
    | Alternativ för tillgänglighet | **Ingen redundans krävs för infrastruktur**. |
    | Bild | Standardvärdet är **Windows Server 2019 Data Center**. |
    | Storlek | Standard **ds1 v2** är standard. |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösen ordet igen. |
    | **Regler för inkommande portar** |  |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Ange *http (80)* och *RDP (3389)*. |
    | **Spara pengar** |  |
    | Har du redan en Windows-licens? | Standardvärdet är **Nej**. |

1. Välj **Nästa: diskar**.

1. Behåll standardvärdena i **skapa en virtuell dator – diskar** och välj **Nästa: nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Standardvärdet är **myVirtualNetwork**. |
    | Undernät | Standardvärdet är **myVirtualSubnet (10.1.0.0/24)**. |
    | Offentlig IP-adress | Standard till **(ny) myVm-IP**. |
    | Nätverks säkerhets grupp för nätverkskort | Standard för **Basic**. |
    | Offentliga inkommande portar | Standard för att **tillåta valda portar**. |
    | Välj inkommande portar | Standardvärdet är **http** och **RDP**.

1. Välj **Nästa: hantering**.

1. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

1. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myvmstorageaccount*. Om det här namnet tas skapar du ett unikt namn.|
    | Typ av konto | Standardvärdet **lagring (generell användning v1)**. |
    | Prestanda | Standardvärdet är **standard.** |
    | Replikering | Standard är **Lokalt Redundant lagring (LRS)**. |

1. Välj **OK** och välj sedan **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Upprepa proceduren i föregående avsnitt om du vill skapa en annan virtuell dator.

> [!IMPORTANT]
> För namnet på den **virtuella datorn** anger du *myVm2*.
>
> För **diagnos lagrings konto** kontrollerar du att du väljer **myvmstorageaccount**, i stället för att skapa en.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

När du har skapat *myVm1* ansluter du till Internet.

1. Sök efter och välj *myVm1* i Azure Portal.

1. Välj **Anslut** och sedan **RDP**.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

    Sidan **Anslut** öppnas.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna RDP-filen. Välj **Anslut** om du uppmanas att göra det.

1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

    > [!NOTE]
    > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikat varning när du loggar in. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

1. Ange `ping myVm2`.

    Du får ett meddelande som liknar dessa utdata:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` misslyckas eftersom `ping` använder Internet Control Message Protocol (ICMP). Som standard tillåts inte ICMP genom Windows-brandväggen.

1. Ange det efterföljande kommandot för att tillåta *myVm2* att pinga *myVm1* i ett senare skede:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Detta kommando tillåter ICMP-inkommande genom Windows-brandväggen:

1. Stäng fjärrskrivbordsanslutningen till *myVm1*.

1. Utför stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet) igen, men anslut till *myVm2*.

1. Från en kommandotolk anger du `ping myvm1`.

    Du får tillbaka något som liknar det här meddelandet:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Du får svar från *myVm1* eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn *myVm1* i steg 3.

1. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du är ansluten till en virtuell dator från Internet och kommunicerat på ett säkert sätt mellan de två virtuella datorerna.

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Sök efter och välj *myResourceGroup*.

1. Välj **Ta bort resursgrupp**.

1. Ange *myResourceGroup* för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om inställningar för virtuella nätverk finns i [skapa, ändra eller ta bort ett virtuellt nätverk](manage-virtual-network.md).

Som standard tillåter Azure säker kommunikation mellan virtuella datorer. Azure tillåter endast inkommande fjärr skrivbords anslutningar till virtuella Windows-datorer från Internet. Mer information om typer av nätverkskommunikation för virtuella datorer finns i [filtrera nätverks trafik](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Azure-tjänster kostar pengar. Azure Cost Management hjälper dig att ställa in budgetar och konfigurera aviseringar för att behålla utgifter under kontroll. Analysera, hantera och optimera dina Azure-kostnader med Cost Management. Mer information finns i snabb starten [när du analyserar dina kostnader](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).