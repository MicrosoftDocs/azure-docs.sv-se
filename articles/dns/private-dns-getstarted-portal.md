---
title: Snabbstart – Skapa en privat DNS-zon i Azure med hjälp av Azure Portal
description: I den här snabbstarten skapar och testar du en privat DNS-zon och -post i Azure DNS. Det här är en stegvis guide för att skapa och hantera din första privata DNS-zon och DNS-post med hjälp av Azure Portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: 2115062db8615ca782cb987903e5ebfc83400cd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535454"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Snabbstart: Skapa en privat DNS-zon i Azure med hjälp av Azure Portal

Den här snabbstarten går igenom stegen för att skapa din första privata DNS-zon och DNS-post med hjälp av Azure Portal.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i ett virtuellt nätverk anger du listan med virtuella nätverk som får matcha poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistrering har aktiverats Azure DNS även zonposterna när en virtuell dator skapas, ändrar dess IP-adress eller tas bort.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här snabbstarten med [hjälp Azure PowerShell](private-dns-getstarted-powershell.md) eller [Azure CLI.](private-dns-getstarted-cli.md)

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas en DNS-zon **med namnet private.contoso.com** i en resursgrupp med namnet **MyAzureResourceGroup**.

En DNS-zon innehåller DNS-poster för en domän. Om du vill låta Azure DNS vara värd för din domän skapar du en DNS-zon för det domännamnet.

![Privat DNS zonsökning](media/private-dns-portal/search-private-dns.png)

1. I portalens sökfält skriver du **privata DNS-zoner** i sökrutan och trycker på **Retur.**
1. Välj **Privat DNS zon**.
2. Välj **Skapa privat DNS-zon.**

1. På sidan **Skapa Privat DNS zon** skriver eller väljer du följande värden:

   - **Resursgrupp:** Välj **Skapa ny,** ange *MyAzureResourceGroup* och välj **OK.** Resursgruppens namn måste vara unikt inom Azure-prenumerationen.
   -  **Namn:** Skriv *private.contoso.com* det här exemplet.
1. För **Resursgruppsplats** väljer du **USA, västra centrala.**

1. Välj **Granska + skapa**.

1. Välj **Skapa**.

Det kan ta några minuter att skapa zonen.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Välj befintlig resursgrupp) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | USA, västra centrala      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

Om du vill länka den privata DNS-zonen till ett virtuellt nätverk skapar du en virtuell nätverkslänk.

![Länk för att lägga till virtuellt nätverk](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Öppna **resursgruppen MyAzureResourceGroup** och välj **private.contoso.com** privat zon.
2. I den vänstra rutan väljer du **Virtuella nätverkslänkar**.
3. Välj **Lägg till**.
4. Skriv **myLink** som **länknamn.**
5. För **Virtuellt nätverk** väljer **du myAzureVNet**.
6. Markera kryssrutan **Aktivera automatisk** registrering.
7. Välj **OK**.

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

1. På portalsidan uppe till vänster väljer **du Skapa en resurs** och sedan Windows Server **2016 Datacenter.**
1. Välj **MyAzureResourceGroup** som resursgrupp.
1. Skriv **myVM01** – som namn på den virtuella datorn.
1. Välj **USA, västra centrala** som **Region.**
1. Ange ett namn för administratörens användarnamn.
2. Ange ett lösenord och bekräfta lösenordet.
5. För **Offentliga inkommande portar** väljer du **Tillåt** valda portar och sedan **RDP (3389)** för **Välj inkommande portar.**
10. Acceptera de andra standardvärdena för sidan och klicka sedan **på Nästa: Diskar >**.
11. Acceptera standardinställningarna på sidan **Diskar och** klicka sedan på **Nästa: Nätverk >**.
1. Kontrollera att **myAzureVNet har** valts för det virtuella nätverket.
1. Acceptera de andra standardvärdena för sidan och klicka sedan på **Nästa: Hantering >**.
2. För **Startdiagnostik** väljer **du Av,** accepterar de andra standardvärdena och väljer sedan **Granska + skapa.**
1. Granska inställningarna och klicka sedan på **Skapa.**

Upprepa dessa steg och skapa en annan virtuell dator med namnet **myVM02.**

Det tar några minuter för båda de virtuella datorerna att slutföras.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

 I följande exempel skapas en post med den relativa **namn-databasen** i DNS-zonen **private.contoso.com**, i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är "A", med IP-adressen **myVM01**.

1. Öppna **resursgruppen MyAzureResourceGroup** och välj **private.contoso.com** privat zon.
2. Välj **+ Postuppsättning**.
3. I **Namn** skriver du **db**.
4. För **IP-adress** anger du den IP-adress som visas för **myVM01.** Detta bör registreras automatiskt när den virtuella datorn startades.
5. Välj **OK**.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchning för din **private.contoso.com** privata zonen.

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

När resursgruppen **MyAzureResourceGroup inte** längre behövs tar du bort den för att ta bort resurserna som skapades i den här snabbstarten.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier för Azure DNS Private Zones](private-dns-scenarios.md)
