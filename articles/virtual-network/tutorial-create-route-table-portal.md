---
title: Dirigera nätverkstrafik – självstudie – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här självstudien får du lära dig att dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: e047f46e110e1f7b1d544545c80bd1097ae65167
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221926"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Självstudie: Dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen

Som standard dirigerar Azure trafik mellan alla undernät inom ett virtuellt nätverk. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Anpassade vägar är användbara när du till exempel vill dirigera trafik mellan undernät via en virtuell nätverks installation (NVA). I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa en NVA som dirigerar trafik
> * Skapa en routningstabell
> * Skapa en väg
> * Associera en routningstabell till ett undernät
> * Distribuera virtuella datorer till olika undernät
> * Dirigera trafik från ett undernät till ett annat via en NVA

I den här självstudien används [Azure Portal](https://portal.azure.com). Du kan också använda [Azure CLI](tutorial-create-route-table-cli.md) eller [Azure PowerShell](tutorial-create-route-table-powershell.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-nva"></a>Skapa en NVA

Virtuella nätverks installationer (NVA) är virtuella datorer som hjälper till med nätverksfunktioner, t. ex. Routning och brand Väggs optimering. Den här självstudien förutsätter att du använder **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem om du vill.

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

1. Välj **säkerhet**  >  **Windows Server 2016 Data Center**.

    ![Windows Server 2016 Data Center, skapa en virtuell dator Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. På sidan **skapa en virtuell dator** under **grunderna** anger eller väljer du den här informationen:

    | Avsnitt | Inställningen | Action |
    | ------- | ------- | ----- |
    | **Projektinformation** | Prenumeration | Välj din prenumeration. |
    | | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj **OK**. |
    | **Instansinformation** | Namn på virtuell dator | Ange *myVmNva*. |
    | | Region | Välj **(US) USA, östra**. |
    | | Alternativ för tillgänglighet | Välj **ingen infrastruktur redundans krävs**. |
    | | Bild | Välj **Windows Server 2016 Data Center**. |
    | | Storlek | Behåll standard **ds1 v2-standarden**. |
    | **Administratörs konto** | Användarnamn | Ange ett valfritt användarnamn. |
    | | Lösenord | Ange ett lösen ord som du väljer, vilket måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Bekräfta lösenord | Ange lösen ordet igen. |
    | **Regler för inkommande portar** | Offentliga inkommande portar | Välj **ingen**. |
    | **Spara pengar** | Har du redan en Windows Server-licens? | Välj **Nej**. |

    ![Grunderna, skapa en virtuell dator, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Välj sedan **Nästa: diskar >**.

1. Under **diskar** väljer du de inställningar som passar bäst för dina behov och väljer sedan **nästa: nätverks >**.

1. Under **nätverk**:

    1. För **virtuellt nätverk** väljer du **Skapa nytt**.
    
    1. I dialog rutan **Skapa virtuellt nätverk** , under **namn**, anger du *myVirtualNetwork*.

    1. I **adress utrymme** ersätter du det befintliga adress intervallet med *10.0.0.0/16*.

    1. I **undernät** väljer du ikonen **ta bort** för att ta bort det befintliga under nätet och anger sedan följande kombinationer av namn och **adress intervall** för **under nätet** . När ett giltigt namn och intervall har angetts visas en ny tom rad under den.

        | Namn på undernät | Adressintervall |
        | ----------- | ------------- |
        | *Offentlig* | *10.0.0.0/24* |
        | *Privat* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Välj **OK** för att stänga dialog rutan.

    1. I **undernät** väljer du **DMZ (10.0.2.0/24)**.

    1. I **offentlig IP** väljer du **ingen**, eftersom den virtuella datorn inte ansluter via Internet.

    1. Välj **Nästa: hanterings >**.

1. Under **hantering**:

    1. I **lagrings konto för diagnostik** väljer du **Skapa nytt**.
    
    1. I dialog rutan **skapa lagrings konto** anger eller väljer du den här informationen:

        | Inställning | Värde |
        | ------- | ----- |
        | Namn | *mynvastorageaccount* |
        | Typ av konto | **Lagring (generell användning v1)** |
        | Prestanda | **Standard** |
        | Replikering | **Lokalt redundant lagring (LRS)** |
    
    1. Välj **OK** för att stänga dialog rutan.

    1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** och Azure verifierar konfigurationen.

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

    Det tar några minuter att skapa den virtuella datorn. Vänta tills Azure har skapat den virtuella datorn. Sidan **distributionen är** igång visar distributions information.

1. När den virtuella datorn är klar väljer du **Gå till resurs**.

## <a name="create-a-route-table"></a>Skapa en routningstabell

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. I rutan Sök anger du *routningstabellen*. När **routningstabellen** visas i Sök resultaten väljer du den.

3. På sidan **routningstabell** väljer du **skapa**.

4. I **Skapa routningstabell** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | *myRouteTablePublic* |
    | Prenumeration | Din prenumeration |
    | Resursgrupp | **myResourceGroup** |
    | Plats | **(USA) USA, östra** |
    | Väg spridning för virtuell nätverks-Gateway | **Aktiverad** |

    ![Skapa routningstabell, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Välj **Skapa**.

## <a name="create-a-route"></a>Skapa en väg

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera routningstabellen. Sök efter och välj **routningstabeller**.

1. Välj namnet på din routningstabell (**myRouteTablePublic**).

1. Välj **vägar**  >  **Lägg till**.

    ![Lägg till väg, routningstabell Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. I **Lägg till väg** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Vägnamn | *ToPrivateSubnet* |
    | Adressprefix | *10.0.1.0/24* (adress intervallet för det *privata* under nätet som skapades tidigare) |
    | Nästa hopptyp | **Virtuell installation** |
    | Nexthop-adress | *10.0.2.4* (en adress inom adress intervallet för *DMZ* -undernätet) |

1. Välj **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **virtuella nätverk**.

1. Välj namnet på ditt virtuella nätverk (**myVirtualNetwork**).

1. I meny fältet för det virtuella nätverket väljer du **undernät**.

1. Välj **offentlig** i listan undernät för virtuellt nätverk.

1. I **routningstabellen** väljer du den routningstabell som du skapade (**myRouteTablePublic**) och väljer sedan **Spara** för att koppla routningstabellen till det *offentliga* under nätet.

    ![Associera routningstabellen, under näts lista, virtuellt nätverk Azure Portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Aktivera IP-vidarebefordran

Aktivera sedan IP-vidarebefordring för den nya virtuella NVA-datorn, *myVmNva*. När Azure skickar nätverks trafik till *myVmNva*, och om trafiken är avsedd för en annan IP-adress, skickar IP-vidarebefordring trafiken till rätt plats.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera den virtuella datorn. Sök efter och välj **virtuella datorer**.

1. Välj namnet på den virtuella datorn (**myVmNva**).

1. I meny raden för den virtuella datorns NVA väljer du **nätverk**.

1. Välj **myvmnva123**. Det är det nätverksgränssnitt som Azure skapade för den virtuella datorn. Azure lägger till siffror för att säkerställa ett unikt namn.

    ![Nätverk, virtuell nätverks installation (NVA) virtuell dator (VM), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. I meny raden för nätverks gränssnittet väljer du **IP-konfigurationer**.

1. På sidan **IP-konfigurationer** anger du **IP-vidarebefordran** till **aktive rad** och väljer **Spara**.

    ![Aktivera IP-vidarebefordring, IP-konfigurationer, nätverks gränssnitt, virtuell nätverks installation (virtuell dator) Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Skapa offentliga och privata virtuella datorer

Skapa en offentlig virtuell dator och en privat virtuell dator i det virtuella nätverket. Senare använder du dem för att se att Azure dirigerar trafik för det *offentliga* undernätet till det *privata* undernätet via NVA.

Följ stegen i [skapa en NVA](#create-an-nva) tidigare om du vill skapa en offentlig virtuell dator och en privat virtuell dator. Du behöver inte vänta på att distributionen ska slutföras eller gå till den virtuella dator resursen. Du använder de flesta av samma inställningar, förutom vad som beskrivs nedan.

Innan du väljer **skapa** för att skapa den offentliga eller privata virtuella datorn går du till följande två underavsnitt ([offentlig virtuell dator](#public-vm) och [privat virtuell dator](#private-vm)), som visar de värden som måste vara olika. Du kan fortsätta till nästa avsnitt ([dirigera trafik via en NVA](#route-traffic-through-an-nva)) när Azure har distribuerat båda virtuella datorerna.

### <a name="public-vm"></a>Offentlig virtuell dator

| Flik | Inställning | Värde |
| --- | ------- | ----- |
| Grunder | Resursgrupp | **myResourceGroup** |
| | Namn på virtuell dator | *myVmPublic* |
| | Offentliga inkommande portar | **Tillåt valda portar** |
| | Välj inkommande portar | **RDP** |
| Nätverk | Virtuellt nätverk | **myVirtualNetwork** |
| | Undernät | **Offentlig (10.0.0.0/24)** |
| | Offentlig IP-adress | Standard |
| Hantering | Lagringskonto för diagnostik | **mynvastorageaccount** |

### <a name="private-vm"></a>Privat virtuell dator

| Flik | Inställning | Värde |
| --- | ------- | ----- |
| Grunder | Resursgrupp | **myResourceGroup** |
| | Namn på virtuell dator | *myVmPrivate* |
| | Offentliga inkommande portar | **Tillåt valda portar** |
| | Välj inkommande portar | **RDP** |
| Nätverk | Virtuellt nätverk | **myVirtualNetwork** |
| | Undernät | **Privat (10.0.1.0/24)** |
| | Offentlig IP-adress | Standard |
| Hantering | Lagringskonto för diagnostik | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logga in på myVmPrivate via fjärrskrivbord

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera din privata virtuella dator. Sök efter och välj **virtuella datorer**.

1. Välj namnet på den privata virtuella datorn (**myVmPrivate**).

1. I meny raden för virtuell dator väljer du **Anslut** för att skapa en fjärr skrivbords anslutning till den privata virtuella datorn.

1. På sidan **Anslut med RDP** väljer du **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna den nedladdade *RDP* -filen. Välj **Anslut** om du uppmanas att göra det. Välj **fler alternativ**  >  **Använd ett annat konto** och ange sedan det användar namn och lösen ord som du angav när du skapade den privata virtuella datorn.

1. Välj **OK**.

1. Om du får en certifikat varning under inloggnings processen väljer du **Ja** för att ansluta till den virtuella datorn.

### <a name="enable-icmp-through-the-windows-firewall"></a>Aktivera ICMP via Windows-brandväggen

I ett senare steg använder du vägspårningsverktyget för att testa routningen. Vägspårning använder ICMP (Internet Control Message Protocol), vilket Windows-brandväggen nekar som standard. Aktivera ICMP via Windows-brandväggen.

1. På fjärrskrivbordet för *myVmPrivate* öppnar du PowerShell.

1. Ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Du använder spårnings väg för att testa routning i den här självstudien. För produktionsmiljöer rekommenderar vi inte att du tillåter ICMP via Windows-brandväggen.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Aktivera IP-vidarebefordran i myVmNva

Du [aktiverade IP-vidarebefordran](#turn-on-ip-forwarding) för den virtuella datorns nätverksgränssnitt med hjälp av Azure. Den virtuella datorns operativsystem behöver också vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordran för operativsystemet för den virtuella datorn *myVmNva* med följande kommandon.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* öppnar du ett fjärrskrivbord till den virtuella datorn *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Från PowerShell på den virtuella datorn *myVmNva* anger du det här kommandot för att aktivera IP-vidarebefordring:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Starta om den virtuella datorn *myVmNva* : i aktivitets fältet väljer du **Starta**  >  **kraft**, **övrigt (planerat)**  >  **Fortsätt**.

    Detta kopplar också från fjärrskrivbordssessionen.

1. När den virtuella datorn *myVmNva* har startat om skapar du en fjärrskrivbordssession till den virtuella datorn *myVmPublic*. När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* öppnar du en kommandotolk och kör följande kommando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Öppna PowerShell i fjärr skrivbordet för *myVmPublic*.

1. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testa routningen av nätverkstrafik

Först testar vi att dirigera nätverkstrafik från den virtuella datorn *myVmPublic* till den virtuella datorn *myVmPrivate*.

1. Från PowerShell på den virtuella datorn *myVmPublic* anger du följande kommando:

    ```powershell
    tracert myVmPrivate
    ```

    Svaret liknar det här exemplet:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Du kan se att det första hoppet är till 10.0.2.4, vilket är NVA privata IP-adress. Det andra hoppet är till den privata IP-adressen för den virtuella datorn *myVmPrivate* : 10.0.1.4. Tidigare lade du till vägen till vägtabellen *myRouteTablePublic* och associerade den till det *offentliga* undernätet. Därför skickade Azure trafiken via NVA och inte direkt till det *privata* undernätet.

1. Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* anger du följande kommando:

    ```cmd
    tracert myVmPublic
    ```

    Det här kommandot testar routningen av nätverks trafik från den virtuella datorn *myVmPrivate* till den virtuella *myVmPublic* -datorn. Svaret liknar det här exemplet:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Du kan se att Azure dirigerar trafik direkt från den virtuella datorn *myVmPrivate* till den virtuella *myVmPublic* -datorn. Som standard dirigerar Azure trafik direkt mellan undernät.

1. Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

När resurs gruppen inte längre behövs tar du bort *myResourceGroup* och alla resurser den har:

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera resurs gruppen. Sök efter och välj **resurs grupper**.

1. Välj namnet på resurs gruppen (**myResourceGroup**).

1. Välj **Ta bort resursgrupp**.

1. I bekräftelse dialog rutan anger du *myResourceGroup* för **Skriv resurs gruppens namn** och välj sedan **ta bort**. Azure tar bort *myResourceGroup* och alla resurser som är kopplade till den resurs gruppen, inklusive dina väg tabeller, lagrings konton, virtuella nätverk, virtuella datorer, nätverks gränssnitt och offentliga IP-adresser.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en routningstabell och associerat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Nu kan du distribuera olika förkonfigurerade NVA från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), vilket ger många användbara nätverksfunktioner. Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men Azure kan inte distribuera resurser för vissa PaaS-tjänster till ett virtuellt nätverk. Det är möjligt att begränsa åtkomsten till resurser i vissa Azure PaaS-tjänster, även om begränsningen bara får vara trafik från ett undernät i ett virtuellt nätverk. Information om hur du begränsar nätverks åtkomsten till Azure PaaS-resurser finns i nästa självstudie.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure-tjänster kostar pengar. Azure Cost Management hjälper dig att ställa in budgetar och konfigurera aviseringar för att behålla utgifter under kontroll. Analysera, hantera och optimera dina Azure-kostnader med Cost Management. Mer information finns i snabb starten [när du analyserar dina kostnader](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).