---
title: 'Snabbstart: Skapa ett virtuellt nätverk – Azure Portal'
titleSuffix: Azure Virtual Network
description: I den här snabbstarten lär du dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531175"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure-portalen

I den här snabbstarten lär du dig att skapa ett virtuellt nätverk med hjälp av Azure Portal. Du distribuerar två virtuella datorer (VM). Därefter kommunicerar du säkert mellan virtuella datorer och ansluter till virtuella datorer från Internet. Ett virtuellt nätverk är den grundläggande byggstenen för ditt privata nätverk i Azure. Det gör det möjligt för Azure-resurser, som virtuella datorer, att kommunicera säkert med varandra och med Internet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **Skapa en** resurs i det övre vänstra hörnet i portalen.

2. I sökrutan anger du **Virtual Network**. Välj **Virtual Network** i sökresultatet.

3. På sidan **Virtual Network** väljer du **Skapa**.

4. I **Skapa virtuellt** nätverk anger eller väljer du den här informationen **på fliken** Grundläggande:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** |   |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **Skapa ny**.  </br> Ange **myResourceGroup**. </br> Välj **OK**. |
    | **Instansinformation** |   |
    | Name | Ange **myVNet**. |
    | Region | Välj **(USA) USA, östra**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Skapa virtuella Azure Portal" border="true":::

5. Välj fliken **IP-adresser** eller välj **knappen Nästa: IP-adresser** längst ned på sidan.

6. I **IPv4-adressutrymmet** väljer du det befintliga adressutrymmet och ändrar det till **10.1.0.0/16**.

7. Välj **+ Lägg till undernät** och ange sedan **MySubnet** som **undernätsnamn** och **10.1.0.0/24** som **adressintervall för undernätet.**

8. Välj **Lägg till**.

9. Välj **fliken** Säkerhet eller välj **knappen Nästa:** Säkerhet längst ned på sidan.

10. Under **BastionHost** väljer du **Aktivera**. Ange följande information:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Skyddsnamn | Ange **myBastionHost** |
    | Adressutrymme för AzureBastionSubnet | Ange **10.1.1.0/24** |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> I **Namn** anger du **myBastionIP**. </br> Välj **OK**. |

11. Välj fliken **Granska + skapa** eller välj knappen Granska **+** skapa.

12. Välj **Skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Längst upp till vänster i portalen väljer du Skapa **en virtuell dator för**  >    >  **resursbearbetning.** 
   
2. I **Skapa en virtuell dator** skriver eller väljer du värdena på fliken **Grundläggande:**

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projektinformation** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM1** |
    | Region | Välj **USA, östra** |
    | Tillgänglighetsalternativ | Välj **Ingen infrastrukturredundans krävs** |
    | Bild | Välj **Windows Server 2019 Datacenter** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller ta standardinställningen |
    | **Administratörskonto** |  |
    | Användarnamn | Ange ett användarnamn |
    | Lösenord | Ange ett lösenord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken Nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **mySubnet** |
    | Offentlig IP-adress | Välj **Ingen** |
    | Nätverkssäkerhetsgrupp för nätverkskort | Välj **Basic**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

1. Längst upp till vänster i portalen väljer du Skapa **en virtuell dator för**  >    >  **resursbearbetning.** 
   
2. I **Skapa en virtuell dator** skriver eller väljer du värdena på **fliken** Grundläggande:

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projektinformation** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **myResourceGroup** |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM2** |
    | Region | Välj **USA, östra** |
    | Tillgänglighetsalternativ | Välj **Ingen infrastrukturredundans krävs** |
    | Bild | Välj **Windows Server 2019 Datacenter** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj VM-storlek eller ta standardinställningen |
    | **Administratörskonto** |  |
    | Användarnamn | Ange ett användarnamn |
    | Lösenord | Ange ett lösenord |
    | Bekräfta lösenordet | Ange lösenordet igen |
    | **Regler för inkommande portar** |    |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken Nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Välj **myVNet**. |
    | Undernät | Välj **mySubnet** |
    | Offentlig IP-adress | Välj **Ingen** |
    | Nätverkssäkerhetsgrupp för nätverkskort | Välj **Basic**|
    | Nätverk för offentliga inkommande portar | Välj **Ingen**. |
   
5. Välj fliken **Granska +** skapa eller välj den blå knappen **Granska +** skapa längst ned på sidan.
  
6. Granska inställningarna och välj sedan **Skapa**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Ansluta till myVM1

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera din privata virtuella dator. Sök efter och välj **Virtuella datorer.**

2. Välj namnet på den privata virtuella datorn **myVM1.**

3. I menyraden för virtuella datorer väljer **du Anslut** och sedan **Bastion.**

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Ansluta till myVM1 med Azure Bastion" border="true":::

4. På sidan **Anslut** väljer du den blå **knappen Använd Bastion.**

5. På sidan **Bastion** anger du det användarnamn och lösenord som du skapade för den virtuella datorn tidigare.

6. Välj **Anslut**.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell i **skyddsanslutningen för myVM1.**

2. Ange `ping myvm2`.

    Du får ett meddelande som liknar följande utdata:

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

3. Stäng skyddsanslutningen till **myVM1**.

4. Slutför stegen i Anslut [till myVM1,](#connect-to-myvm1)men anslut till **myVM2.**

5. Öppna PowerShell på **myVM2** och ange `ping myvm1` .

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

7. Stäng skyddsanslutningen till **myVM2**.

## <a name="clean-up-resources"></a>Rensa resurser

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. 

Du har anslutit till en virtuell dator från Internet och kommunicerat säkert mellan de två virtuella datorerna.

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser som den innehåller:

1. Sök efter och välj **myResourceGroup.**

1. Välj **Ta bort resursgrupp**.

1. Ange **myResourceGroup** som **SKRIV RESURSGRUPPENS NAMN och** välj Ta **bort.**

## <a name="next-steps"></a>Nästa steg

Mer information om inställningar för virtuella nätverk finns i [Skapa, ändra eller ta bort ett virtuellt nätverk.](manage-virtual-network.md)

Mer information om typer av VM-nätverkskommunikation finns i [Filtrera nätverkstrafik.](tutorial-filter-network-traffic.md)
