---
title: Snabb start – skapa en privat Azure-DNS-zon med hjälp av Azure Portal
description: I den här snabb starten skapar och testar du en privat DNS-zon och-post i Azure DNS. Det här är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och-post med hjälp av Azure Portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310051"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Snabb start: skapa en privat Azure-DNS-zon med hjälp av Azure Portal

Den här snabb starten vägleder dig genom stegen för att skapa din första privata DNS-zon och-post med hjälp av Azure Portal.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i ett virtuellt nätverk anger du listan med virtuella nätverk som får matcha poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistrering har Aktiver ATS uppdaterar Azure DNS även zon posterna när en virtuell dator skapas, ändrar dess IP-adress eller raderas.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här snabb starten med [Azure PowerShell](private-dns-getstarted-powershell.md) eller [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas en DNS-zon med namnet **Private.contoso.com** i en resurs grupp med namnet **MyAzureResourceGroup**.

En DNS-zon innehåller DNS-poster för en domän. Om du vill låta Azure DNS vara värd för din domän skapar du en DNS-zon för det domännamnet.

![Sök i Privat DNS zoner](media/private-dns-portal/search-private-dns.png)

1. I Portal Sök fältet skriver du **privata DNS-zoner** i sökrutan och trycker på **RETUR**.
1. Välj **privat DNS zon**.
2. Välj **skapa privat DNS-zon**.

1. På sidan **skapa privat DNS zon** skriver eller väljer du följande värden:

   - **Resurs grupp**: Välj **Skapa ny**, ange *MyAzureResourceGroup* och välj **OK**. Resursgruppens namn måste vara unikt inom Azure-prenumerationen.
   -  **Namn**: Skriv *Private.contoso.com* i det här exemplet.
1. För **resurs gruppens plats** väljer du **västra centrala USA**.

1. Välj **Granska + skapa**.

1. Välj **Skapa**.

Det kan ta några minuter att skapa zonen.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Välj en befintlig resurs grupp) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | USA, västra centrala      |
| **\<IPv4-address-space>**   | 10.2.0.0 \ 16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0 \ 24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

Om du vill länka den privata DNS-zonen till ett virtuellt nätverk skapar du en virtuell nätverkslänk.

![Lägg till virtuellt nätverks länk](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Öppna resurs gruppen **MyAzureResourceGroup** och välj den privata **Private.contoso.com** -zonen.
2. I det vänstra fönstret väljer du **länkar till virtuella nätverk**.
3. Välj **Lägg till**.
4. Skriv en **länk** till **länk namnet**.
5. För **virtuellt nätverk** väljer du **myAzureVNet**.
6. Markera kryss rutan **Aktivera automatisk registrering** .
7. Välj **OK**.

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

1. Välj **skapa en resurs** på Portal sidan längst upp till vänster och välj sedan **Windows Server 2016 Data Center**.
1. Välj **MyAzureResourceGroup** för resurs gruppen.
1. Skriv **myVM01** – för namnet på den virtuella datorn.
1. Välj **västra centrala USA** för **regionen**.
1. Ange ett namn på administratörs användar namnet.
2. Ange ett lösen ord och bekräfta lösen ordet.
5. För **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** för **Välj inkommande portar**.
10. Godkänn de andra standardvärdena för sidan och klicka sedan på **Nästa: diskar >**.
11. Godkänn standardvärdena på sidan **diskar** och klicka sedan på **nästa: nätverks >**.
1. Kontrol lera att **myAzureVNet** har valts för det virtuella nätverket.
1. Godkänn de andra standardvärdena för sidan och klicka sedan på **Nästa: hanterings >**.
2. För **startdiagnostik** väljer du **av**, godkänner de andra standardvärdena och väljer sedan **Granska + skapa**.
1. Granska inställningarna och klicka sedan på **skapa**.

Upprepa de här stegen och skapa en annan virtuell dator med namnet **myVM02**.

Det tar några minuter för båda virtuella datorerna att slutföras.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **Private.contoso.com** i resurs gruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på post uppsättningen är **db.Private.contoso.com**. Post typen är "A", med IP-adressen för **myVM01**.

1. Öppna resurs gruppen **MyAzureResourceGroup** och välj den privata **Private.contoso.com** -zonen.
2. Välj **+ Postuppsättning**.
3. I **namn** skriver du **db**.
4. För **IP-adress** anger du den IP-adress som du ser för **myVM01**. Detta bör registreras automatiskt när den virtuella datorn startas.
5. Välj **OK**.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namn matchningen för din privata **Private.contoso.com** -zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:
   ```
   ping myVM01.private.contoso.com
   ```
   Du bör se utdata som liknar det här:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Nu pingar du det **db**-namn som du skapade tidigare:
   ```
   ping db.private.contoso.com
   ```
   Du bör se utdata som liknar det här:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen **MyAzureResourceGroup** för att ta bort de resurser som skapats i den här snabb starten.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier för Azure DNS Private Zones](private-dns-scenarios.md)

