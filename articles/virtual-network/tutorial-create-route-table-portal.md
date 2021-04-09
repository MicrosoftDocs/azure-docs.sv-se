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
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: f8090ea9c0d307d1bd290c4cf4dac9bfaabf7c4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576361"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Självstudie: Dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen

Som standard dirigerar Azure trafik mellan alla undernät inom ett virtuellt nätverk. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Anpassade vägar är användbara när du till exempel vill dirigera trafik mellan undernät via en virtuell nätverks installation (NVA). I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en NVA som dirigerar trafik
> * Skapa en routningstabell
> * Skapa en väg
> * Associera en routningstabell till ett undernät
> * Distribuera virtuella datorer till olika undernät
> * Dirigera trafik från ett undernät till ett annat via en NVA

I den här självstudien används [Azure Portal](https://portal.azure.com). Du kan också använda [Azure CLI](tutorial-create-route-table-cli.md) eller [Azure PowerShell](tutorial-create-route-table-powershell.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. Välj **nätverk**  >  **virtuellt nätverk** på Azure Marketplace eller Sök efter **Virtual Network** i sökrutan.

2. Välj **Skapa**.

2. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange **myResourceGroup**. </br> Välj **OK**. |
    | Name | Ange **myVirtualNetwork**. |
    | Location | Välj **(US) USA, östra**.|

3. Välj fliken **IP-adresser** eller klicka på knappen **Nästa: IP-adresser** längst ned på sidan.

4. I **IPv4-adress utrymme** väljer du det befintliga adress utrymmet och ändrar det till **10.0.0.0/16**.

4. Välj **+ Lägg till undernät** och ange sedan **offentlig** för **under nätets namn** och **10.0.0.0/24** för **under nätets adress intervall**.

5. Välj **Lägg till**.

6. Välj **+ Lägg till undernät** och ange sedan **privat** för under nätets **namn** och **10.0.1.0/24** för **under nätets adress intervall**.

7. Välj **Lägg till**.

8. Välj **+ Lägg till undernät** och ange sedan **DMZ** för **under nätets namn** och **10.0.2.0/24** för **under nätets adress intervall**.

9. Välj **Lägg till**.

10. Välj fliken **säkerhet** eller Välj **Nästa: knappen säkerhet** längst ned på sidan.

11. Under **BastionHost** väljer du **Aktivera**. Ange den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skydds namn | Ange **myBastionHost** |
    | AzureBastionSubnet-adressutrymme | Ange **10.0.3.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Som **namn** anger du **myBastionIP**. </br> Välj **OK**. |

12. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

13. Välj **Skapa**.

## <a name="create-an-nva"></a>Skapa en NVA

Virtuella nätverks installationer (NVA) är virtuella datorer som hjälper till med nätverksfunktioner, t. ex. Routning och brand Väggs optimering. I den här självstudien förutsätter vi att du använder **Windows Server 2019 Data Center**. Du kan välja ett annat operativsystem om du vill.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVMNVA** |
    | Region | Välj **(US) USA, östra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVirtualNetwork**. |
    | Undernät | Välj **DMZ** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="create-a-route-table"></a>Skapa en routningstabell

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. I rutan Sök anger du **routningstabellen**. När **routningstabellen** visas i Sök resultaten väljer du den.

3. På sidan **routningstabell** väljer du **skapa**.

4. I **skapa routningstabell** på fliken **grundläggande** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** |    |
    | Region | Välj **USA, östra**. |
    | Name | Ange **myRouteTablePublic**. |
    | Sprida Gateway-vägar | Välj **Ja**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Skapa routningstabellen, Azure Portal." border="true":::

5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.

## <a name="create-a-route"></a>Skapa en väg

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera routningstabellen. Sök efter och välj **routningstabeller**.

2. Välj namnet på din routningstabell **myRouteTablePublic**.

3. På sidan **myRouteTablePublic** i avsnittet **Inställningar** väljer du **vägar**.

4. På sidan vägar väljer du knappen **+ Lägg till** .

5. I **Lägg till väg** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Vägnamn | Ange **ToPrivateSubnet** |
    | Adressprefix | Ange **10.0.1.0/24** (adress intervallet för det **privata** under nätet som skapades tidigare) |
    | Nästa hopptyp | Välj **Virtuell installation**. |
    | Nexthop-adress | Ange **10.0.2.4** (en adress inom adress intervallet för **DMZ** -undernätet) |

6. Välj **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **virtuella nätverk**.

2. Välj namnet på ditt virtuella nätverk **myVirtualNetwork**.

3. På sidan **myVirtualNetwork** i avsnittet **Inställningar** väljer du **undernät**.

4. Välj **offentlig** i listan undernät för virtuellt nätverk.

5. I **routningstabellen** väljer du den routningstabell som du skapade **myRouteTablePublic**. 

6. Välj **Spara** för att koppla din routningstabell till det **offentliga** under nätet.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Associera routningstabellen, under näts lista, virtuellt nätverk Azure Portal." border="true":::

## <a name="turn-on-ip-forwarding"></a>Aktivera IP-vidarebefordran

Aktivera sedan IP-vidarebefordring för den nya virtuella NVA-datorn, **myVMNVA**. När Azure skickar nätverks trafik till **myVMNVA**, och om trafiken är avsedd för en annan IP-adress, skickar IP-vidarebefordring trafiken till rätt plats.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera den virtuella datorn. Sök efter och välj **virtuella datorer**.

2. Välj namnet på den virtuella datorn **myVMNVA**.

3. På sidan Översikt över **myVMNVA** i **Inställningar** väljer du **nätverk**.

4. På sidan **nätverk** i **myVMNVA** väljer du nätverks gränssnittet bredvid **nätverks gränssnittet**.  Namnet på gränssnittet kommer att börja med **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Nätverk, virtuell nätverks installation (NVA) virtuell dator (VM), Azure Portal" border="true":::

5. På sidan Översikt över nätverks gränssnitt väljer du **IP-konfigurationer** i **Inställningar**.

6. På sidan **IP-konfigurationer** anger du **IP-vidarebefordran** till **aktive rad** och väljer sedan **Spara**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Aktivera IP-vidarebefordran" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Skapa offentliga och privata virtuella datorer

Skapa en offentlig virtuell dator och en privat virtuell dator i det virtuella nätverket. Senare använder du dem för att se att Azure dirigerar trafik för det **offentliga** undernätet till det **privata** undernätet via NVA.


### <a name="public-vm"></a>Offentlig virtuell dator

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVMPublic** |
    | Region | Välj **(US) USA, östra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVirtualNetwork**. |
    | Undernät | Välj **offentlig** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

### <a name="private-vm"></a>Privat virtuell dator

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVMPrivate** |
    | Region | Välj **(US) USA, östra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVirtualNetwork**. |
    | Undernät | Välj **privat** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

### <a name="sign-in-to-private-vm"></a>Logga in på privat virtuell dator

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera din privata virtuella dator. Sök efter och välj **virtuella datorer**.

2. Välj namnet på den privata virtuella datorn **myVmPrivate**.

3. I meny raden för virtuell dator väljer du **Anslut** och väljer sedan **skydds**.

4. På sidan **Anslut** väljer du knappen blå **användnings skydds** .

5. På sidan **skydds** anger du det användar namn och lösen ord som du skapade för den virtuella datorn tidigare.

6. Välj **Anslut**.

### <a name="configure-firewall"></a>Konfigurera brandvägg

I ett senare steg använder du vägspårningsverktyget för att testa routningen. Vägspårning använder ICMP (Internet Control Message Protocol), vilket Windows-brandväggen nekar som standard. 

Aktivera ICMP via Windows-brandväggen.

1. Öppna PowerShell med administratörs behörighet i skydds-anslutningen för **myVMPrivate**.

2. Ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Du använder spårnings väg för att testa routning i den här självstudien. För produktionsmiljöer rekommenderar vi inte att du tillåter ICMP via Windows-brandväggen.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Aktivera IP-vidarebefordring i myVMNVA

Du [aktiverade IP-vidarebefordran](#turn-on-ip-forwarding) för den virtuella datorns nätverksgränssnitt med hjälp av Azure. Den virtuella datorns operativ system måste också vidarebefordra nätverks trafik. 

Aktivera IP-vidarebefordran för **myVMNVA** med dessa kommandon.

1. Från PowerShell på den virtuella datorn **myVMPrivate** öppnar du ett fjärr skrivbord till den virtuella **myVMNVA** -datorn:

    ```powershell
    mstsc /v:myvmnva
    ```

2. Från PowerShell på den virtuella datorn **myVMNVA** anger du det här kommandot för att aktivera IP-vidarebefordring:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Starta om **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. När **myVMNVA** har startats om skapar du en fjärrskrivbordssession till **myVMPublic**. 
    
    När du fortfarande är ansluten till **myVMPrivate** öppnar du PowerShell och kör det här kommandot:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Öppna PowerShell i fjärr skrivbordet för **myVMPublic**.

6. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testa routningen av nätverkstrafik

Vi börjar med att testa routning av nätverks trafik från **myVMPublic** till **myVMPrivate**.

1. Från PowerShell på **myVMPublic** anger du följande kommando:

    ```powershell
    tracert myvmprivate
    ```

    Svaret liknar det här exemplet:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Du kan se att det första hoppet är till 10.0.2.4, vilket är **myVMNVA** privata IP-adress. 

    * Det andra hoppet är till den privata IP-adressen för **myVMPrivate**: 10.0.1.4. 

    Tidigare lade du till vägen till vägtabellen **myRouteTablePublic** och associerade den till det *offentliga* undernätet. Azure skickade trafiken genom NVA och inte direkt till det *privata* under nätet.

2. Stäng fjärrskrivbordssessionen till **myVMPublic**, vilket låter dig fortfarande vara ansluten till **myVMPrivate**.

3. Öppna PowerShell på **myVMPrivate**, ange följande kommando:

    ```powershell
    tracert myvmpublic
    ```

    Det här kommandot testar routningen av nätverks trafik från den virtuella datorn *myVmPrivate* till den virtuella *myVmPublic* -datorn. Svaret liknar det här exemplet:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Du kan se att Azure dirigerar trafik direkt från *myVMPrivate* till *myVMPublic*. Som standard dirigerar Azure trafik direkt mellan undernät.

4. Stäng skydds-sessionen för **myVMPrivate**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurs gruppen inte längre behövs tar du bort **myResourceGroup** och alla resurser den innehåller:

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera resurs gruppen. Sök efter och välj **resurs grupper**.

2. Välj namnet på din resurs grupp **myResourceGroup**.

3. Välj **Ta bort resursgrupp**.

4. I bekräftelse dialog rutan anger du **myResourceGroup** för **Skriv resurs gruppens namn** och välj sedan **ta bort**. 


## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* Skapade en routningstabell och kopplade den till ett undernät.
* Skapade en enkel NVA som dirigerade trafik från ett offentligt undernät till ett privat undernät. 

Du kan distribuera olika förkonfigurerade NVA från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), vilket ger många användbara nätverksfunktioner. 

Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Information om hur du filtrerar nätverks trafik i ett virtuellt nätverk finns i:
> [!div class="nextstepaction"]
> [Självstudie: filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av Azure Portal](tutorial-filter-network-traffic.md)
