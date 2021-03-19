---
title: 'Snabb start: skapa ett virtuellt nätverk – Azure Portal'
titleSuffix: Azure Virtual Network
description: I den här snabb starten lär du dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606076"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure-portalen

I den här snabb starten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Du distribuerar två virtuella datorer (VM). Därefter kommunicerar du säkert mellan virtuella datorer och ansluter till virtuella datorer från Internet. Ett virtuellt nätverk är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera på ett säkert sätt med varandra och med Internet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. I rutan Sök anger du **Virtual Network**. Välj **Virtual Network** i Sök resultaten.

3. På sidan **Virtual Network** väljer du **skapa**.

4. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **Skapa ny**.  </br> Ange **myResourceGroup**. </br> Välj **OK**. |
    | **Instansinformation** |   |
    | Name | Ange **myVNet**. |
    | Region | Välj **(US) USA, östra**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Skapa ett virtuellt nätverk Azure Portal" border="true":::

5. Välj fliken **IP-adresser** eller klicka på knappen **Nästa: IP-adresser** längst ned på sidan.

6. I **IPv4-adress utrymme** väljer du det befintliga adress utrymmet och ändrar det till **10.1.0.0/16**.

7. Välj **+ Lägg till undernät** och ange sedan **undernät** för **under** nätets namn och **10.1.0.0/24** för **under nätets adress intervall**.

8. Välj **Lägg till**.

9. Välj fliken **säkerhet** eller Välj **Nästa: knappen säkerhet** längst ned på sidan.

10. Under **BastionHost** väljer du **Aktivera**. Ange den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skydds namn | Ange **myBastionHost** |
    | AzureBastionSubnet-adressutrymme | Ange **10.1.1.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> Som **namn** anger du **myBastionIP**. </br> Välj **OK**. |

11. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

12. Välj **Skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM1** |
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
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **mySubnet** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**. 
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM2** |
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
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **mySubnet** |
    | Offentlig IP-adress | Välj **ingen** |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska + skapa** eller Välj knappen blå **granskning + skapa** längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="connect-to-myvm1"></a>Anslut till myVM1

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera din privata virtuella dator. Sök efter och välj **virtuella datorer**.

2. Välj namnet på den privata virtuella datorn **myVM1**.

3. I meny raden för virtuell dator väljer du **Anslut** och väljer sedan **skydds**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Ansluta till myVM1 med Azure skydds" border="true":::

4. På sidan **Anslut** väljer du knappen blå **användnings skydds** .

5. På sidan **skydds** anger du det användar namn och lösen ord som du skapade för den virtuella datorn tidigare.

6. Välj **Anslut**.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell i skydds-anslutningen för **myVM1**.

2. Ange `ping myvm2`.

    Du får ett meddelande som liknar dessa utdata:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Stäng skydds-anslutningen till **myVM1**.

4. Slutför stegen i [Anslut till myVM1](#connect-to-myvm1), men Anslut till **myVM2**.

5. Öppna PowerShell på **myVM2**, ange `ping myvm1` .

    Du får något som liknar det här meddelandet:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Stäng skydds-anslutningen till **myVM2**.

## <a name="clean-up-resources"></a>Rensa resurser

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. 

Du är ansluten till en virtuell dator från Internet och kommunicerat på ett säkert sätt mellan de två virtuella datorerna.

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Sök efter och välj **myResourceGroup**.

1. Välj **Ta bort resursgrupp**.

1. Ange **myResourceGroup** för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om inställningar för virtuella nätverk finns i [skapa, ändra eller ta bort ett virtuellt nätverk](manage-virtual-network.md).

Mer information om typer av nätverkskommunikation för virtuella datorer finns i [filtrera nätverks trafik](tutorial-filter-network-traffic.md).
